![](Screenshot%202024-06-13%20at%2014.04.20.png)

… with bonus profiling! (This has made the examples a little longer, but fortunately they should still be no less straightforward to follow.)

In julia:

```julia
using Libdl, Images, Random, BenchmarkTools

function makeMask()
	Random.seed!(1)
	w = 1000
	h = 1000
	m = rand(Float64, h, w)
	mask = colorview(Gray, m)
	mask2 = imfilter(mask, Kernel.gaussian(3))
	mask3 = colorview(Gray, [x .> 0.54 ? 1.0 : 0.0 for x in mask2])
	return channelview(mask3)
end

function main()
	m = Int32.(makeMask()) # type must match for csum

	println("\n\n\nC code's sum() with $(length(m)) values and -O0")
	run(`gcc -std=gnu2x -O0 -c withinterop.c`)
	run(`g++ -shared -o withinterop.so withinterop.o -lm -fPIC`)
	Libdl.dlopen("./withinterop.so") do ccode
		# do … end syntax so we don't forget to close the library
		
		# wrapper functions
		test() = @ccall $(Libdl.dlsym(ccode, :test))()::Cint
		csum(mat::Matrix{Cint})::Cint = @ccall $(Libdl.dlsym(ccode, :sum))(pointer(mat)::Ptr{Cint}, size(mat)[2]::Cint, size(mat)[1]::Cint)::Cint

		println("calculated sum of $(csum(m))")
		display(@benchmark $csum($m))	# apparently the $ sign escapes the scope? https://discourse.julialang.org/t/benchmarking-function-in-local-scope/110228

	end

	println("\n\n\nC code's sum() with $(length(m)) values and -O3")
	run(`gcc -std=gnu2x -O3 -c withinterop.c`)
	run(`g++ -shared -o withinterop.so withinterop.o -lm -fPIC`)
	Libdl.dlopen("./withinterop.so") do ccode
		
		test() = @ccall $(Libdl.dlsym(ccode, :test))()::Cint
		csum(mat::Matrix{Cint})::Cint = @ccall $(Libdl.dlsym(ccode, :sum))(pointer(mat)::Ptr{Cint}, size(mat)[2]::Cint, size(mat)[1]::Cint)::Cint

		println("calculated sum of $(csum(m))")
		display(@benchmark $csum($m))

	end

	println("\n\n\nC code's sum() with $(length(m)) values and -Ofast")
	run(`gcc -std=gnu2x -Ofast -c withinterop.c`)
	run(`g++ -shared -o withinterop.so withinterop.o -lm -fPIC`)
	Libdl.dlopen("./withinterop.so") do ccode
		
		test() = @ccall $(Libdl.dlsym(ccode, :test))()::Cint
		csum(mat::Matrix{Cint})::Cint = @ccall $(Libdl.dlsym(ccode, :sum))(pointer(mat)::Ptr{Cint}, size(mat)[2]::Cint, size(mat)[1]::Cint)::Cint

		println("calculated sum of $(csum(m))")
		display(@benchmark $csum($m))

	end

	println("\n\n\nJulia inbuilt sum() with $(length(m)) values")
	println("calculated sum of $(sum(m))")
	display(@benchmark sum($m))

end

main()
```

Yes, declaring the wrapper function all at once has awful syntax.

In C:

```c
#include <stdint.h>

int test() {
	return 100;
}

int sum(int* arr, int width, int height) {
	const int length = width * height;
	int s = 0;
	for (int i = 0; i < length; ++i) {
		s += arr[i];
	}
	return s;
}
```