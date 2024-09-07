It's like the [last entry](0907%20Rotational%20alignment%20concept.md), except it works better! How good. I'd probably say that this one (after some in-situ tuning) works well enough already to be used in the research.

## The theory

- Compute the arguments/radius for each lead centroid.
- You go pad-wise, comparing the pad centroid argument to each lead argument bin label. If they match within some tolerance (which in a more sophisticated algorithm could be defined based on the sizes of the two centroids in question) then you declare them a possible match and you append the pad centroid angle to an array.
- You now have an array of angles which correspond to possible matches. You then do some sort of ranking to pick the best placement angle, likely by looking for the most common value within some tolerance band, and then taking the average value within that tolerance band.

This is probably only appropriate when you have many leads to match. It'd probably be necessary to add provision to weight the quality of each match to improve the quality of results, particularly when there are only a few pads.

- A weighting method could be achieved by discretising the angles. You set up bins for each 10° angular range. As you go pad-wise, you calculate the quality of the match based on the inverse of the pad argument–lead argument separation, and increment the value of the bin corresponding to the pad angle by that amount.
- You then rank the placement locations simply by ranking the bins, and then pick the best placement location.

This last method is better in my eyes, and I'll implement it first. However, I suspect that it could suffer a little more than it should when pads fall at the edge of the bins, so I may in future need to add some form of oversampling: using more bins than are needed at the outset, and then doing a rolling average across a few bins (a convolution) to produce a better representation of angles.

## The implementation

I just implemented this, and yeah, it blows the first method out of the water. Hands down this is the better option, not least because it gives the user a second choice (and third, and fourth, … ) in case the first option glitches — just as we were pitching from the design point of view.

**Input**

![](before-bins.png)

**Analysis**

![](ranking-bins.png)

**Output**

![](after-bins.png)

**The code**

The algorithm does end up having to compare every pad to every lead, which could in theory give a large number of comparisons, but I genuinely do not have time to care about that at this point. That point aside, I think it's pretty clean and self-explanatory, and it even seems to work when I introduce unrelated pads.

```julia
using Plots
default(
	fontfamily="LinLibertine_Rah",
	background_color=:transparent,
	foreground_color=:gray,
	size=(720, 720),
)

j = 1im
deg = 2π/360	# conversion factor that looks like a unit

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

pads = copy(leads)	# we'll start with them matching, but they could be in literally any order (well, not really, but we'll go with it for now)

# add spurious pads that are in theory not part of the footprint
# push!(pads, 0+0j)
# push!(pads, 1+0j)
# push!(pads, -3+1j)
# push!(pads, -4+1j)
# push!(pads, 5+5j)

## work coordinate system
# the whole thing works off a reference pad, so we might as well prototype it as such

pivot = leads[1]
pads .-= pivot
leads .-= pivot

## misalignment
# this is what we'll eventually be trying to calculate and fix

pads .*= exp(j*92deg)
pads .*= 1	# scale factor, might occur on a board
pads .+= 0j	# mostly taken out by the positioning step, but still worth simulating

## vis

scatter(pads, color=:lightgreen, xlims=(-5, 5), ylims=(-5, 5), label="pads")
scatter!(leads, color=:magenta, label="leads") |> display
savefig("before-bins.png")

## construct bins

binSize = 3deg
numBins = 360deg/binSize |> round |> Int
binSize = 360deg/numBins
binLabels = binSize .* ((1:numBins) .- 0.5)
bins = zeros(Float64, numBins)

## work out argument bands

leadCoords = [(abs(l), angle(l)) for l in leads]
argTol = 0.5 # hard-code

## go pad-wise

for p in pads

	r = abs(p)
	φ = angle(p)

	# see which arg bands it might touch
	for l in leadCoords
		if isapprox(r, l[1], atol=argTol)
			# pad can intersect the lead at the right angle
			
			# calculate that angle
			angle = φ - l[2]
			while angle < 0deg angle += 360deg end
			while angle >= 360deg angle -= 360deg end

			# calculate the quality at that angle
			quality = 1 # TODO

			# find the relevant bin
			binNum = 1 + (angle/binSize |> floor |> Int)

			# store in the bin
			bins[binNum] += quality

		end
	end

end

# visualise
bar(binLabels/1deg, bins,
	color=:lightblue,
	xlabel="rotation being considered, degrees",
	ylabel="favourability, relative",
	title="Plot showing relative quality of all possible rotational corrections"
) |> display
savefig("ranking-bins.png")

# rank them
sp = sortperm(bins, rev=true)

rankedAngles = binLabels[sp]

## correct the alignment

newPads = pads .* exp(-j*rankedAngles[1])
newPads2 = pads .* exp(-j*rankedAngles[2])

scatter(newPads2, color=:green, label="pads (backup choice)")
scatter!(leads, color=:magenta, label="leads")
scatter!(newPads, color=:lightgreen, xlims=(-5, 5), ylims=(-5, 5), label="pads (best fit)") |> display
savefig("after-bins.png")
```