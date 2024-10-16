My success with the rotational stuff earlier today went to my head, and I decided that the wicking step surely couldn't be that hard, so I had a go at it. (I also have a photonics assignment that I'm trying to productively avoid.)

My implementation does not work the way I thought it would, but if this morning's experience is anything to go by then that's probably why it actually does work.

Basically, we assume that the previous two steps got all of the pads aligned to a pretty good degree — this means that we can make pairs of pads and leads, 1:1, and analyse the quality of fit of each pair. Mathematically, I just calculate the mean positional error and mean angular error and then make a correction by that amount.

## Graphical explanation

We take a haphazardly simulated input (this is supposed to be the output of the rotation stage).

![](before-wicking.png)

We do some vector math to calculate the individual errors (little white arrows — you might need to view this page in dark mode) and then the mean errors. The mean errors are a little difficult to represent visually, but I've tried to show the average positional error in blue and the average rotational error with the grey dashed line (which deviates from the $y$ axis by the error angle).

![](analysed-wicking%201.png)

We then just apply opposite positional and rotational offsets to the pads to correct the error. (Well, that's what we do in simulation. On the machine we make this correction by jogging the head.)

![](after-wicking.png)

## The code

> [!NOTE] Edit notes
> I've had to come back and edit this code after the fact to add the work coordinate system bit. Turns out that the previous version (without this mod) was overly sensitive to asymmetric footprints. This new version uses the mean of all of the lead centroids as the origin for the rotational transforms.
> 
> This may be something to keep in mind in future if weighting by centroids (will have to consider weighting the reference centre of the component too).
> 
> I've also added a weighting and modified the angular error calculation function to deal with a few glitches I was observing.
> 
> (I've also added some `sleep`s in there so that you can watch the algorithm tackle the problem. You can still replay this as normal using the plot navigation arrows in VS Code.)

```julia
# the final stage!

# we'll assume that all placements are good, meaning that all lead centroids are nearest their final pad centroid.

using Plots, Statistics
default(
	fontfamily="LinLibertine_Rah",
	size=(720, 720),
	label="",
	
	# logbook
	background_color=:transparent,
	foreground_color=:gray,

	# discord
	# background_color="#1e1e1e",
	# foreground_color="#ccc",
)

j = 1im
deg = 2π/360	# conversion factor that looks like a unit

## set up with default positions

# leads
leads::Vector{ComplexF64} = [
	-1 + 2j
	# +0 + 2j
	+1 + 2j

	+2 + 1j
	+2 + 0j
	# +2 - 1j

	+1 - 2j
	# +0 - 2j
	# -1 - 2j

	-2 - 1j
	-2 + 0j
	-2 + 1j

	4.5
	4.5 + 4.5j

]

# pads
pads = copy(leads)	# we'll start with them matching, but they could be in literally any order (well, not really, but we'll go with it for now)
pads .*= exp(j*-8deg)
pads .*= 1.1	# scale factor, might occur on a board
pads .+= 0.4 + 0.1j	# mostly taken out by the positioning step, but still worth simulating

## work coordinate system

pivot = mean(leads)
pads .-= pivot
leads .-= pivot

## vis

scatter(pads, color=:lightgreen, xlims=(-5, 5), ylims=(-5, 5), label="pads")
scatter!(leads, color=:black, label="leads", title="Before wicking") |> display
savefig("images/before-wicking.png")
# sleep(1)

## make pairs

pairs = []

for l in leads

	# TODO (?) make sure matching is exactly 1 lead to 1 pad (or 0 lead to 1 pad) — at the moment there's nothing stopping a pad being matched with 2+ leads

	deltas = abs.(l .- pads)
	nearestPadIndex = argmin(deltas)
	p = pads[nearestPadIndex]

	push!(pairs, (l, p))

end

errorLevers = [x[1] for x in pairs]
errorArms = [x[2] - x[1] for x in pairs]

quiver!(
	[real(x[1]) for x in pairs],
	[imag(x[1]) for x in pairs],
	quiver=(
		[real(e) for e in errorArms],
		[imag(e) for e in errorArms]
	),
	color=:white,
	label="ideal pad movement",
	title="Wicking analysis",
)

## calculate positional error

posError = mean(errorArms)
quiver!([0], [0], quiver=([real(posError)], [imag(posError)]), color=:lightblue, label="overall pad movement")

## calculate rotational error

function angularError(lever, arm)
	a = angle(lever+arm) - angle(lever)
	while a < -180 a += 360deg end
	while a >= 180deg a -= 360deg end
	println(a)
	return a
end

errorLeverLengths = abs.(errorLevers)
angleAdjustments = angularError.(errorLevers, errorArms)
rotationalError = mean(angleAdjustments .* errorLeverLengths) / mean(errorLeverLengths)

# plotting
guideStart = 4j
guideEnd = guideStart*exp(j*rotationalError)
guideDiff = guideEnd - guideStart
quiver!([real(guideStart)], [imag(guideStart)], quiver=([real(guideDiff)], [imag(guideDiff)]), color="#777")
plot!([0+0j, guideEnd], linestyle=:dash, color="#777") |> display
savefig("images/analysed-wicking.png")
#sleep(1)

## correct that error
pads .-= posError
pads .*= exp(-j*rotationalError)

scatter(pads, color=:lightgreen, xlims=(-5, 5), ylims=(-5, 5), label="pads")
scatter!(leads, color=:black, label="leads", title="After wicking") |> display
savefig("images/after-wicking.png")

rotationalError/1deg
```