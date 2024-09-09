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