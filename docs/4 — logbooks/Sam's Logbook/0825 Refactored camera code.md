I mentioned yesterday that the code would probably need to be slightly refactored in order to make it a bit more viable for application use and swap the paradigm around. Here's my first attempt at laying the groundwork for such an approach.

```julia
using Base.Threads, ImageCore, ImageShow, Images, Test

# setup
fps::Int = 25
width::Int = 64*30
height::Int = 64*30
bytesPerFrame = Int(width*height*3/2)
whq::Int = (width*height)÷4	# quarter width height product

videoStreamCommand = `rpicam-vid --flush -t 0 --camera 0 --nopreview --codec yuv420 --framerate $fps --width $width --height $height --inline --listen -o -`

rtspPort = 4567
mtxPath = "havent-the-foggiest"
ffmpegRtspCommand = `ffmpeg -f rawvideo -pix_fmt yuv420p -s:v 1280x720 -i /dev/stdin -c:v libx264 -preset ultrafast -tune zerolatency -fpsmax 25 -f rtsp rtsp://localhost:$rtspPort/$mtxPath`

#* MAIN LOOP AND SUPPORTING CODE
# will run this in its own thread so it's easier to send control signals to stop it
# could just kill it part way through, but this method ensures that the streams always closed
# and therefore you never get 'resource busy' from the camera

# that said, the main code should most likely be in the main loop for a production version

doMainLoop = false
doMainLoop_lock = ReentrantLock()

function setDoMainLoop(state)
	global doMainLoop, doMainLoop_lock
	lock(doMainLoop_lock) do 
		doMainLoop = state
	end
end

function getDoMainLoop()
	global doMainLoop, doMainLoop_lock
	return lock(doMainLoop_lock) do
		return doMainLoop
	end
end

function startMainLoop()
	setDoMainLoop(true)
	@async main()
end

stopMainLoop() = setDoMainLoop(false)

function main()

	rawFrame = zeros(UInt8, bytesPerFrame)	# primary camera most recent frame

	open(ffmpegRtspCommand) do outStream
		open(videoStreamCommand) do yuvStream	# primary camera Y'UV a.k.a. Y'CbCr stream
		
			while getDoMainLoop()

				# get a frame
				readbytes!(yuvStream, rawFrame)

				# "pipe" frame to ffmpeg
				write(outStream, rawFrame)

				# do CV processing on frame
				# Could either do this synchronously (to this thread) by calling into the processing function.
				# If this doesn't delay the following thread too much then that CV process can pass the computed output data
				# to a seperate logic thread using a Channel (see `help?> Channel`).
				# That or we just `deepcopy(…` the raw frame into a threadsafe secondary buffer and then let all of the CV happen
				# in another thread.
				# I'm not sure at this point which of these options is most favourable. Probably we just pick one and
				# see how it goes, but at any rate, this is the place to put the first bit of logic:
				# TODO
				#? maybe this is where I just directly call into my centroids finding C code?
				# then put the output of that down the channel?

				# sleep for a bit
				# do this if other threads need locked resources (because readbytes! busy waits)
				sleep(1/fps/8) 	# one eighth of frame period (1 sec / fps / fraction)
								# don't want this too small, lest the other threads struggle to line up a lock
								# don't want this too big, as we may on occasion need to pipe / process multiple frames
								# 	in one camera frame period in order to catch up (if we somehow fall behind)

			end
		end
	end
end

#* OTHER ROUTINES

function displayImage(rawFrame)
	# TODO
