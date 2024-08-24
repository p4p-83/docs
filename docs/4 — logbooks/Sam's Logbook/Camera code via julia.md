I had another crack at this. There are a few steps in here that are quite likely to be increasing latency, but this code demonstrates a few key things nonetheless:

- video capture into julia code without stupid scrolling frames
- decoding colour information
- grabbing frames only as needed in the main code, and having these frames be relatively recent

Here's the code in its entirety. (See the `##` section comments for more info as to what each part does.) Note that you have to run this is sections as otherwise it'll start itself up and then immediately wrap back up.

```julia
using Base.Threads, ImageCore, ImageShow, Images, Test

# setup
fps::Int = 25
width::Int = 64*30
height::Int = 64*30
bytesPerFrame = Int(width*height*3/2)
whq::Int = (width*height)÷4	# quarter width height product

cmd = `rpicam-vid --flush -t 0 --camera 0 --nopreview --codec yuv420 --framerate $fps --width $width --height $height --inline --listen -o -`

# shared variables & thread safety provisions
jobCtrlLock = ReentrantLock()
doReadJob = true

framelock = ReentrantLock()
frame = zeros(UInt8, bytesPerFrame)

# the helper routine that we'll run in the background
# this will keep flushing frames through the `frame` variable
function parallelReadJob()

	global doReadJob, jobCtrlLock
	
	println("spun up the parallel read job")
	
	open(cmd) do yuvStream

		println("aquired stream")
		
		keepGoing = true
		while keepGoing

			# build up the private frame worth of bytes
			lock(framelock) do
				nb = readbytes!(yuvStream, frame, bytesPerFrame)
				@test nb == bytesPerFrame
			end
			
			sleep(1.0/4/fps)

			lock(jobCtrlLock) do
				keepGoing = doReadJob
			end

		end

	end

	println("exiting parallel read job")

end

# methods that interact with the async helper task
function stopParallelReadJob()
	global jobCtrlLock, doReadJob
	@lock jobCtrlLock doReadJob = false
end


function updateFrame!(f)
	return @lock framelock copyto!(f, frame)
end

# stuff that gets used by the "application code"
f = deepcopy(frame)	# initial value needs the correct dimensions
y = reshape(view(frame, 1:(4whq)), width, height)			# `y`, `u`, and `v` are all views
u = reshape(view(frame, 4whq.+(1:whq)), width÷2, height÷2)	# we can therefore construct these 3 in advance
v = reshape(view(frame, 5whq.+(1:whq)), width÷2, height÷2)	# their underlying data is always linked to `f`

# "application code" definitions
function getImage()
	updateFrame!(f)
	y2 = Float32.(y')
	u2 = repeat(u', inner=[2,2])
	v2 = repeat(v', inner=[2,2])
	return colorview(YCbCr, y2, u2, v2)
end

## startup code
job = @async parallelReadJob()

## as needed, looped code
getImage()

# for example
displayImage(t) = getImage() |> display
t = Timer(displayImage, 0, interval=0.75)

## wind-down code
close(t)
stopParallelReadJob()

## for reference
save("appjloutput.png", getImage())
```

A sample frame to prove it works:

![](appjloutput.png)

The colour balance is passable. It's not great, but whatever.

## Keying

Wasn't intending to do this yet, but it wasn't actually that hard. Here's one method.

In this case I'm chroma keying, and converting anything overly red to be black.

```julia
function getImage()
	updateFrame!(f)
	y2 = Float32.(y')
	u2 = repeat(u', inner=[2,2])
	v2 = repeat(v', inner=[2,2])

	# added some code to blacken pixels when red chrominance is above 145
	mask = v2 .>= 145
	y2[mask] .= 0
	u2[mask] .= 128
	v2[mask] .= 128

	return colorview(YCbCr, y2, u2, v2)
end
```

![](appjloutput145.png)

Notice that the red blanket turned black. (As did some of the wood in frame.)

## Next steps

Maybe refactor a bit for speed and otherwise try to connect into FFmpeg — this code needs to run in parallel with the front end video stream, so I do need to make sure that both can get the data.

Possibly it will be best to flip the paradigm of the above code. The above code reads the camera data in an auxiliary thread, and the main thread is left to the user code that interprets the occasional frame (supplied by the aux thread). This is good for prototyping, as running things in the main context is really easy. However, in future I think a better approach would be to use the camera data code as the master code and then run all other things seperate to that. This means I'll just run a loop that will read in one frame at a time and then do stuff based on that. It might send every frame to an ffmpeg instance using `write(io…)` and might send every tenth frame to a dedicated CV thread.