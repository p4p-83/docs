Using yesterday's algorithm, but nucleating in a grid:

```julia
using Images, ImageFiltering, Random, Statistics, Reel

Random.seed!(1)
framedelay = 0.1
animate = false

##

w = 1000
h = 1000
m = rand(Float64, h, w)
mask = colorview(Gray, m)
mask2 = imfilter(mask, Kernel.gaussian(20))
mask3 = colorview(Gray, [x .> 0.51 ? 1.0 : 0.0 for x in mask2])

##

frames = Frames(MIME("image/png"), fps=clamp(1/framedelay, 0.1, 30))

@enum PixelStatus begin
	unseen
	seen_outsidepad
	seen_insidepad
	centroid
end

mutable struct Point{T<:Real}
	x::T
	y::T
	Point(x,y) = new{typeof(x)}(x,y)
	Point(yx::Tuple) = new{typeof(yx[1])}(yx[2], yx[1])
end

import Base: +
function +(a::Point, b::Point)
	p = a
	p.x += b.x
	p.y += b.y
	return p
end
function +(p::Point, v::Vector)
	p.x += v[1]
	p.y += v[2]
	return p
end

function visualise(mask, state)

	img = Dict{Tuple{Bool, PixelStatus}, HSV}(
		# not pad
		(false, unseen) => HSV(220, 0.5, 0.2),
		(false, seen_insidepad) => HSV(0, 1, 1),		# should not be possible
		(false, seen_outsidepad) => HSV(110, 0.5, 0.3),

		# pad
		(true, unseen) => HSV(220, 0.7, 0.7),
		(true, seen_insidepad) => HSV(110, 0.7, 0.7),
		(true, seen_outsidepad) => HSV(0, 1, 1),		# should not be possible

		# centroid
		(false, centroid) => HSV(340, 0.5, 0.5),
		(true, centroid) => HSV(350, 0.5, 1),
	)[(mask ≈ 1, state)]

end

states = fill(unseen, size(mask)...)

function splatSearch(mask::Matrix{Float64}, startPoint::Point{Int64})

	global states

	search = Vector{Point{Int}}()
	push!(search, startPoint)	# entry point

	xcoords = Vector{Int}()
	ycoords = Vector{Int}()

	while length(search) > 0

		searchPoint = pop!(search)
		if animate println("evaluating $searchPoint") end

		px = try
			view(mask, searchPoint.y, searchPoint.x)
		catch
			# out of bounds
			if animate println("out of bounds") end
			continue
		end

		st = view(states, searchPoint.y, searchPoint.x)

		# exit early if already seen
		if st[1] != unseen
			if animate println("already seen") end
			continue
		end

		# not already seen
		if px[1] ≈ 1

			if animate println("inside pad") end

			# inside pad
			st[1] = seen_insidepad
			push!(xcoords, searchPoint.x)
			push!(ycoords, searchPoint.y)

			pushfirst!(search, Point(searchPoint.x + 1, searchPoint.y))
			pushfirst!(search, Point(searchPoint.x - 1, searchPoint.y))
			pushfirst!(search, Point(searchPoint.x, searchPoint.y + 1))
			pushfirst!(search, Point(searchPoint.x, searchPoint.y - 1))

		else

			if animate println("outside pad") end
			
			# outside pad
			st[1] = seen_outsidepad

		end

		# if animated display(search) end
		if animate println("$(length(search)) location(s) left to search\n") end

		if animate img = visualise.(mask, states) end
		if animate push!(frames, img) end
		if animate display(img) end
		if animate sleep(framedelay) end

	end

	if animate println("") end

	if length(xcoords) > 0
		xbar = mean(xcoords)
		ybar = mean(ycoords)
		centre = Int.(round.((ybar, xbar), digits=0))
		states[centre...] = centroid
	
		if animate display(visualise.(mask3, states)) end

		return (Point(centre), length(xcoords))
	else
		return nothing
	end

end


nucleationSites = [Point(x, y) for x=1:20:w for y=1:20:h]
centroids = []

@time for ns in nucleationSites
	c = splatSearch(channelview(mask3), ns)
	if !isnothing(c)
		push!(centroids, c)
	end
end

display(centroids)
display(visualise.(mask3, states))

if animate
	[push!(frames, visualise.(mask3, states)) for _=1:50]
	write("nucleation.gif", frames)
end
```

