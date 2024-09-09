And it seems that I've come full circle — I've proven that I was rather misguided in [[0909 Finding the centre point using the law of sines]] in saying that this worked brilliantly. It does not work brilliantly in real-world scenarios.

A simple scale factor is enough to completely throw this method. Why? Because in [[0908 Centre-finding]] I naïvely assumed that the error could be described by a rotation and that there was a rotation (about one point in the plane) that would cancel all errors. This doesn't work when there's a scale factor, as the radius between the apparent start and end points (lead centroid location, pad centroid location) is not constant due to the scale factor. Thus my chord is now no longer a chord, my isosceles triangle is actually scalene, and everything's messed up.

Unfortunately, this isn't recoverable, as that was a fundamental assumption of that approach.

Other issues have also come to light — the method was too rigid and often predicted centres of rotation in impossible positions, as it neglected the directions of the normal vectors.

## What now?

Fortunately, I do have a much better understanding of how the first method in [[0907 Wicking, the final CV puzzle piece]] might be working.

I've made a few observations.

### The mean of the start points is a better predictor of the centre point

Just now, I observed that virtual centre of rotation as predicted by the law of sines method is actually just the mean of the midpoints of the chords, assuming that there is no net translation (i.e. the error vectors sum to zero) because you're already adjusted for that, and also assuming that there is no scale factor. The first assumption is required for the mean value to be correct; the second assumption is needed because otherwise the law of sines method gives the incorrect value so you don't get any ground truth.

This works because by eliminating the translational error component, any remaining error must by definition be purely rotational. (There may also be a scale component, but we're powerless to deal with that. Fortunately the scale error can't do too much harm as the mean is already zero, so provided the scale is isotropic its impact on the rotation error vectors should cancel itself out.) A purely rotational transformation by definition must have a centre point.

I suspect the underlying reason that the mean works has to do with the fact that the mean is just the centre point of a set of numbers as well as the nature of the errors. They're now zero-sum.

- In the simplest case with only two points, the errors must be equal and opposite. There can be no other way — they must be equal, because that's what the 'pre-translation' step does. They must be tangent to the circle, because otherwise they'd have been removed again. They must also be opposite. Clearly there can be only one centre point, and that is half way between any two corresponding points.
- When you have three points, they can have unequal errors. If the errors are equal then you physically must be looking at an equilateral triangle: the errors are zero sum, so they must all be at 120° from each other, and they must also be in the tangential direction. The centre of an equilateral triangle is the mean point, so that works. If the triangle is not equilateral then some of the errors must be a lot smaller, and therefore the mean is expected to be offset — it physically has to be closer to the smaller errors to ensure they subtend the same angle as the bigger, now-further-away errors.

I can verify it works by checking the angles subtended by each movement. They're the same. I find the following visualisation slightly comforting. While I haven't concretely proved this phenomenon mathematically, it looks visually quite sound.

![[whydoesthemeanwork.png]]

Here's what happens with a scale factor. You can see that it's not quite as simple — the end points are further away in this case — but the centre point is still the best guess.

![[whydoesthemeanwork 3.png]]

Here's the code for that.

```julia
using Plots, Statistics

deg = 2π/360
j = im

scale = 1.1

getPointOnCircle(a) = 3cis(a)

circ = [getPointOnCircle.(a) for a in 0:1deg:360deg]

angles = [0deg, 30deg, 120deg]
points = getPointOnCircle.(angles)
points2 = scale.*getPointOnCircle.(angles.+10deg)
moves = points2 .- points

# make origin more insightful
c2 = mean(points)
circ .-= c2
points .-= c2
points2 .-= c2
c = mean(circ)
c2 = mean(points)

scatter([c], color="#aaa", label="initial centre of rotation")
plot!(circ, color="#aaa", linestyle=:dash, label="circumference of initial rotation")
scatter!(points2, color="#f88", label="initial pad centroids")
scatter!(points, color=:lightgreen, label="lead centroids")

quiver!(points, quiver=reim.(moves), color="#f88", label="initial movement plan")

pretranslation = mean(moves)
points2 .-= pretranslation
moves = points2 .- points

quiver!(points, quiver=reim.(moves), color="#f33", label="new movement plan")
scatter!(points2, color="#f33", label="pad centroids after pretranslation")

quiver!([c], quiver=[reim.(mean(moves))])

scatter!([mean(points2)], markershape=:cross)

angle.(points2.-c2) .- angle.(points.-c2)

for p in points
	arc = [p*cis(a) for a in 0:1deg:360deg]
	plot!(arc, color="#888", linestyle=:dashdot)
end
for p in points2
	arc = [p*cis(a) for a in 0:1deg:360deg]
	plot!(arc, color="#ccc", linestyle=:dashdot)
end
plot!([], color="#888", linestyle=:dashdot, label="rings of constant radius around new centre of rotation")

for r in eachrow(hcat(points, points2))
	ang = angle.(r[2].-c2) .- angle.(r[1].-c2)
	plot!([r[1], c2, r[2]], color="#ccc", linestyle=:dashdotdot, label="sector subtending $(ang/1deg)°")
	# println(r[1])
end

plot!() |> display
savefig("images/whydoesthemeanwork.png")
```

No idea how I can more conclusively prove this in my report, but this understanding will have to do for now.

### The mean is the only thing that copes with scale errors

In [[0909 Finding the centre point using the law of sines]] I discovered that the law of sines method absolutely fell apart when there was a scale error. I also found that the above method (remove the net position error then find the centre as the mean) is the only tool that can cope with the scenarios at hand.

### Virtual centres of rotation

A positive consequence of my investigation into the law of sines was that it forced me to properly figure out an algorithm for calculating the head movement from a rotation and an ideal virtual centre location. This is still very relevant.

## What next?

I will probably rewrite my first algorithm to be more direct, now that I know what the underpinning principle is and should be. I will do it in a sensible, straightforward manner and I'll use the same virtual centre of rotation principle as before (and largely the same code there) to map the calculated rotation to something that the machine can actually achieve. This will include factoring in that initial translational step.

I'll then leave this stupid CV stuff the hell alone and try get the machine hardware up to scratch.