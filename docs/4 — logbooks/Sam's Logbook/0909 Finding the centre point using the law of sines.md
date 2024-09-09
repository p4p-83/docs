Last night's method to locate the centre involved fitting $y=mx+b$ algebraic line formulae and then subbing into an algebraic expression (rearranged in advance) to give the point of intersection between the lines. Unfortunately, I was not convinced by this method as it may break with vertical lines that have an $x$-intercept ($m\to \infty$).

I decided to try a geometric approach. After wildly over-complicating it, I realised that I just had one big triangle and I could try to solve it directly. Turns out I'm not the first person to think about triangulation with non-right-angle triangles.

Here's a good landing page listing the best solution method for different types of triangles. <https://www.mathsisfun.com/algebra/trig-solving-triangles.html>

As I had all three angles but only one side, the law of sines was the most appropriate. <https://en.wikipedia.org/wiki/Law_of_sines>

All I had to do was implement it in code. First I did it by decomposing the complex numbers. Then I did it again, this time trying to just use division, multiplication, addition, subtraction, and normalisation (no decompositions / angles / $cis$ functions), but spoiler: I failed. Oh well. Both seem to be pretty solid, although the first is a bit more readable.

```julia
# … continued from earlier code

# just use the law of sines
# https://en.wikipedia.org/wiki/Law_of_sines

## law of sines, implemented by hand with angles

# start at first midpoint
# includedAngleSeenByMidpoint1 = angle(normals[1]) - angle(midpoints[2]-midpoints[1])
includedAngleSeenByMidpoint2 = angle(normals[2]) - angle(midpoints[1]-midpoints[2])
includedAngleSeenByCentrepoint = angle(normals[1]) - angle(normals[2])
sideBetweenMidpoints = abs(midpoints[2] - midpoints[1])

# law of sines
sideBetweenMidpoint1AndCentrepoint = sideBetweenMidpoints * sin(includedAngleSeenByMidpoint2) / sin(includedAngleSeenByCentrepoint) * cis(angle(normals[1]))

# vector sum
centre = midpoints[1] + sideBetweenMidpoint1AndCentrepoint

# visualise this
quiver!([midpoints[1]], quiver=[reim.(sideBetweenMidpoint1AndCentrepoint)])

## law of sines, but I attempted to do it without breaking the complex numbers up
# (didn't succeed at that)

norm(z::Complex) = z / abs(z)

m1 = midpoints[1]
m2 = midpoints[2]
m1_c = norm(normals[1])
m2_c = norm(normals[2])

m1_m2_fulllength = m2-m1
m1_m2 = norm(m1_m2_fulllength)
m1_m2_c = m1_m2/m2_c
m1_c_m2 = m1_c/m2_c

m1_c_fulllength = m1_m2_fulllength * imag.(m1_m2_c) / imag.(m1_c_m2) * m1_c / m1_m2

quiver!([m1], quiver=[reim.(m1_c_fulllength)])
scatter!([centre], label="calculated centre point", color=:red)

savefig("triangulation.pdf")
plot!() |> display
```

And we find the centre with a minimum of hassle.

![](triangulation.png)

I think this is about as clean as the math is going to get. All that remains is to tidy up the variable naming, add the algorithmic bits, and refactor into a nice set of code to pull into the prototype.

---

I was impatient. Here it is, with the names a bit more standardised. Still hurts my brain, but it seems to work and be resilient (no obvious issues with the sign convention).

```julia
## solve for centre point using the law of sines
# https://en.wikipedia.org/wiki/Law_of_sines

# these are given to us in the algorithm
m1 = midpoints[1]					# x denotes phasor with head at point x
m2 = midpoints[2]
angle_n1 = angle(normals[1]) 		# angle_x denotes angle of phasor x from the real axis
angle_n2 = angle(normals[2])

# calculate required angles and lengths from given data
m1_m2 = m1 - m2						# x_y denotes a line / vector thing with START y and END x (i.e. x-y)
									# this one is the bridge between the two midpoints in question
m1_m2_horiz = angle(m1_m2)			# angle subtended from real axis to m1 about m2 (again, label the end point first)
c_m2_m1 = angle_n2 - m1_m2_horiz	# angle subtended about m2 from c to m1
m1_c_m2 = angle_n1 - angle_n2

# apply law of sines
c_m1 = cis(angle_n1) * abs(m1_m2) * sin(c_m2_m1) / sin(m1_c_m2)

# get centre point with 'vector' addition
effectiveCentreOfRotation = m1 + c_m1
scatter!([effectiveCentreOfRotation], label="calculated intersection point", color=:red)
quiver!([m1], quiver=[reim.(c_m1)])
```

![](triangulation%201.png)

I actually still need to implement the parallelism check between `angle_n1` and `angle_n2`, but that's pretty trivial. I wonder if I also need a check to make sure `m1` and `m2` aren't too close together, or if the parallelism check would already prevent this from causing problems?