![](nucleation%202.gif)

Sadly, this is a bit slow.

I'm going to try speeding it up by locking the search pattern. We'll just scan through each pixel one by one. The code for this has been enough work and I cannot be bothered trying to explain it in words as well. Read the code. It's a bit messy but the major functions are fairly self-documenting.

```julia
using Images, ImageFiltering, Random, Statistics, BenchmarkTools

module Analyser
	using Images, Reel

	@enum PixelStatus begin
		unseen
		seen_outsidepad
		seen_insidepad
		centroid
	end

	function visualise(mask, state::PixelStatus)

		img = Dict{Tuple{Bool, PixelStatus}, HSV}(
			# not pad
			(false, unseen) => HSV(220, 0.5, 0.2),
			(false, seen_insidepad) => HSV(0, 1, 1),		# should not be possible
			(false, seen_outsidepad) => HSV(110, 0.5, 0.3),

			# pad
			(true, unseen) => HSV(220, 0.7, 0.7),
			(true, seen_insidepad) => HSV(110, 0.7, 0.7),
			(true, seen_outsidepad) => HSV(0, 1, 1),		# should not be possible

			# centroid
			(false, centroid) => HSV(340, 0.5, 0.5),
			(true, centroid) => HSV(350, 0.5, 1),
		)[(mask ≈ 1, state)]

	end

	doAnimation() = false

	function status(message)
		if doAnimation() println(message) end
	end

	frames = Frames(MIME("image/png"), fps=15)

	function preview(mask, states)
		global frames
		v = visualise.(mask, states)
		display(v)
		push!(frames, v)
	end

	mutable struct Region
		xsum::Int64
		ysum::Int64
		count::Int64
		abandoned::Bool
	end

	regions::Vector{Region} = []
	pointers::Vector{Int64} = []
	nextIdentity::Int64 = 1

	identities::Matrix{Int64} = zeros(2, 0)

	states::Matrix{PixelStatus} = fill(unseen, 2, 0)

	function setup(mask::Matrix{Float64})
		global regions, pointers, nextIdentity, identities
		regions = [Region(0, 0, 0, false) for _=1:1000] # don't use fill, because it breaks (need copies not views)
		pointers = [i for i=1:1000]
		nextIdentity = 1
		identities = zeros(Int64, size(mask)...)
	end

	# must not be called on top row or left edge
	function analysePixel(mask::Matrix{Float64}, x::Int64, y::Int64)::PixelStatus
		global regions, pointers, nextIdentity, identities
		status("\nanalysing pixel at $x, $y")

		thisPixel = @view mask[y, x]
		status("pixel is $(thisPixel[1])")
		# pixelAbove = @view mask[y-1, x]
		# pixelLeft = @view mask[y, x-1]

		# return if not a pad
		if thisPixel[1] < 0.5
			status("not a pad")
			return seen_outsidepad
		end

		# checkpoint: must be part of a pad
		status("pad")

		# get pad identifier
		thisIdentity = @view identities[y, x]
		identityAbove = @view identities[y-1, x]
		identityLeft = @view identities[y, x-1]
		status("above is $(identityAbove[1]), left is $(identityLeft[1])")

		if identityLeft[1] > 0 && identityAbove[1] > 0
			# two definitions
			status("connected both top and left")

			if identityLeft[1] == identityAbove[1]
				# the same pad identified multiple times
				status("top and left match")
				thisIdentity[1] = identityLeft[1]
			else
				# inconsistent — two pads that were thought different are merging
				status("merging top and left")
				if identityLeft[1] < identityAbove[1]
					# point larger number at smaller number
					status("merging into left pad")
					pointers[identityAbove[1]] = identityLeft[1]
					thisIdentity[1] = identityLeft[1]
				else
					status("merging into top pad")
					pointers[identityLeft[1]] = identityAbove[1]
					thisIdentity[1] = identityAbove[1]
				end
			end

		elseif identityLeft[1] > 0
			# only pad to the left
			status("connected to left")
			thisIdentity[1] = identityLeft[1]

		elseif identityAbove[1] > 0
			# only pad above
			status("connected above")
			thisIdentity[1] = identityAbove[1]

		else
			# possible new pad
			status("this might be a new pad")
			thisIdentity[1] = nextIdentity
			nextIdentity += 1
		end

		# checkpoint:
		# thisIdentity is defined
		# pads have been merged if need be
		# new pad has been created if needed
		status("computed thisIdentity = $(thisIdentity[1])")

		# record statistics
		status("recording statistics to regions[$(pointers[thisIdentity[1]])]")
		region = @view regions[pointers[thisIdentity[1]]]
		region[1].xsum += x
		region[1].ysum += y
		region[1].count += 1
		
		# checkpoint: statistics have been merged
		return seen_insidepad

	end

	function analyseAll(mask)
		status("beginning analysis of all pixels")
		
		if doAnimation()
			global states
			states = fill(unseen, size(mask)...)
		end

		# remember the rule: must not run on first row or first column
		(height, width) = size(mask)
		for x = 2:width
			for y = 2:height
				st = analysePixel(mask, x, y)
				if doAnimation() states[y, x] = st
					sleep(st == seen_outsidepad ? 0.05 : 1.25)
					preview(mask, states)
				end
			end
		end

		status("\nfinished analysis of all pixels\n")
	end

	# actually merges pads
	# (analysePixel only adjusts pointers, this method actually moves data)
	function postProcess()::Vector{Tuple{Float64, Float64, Int64}}
		status("beginning post-processing")
		status(pointers)

		maxIdentity = nextIdentity-1

		# step 1: finish merging pads
		for i = maxIdentity:-1:1
			# bigger numbers are always redirected into smaller numbers
			# hence if we transfer big numbers first we only need to run this loop once

			if pointers[i] != i
				# pad got merged
				status("\nregion $i will be merged into $(pointers[i])")

				# make transfer
				status("regions[$(pointers[i])] before: $(regions[pointers[i]])")
				status("regions[$i] will be added: $(regions[i])")
				regions[pointers[i]].xsum += regions[i].xsum
				regions[pointers[i]].ysum += regions[i].ysum
				regions[pointers[i]].count += regions[i].count
				status("regions[$(pointers[i])] after: $(regions[pointers[i]])")
				
				# flag that last one as abandoned
				regions[i].abandoned = true

			end

		end

		# step 2: compute centroid information
		centroids::Vector{Tuple{Float64, Float64, Int64}} = []
		
		for i = 1:(maxIdentity)
			region = @view regions[i]
			if region[1].abandoned continue end
			weight = region[1].count
			xbar = region[1].xsum / weight
			ybar = region[1].ysum / weight
			push!(centroids, (xbar, ybar, weight))
		end

		# return centroids
		return centroids
		
		status("finished post-processing\n")

	end

	function finalPreview(mask, centroids)
		global states, frames

		if !doAnimation() states = fill(unseen, size(mask)...) end

		for c in centroids
			cc = Int64.(round.(c, digits=0))
			states[cc[2], cc[1]] = centroid
		end

		[preview(mask, states) for _=1:50]
		write("indirection.gif", frames)

	end


end

function makeMask()::Matrix{Float64}
	w = 55
	h = 55
	m = rand(Float64, h, w)
	mask = colorview(Gray, m)
	mask2 = imfilter(mask, Kernel.gaussian(3))
	mask3 = colorview(Gray, [x .> 0.54 ? 1.0 : 0.0 for x in mask2])
	return m3 = channelview(mask3)
end

##

function analyse(mask)
	Analyser.setup(mask)
	Analyser.analyseAll(mask)
	return Analyser.postProcess()
end

function main()
	
	Random.seed!(1)
	mask = makeMask()

	display(@benchmark analyse(mask))
	centroids = analyse(mask)

	# display(centroids)
	Analyser.finalPreview(mask, centroids)

end

main()
```

