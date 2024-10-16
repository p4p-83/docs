So, the maths in [0908 Actually doing the maths](0908%20Actually%20doing%20the%20maths.md) was painful. I need a bit of a different approach. Here's one: can I find the centre of rotation by inspection?

```julia
using Plots; default( fontfamily="LinLibertine_Rah", size=(720, 720), label="", background_color=:transparent, foreground_color="#777", dpi=300 )

deg = 2π/360
j = im

function drawPerpLines(movement)

	pllel = cis(angle(movement))
	perp = j*pllel

	for n ∈ -5:0.25:5
		plot!([n*pllel - 10perp, n*pllel + 10perp], color="#ccc")
	end
end

centre = 1+0j # adjust the centre of rotation here

points = [-2+2j, 1+1j, 2+0j]
points2 = (points.-centre) .* cis(30deg) .+ centre
movements = points2 .- points

scatter([centre], xlims=(-3,3), ylims=(-3,3), color=:white, label="centre of rotation")

drawPerpLines.(movements)

scatter!(points, color=:lightblue, label="start")
scatter!(points2, color=:magenta, label="end")

quiver!(points, quiver=reim.(movements), color=:black)

savefig("images/finding the centre of rotation.png")
plot!() |> display
```

Here we have a rotation about the origin.

![](finding%20the%20centre%20of%20rotation.png)

Not too difficult. Here's another one, this time with the centre of rotation offset a bit.![](finding%20the%20centre%20of%20rotation%201.png)

And another.

![](finding%20the%20centre%20of%20rotation%202.png)

I'm drawing grids of lines perpendicular to each movement arrow. While there are a lot of intersections, it seems that there is still a way to predict the centre point: draw a perpendicular line from the centre of each movement arrow outwards. The intersection of these gives the centre of rotation.

![](finding%20the%20centre%20of%20rotation%207.png)

This method feels kind of obvious in hindsight. While I still don't have an algorithmic way to empirically choose a centre point when these lines don't 100% agree, I feel that this is a definite step in the right direction.

(Clearly I should have taken that NCEA L1 geometric reasoning topic more seriously…)

I still don't feel entirely convinced, so here's an understanding of why this must work.

