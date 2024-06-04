So it begins. I have a hypothesis that the best approach to identifying snap regions is to capture an image, compute 'pad probabilities' essentially by thresholding a single channel, then finding the centroid of each candidate pad.

Yesterday I figured out how to capture images.

```julia
function capture(shutter=12e-3)
	w = 4608
	h = 2592
	channels = 3 # RGB
	raw = read(`libcamera-still --width $w --height $h --autofocus-on-capture --nopreview --shutter=$(shutter)s --analoggain=1.0 --awb=custom --awbgains=0.67,1.7 --hdr=off --denoise=cdn_fast --hflip --immediate --encoding=rgb --output=-`)
	mat = reshape(raw, channels, w, h)
	colorview(RGB{N0f8}, mat)
end

# example usage
# here with 1/25 sec shutter speed
@time img = capture(1/25)
# typically takes 0.527536 seconds (libcamera-still does a cold start every time)
```

Today I have written a basic thresholding script.

```julia
function threshold(img, lower, upper)

	# extract underlying
	mat = channelview(img)

	# red channel only
	r = mat[1,:,:]

	# threshold it
	a = [
		if px < lower
			UInt8(0)
		elseif px > upper
			UInt8(0)
		else
			UInt8(255)
		end
	for px in r]

	# reinterpret as fixed-point
	b = reinterpret.(N0f8, a)

	# get as an image
	colorview(Gray{N0f8}, b)
end

# example usage
@time thimg = threshold(img, 120, 200)
# `img` is captured in the previous code snippet
# here 120 is the lower threshold
# here 200 is the upper threshold
```

So far, I've exposed three degrees of freedom — shutter speed, lower threshold, and upper threshold. I probably don't need all three. Perhaps a 'range' and 'offset' would be enough, but that's a decision for later. Note however that modifying the shutter speed is one of the most effective and necessary tools available.

And what does this give me?

![](download-2.png)

Nothing great. Let's try a different strategy that is not binary black/white. Note the different parameters this time.

```julia
function threshold2(img, centre::Int, range::Int)

	mat = channelview(img)
	r = mat[1,:,:]

	multiplier::Int = round(255 / (range/2))

	tones = [
		if px < centre - range/2
			UInt8(0)
		elseif px > centre + range/2
			UInt8(0)
		elseif px < centre
			UInt8((centre - px)*multiplier)
		else
			UInt8((px - centre)*multiplier)
		end
	for px in r]

	tonesfp = reinterpret.(N0f8, tones)
	colorview(Gray{N0f8}, tonesfp)

end

@time img = capture(1/20)
@time thimg = threshold2(img, 200, 50)
# 0.047837 seconds (7 allocations: 34.172 MiB)
```

This isn't fantastic either.

![](download-3.png)

It's looking fairly difficult to select between the plating and the silk screen, at least given the present lighting conditions. I'm not sure how much this will change when trying to select solder paste, but I'm getting the impression that it's not super easy to do this with thresholds alone.

I've modded this code to put the current output in the green channel. The red and blue channels now show "hot" and "cold" respectively, making it much easier to tune the centre and see why things are dimming.

```julia
function threshold3(img, centre::Int, range::Int)

	mat = channelview(img)
	r = mat[1,:,:]

	multiplier::Int = floor(255 / range)

	go = [
		if px < centre - range/2
			UInt8(0)
		elseif px > centre + range/2
			UInt8(0)
		elseif px < centre
			UInt8((centre - px)*(multiplier*2))
		else
			UInt8((px - centre)*(multiplier*2))
		end
	for px in r]

	ro = UInt8.(clamp.((r .- (centre - range)).*multiplier, 0, 255))

	bo = UInt8.(clamp.(((centre + range) .- r).*multiplier, 0, 255))

	a = zeros(UInt8, 3, size(r)...)
	a[1,:,:] = ro
	a[2,:,:] = go
	a[3,:,:] = bo

	tonesfp = reinterpret.(N0f8, a)
	colorview(RGB{N0f8}, tonesfp)

end

@time img = capture(1/43)
@time thimg = threshold3(img, 200, 70)
# 0.231807 seconds (13 allocations: 113.907 MiB, 1.09% gc time)
```

![](download-4.png)

This technique isn't as easy as I might have first hoped. There are a few things that might be necessary to make it work.