![](Screenshot%202024-06-12%20at%2022.01.51.png)

```
BenchmarkTools.Trial: 1955 samples with 1 evaluation.
 Range (min … max):  2.086 ms … 22.945 ms  ┊ GC (min … max):  0.00% … 89.71%
 Time  (median):     2.197 ms              ┊ GC (median):     0.00%
 Time  (mean ± σ):   2.554 ms ±  2.480 ms  ┊ GC (mean ± σ):  13.78% ± 12.39%

  █                                                           
  █▇▁▁▁▁▁▁▁▁▁▁▁▁▁▁▁▁▁▁▁▁▁▁▁▁▁▁▁▁▁▁▁▁▁▁▁▁▁▁▁▁▁▁▁▁▁▁▁▁▁▁▁▁▁▁▅▇ █
  2.09 ms      Histogram: log(frequency) by time     19.8 ms <

 Memory estimate: 3.13 MiB, allocs estimate: 45779.
```

![](indirection.gif)

But perhaps this is not a representative tile size. Unfortunately it collapses with a bigger mask. Boo!

![](Screenshot%202024-06-12%20at%2022.21.11.png)

```julia
using Images, ImageFiltering, Random, Statistics, BenchmarkTools

module Analyser
	using Images, Reel

	@enum PixelStatus begin
		unseen
		seen_outsidepad
		seen_insidepad
		centroid
	end

	function visualise(mask, state::PixelStatus)

		img = Dict{Tuple{Bool, PixelStatus}, HSV}(
			# not pad
			(false, unseen) => HSV(220, 0.5, 0.2),
			(false, seen_insidepad) => HSV(0, 1, 1),		# should not be possible
			(false, seen_outsidepad) => HSV(110, 0.5, 0.3),

			# pad
			(true, unseen) => HSV(220, 0.7, 0.7),
			(true, seen_insidepad) => HSV(110, 0.7, 0.7),
			(true, seen_outsidepad) => HSV(0, 1, 1),		# should not be possible

			# centroid
			(false, centroid) => HSV(340, 0.5, 0.5),
			(true, centroid) => HSV(350, 0.5, 1),
		)[(mask ≈ 1, state)]

	end

	doAnimation() = false

	function status(message)
		if doAnimation() println(message) end
	end

	frames = Frames(MIME("image/png"), fps=15)

	function preview(mask, states)
		global frames
		v = visualise.(mask, states)
		display(v)
		push!(frames, v)
	end

	mutable struct Region
		xsum::Int64
		ysum::Int64
		count::Int64
		abandoned::Bool
	end

	regions::Vector{Region} = []
	pointers::Vector{Int64} = []
	nextIdentity::Int64 = 1

	identities::Matrix{Int64} = zeros(2, 0)

	states::Matrix{PixelStatus} = fill(unseen, 2, 0)

	function setup(mask::Matrix{Float64})
		global regions, pointers, nextIdentity, identities
		regions = [Region(0, 0, 0, false) for _=1:1000] # don't use fill, because it breaks (need copies not views)
		pointers = [i for i=1:1000]
		nextIdentity = 1
		identities = zeros(Int64, size(mask)...)
	end

	# must not be called on top row or left edge
	function analysePixel(mask::Matrix{Float64}, x::Int64, y::Int64)::PixelStatus
		global regions, pointers, nextIdentity, identities
		status("\nanalysing pixel at $x, $y")

		thisPixel = @view mask[y, x]
		status("pixel is $(thisPixel[1])")
		# pixelAbove = @view mask[y-1, x]
		# pixelLeft = @view mask[y, x-1]

		# return if not a pad
		if thisPixel[1] < 0.5
			status("not a pad")
			return seen_outsidepad
		end

		# checkpoint: must be part of a pad
		status("pad")

		# get pad identifier
		thisIdentity = @view identities[y, x]
		identityAbove = @view identities[y-1, x]
		identityLeft = @view identities[y, x-1]
		status("above is $(identityAbove[1]), left is $(identityLeft[1])")

		if identityLeft[1] > 0 && identityAbove[1] > 0
			# two definitions
			status("connected both top and left")

			if identityLeft[1] == identityAbove[1]
				# the same pad identified multiple times
				status("top and left match")
				thisIdentity[1] = identityLeft[1]
			else
				# inconsistent — two pads that were thought different are merging
				status("merging top and left")
				if identityLeft[1] < identityAbove[1]
					# point larger number at smaller number
					status("merging into left pad")
					pointers[identityAbove[1]] = identityLeft[1]
					thisIdentity[1] = identityLeft[1]
				else
					status("merging into top pad")
					pointers[identityLeft[1]] = identityAbove[1]
					thisIdentity[1] = identityAbove[1]
				end
			end

		elseif identityLeft[1] > 0
			# only pad to the left
			status("connected to left")
			thisIdentity[1] = identityLeft[1]

		elseif identityAbove[1] > 0
			# only pad above
			status("connected above")
			thisIdentity[1] = identityAbove[1]

		else
			# possible new pad
			status("this might be a new pad")
			thisIdentity[1] = nextIdentity
			nextIdentity += 1
		end

		# checkpoint:
		# thisIdentity is defined
		# pads have been merged if need be
		# new pad has been created if needed
		status("computed thisIdentity = $(thisIdentity[1])")

		# record statistics
		status("recording statistics to regions[$(pointers[thisIdentity[1]])]")
		region = @view regions[pointers[thisIdentity[1]]]
		region[1].xsum += x
		region[1].ysum += y
		region[1].count += 1
		
		# checkpoint: statistics have been merged
		return seen_insidepad

	end

	function analyseAll(mask)
		status("beginning analysis of all pixels")
		
		if doAnimation()
			global states
			states = fill(unseen, size(mask)...)
		end

		# remember the rule: must not run on first row or first column
		(height, width) = size(mask)
		for x = 2:width
			for y = 2:height
				st = analysePixel(mask, x, y)
				if doAnimation() states[y, x] = st
					# sleep(st == seen_outsidepad ? 0.05 : 1.25)
					preview(mask, states)
				end
			end
		end

		status("\nfinished analysis of all pixels\n")
	end

	# actually merges pads
	# (analysePixel only adjusts pointers, this method actually moves data)
	function postProcess()::Vector{Tuple{Float64, Float64, Int64}}
		status("beginning post-processing")
		status(pointers)

		maxIdentity = nextIdentity-1

		# step 1: finish merging pads
		for i = maxIdentity:-1:1
			# bigger numbers are always redirected into smaller numbers
			# hence if we transfer big numbers first we only need to run this loop once

			if pointers[i] != i
				# pad got merged
				status("\nregion $i will be merged into $(pointers[i])")

				# make transfer
				status("regions[$(pointers[i])] before: $(regions[pointers[i]])")
				status("regions[$i] will be added: $(regions[i])")
				regions[pointers[i]].xsum += regions[i].xsum
				regions[pointers[i]].ysum += regions[i].ysum
				regions[pointers[i]].count += regions[i].count
				status("regions[$(pointers[i])] after: $(regions[pointers[i]])")
				
				# flag that last one as abandoned
				regions[i].abandoned = true

			end

		end

		# step 2: compute centroid information
		centroids::Vector{Tuple{Float64, Float64, Int64}} = []
		
		for i = 1:(maxIdentity)
			region = @view regions[i]
			if region[1].abandoned continue end
			weight = region[1].count
			xbar = region[1].xsum / weight
			ybar = region[1].ysum / weight
			push!(centroids, (xbar, ybar, weight))
		end

		# return centroids
		return centroids
		
		status("finished post-processing\n")

	end

	function finalPreview(mask, centroids)
		global states, frames

		if !doAnimation() states = fill(unseen, size(mask)...) end

		for c in centroids
			cc = Int64.(round.(c, digits=0))
			states[cc[2], cc[1]] = centroid
		end

		[preview(mask, states) for _=1:50]
		write("indirection.gif", frames)

	end


end

function makeMask()::Matrix{Float64}
	# Random.seed!(1)
	w = 1000
	h = 1000
	m = rand(Float64, h, w)
	mask = colorview(Gray, m)
	mask2 = imfilter(mask, Kernel.gaussian(20))
	mask3 = colorview(Gray, [x .> 0.510 ? 1.0 : 0.0 for x in mask2])
	return m3 = channelview(mask3)
end

##

function analyse(mask)
	Analyser.setup(mask)
	Analyser.analyseAll(mask)
	return Analyser.postProcess()
end

mask = makeMask()
@benchmark analyse(m) setup=(m = makeMask())
@time centroids = analyse(mask)
# display(centroids)
Analyser.finalPreview(mask, centroids)
```

Long story short, the first method gives 13 ms with BenchmarkTools, and the second gives 884 ms. Don't do it the second way!

— Addendum: I suspect, from the insanely high memory allocation stats, that my algorithm is not doing quite what I thought, because the whole goal was to *avoid* too many reallocations.