end
```

This one lacks the inline processing that I had previously, as I'd like to test the performance of this code when fed directly into ffpmeg before going too much further. At that point I might test yesterday's keying approach just for kicks to see how fast that can run, and then after that I'll get to work integrating this with the centroid calculating C code.

I expect that for this CV section, we'll only want to perform the calculations maybe once every 5 or 10 frames, and that there would therefore be some logic that skips the C code for the other 4 or 9 frames.

First though, we need to test with ffmpeg by somehow getting this into the MediaMTX startup stuff. I'll have to talk to James to figure out a final solution, but I suspect the best approach for now will be to;

1. Remove the MediaMTX `runOnInit:` stuff on line 607 of the interface repo's [interface/stream/mediamtx.yml](https://github.com/p4p-83/interface/blob/main/stream/mediamtx.yml).
2. Change line 11 of the [interface/stream/run.sh](https://github.com/p4p-83/interface/blob/main/stream/run.sh) file from `./mediamtx` to instead call `julia the_code_that_I_just_wrote.jl` so that the julia code is the master of all of this.
3. Add a ``open(`./mediamtx`)`` to my julia code to cover that base. (Could use `run` but I that might block the rest of the thread (?) until MediaMTX finishes, which is never happening. `open` is better because it means we can `close` it when we're wanting to wrap everything up, so it'll be cleaner.)

That said, there are probably a few approaches, including bringing the full functionality of the run.sh script into the julia code and then just running that directly.

---

I'm part way through testing, and it seems like the `write(ffmpegOutStream, …` part isn't having the desired effect. (I don't get a stream to connect to in the browser, and I'm also missing the frame logs in the REPL.)

However, a direct pipeline in julia does seem to work:

```julia
##

m = open(mediaMtxCommand)
p = open(pipeline(videoStreamCommand, ffmpegRtspCommand))

##

close(p)
close(m)
```

---

Oh I'm an idiot.

```julia
ffmpegOutStream = open(ffmpegRtspCommand)
```

is very much *not* the same as

```julia
ffmpegOutStream = open(ffmpegRtspCommand, "r+")
```

Turns out you can't write to the process if you've opened it in *read only mode*. Duh. Forgot that was the default…

Goodbye, 3 hours of my life. Nice knowing you.

---

Yeah. It works now. ~~The latency isn't so flash, but I don't know if that's just my hardware and networking etc or if it's my new code being genuinely awful. (And I don't actually *want* to know at this point in time, so any diagnostic work on this front can wait for another day.)~~ I dropped the resolution back the a value comparable to James' original setting, and the latency improved. It's still not a million dollars, but whatever — I've got bigger fish to fry at the moment.

~~Here's the code that is doing the stuff, for the records. (It's also [here.](https://github.com/p4p-83/interface/pull/3))~~

Note: this is very out of date now. See the GitHub instead.

```julia
# run.jl
# 25 Aug '24
# the main application logic for the streaming and (eventually) CV
# sets up MediaMTX and tees the camera data to both CV as desired and FFmpeg

# running this code:
# the main logic of this code is set up to run in a seperate thread for dev purposes
# the best way to run this in a testing environment is to load up a REPL session
# and evaluate this code, either with VS Code, with `julia -i run.jl`, or with `include("run.jl")`

# then once you have a REPL with this code as above, you can control things with
# `start()` to kick everything off
# `stop()` to shut down gracefully (note that you may have to type this blindly — there may be stuff getting printed to stderr — but if you don't spell it wrong it will work despite this)

# BUGS for some reason `start()` seems to fail every second time, just like clockwork (FFmpeg error Could not write header for output file #0 (incorrect codec parameters ?): Invalid data found when processing input     Error initializing output stream 0:0 --)
# BUGS running `start()` too soon after `stop()` fails (error seems to be that the RTSP port couldn't be aquired / had yet to be released:     Connection to tcp://localhost:8554?timeout=0 failed: Connection refused       Could not write header for output file #0 (incorrect codec parameters ?): Connection refused      Error initializing output stream 0:0 --)

using Base.Threads, ImageCore, ImageShow, Images, Test, Crayons.Box

# you need to cd() the REPL (if using) into interface/stream
# eg `cd("interface/stream")`
@test split(pwd(), "/")[end-1:end] == ["interface", "stream"]

# filesystem setup (replaces run.sh)
if !isdir("mediamtx")
	run(`bash setup.sh`)
end

cp("mediamtx.yml", "mediamtx/mediamtx.yml", force=true)

# setup
fps::Int = 25
width::Int = 64*30
height::Int = 64*30
bytesPerFrame = Int(width*height*3/2)
whq::Int = (width*height)÷4	# quarter width height product

videoStreamCommand = `rpicam-vid --flush -t 0 --camera 0 --nopreview --codec yuv420 --framerate $fps --width $width --height $height --inline --listen -o -`

rtspPort = 8554
mtxPath = "cm3"
ffmpegRtspCommand = `ffmpeg -f rawvideo -pix_fmt yuv420p -s:v $(width)x$(height) -i /dev/stdin -c:v libx264 -preset ultrafast -tune zerolatency -fpsmax $fps -f rtsp rtsp://localhost:$rtspPort/$mtxPath`

mediaMtxCommand = `mediamtx/mediamtx`

#* MAIN LOOP AND SUPPORTING CODE
# will run this in its own thread so it's easier to send control signals to stop it
# could just kill it part way through, but this method ensures that the streams always closed
# and therefore you never get 'resource busy' from the camera

# that said, the main code should most likely be in the main loop for a production version

doMainLoop = false
doMainLoop_lock = ReentrantLock()

function setDoMainLoop(state)
	global doMainLoop, doMainLoop_lock
	lock(doMainLoop_lock) do 
		doMainLoop = state
	end
end

function getDoMainLoop()
	global doMainLoop, doMainLoop_lock
	return lock(doMainLoop_lock) do
		return doMainLoop
	end
end

function start()
	@test getDoMainLoop() == false # can't start if already started
	setDoMainLoop(true)
	@async main()
end

stop() = setDoMainLoop(false)

function withMediaMtx(f::Function)
	local mediaMtxStream
	try
		try
			mediaMtxStream = open(mediaMtxCommand)
		catch
			@error "MediaMTX didn't start"
		end

		println("started MediaMTX")
		f()
	
	finally
		try
			close(mediaMtxStream)
		catch
			@warn "MediaMTX didn't close nicely"
		end
	end
end

function withFfmpeg(f::Function)
	local ffmpegOutStream
	try
		try
			ffmpegOutStream = open(ffmpegRtspCommand)
		catch
			@error "FFmpeg didn't start"
		end
		println("started FFmpeg")
		f(ffmpegOutStream)

	finally
		try
			close(ffmpegOutStream)
		catch
			@warn "FFmpeg didn't close nicely"
		end
	end
end

function withCamera(f::Function; cameraNum=0)
	local yuvStream
	try
		try
			yuvStream = open(videoStreamCommand) # primary camera Y'UV a.k.a. Y'CbCr stream
		catch
			@error "camera $cameraNum didn't start"
		end

		println("started camera $cameraNum")
		f(yuvStream)

	finally
		try
			close(yuvStream)
		catch
			@warn "camera $cameraNum didn't close nicely"
		end
	end
end

function main()

	sleep(1)
	println("\n\n")
	println("Started main loop. (Use `stop()` to stop this in future.)" |> MAGENTA_BG)
	
	local ffmpegOutStream, mediaMtxStream, cam1Stream
	rawFrame = zeros(UInt8, bytesPerFrame)	# primary camera most recent frame

	try
		mediaMtxStream = open(mediaMtxCommand, "r")
		println("started MediaMTX" |> MAGENTA_BG)
		sleep(1)
		
		ffmpegOutStream = open(ffmpegRtspCommand, "r+")
		println("started FFmpeg" |> MAGENTA_BG)
		sleep(1)
		
		cam1Stream = open(videoStreamCommand, "r")
		println("started camera" |> MAGENTA_BG)
		sleep(1)
	
		while getDoMainLoop()

			# println("frame loop")

			# get a frame
			readbytes!(cam1Stream, rawFrame)

			# "pipe" frame to FFmpeg
			write(ffmpegOutStream, rawFrame)

			# y = reshape(view(rawFrame, 1:(4whq)), width, height)			# `y`, `u`, and `v` are all views
			# u = reshape(view(rawFrame, 4whq.+(1:whq)), width÷2, height÷2)	# we can therefore construct these 3 in advance
			# v = reshape(view(rawFrame, 5whq.+(1:whq)), width÷2, height÷2)	# their underlying data is always linked to `f`
			# y2 = Float32.(y')
			# u2 = repeat(u', inner=[2,2])
			# v2 = repeat(v', inner=[2,2])
			# display(colorview(YCbCr, y2, u2, v2))

			# do CV processing on frame
			# Could either do this synchronously (to this thread) by calling into the processing function.
			# If this doesn't delay the following thread too much then that CV process can pass the computed output data
			# to a seperate logic thread using a Channel (see `help?> Channel`).
			# That or we just `deepcopy(…` the raw frame into a threadsafe secondary buffer and then let all of the CV happen
			# in another thread.
			# I'm not sure at this point which of these options is most favourable. Probably we just pick one and
			# see how it goes, but at any rate, this is the place to put the first bit of logic:
			# TODO
			#? maybe this is where I just directly call into my centroids finding C code?
			# then put the output of that down the channel?

			# sleep for a bit
			# do this if other threads need locked resources (because readbytes! busy waits)
			sleep(1/fps/8) 	# one eighth of frame period (1 sec / fps / fraction)
							# don't want this too small, lest the other threads struggle to line up a lock
							# don't want this too big, as we may on occasion need to pipe / process multiple frames
							# 	in one camera frame period in order to catch up (if we somehow fall behind)

		end
		println("Got stop signal. Closing up." |> MAGENTA_BG)

	catch err
		println("got error $err")

	finally
		try close(cam1Stream); 		println("closed camera" |> MAGENTA_BG) 	catch err @warn "couldn't close camera stream (err $err)" 		end
		try close(ffmpegOutStream); println("closed FFmpeg" |> MAGENTA_BG) 	catch err @warn "couldn't close FFmpeg instance (err $err)" 	end	
		try close(mediaMtxStream); 	println("closed MediaMTX" |> MAGENTA_BG) 	catch err @warn "couldn't close MediaMTX instance (err $err)" 	end
	end
	println("All done. Main loop out. Use `start()` if you wish to begin again." |> MAGENTA_BG)

end
```

---

It's later in the day, and I've just got compositing to work. See the GitHub for the demo. I've recorded the script that made the data file [here](0825%20Compositing%20tests.md).