I'm going to try again one last time before concluding that the bindings are broken. Let's see what happens.

> [!failure]
> Don't follow these steps either. The bindings seem to just be plain broken.

**"Working directory"**

I'm creating a working folder in ~/Development/2024/p4p/JuliaOpenCV. I'll open this in VS Code and begin a shell session from there.

```zsh

git clone https://github.com/JuliaInterop/libcxxwrap-julia.git
cd libcxxwrap-julia

```

Now on the Julia REPL (you can actually just copy-paste this in verbatim if you want — Julia is smart enough to work out which pane you want).

```
pkg> develop libcxxwrap_julia_jll
julia> import libcxxwrap_julia_jll
julia> libcxxwrap_julia_jll.dev_jll()
```

You should end up with the following result:

`[ Info: libcxxwrap_julia_jll dev'ed out to /Users/Sam/.julia/dev/libcxxwrap_julia_jll with pre-populated override directory`

**Override directory**

Let's now open a new terminal and cd into that directory.

```zsh
cd /Users/Sam/.julia/dev/libcxxwrap_julia_jll
cd override
```

We'll call this the "override directory" from now onwards.

We need to clear the directory out.

```zsh
pwd # gives /Users/Sam/.julia/dev/libcxxwrap_julia_jll/override
rm -rf *
```

Then we obtain a few critical paths.

```zsh
# find the path to Julia yourself
# `where julia` might help you — although be aware that it may be a symlink that you have then follow
# think that'd be `which julia` on Linux
# in my case, I installed with Juliaup, and `where` wasn't much help. I found it here:
julia_prefix=~/.julia/juliaup/julia-1.10.3+0.x64.apple.darwin14

# the path to the clone of libcxxwrap-julia
source_path=/Users/Sam/Development/2024/p4p/JuliaOpenCV/libcxxwrap-julia
```

Now we build libcxxwrap. It seems that the important part is that we will end up with a libcxxwrap Julia library as well as the CMake files. Building OpenCV for Julia seems to be dependent on these CMake files etc.

Ensure you don't have libcxxwrap or CxxWrap.jl installed using the Julia package manager. Then rebuild them from source.

```zsh
cmake -DJulia_PREFIX=$julia_prefix $source_path
cmake --build . --config Release
```

When that finishes, we can set up the relevant override by editing `~/.julia/artifacts/Overrides.toml` to contain the following:

```toml
[3eaa8342-bff7-56a5-9981-c04077f7cee7]
libcxxwrap_julia = "/Users/Sam/.julia/dev/libcxxwrap_julia_jll/override"
```

Now we can install CxxWrap.jl and test for correctness (in any terminal; I chose to use a separate one).

```
pkg> add CxxWrap
julia> using CxxWrap
julia> CxxWrap.CxxWrapCore.prefix_path()
```

The expected results for this look as follows

![](Screenshot%202024-06-01%20at%2014.07.09.png)

**Back in the working directory**

Back in /Users/Sam/Development/2024/p4p/JuliaOpenCV, it's time to start prepping to build OpenCV proper. Create a new subdirectory and clone the OpenCV source code.

```zsh
mkdir opencv
cd opencv
git clone https://github.com/opencv/opencv.git
git clone https://github.com/opencv/opencv_contrib.git
```

Now we build it, and cross our fingers.

```zsh
mkdir build_opencv
cd build_opencv

time cmake -DOPENCV_EXTRA_MODULES_PATH=../opencv_contrib/modules -DWITH_JULIA=ON -DJulia_EXECUTABLE=/Users/Sam/.julia/juliaup/julia-1.10.3+0.x64.apple.darwin14/bin/julia -DJULIA_PKG_INSTALL_PATH=/Users/Sam/.julia/packages ../opencv
# ~2.5 mins

time caffeinate sudo make install
```

While this was in progress, I repeated the same steps on a Raspberry Pi, this time working from /home/Sam/Development/JuliaOpenCV.

```zsh
# Raspberry Pi
julia_prefix=~/.julia/juliaup/julia-1.10.3+0.aarch64.linux.gnu/
source_path=/home/Sam/Development/JuliaOpenCV/libcxxwrap-julia

# had to increase swap file (to augment RAM) to 4096 MB
# see https://pimylifeup.com/raspberry-pi-swap-file/

# when compiling libcxxwrap_julia_jll, add jobs flag to avoid running out of memory and having cc1plus killed by the OS
cmake -DJulia_PREFIX=$julia_prefix $source_path
cmake --build . --config Release -j 1 --parallel 1
```