The thing I like about this method is that the answer is in theory well defined regardless of the orientations of the normal vectors. Unlike the previous implementation, it won't immediately die if one of the normal vectors is vertical. The only way the length $\overrightarrow{CM_1}$ will go to zero is if the angle $\angle CM_1M_2$ is zero, which can only happen if the centre point is on top of $M_1$, so this is actually correct behaviour. The only way that the length $\overrightarrow{CM_1}$ can blow up to infinity is if the angle $\angle M_1CM_2$ goes to zero, and that can only happen if the lines are parallel or essentially parallel — this is something we test for and guard against anyway. Put simply, this method should be much better behaved that what yesterday's method would have achieved.

---

I've realised that I actually do need the tests to cope with a scenario where the error vectors are parallel. In this case, so long as they are sufficiently far apart and in opposition, I think the best option is to just set the rotation point as the average point between them, perhaps weighted to the side of the smaller error vector. This is easier explained visually:

![](Image%2010.jpeg)

So, this makes the algorithm branching slightly more complex for the peerwise comparisons:

1. If the angle between the normals is approximately 0°, they are parallel and the underlying errors can be said to be pointing in the same direction. The error is therefore purely a translation, and *no centre of rotation should be calculated for this pairing*.
2. If the angle between the normals is approximately 180°, they are parallel but the underlying errors are in opposition. This therefore suggests a pure rotation, so a *centre of rotation should be calculated* and can be calculated by a weighted average of the error midpoints.
3. In all other cases the lines are not perfectly parallel and a point of intersection will exist. This point of intersection gives the desired centre of rotation, so we calculate it using the law of sines (as already described).

We then look at the results from all of the peerwise comparisons:

1. If no virtual centre estimates were made, we assume that all comparisons were suggestive of pure translation, and therefore there will be no rotational correction and only a translational correction. We calculate the translational correction as the average value of the error vectors.
2. If at least one virtual centre estimate was made, we assume that we need rotation and a corrective translation (which will be zero in the special case that the virtual centre of rotation is coaxial with the actual nozzle axis of rotation). The appropriate steps in this case are noted below and documented in the code snippet.
	1. Choose a single virtual centre of rotation by averaging all of the estimates.
	2. Determine the appropriate rotational angle by calculating the angular change of all of the points as seen from the virtual centre of rotation and taking the average value.
	3. Calculate the corrective translation by observing the linear error created when the virtual centre of rotation is rotated about the true rotational axis by the calculated rotational correction value.
	4. Drive the machine. Rotate the nozzle by the calculated rotational correction value and translate it by the translational correction value.

```julia
# steps to correct the positions where a virtual centre of rotation can be identified.

## correct the positions

realCentreOfRotation = -0.3+0.1j

scatter(points, xlims=(-6,6), ylims=(-6,6), color=:white, label="current")
scatter!(points2, color=:lightgreen, label="desired")

scatter!([effectiveCentreOfRotation], label="desired rotational axis", color=:black, markershape=:xcross)
scatter!([realCentreOfRotation], label="available rotational axis", color=:black, markershape=:cross)

# work out the movements
getRequiredRotation(startpoint, endpoint) = angle(endpoint-effectiveCentreOfRotation) - angle(startpoint-effectiveCentreOfRotation)
individualRotations = getRequiredRotation.(points, points2)
chosenRotation = mean(individualRotations)

errorAfterOffAxisRotation = (effectiveCentreOfRotation-realCentreOfRotation)*(cis(chosenRotation)-1)
correctiveTranslation = -errorAfterOffAxisRotation

# simulate movements
points3 = (points.-realCentreOfRotation) .* cis(rotation) .+ realCentreOfRotation
points3 .+= correctiveTranslation

# verify
scatter!(points3, label="after corrections", color=:darkgreen, markershape=:xcross)

savefig("images/making the corrections.png")
plot!() |> display
```

![](making%20the%20corrections.png)

This algorithm is shaping up to have a few more branches than I would have expected and wanted, but I don't think that's the end of the world. The number of branches is still manageable so I don't think I'm too at risk of introducing bugs due to branching edge-cases.

---

Actually, no, there are still issues. The present implementation behaves nonsensically when the normal vectors are in opposition, full stop. It puts the centre of rotation outside when clearly it must be on the inside.

Also, geometrically speaking, there are actually two possible solutions to this and we've only found one.

We can probably test for this scenario by comparing the angles of the normal vectors. If there is an angle of >90° between them, they are in opposition, and the present method is unfortunately incorrect.

> [!QUESTION] Realism
> How likely is this in practice? Is it physically possible? How would the correct behaviour even look?

![](verification-of-vcr-code.png)

