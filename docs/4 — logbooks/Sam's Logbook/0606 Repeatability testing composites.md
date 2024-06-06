James took the lead on this, so see his logbook. We made composite images to analyse the repeatability. The general idea is to run the gantry in a sequence, taking photos at the "same" location (read: should be the same, but may not be, and that's what we're testing).

James collected a bunch of such photos and provided colour analyses. Here are my analyses which first run a threshold on the images (see [0604 Centroid finding, Day 1](0604%20Centroid%20finding,%20Day%201.md)) to focus in on the details a bit more.

```julia
using Images
```

Here's a simple greyscale overlay.

```julia
# get image size
img = load("1-X180000-Y110000.png")
sum = zeros(Int, size(channelview(img))[2:3]...)

# set total number available
count = 20

for n=1:count

    println("adding $n")

    img = load("$n-X180000-Y110000.png")
    mask = threshold(img, 155, 250)

    sum .+= channelview(mask)

end

composite = sum ./ count
compimg = colorview(Gray, composite)

save("composite.png", compimg)
```

![](composite.png)

Here's a greyscale range pattern.

```julia
# get image size
img = load("1-X180000-Y110000.png")
sum = zeros(Int, size(channelview(img))[2:3]...)

# set total number available
count = 20

for n=1:count

	println("adding $n")

	img = load("$n-X180000-Y110000.png")
	mask = threshold(img, 155, 250)

	sum .+= channelview(mask)

end

composite = clamp01.(sum) ./2
composite .+= mask ./ 2
compimg = colorview(Gray, composite)

save("composite_showing_range.png", compimg)
```

![](composite_showing_range.png)

And here this is, with the varying layers colourised. The idea here is to demonstrate that the drift is occurring in a constant direction, rather than the placement locations being randomly distributed about a central mean location. (Basically, this shows that the error is from the machine frame moving rather than some sort of error inherent in the belts, pulleys and steppers.)

```julia
w = 4056
h = 3040
count = 20
hero = 20
bg = HSV(0,0,0)

colours = fill(bg, h, w)

for n=1:count

	println(n)

	mask = threshold(load("$n-X180000-Y110000.png"), 155, 250)
	hue = (n-1)*359/count
	
	colours[mask .> 0] .= HSV(hue, 0.8, 0.8)

end

heromask = threshold(load("$hero-X180000-Y110000.png"), 155, 250)
colours[heromask .> 0] .= HSV(0, 0, 1)

display(colours)
save("colouredcomposite.png", colours)
```

![](colouredcomposite.png)

Not as pretty as I hoped, but I guess it does get my point across about the type of error and probable source. James is duct taping the frame down as I write this, presumably that he can gather re-run his tests with this source of error largely suppressed.

See James's logbook (of the same date) for more data and to see his processing code and results. I have really only recorded this here as it felt like a somewhat logical continuation on the code that I've written (eg for thresholding) over the past few days — and so that I have a bookmark (so to speak) on the date so I can find this stuff easier next time we do this kind of testing.
