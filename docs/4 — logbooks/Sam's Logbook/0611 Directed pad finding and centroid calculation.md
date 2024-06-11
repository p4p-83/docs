Sort of based on what I believe to be happening under the hood with the Felzenszwalb algorithm trailed in [0606 ImageSegmentation.jl test](0606%20ImageSegmentation.jl%20test.md). This is an algorithm to first search for a pad in a directed manner, and then find the centroid of that pad.

The pad finding part of the algorithm is pretty crude in this demo. Ideally you'd search a sector based on the cardinal direction as input by the user, eg with `W`, `A`, `S`, and `D` keys (see image at the end of this page). This would find the nearest pad in, say, the upward direction (for a `W`) by searching arcs from 45° to 135° (polar coords, CCW of horizontal) of increasing radius. The demo however chooses a straight upward line of increasing "radius" because, well, it's only a demo.

The centroid finding method just performs a crude search of each neighbouring pixel, starting with the first "pad" pixel as found by the initial pad search. This method tries every possible place (an tries many of the places more than once) and sums up all coordinates of located pads.

The following GIF gives some idea of what is going on. (And largely just reproduces the output you get in the REPL if you run code itself.)

![](anim.gif)

As you can see, I did not bother to use an actual masked image. This is mostly because I don't really have a proper one to hand. However, this randomly generated mask does an alright job at conveying the general idea.

```julia
using Plots, Random, Images, Statistics, Reel
gr()

# visualisation prefs
Random.seed!(15)
framedelay = 0.1

# fake mask
w = 20
h = 20
mask = [0.25 + 0.5b for b=rand(Bool, h, w)]

# search state
search = Vector{Tuple{Int, Int}}()
splat = false
push!(search, (h, w/2))	# entry point

# centroid calculation
xcoords = Vector{Int}()
ycoords = Vector{Int}()

# gif generation
frames = Frames(MIME("image/png"), fps=clamp(1/framedelay, 0.1, 30))

# helper function to visualise an intermediate step
function visualise(mat)
	z = [0 for _=mat]
	img = colorview(HSV, z, z, mat)
	push!(frames, img)
	display(img)
end

# begin the search in earnest
println("beginning search\n")

while length(search) > 0

	global splat

	spotlight = pop!(search)
	p = try
		view(mask, spotlight...)
	catch e
		println("doesn't exist, so skipping")
		continue
	end

	println("evaluating $spotlight = $(p[1])")

	if (p[1] == 0.75)

		println("white, not yet searched")
		p[1] = 1
		
		if !splat
			println("diversifying (switching to splat mode)")
			splat = true
		end

		println("recording for centroid calc")
		push!(xcoords, spotlight[2])
		push!(ycoords, spotlight[1])
		
		
		println("queuing neighbouring points for follow-up")
		push!(search, (spotlight[1]-1, spotlight[2]))
		push!(search, (spotlight[1]+1, spotlight[2]))
		push!(search, (spotlight[1], spotlight[2]-1))
		push!(search, (spotlight[1], spotlight[2]+1))

	elseif (p[1] == 0.25)

		println("black, not yet searched")
		p[1] = 0

		if splat
			println("no use")
		else
			println("going up")
			push!(search, (spotlight[1]-1, spotlight[2]))
		end

	else

		println("already searched, skipping\n")
		continue

	end

	println("visualising")
	visualise(mask)
	sleep(framedelay)

	println("$(length(search)) location(s) left to search\n")

end

# visualise results of search
println("calculating centroid")
xbar = mean(xcoords)
ybar = mean(ycoords)
centroid = Int.(round.((ybar, xbar), digits=0))

println("visualising")
img = fill(HSV(0, 0, 0), size(mask)...)
img[mask .== 0] .= HSV(110, 0.4, 0.2)
img[mask .== 1] .= HSV(110, 0.4, 0.6)
img[centroid...] = HSV(110, 0, 1)
println("centroid at $(centroid) with weight $(length(xcoords))")

[push!(frames, img) for _=1:10]
display(img)

# export
write("anim.gif", frames)

```

This all essentially used depth-first search. We can switch the `push!(search, …` statements to `pushfirst!(search, …`, giving us an alternative visualisation. The results are otherwise the same.

![](anim_bfs.gif)

Here's a better pad finding algorithm that actually finds the pad that the user would expect:

![](Page%201,%20object%205.jpg)

(Note that the alternating search direction is not necessary — I just drew it that way to keep the line continuous to make the pattern clearer.)