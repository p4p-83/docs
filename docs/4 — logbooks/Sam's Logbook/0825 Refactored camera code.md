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

First though, we need to test with ffmpeg by somehow getting this into the MediaMTX startup stuff. I'll have to talk to James to figure out a final solution, but I suspect the best approach for now will be to;

1. Remove the MediaMTX `runOnInit:` stuff on line 607 of the interface repo's [interface/stream/mediamtx.yml](https://github.com/p4p-83/interface/blob/main/stream/mediamtx.yml).
2. Change line 11 of the [interface/stream/run.sh](https://github.com/p4p-83/interface/blob/main/stream/run.sh) file from `./mediamtx` to instead call `julia the_code_that_I_just_wrote.jl` so that the julia code is the master of all of this.
3. Add a ``open(`./mediamtx`)`` to my julia code to cover that base. (Could use `run` but I that might block the rest of the thread (?) until MediaMTX finishes, which is never happening. `open` is better because it means we can `close` it when we're wanting to wrap everything up, so it'll be cleaner.)

That said, there are probably a few approaches, including bringing the full functionality of the run.sh script into the julia code and then just running that directly.