- Proper lighting. I don't have this set up yet. I suspect we'll have to work out how to light the board to provide the required contrast. This is something that will have to be considered in conjunction with the mechanical design of the machine.
- Some analysis of the optical properties of solder paste. Most importantly: what does it do differently from everything else on a PCB? How would we light it to make it look as different/distinct as possible?
- Consider using software like Photoshop or DaVinci Resolve to process sample images to allow us to experiment with existing effects more.
- Or, can we reframe the question? Instead of selecting *for* the pads, could we select *against* the other features as much as possible, both in this step and in the other steps? This step doesn't exist in isolation, and in theory all we care about is that the final snapping is reliable — not necessarily that we can 100% clean the data at this point. Could we clean it earlier? Could it be permissible to clean it later?
- What other strategies exist? How much effort do we put into them at this point in time?
- How do engineers discern paste from the rest of the board? (This sounds stupid, but it ties into the above point about what makes it different — the texture, perhaps? If that is the case, we could try to replace this tone detection step at least in part with a texture detection step.)

For reference, here's the input image used to generate that final heatmap image (extracted from `img` using `display(img)`).

![](download-5.png)

And here using `save("testimage.png", img)`. This method provides a noticeably sharper image, which is a bit odd. I only chose to try this method to try and manage the colours a little better and avoid unnecessary degradation. Perhaps I should use `save(` more frequently for the documentation, instead of the (admittedly much more convenient) method of dragging/dropping out of the VS Code side panel that automatically shows up.

![](testimage.png)

I'm also noticing that Julia is slow to run code the first time, but faster in subsequent iterations. These times I have noted as comments in the above scripts. Note that this is all running on the Raspberry Pi 5. It can be seen that it's easy to absolutely tank the execution speed of the thresholding algorithm. I'm starting to think that it's incredibly unrealistic to expect the CV algorithm to run anywhere near 30 fps. I'm also starting to wonder if I'll be forced to either optimise the Julia code, or switch to C++ anyway. (That, or perhaps OpenCV has some sufficiently similar, hand-optimised algorithms to offer?)

— Actually, that's not a bad idea… can OpenCV do this part? Can it do something sufficiently similar? How do CV people do this sort of thing?

— Addendum: thanks for these *Algovision* PCBs, James. They are proving surprisingly useful for algorithm visualisation.

# Lighting tests

This wouldn't actually be complete without some form of lighting test. I've since realised that the front-facing 'Algovision' text on the RHS PCB is metal, not silk and is therefore expected to show in the mask. (Yeah, duh.)

Here's the input.

![](withtorchandpaper.png)

I've got a piece of paper just above the camera and I'm shining a torch at it from above. This provides a diffused white light source across a limited angle.

Here's the code.

```julia
using Images

img = load("withtorchandpaper.png")

function threshold(img, lower, upper)

	# extract underlying
	mat = reinterpret.(UInt8, N0f8.(channelview(img)))

	# red channel only
	r = mat[1,:,:]

	# threshold it
	a = [
		if px < lower
			UInt8(0)
		elseif px > upper
			UInt8(0)
		else
			UInt8(255)
		end
	for px in r]

	# reinterpret as fixed-point
	b = reinterpret.(N0f8, a)

	# get as an image
	colorview(Gray{N0f8}, b)
end

@time img2 = threshold(img, 150, 245)
# 0.035001 seconds (9 allocations: 68.344 MiB, 2.80% gc time)

save("withtorchandpaper_mask.png", img2)
```

Here's the output.

![](withtorchandpaper_mask.png)

Not even half bad! That's a really clean mask. (Just a shame that our real device will be using solder paste, not the raw silvery plating.)

Adjusting the thresholds, I can get a mask of the solder mask layer. (Same exposure, with lower bound 10 and upper bound 55.)

![](withtorchandpaper_maskmask.png)

Or even the silkscreen / overlay layer, although there's a bit of tonal overlap so this mask is nowhere near as clean (the copper / metal layer bleeds in). I guess that choosing the lighting is therefore a bit of a compromise: the lighting that creates a clean mask for one layer may not be the same kind of lighting to get the cleanest mask of a different layer. This one is from 55 to 150.

![](withtorchandpaper_silkmask.png)

At any rate, the technique is more promising than I originally gave it credit for, and that all came down to actually paying in a few minutes' thought to the lighting. I'm sure with some more testing and a bit of prototyping I could improve these masks further. I guess I just need some paste now!