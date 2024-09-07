
> [!NOTE]
> Already supplanted by [0907 Rotational alignment concept development](0907%20Rotational%20alignment%20concept%20development.md)


I've been trying to think through how alignment could work, and it's really a little challenging. Presently I'm trying to see if there's any overly simple method of doing it, and the most bare-bones method I could think of is a simple least-squares line of best fit on a scatter plot of the polar-coord-space angle of each centroid.

```julia
using Plots, Random, Statistics, CurveFit
default(
	fontfamily="LinLibertine_Rah",
	background_color=:transparent,
	foreground_color=:gray,
	size=(720, 720),
)
Random.seed!(1)

j = 1im

## set up with default positions

leads::Vector{ComplexF64} = [
	-1 + 2j
	+0 + 2j
	+1 + 2j

	+2 + 1j
	+2 + 0j
	+2 - 1j

	+1 - 2j
	+0 - 2j
	-1 - 2j

	-2 - 1j
	-2 + 0j
	-2 + 1j

]

pads = shuffle(leads)	# we'll start with them matching, but they could be in literally any order (well, not really, but we'll go with it for now)

## work coordinate system
# the whole thing works off a reference pad, so we might as well prototype it as such

pivot = leads[1]
pads .-= pivot
leads .-= pivot

## misalignment
# this is what we'll eventually be trying to calculate and fix

pads .*= exp(j*0.5π)
pads .*= 1.05	# scale factor, might occur on a board
pads .+= 0.01j	# mostly taken out by the positioning step, but still worth simulating

## vis

scatter(pads, color=:lightgreen, xlims=(-5, 5), ylims=(-5, 5))
scatter!(leads, color=:magenta)
savefig("before.png")

## alternate view
# let's see what we can do about rotational errors — can we just ignore radius entirely?

padAngles = angle.(pads)
leadAngles = angle.(leads)

sortedPadAngles = sort(padAngles)
sortedLeadAngles = sort(leadAngles)

scatter(sortedPadAngles, sortedLeadAngles, label="current lead/pad matching")
observedIntercept, observedSlope = linear_fit(sortedPadAngles, sortedLeadAngles)

plot!([-π,π], [-π, π], color=:gray, linestyle=:dash, label="fit of the by-definition correct placement") # the line that we'd expect to see if the placement was perfect
plot!([0, 2π], [observedIntercept, observedIntercept+2π*observedSlope], label="fit of the present placement")	# fit of our less great placement

correctionAngle = observedIntercept

scatter!(sortedPadAngles.+correctionAngle, sortedLeadAngles, label="expected lead/pad matching after rotation")	# corrected placements
savefig("fits.png")


## make correction and revisualise
correctedPads = pads .* exp(j*correctionAngle)

scatter(correctedPads, color=:lightgreen, xlims=(-5, 5), ylims=(-5, 5), label="pads")
scatter!(leads, color=:magenta, label="leads")
savefig("after.png")

```

Somehow it requires that slight positive displacement on the pads in order to give a sensible result. I'm not sure that is in itself a deal-breaker though, as we can just just slightly offset the work coordinate system to ensure this.

The biggest area for improvement would lie in better matching the centroids (at the moment I just sort by angle, but this isn't the best option) and then in making sure the curve fitting has a slope of 1. I haven't done this here as there's likely a more algorithmic, mathematical approach to do all of this better — this is just a proof-of-concept.

**Before**

This algorithm picks up after the position alignment stage — in that stage, we adjusted the work coordinate system to perfectly align one pad with one lead (and we probably moved the machine to the new position too).

![](before%201.png)

**Analysis**

We work out the amount of angular error with "statistics".

![](fits%201.png)

**Correction**

We look at the difference in the intercept of the two lines of best fits, as this tells us how off the rotation of the component is. We then spin the pads by that much (yes, my work coordinate system frame of reference is maybe a little poorly thought out) so that the component can be placed in the right spot.

![](after.png)

I have observed that any negative *y* offset in the reference pad, however slight, will wreak havoc with the placement. A positive offset will create a rotational error too, but nowhere near as severely or quickly and this might be something that can be corrected for if the slope of the line of best fit is constrained to unity. I have (as above) tested the effect of a scale error and it seems that this is tolerated just fine (I guess because the angles remain in the same order). I have yet to test the effects of neighbouring unrelated pads.

This approach isn't perfect, but I think it shows that a statistical approach could be the way to go.

---

- Perhaps the reference pad should be removed from the set of pads being tested?
- Perhaps the argument (you know, the polar radius) could/should be used to select which pads can match with which other pads so that the matches are optimised for quality.
- We can then use the average angular error of the matched pairs to calculate the correction angle. (This would probably be possible if we neglected angle when doing the matching of pads.)

Alternatively, what about a "two point" approach? You take the most salient lead centroid and look for an appropriately salient and plausibly positioned pad centroid on the board. You then maybe use a third point as a check point, but basically, you try to define the rotation based on this second pad.

- This approach lends itself to direct user input as a possible fallback.
- I guess the stats approach is the expansion of this — but perhaps I implement with two points first (so I can figure out how that works) and then work on generalising it / improving the robustness with some stats.
- A "two point" approach would be great for resistors and capacitors and whatnot, as their shouldn't (if we make high quality masks) be any more than two centroids on these — they have but two leads!
- Ranking by centroid saliency and limiting the number of them used would in theory help improve robustness against mask noise.

Notice that if I implement this approach, I have three stages, and the following rhythm:

1. Snap the reference pad. This pays attention to only 1 centroid.
2. Snap the rotation. This has now factored in 2 centroids.
3. Optimise the fit. This factors in all of the centroids.

I think this brings back a bit closer to the original idea (which was supposedly reasonably easy to do).