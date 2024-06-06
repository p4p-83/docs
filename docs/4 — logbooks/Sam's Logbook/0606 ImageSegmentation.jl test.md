I gave [ImageSegmentation.jl](https://juliaimages.org/latest/pkgs/segmentation/) a go a few days ago using their demo image of a horse. I wasn't particularly convinced at the time, but I figured that I should at least put our image through it before I deleted the test code (it's polluting my desktop).

The code actually works fairly well when operating on the masked image. Here I'm using it to recolour each segment.

This:

![](withtorchandpaper_mask_cropped.png)

Processed with this:

```julia
using Images, ImageSegmentation, Random

# load the mask and get segments
mask = load("withtorchandpaper_mask_cropped.png")
segments = felzenszwalb(mask, 100, 20)

# generate the colour scheme
Random.seed!(13) # make sure it's the same each time
colours = [RGB(rand(3)...) for _=1:length(segment_labels(segments))]

# generate recoloured image from segment labels and new colours
colouredsegs = map(segnum -> colours[segnum], labels_map(segments))

save("withtorchandpaper_mask_cropped_recoloured.png", colouredsegs)
```

Gives:

![](withtorchandpaper_mask_cropped_recoloured.png)

Not half bad! It even partly cleans up the voids in the mask within certain letters (on account of the minimum segment size parameter). It's pretty good for a first attempt, and I'm sure if we pursued this approach, a little fine-tuning could get even cleaner results.

The eventual goal would of course not be to recolour things: the idea is more that this is one possible method of identifying each possible snap location. Where the mask only had "could be copper" and "not copper" data, this segmented representation can tell you both what is copper and to which pad a given piece of copper belongs.

Caveats? Felzenszwalb's algorithm seems very computationally intensive. However, it's probably feasible to write our own, strength-reduced version if need be, given the output would likely be fed into a centroid detection algorithm.