- Pure rotation rotates all points around the same centre point.
- Points in rotation about their centre point trace out circles centred about the centre point.
- A line passing through the centre of a circle is called a diameter line. ([See this Wikipedia page for circle and chord terminology.](<https://en.wikipedia.org/wiki/Chord_(geometry)>))
- A line between two points on the same circumference is called a chord. Any point on the circumference of a circle is by definition one radius away from the centre, so therefore any two circumference points will define an isosceles triangle with the circle's centre point.
- The start point and end point from the rotation are, as above, part of the circumference, and so therefore define isosceles triangles.
- The perpendicular bisector of the chord between the start and end points is therefore co-linear with the line of symmetry through the isosceles triangle and thus also co-linear with the diameter of the circle, and therefore co-incident with the centre point of the circle.

So to recap, the straight line connecting the start and end points (which is observed error line) is a chord on circle of constant radius and the outside side of an isosceles triangle made with the centre of rotation, and thus its perpendicular bisector is guaranteed to coincide with the origin. I can therefore pinpoint the centre of rotation with only two sets of points, as expected.

> [!DRAFT] Note for final report writing
> If I find the need to make this proof in the final report, I think the best way to do it first starting with the centre point, doing a forward analysis, and only after that doing the reverse analysis.
> 
> Forward analysis starts with the centre point — we assume we know the centre point for the sake of the analysis — and observes that there will be a start point. It then notes that the end point will be revolved around this centre of rotation. Thus both points are clearly one radius away from the centre of the circle. We can then use these two radii as sides of an isosceles triangle, where the third side is the chord connecting the start and end points. We can then note that the isosceles triangle has a line of symmetry, and that line of symmetry is coincident with the centre of the circle. We can also note that the line of symmetry is at right angles to the chord (third side of the isosceles triangle) by symmetry, and that by symmetry it is also coincident with the midpoint of that chord. In other words, the line of symmetry is a perpendicular bisector of the chord, and thus a perpendicular bisector of the chord will be coincident with the centre of rotation.
> 
> We can therefore, in the reverse analysis, say that if we have two sets of start and end points, we can determine two chords. We can draw two perpendicular bisectors, one per chord, in full knowledge that the centre of the circle will be coincident with both. So long as these two perpendicular bisectors are not parallel, their point of intersection must be the centre of rotation. (If they are parallel and co-linear, we will need a third to define the point. If they are parallel but not co-linear, we can say that no rotation is required (although if the chords are non-zero in length, we still need a pure translation).)

This last visualisation used some slightly more sophisticated plotting code.

```julia
using Plots; default( fontfamily="LinLibertine_Rah", size=(720, 720), label="", background_color="#fffe", background_color_inside=:transparent, foreground_color="#777", dpi=300 )

deg = 2π/360
j = im

function drawPerpLines(movement)

	pllel = cis(angle(movement))
	perp = j*pllel

	for n ∈ -5:1:5
		plot!([n*pllel - 10perp, n*pllel + 10perp], color="#ccc")
	end
end

function drawPerpBisectors(endpoint, startpoint)
	movement = endpoint - startpoint
	midpoint = 0.5*(endpoint+startpoint)

	bisectorHeading = cis(angle(j*movement))

	trace = [midpoint-10bisectorHeading, midpoint+10bisectorHeading]
	scatter!([midpoint], color="navy", markershape=:xcross)
	plot!(trace, color="navy", linestyle=:dashdotdot)

end

function drawArc(p)

	arc = [(p-centre) * cis(r) + centre for r ∈ 0:1deg:rotation]
	plot!(arc, color="navy", linestyle=:dashdot)

	arc = [(p-centre) * cis(r) + centre for r ∈ 0:1deg:360deg]
	plot!(arc, color="navy", linestyle=:dashdot, linealpha=0.2)

end

function drawIsoscelesTriangle(endpoint, startpoint)

	plot!([centre, startpoint, endpoint, centre], color="navy", linestyle=:dashdotdot, linealpha=0.2)

end

centre = 1+0j
rotation = 30deg

points = [-2+2j, 1+1j, 2+0j]
points2 = (points.-centre) .* cis(rotation) .+ centre
movements = points2 .- points

scatter([centre], xlims=(-3,3), ylims=(-3,3), color=:white, label="centre of rotation")

drawArc.(points)
drawPerpLines.(movements)
drawPerpBisectors.(points2, points)
drawIsoscelesTriangle.(points2, points)

scatter!(points, color=:lightblue, label="start")
scatter!(points2, color=:lightgreen, label="end")

quiver!(points, quiver=reim.(movements), color=:black)


savefig("images/finding the centre of rotation.png")
plot!() |> display
```

But what happens when there's also a position error? What then?

Let's change the relevant section of code and find out.

```julia
centre = 1+0j
rotation = 30deg

offset = 1+0j

points = [-2+2j, 1+1j, 2+0j]
points2 = (points.-centre) .* cis(rotation) .+ centre .+ offset
movements = points2 .- points

scatter([centre], xlims=(-3,3), ylims=(-3,3), color=:white, label="centre of rotation")

# drawArc.(points)
drawPerpLines.(movements)
drawPerpBisectors.(points2, points)
# drawIsoscelesTriangle.(points2, points)

scatter!(points, color=:lightblue, label="start")
scatter!(points2, color=:lightgreen, label="end")

quiver!(points, quiver=reim.(movements), color=:black)

savefig("images/finding the centre of rotation.png")
plot!() |> display
```

![](finding%20the%20centre%20of%20rotation%208.png)

Reassuringly, the code still finds a centre of rotation, but it's not in the same place. This is fascinating, as it's not the behaviour I expected (I didn't really expect it to produce a coherent result), and maybe not the most useful or relevant behaviour — I can't move the centre of rotation of the nozzle. However, if this works then that's probably fine — I'll just do a compound movement to achieve the required rotation and position shift.

The most interesting thing is that it seems that the code has identified a centre of rotation that would cancel all of the translational offsets too. I did not even consider that as a possibility…

I guess now I have two options going forward.

- I try to correct for the translation error first. I guess you define the "zero error" position as the position where the centre of rotation is aligned with the nozzle's centre of rotation.
- I just do the above perpendicular bisector analysis first and then somehow decompose the results of that into a translation and rotation.

That said, both of those options are probably just different sides of the same mathematical relationship, and so I probably just have to do the maths first and then work out the most efficient / straightforward implementation from that.

Programmatically speaking, I have one thing left to implement before this can truly be of use: I need some code to find the intersection of a pair of perpendicular bisectors. I suspect I'll do this algebraically first and then code in the results. (In a working implementation, I'll calculate the intersection point for each possible pairing of perpendicular bisectors — it's the damn handshake problem again — and then average this point out to work out where the effective centre is. I don't expect all of the real lines to line up so nicely at exactly the same point.)

---

Here's where I think I'll go next.

1. Perform this analysis to get the equivalent centre of rotation. (Includes doing the math to numerically pinpoint it.)
2. Calculate the required angle of rotation.
3. Assume the head is at (0,0). Work out where the equivalent centre of rotation will end up after rotating the head by the calculated amount.
4. Calculate the required translation that will put the equiv centre of rotation back to where it was.
5. Drive the head by the calculated rotational amount and drive the gantry by the calculated translational amount.

There. Easy math. Just like spinning a piece of paper about an off-centre finger. (Yes, that's how I worked this algorithm out.)

~~The only issue I can see is if there is no rotation needed, and only translation — this could send the centre of rotation out to infinity. I may need to check the position of the centre of rotation, and if it starts flying well away I'll just assume that only translation is required for the time being and short-circuit the above algorithm (just average the vectors and move by that amount). (I could redo the whole algorithm to do translation first, rotation second, but I'm a bit too partial to the above algorithm to do that just at the moment.)~~ I can achieve this by just introducing a parallelism test when doing the peerwise perp. bisector intersection tests. If two perp. bisectors are parallel or essentially parallel, I will just exit that comparison early and not push any intersection point. In the case of pure translation, all of the perpendicular bisectors will be parallel, and therefore no intersection points will be pushed; I can therefore just do a branch to the pure translation correction if I find that there is no centre point. I've realised that I need to do the parallelism test anyway, as parallel lines have no intersection point, and almost-parallel lines will give overly poor-quality data (given there'll be a little bit of noise on each point and thus each angle).

---

More progress!

[Wikipedia did most of my math for me.](https://en.wikipedia.org/wiki/Line–line_intersection)

I wrote the recipe out by hand:

![](Image%209.jpeg)

I got it [working in Desmos.](https://www.desmos.com/calculator/kztsfdhrnt)

I then put some demo code in for it.

```julia
# … previous code
quiver!(points, quiver=reim.(movements), color=:black)

# NEW CODE BEGINS

# find the centre

function getPerpBisector(startpoint, endpoint)
	chord = endpoint - startpoint
	midpoint = 0.5 * (endpoint + startpoint)
	perpAngle = angle(j*chord)

	slope = tan(perpAngle) # m
	intercept = imag(midpoint) - slope*real(midpoint)

	return (intercept, slope)

end

(b1, m1) = getPerpBisector(points[1], points2[1])
(b2, m2) = getPerpBisector(points[2], points2[2])

interceptx = (b2-b1)/(m1-m2)
intercepty = m1*interceptx + b1

intercept = interceptx + j*intercepty

scatter!([intercept], color=:red, label="intercept point!")

# NEW CODE ENDS

savefig("images/finding the centre of rotation.png")
plot!() |> display
```

Et voilà! Just like magic:

![](finding%20the%20centre%20of%20rotation%209.png)

Probably there is some vector method of doing it, but screw that. I've not used a single vector yet, and I don't plan to change that without them making a very compelling case for themselves. Phasors all the way!

---

~~I messed around with a few random formulae, and it seems I can predict the location of the effective centre of rotation. No idea if this will ever be useful, but it is interesting.~~ Doesn't work for different angles.

```julia
using Plots; default( fontfamily="LinLibertine_Rah", size=(720, 720), label="", background_color="#fffe", background_color_inside=:transparent, foreground_color="#777", dpi=300 )

deg = 2π/360
j = im

function drawPerpLines(movement)

	pllel = cis(angle(movement))
	perp = j*pllel

	for n ∈ -5:1:5
		plot!([n*pllel - 10perp, n*pllel + 10perp], color="#ccc")
	end
end

function drawPerpBisectors(endpoint, startpoint)
	movement = endpoint - startpoint
	midpoint = 0.5*(endpoint+startpoint)

	bisectorHeading = cis(angle(j*movement))

	trace = [midpoint-10bisectorHeading, midpoint+10bisectorHeading]
	scatter!([midpoint], color="navy", markershape=:xcross)
	plot!(trace, color="navy", linestyle=:dashdotdot)

end

function drawArc(p)

	arc = [(p-centre) * cis(r) + centre for r ∈ 0:1deg:rotation]
	plot!(arc, color="navy", linestyle=:dashdot)

	arc = [(p-centre) * cis(r) + centre for r ∈ 0:1deg:360deg]
	plot!(arc, color="navy", linestyle=:dashdot, linealpha=0.2)

end

function drawIsoscelesTriangle(endpoint, startpoint)

	plot!([centre, startpoint, endpoint, centre], color="navy", linestyle=:dashdotdot, linealpha=0.2)

end

centre = 1+0.1j
rotation = 30deg

offset = 1.3+0.1j

points = [-2+2j, 1+1j, 2+0j]
points2 = (points.-centre) .* cis(rotation) .+ centre .+ offset
movements = points2 .- points

scatter([centre], xlims=(-3,3), ylims=(-3,3), color=:white, label="centre of rotation")

# drawArc.(points)
drawPerpLines.(movements)
drawPerpBisectors.(points2, points)
# drawIsoscelesTriangle.(points2, points)

scatter!(points, color=:lightblue, label="start")
scatter!(points2, color=:lightgreen, label="end")

quiver!(points, quiver=reim.(movements), color=:black)

# find the centre

function getPerpBisector(startpoint, endpoint)
	chord = endpoint - startpoint
	midpoint = 0.5 * (endpoint + startpoint)
	perpAngle = angle(j*chord)

	slope = tan(perpAngle) # m
	intercept = imag(midpoint) - slope*real(midpoint)

	return (intercept, slope)

end

(b1, m1) = getPerpBisector(points[1], points2[1])
(b2, m2) = getPerpBisector(points[2], points2[2])

interceptx = (b2-b1)/(m1-m2)
intercepty = m1*interceptx + b1

intercept = interceptx + j*intercepty

scatter!([intercept], color=:red, label="intercept point!")

println("predicted effective centre of rot. ( $((pecr = centre+offset*cis(90deg-rotation)+j*offset) |> real) , $(imag(pecr)) )")
println("found effective centre of rotation ( $interceptx , $intercepty )")

# predicted effective centre of rot. ( 1.463397459621556 , 2.5758330249197705 )
# found effective centre of rotation ( 1.463397459621556 , 2.5758330249197714 )

savefig("images/finding the centre of rotation.png")
plot!() |> display
```