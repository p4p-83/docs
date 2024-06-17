I have a hunch that the mathematics for optimisation will be a bit difficult to follow and verify unless we have some method of visualising the results. In theory the machine itself can provide this, but that won't allow particularly quick iteration and adds a whole bunch of other variables in at the same time.

This julia script allows the centroids to be visualised in a manner in a fairly direct manner. It lets positions be programmed in from real world values (eg from datasheets) so that the eventual algorithm can be properly tested in isolation.

I've used the Images.jl package to draw raster graphics of the scene. The inbuilt functions within ImageDraw.jl seem pretty quick to use and sufficient to allow quick prototyping. The idea is that you can take an algorithm idea and draw it first.

Here's a sample output (which is where I've left it for now):

![](connections.png)

The idea above is that if I can draw lines between centroids of interest, I am clearly well prepared to be able to calculate distances and other necessary inputs to an algorithm.

I can also prototype. Recall that we were thinking we position a reference pad first: I can draw based on that. (`connectBetween([footprintc; footprintc2], [padc3[1]])`).

![](connections%201.png)

Here's the (real mess of a source) code file that generated the first image:

```julia
using Images, ImageDraw

framebuf = RGB(0,0,0)

struct Centroid
	location::Vector{Float64}
	weight::Float64
end

mutable struct PlottableCentroid
	centroid::Centroid
	colour::RGB
end

function rotate(point::Vector, reference::Vector, angle)

	delta = point - reference

	# rotation matrix
	R = [
		cos(angle) -sin(angle)
		sin(angle) cos(angle)
	]

	return newLocation = reference + R*delta

end

function rotate(centroid::Centroid, reference, angle)
	oldLocation = centroid.location
	newLocation = rotate(oldLocation, reference, angle)
	return Centroid(newLocation, centroid.weight)
end

function rotate(pcentroid::PlottableCentroid, reference, angle)
	pcentroid.centroid = rotate(pcentroid.centroid, reference, angle)
	return pcentroid
end

function rotate(pcentroids::Vector{PlottableCentroid}, reference::Vector, angle::Any)
	return map(pc->rotate(pc, reference, angle), pcentroids)
end

function translate(centroid::Centroid, translation::Vector)
	oldLocation = centroid.location
	newLocation = oldLocation .+ translation
	return Centroid(newLocation, centroid.weight)
end

function translate(centroids::Vector{Centroid}, translation::Vector)
	return map(c->translate(c, translation), centroids)
end

function translate(pcentroids::Vector{PlottableCentroid}, translation::Vector)
	map(p->PlottableCentroid(translate(p.centroid, translation), p.colour), pcentroids)
end

function getPadCentroids()
	# https://www.mouser.com/pdfdocs/AVXMLCC-TaSubstitution-Footprints.pdf page 5
	# resistor pad
	# two rectangles of (T, W) spaced (L-T, 0) apart
	# for 0805
	T = 0.5 # mm (range 0.25 to 0.75)
	L = 2 # mm (range 1.8 to 2.2)
	W = 1.25 # mm (range 1.05 to 1.45)

	weight = T*W
	left = Centroid([-(L-T)/2, 0], weight)
	right = Centroid([(L-T)/2, 0], weight)
	return [left, right]

end

function getFootprintCentroids()
	# https://www.mouser.com/pdfdocs/AVXMLCC-TaSubstitution-Footprints.pdf page 5
	# resistor footprint
	# two rectangles of (Y, X) spaced (C, 0) apart
	# for 0805
	Y = 1.3	# mm
	X = 1.5 # mm
	C = 1.9 # mm

	weight = Y*X
	left = Centroid([-C/2, 0], weight)
	right = Centroid([C/2, 0], weight)
	return [left, right]

end

function getWH()
	w = 1000
	h = w
	return (w, h)
end

function getXYR(centroid::Centroid)

	(w, h) = getWH()
	m = 100	# magnification

	x = Int(w/2 + round(m*centroid.location[1]))
	y = Int(h/2 - round(m*centroid.location[2]))
	# A = πr^2 so r = sqrt(A/π)
	k = 1
	r = Int(round(k*m*sqrt(centroid.weight / π)))

	return (x, y, r)

end

function plotCentroids(centroids::Vector{PlottableCentroid}, fromScratch)
	global framebuf

	(w, h) = getWH()

	white = RGB(1, 1, 1)
	black = RGB(0, 0, 0)

	function drawCentroid(c, highlight)
		(x, y, r) = getXYR(c.centroid)

		if (highlight)
			draw!(framebuf, Ellipse(CirclePointRadius(x, y, r+2)), white)
		else
			draw!(framebuf, Ellipse(CirclePointRadius(x, y, r)), c.colour)
			draw!(framebuf, Ellipse(CirclePointRadius(x, y, 2)), black)
		end

	end

	if (fromScratch)
		framebuf = fill(RGB(0,0,0), h, w)
	end
	
	drawCentroid(centroids[1], true)
	drawCentroid.(centroids, false)

	display(framebuf)
	
end

function connect(pcentroid1::PlottableCentroid, pcentroid2::PlottableCentroid)
	c1 = pcentroid1.centroid
	c2 = pcentroid2.centroid

	(x1, y1, r1) = getXYR(c1)
	(x2, y2, r2) = getXYR(c2)

	p1 = Point(x1, y1)
	p2 = Point(x2, y2)

	global framebuf

	draw!(framebuf, LineSegment(p1, p2), RGB{N0f8}(1,1,1))
	display(framebuf)

end

function connectAll(pcentroids::Vector{PlottableCentroid})

	for (i, thisPC) in enumerate(pcentroids)
		
		remaining = @view pcentroids[i:end]

		for secondPC in remaining
			connect(thisPC, secondPC)
		end

	end

end

function connectBetween(padpcs::Vector{PlottableCentroid}, footprintpcs::Vector{PlottableCentroid})
	for padpc in padpcs
		for footpc in footprintpcs
			connect(padpc, footpc)
		end
	end
end

(function main()

	footprintColour = RGB{N0f8}(0, 0.5, 0.25)
	footprintc = map(c->PlottableCentroid(c, footprintColour), getFootprintCentroids())

	footprintc2 = translate(footprintc, [0, 3])

	padColour = RGB{N0f8}(0.25, 0, 0.5)
	padc = map(c->PlottableCentroid(c, padColour), getPadCentroids())

	padc2 = translate(padc, [0, 1])
	padc3 = rotate(padc2, [0, 0], 0.6π)

	plotCentroids(footprintc, true)
	plotCentroids(footprintc2, false)
	plotCentroids(padc3, false)

	# connectAll([footprintc; footprintc2; padc3])
	connectBetween([footprintc; footprintc2], padc3)

	global framebuf
	save("connections.png", framebuf)

end)()
```