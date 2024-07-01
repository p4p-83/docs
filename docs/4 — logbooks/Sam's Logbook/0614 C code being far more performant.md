Perhaps foolishly (I really should be studying for an exam) I wrote a third algorithm for centroid finding (very similar to the first one in [0612 Centroid scanning](0612%20Centroid%20scanning.md)) but this time in C. I was fairly disappointed with the performance of the julia algorithms — they were nowhere near fast enough. I chose to write the first in julia on the grounds that it would be nice to stick with a single language as much as possible, and other parts of the project will be more streamlined if we work in julia. I wrote the second one with the aim of improving the performance of the first, but somehow absolutely tanked the performance in the process.

Yesterday I discovered (mostly on Discord) that simple functions in julia are fairly performant, but that calling into C is at least as performant (but in those cases not by a great margin). Calling into C requires some somewhat unsightly syntax (and some garbage collector paranoia), but is not too difficult otherwise.

The takeaway from all of this is that quick and dirty algorithms in julia are not fast enough, that I do not know how the language works enough to be able to speed julia code up, and that switching to C for critical parts really isn't too difficult.

Writing this C algorithm has allowed me to confirm that the performance is favourable, and slightly surprisingly, the process is more enjoyable. The algorithm was far easier to write and the syntax cleaner in the C code.

First, here's the algorithm. Note that this is not the same as either julia algorithm, although it is resemblant of the first algorithm. Part of this is that I have got better at writing algorithms for this problem simply because I've done it twice already and seen which sorts of things work and which don't. The other part of this is that this is C code, and C is a different language to julia with a different workflow. This new approach fairly well gave up on idiomatic code and self-documentation and just did things as directly as possible and as strategically as possible. (I'm not just comparing julia and C here — I was also trying to prove to myself that real-time image processing with any sort of custom code was going to be feasible.)

![](Page%201,%20object%205-1.jpg)

I wrote the following wrapper to call into C. It's best run in the REPL to see what's happening.

```julia
# withinterop.jl
using Libdl, Images, Random, BenchmarkTools

function makeMask()
	Random.seed!(1)
	w = 1000
	h = 1000
	m = rand(Float64, h, w)
	mask = colorview(Gray, m)
	mask2 = imfilter(mask, Kernel.gaussian(20))
	mask3 = colorview(Gray, [x .> 0.51 ? 1.0 : 0.0 for x in mask2])
	return Cint.(channelview(mask3))
end

struct Centroid
	x::Cint
	y::Cint
	weight::Cint
end

function main()
	cfile = "algorithm"
	run(`gcc -std=gnu2x -O3 -c $cfile.c`)
	run(`g++ -shared -o $cfile.so $cfile.o -lm -fPIC`)
	centroids = Libdl.dlopen("./$cfile.so") do ccode
		
		function findPads(mat::Matrix{Cint})::Vector{Centroid}
			(height, width) = size(mat)
			matPtr = pointer(mat)
			centroidsList = fill(Centroid(0, 0, 0), 200)
			centroidsListPtr = pointer(centroidsList)
			numCentroids = GC.@preserve centroidsList @ccall $(Libdl.dlsym(ccode, :findPads))(width::Cint, height::Cint, matPtr::Ptr{Cint}, centroidsListPtr::Ptr{Centroid})::Cint
			return centroidsList[1:numCentroids]
		end

		mask = makeMask()
		centroids = GC.@preserve mask findPads(mask)
		display(@benchmark $findPads($mask))

		preview = [px > 0 ? RGB(1, 1, 1) : RGB(0.9, 0.9, 0.9) for px in mask]
		for c in centroids preview[c.y, c.x] = RGB(0, 0, 0) end
		display(preview)

		return centroids

	end
	return centroids
end

centroids = main()
```

I then wrote the following C code. (This gets compiled with `-O3` from within that julia wrapper above.)