```julia
using Plots; default( fontfamily="LinLibertine_Rah", size=(720, 720), label="", background_color="#fffe", background_color_inside=:transparent, foreground_color="#777", dpi=300 )
using Statistics

deg = 2π/360
j = im

## solve for centre point using the law of sines
# https://en.wikipedia.org/wiki/Law_of_sines

# these are given to us in the algorithm
m1 = 1+j					# x denotes phasor with head at point x
m2 = -m1
midpoints = [m1, m2]
angle_n1 = angle(-1) 		# angle_x denotes angle of phasor x from the real axis
angle_n2 = angle(1+0.3j)
normals = cis.([angle_n1, angle_n2])

scatter(midpoints, xlims=(-6,6), ylims=(-6,6), color="#555", label="error vector centre points")
quiver!(midpoints, quiver=reim.(normals), color="#aaa")
plot!(midpoints[1] .+ [0, 15normals[1]], color="#aaa", linestyle=:dash)
plot!(midpoints[2] .+ [0, 15normals[2]], color="#aaa", linestyle=:dash)
plot!(midpoints[1:2], color="#aaa", linestyle=:dash)
quiver!(midpoints.-0.5normals./j, quiver=reim.(normals./j), color="#555")

# calculate required angles and lengths from given data
m1_m2 = m1 - m2						# x_y denotes a line / vector thing with START y and END x (i.e. x-y)
									# this one is the bridge between the two midpoints in question
m1_m2_horiz = angle(m1_m2)			# angle subtended from real axis to m1 about m2 (again, label the end point first)
c_m2_m1 = angle_n2 - m1_m2_horiz	# angle subtended about m2 from c to m1
m1_c_m2 = angle_n1 - angle_n2

# apply law of sines
c_m1 = cis(angle_n1) * abs(m1_m2) * sin(c_m2_m1) / sin(m1_c_m2)

# get centre point with 'vector' addition
effectiveCentreOfRotation = m1 + c_m1
scatter!([effectiveCentreOfRotation], label="calculated intersection point", color=:red)
quiver!([m1], quiver=[reim.(c_m1)])
```

Problem is, it's a bit difficult to know what's actually supposed to happen here. Where even is the centre supposed to be‽

![](verification-of-vcr-code%201.png)

```julia
using Plots; default( fontfamily="LinLibertine_Rah", size=(720, 720), label="", background_color="#fffe", background_color_inside=:transparent, foreground_color="#777", dpi=300 )
using Statistics

deg = 2π/360
j = im

## solve for centre point using the law of sines
# https://en.wikipedia.org/wiki/Law_of_sines

# these are given to us in the algorithm
m1 = 1+j					# x denotes phasor with head at point x
m2 = -m1
midpoints = [m1, m2]
angle_n1 = angle(-1) 		# angle_x denotes angle of phasor x from the real axis
angle_n2 = angle(1+0.3j)
normals = cis.([angle_n1, angle_n2])
errors = normals./j

scatter(midpoints, xlims=(-6,6), ylims=(-6,6), color="#555", label="error vector centre points")
quiver!(midpoints, quiver=reim.(normals), color="#aaa")
plot!(midpoints[1] .+ [0, 15normals[1]], color="#aaa", linestyle=:dash)
plot!(midpoints[2] .+ [0, 15normals[2]], color="#aaa", linestyle=:dash)
plot!(midpoints[1:2], color="#aaa", linestyle=:dash)
quiver!(midpoints.-0.5errors, quiver=reim.(errors), color="#555")

tips = midpoints.+0.5errors
tails = midpoints.-0.5errors
plot!(tips)
plot!(tails)
scatter!([mean(tails[1:2])])
tailsep = abs(tails[2]-tails[1])
circ = [mean(tails[1:2]) + tailsep*cis(ϕ)/2 for ϕ ∈ 0:1deg:360deg]
circ2 = [mean(tails[1:2]) + abs(tips[1]-mean(tails[1:2]))*cis(ϕ) for ϕ ∈ 0:1deg:360deg]
plot!(circ)
plot!(circ2)

# calculate required angles and lengths from given data
m1_m2 = m1 - m2						# x_y denotes a line / vector thing with START y and END x (i.e. x-y)
									# this one is the bridge between the two midpoints in question
m1_m2_horiz = angle(m1_m2)			# angle subtended from real axis to m1 about m2 (again, label the end point first)
c_m2_m1 = angle_n2 - m1_m2_horiz	# angle subtended about m2 from c to m1
m1_c_m2 = angle_n1 - angle_n2

# apply law of sines
c_m1 = cis(angle_n1) * abs(m1_m2) * sin(c_m2_m1) / sin(m1_c_m2)

# get centre point with 'vector' addition
effectiveCentreOfRotation = m1 + c_m1
scatter!([effectiveCentreOfRotation], label="calculated intersection point", color=:red)
quiver!([m1], quiver=[reim.(c_m1)])

savefig("images/verification-of-vcr-code.png")
plot!() |> display
```

But it looks better if you first remove the average error.

```julia
normals = cis.([angle_n1, angle_n2])
errors = normals./j

# BEGIN ADDED CODE
mean(errors)
errors .-= mean(errors)
# END ADDED CODE

scatter(midpoints, xlims=(-6,6), ylims=(-6,6), color="#555", label="error vector centre points")
quiver!(midpoints, quiver=reim.(normals), color="#aaa")
```

![](verification-of-vcr-code%202.png)