```c
// algorithm.c

#include <stdint.h>
#include <stdbool.h>
#include <stdio.h>

#define MAX_SEARCH_BUF 5000
#define MAX_NUM_CENTROIDS 200
#define GRID_STEP 16

typedef struct Point {
	int x, y;
} Point;

typedef struct Centroid {
	int x, y, weight;
} Centroid;

inline Point point(int x, int y) {
	const Point p = {.x = x, .y = y};
	return p;
}

int splatSearch(int width, int height, int (mat)[width][height], int startX, int startY, Centroid *centroidsList, int centroidIndex) {

	#define pixelAt(x, y) (mat[(x)][(y)])

	// printf("attempting to search (%d, %d)\n", startX, startY);

	if (pixelAt(startX, startY) != 1) {
		// pixel is either irrelevant or has already been searched
		// printf("nothing to see here\n");
		return centroidIndex;
	}

	// keep track of search
	Point search[MAX_SEARCH_BUF];
	int searchIndex = 0;

	int xsum = 0, ysum = 0, count = 0;

	// pixel is clearly worth searching
	// seed the search
	search[searchIndex++] = point(startX, startY);

	// keep searching until there are no more search points
	while (searchIndex) {

		// note that this is --searchIndex because we've used searchIndex++ to add: they MUST be coordinated
		const Point thisPoint = search[--searchIndex];

		// printf("pad at (%d, %d)\n", thisPoint.x, thisPoint.y);

		// we already know this pixel is 1 (else it would not be in the search array)
		// record in statistics
		++count;
		xsum += thisPoint.x;
		ysum += thisPoint.y;

		// update pixel to show we've been here
		mat[thisPoint.x][thisPoint.y] = 2;

		// helper macro
		// test if pixel needs to be added to search, and then add it if so
		#define trySearching(dx, dy) {														\
			if (pixelAt((thisPoint.x + (dx)), (thisPoint.y + (dy))) == 1) {					\
				search[searchIndex++] = point((thisPoint.x + (dx)), (thisPoint.y + (dy)));	\
			}																				\
		}

		// see if neighbouring pixels are also pad
		trySearching(-1,  0);
		trySearching( 1,  0);
		trySearching( 0, -1);
		trySearching( 0,  1);

		// bounds check
		if (searchIndex >= MAX_SEARCH_BUF)
			// abort this splat search
			return centroidIndex;

	}

	#undef pixelAt

	// summarise the search
	const Centroid thisCentroid = {
		.x = xsum / count,
		.y = ysum / count,
		.weight = count
	};

	// return results
	centroidsList[centroidIndex] = thisCentroid;
	return centroidIndex + 1;

}

int findPads(int width, int height, int (mat)[width][height], Centroid *centroidsList) {

	// prepare for search
	// we impose the condition that all edge pixels must be non-pad
	// this means that the splatSearches will never go out of bounds even with no explicit bounds checking

	for (int x = 0; x < width; ++x) {
		mat[x][0] = 0;
		mat[x][height-1] = 0;
	}

	for (int y = 0; y < height; ++y) {
		mat[0][y] = 0;
		mat[width-1][y] = 0;
	}

	// seed the search
	// we'll do this on a grid. No need to try starting it at every single pixel

	int centroidIndex = 0;

	for (int x = 1; x < width-1; x += GRID_STEP) {
		for (int y = 1; y < height-1; y += GRID_STEP) {
			// printf("\n\nhello from (%d, %d)\n", x, y);
			centroidIndex = splatSearch(width, height, mat, x, y, centroidsList, centroidIndex);
			// printf("centroid index is now %d", centroidIndex);
			if (centroidIndex >= MAX_NUM_CENTROIDS) return MAX_NUM_CENTROIDS - 1;
		}
	}

	// splatSearch will have already populated centroidsList
	// just return the length (as tracked by splatSearch)
	return centroidIndex;

}
```

You'll notice that just about everything is statically assigned (that's most convenient in C, and I suspect most performant too) and there are a minimum of bounds checks. I've tried to weigh up the tradeoffs as best as reasonably possible. Pixels that are not pad are only searched on average 1/(16^2) times, and pixels that are part of a pad are searched on average (4 + 1/(16^2)) times. For a somewhat sparse image, this means that each pixel is searched very few times. In all ways this is probably the best version of the algorithm I've written so far.

The performance was significantly improved. Where the 'comparable' julia algorithm took around 640 µs (loose stat), and the 'optimised' julia took 800 ms, this new C code (within the julia wrapper) took only 8.7 µs. That's bonkers.

(Note that the memory estimate in the following capture is a little misleading as it does not capture the memory allocated within C.)

![](Screenshot%202024-06-14%20at%2013.38.13.png)

I know what language I'll be choosing from now onwards for these types of tasks.

Here's a picture of the output to prove that the code is actually doing its job (and isn't just fast because it's wrong).

![](plot_50.png)

— Addendum: in future, I should consider changing the `Centroid.x` and `Centroid.y` values to be floats, not ints, if we go ahead with an algorithm of this nature. There's nothing forcing them to be integers (well, except the need to round to the nearest integer pixel when plotting maps like the above from julia, but it'd be better to have julia do the rounding for that specific task), and floats would retain a bit more accuracy.

— Addendum: it seems that `-Ofast` does shave a few fractions of a microsecond off.