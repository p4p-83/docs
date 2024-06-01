This is incredibly painful — the OpenCV docs on this are very poor. Here's my attempt to fill the gaps.

> [!failure]
> Don't follow these steps. I'm keeping them for reference only, but they didn't work and I cannot be bothered fixing them. Try the steps from the following day's attempt instead.

## What's happening here

OpenCV is interoperable with Julia, but the packages available in Julia's package manager are old and broken. Specifically, the CxxWrap version relied upon by OpenCV.jl seems to be broken (perhaps C++ has changed that much since 2016). Therefore I am attempting to build things from scratch in alignment with the online step-by-step guides for each component — doing so should force everything to the newest and most compatible versions.

This process happens across a few steps.

1. We build [libcxxwrap-julia](https://github.com/JuliaInterop/libcxxwrap-julia) from source
2. We build OpenCV from source, which seems to leverage step 1 under the hood.

## Mucking around that I did first

This ain't part of the real process, but I did do it first while floundering around, and I feel that I might as well record it here in case I need to somehow undo it, or in case it factors into some later troubleshooting somewhere.

Notably, I changed which `clang` I was using, following [this answer](https://stackoverflow.com/a/74744927). My path did change after `sudo xcode-select -r` to point to a directory within Xcode, where before it seemed more generic, shorter, and related to the command-line tools.

I also uninstalled the Homebrew-managed version of Julia and switched to the Julia-managed version. I tried various permutations of uninstalling and reinstalling OpenCV.jl (through Julia's package prompt) and its dependencies (eg CxxWrap.jl), ultimately removing them at the end with `pkg> rm [name here]`.

I also installed OpenCV with `brew install opencv`, which pulled in a number of dependencies and installed OpenCV, but because I eventually decided to build from source, I did run `brew uninstall opencv` before proceeding through the following steps.

## Building libcxxwrap-julia

```zsh

# some temporary folder probably
# remember where you put it though
cd ~/Desktop

git clone https://github.com/JuliaInterop/libcxxwrap-julia.git
cd libcxxwrap-julia
julia

```

Now we use the Julia REPL for a bit. Type `]` to access the package manager prompt. Use the delete key when ready to return to the normal julia prompt.

```
pkg> develop libcxxwrap_julia_jll
julia> import libcxxwrap_julia_jll
julia> libcxxwrap_julia_jll.dev_jll()
```

That last step will have made a bunch of files in the `~/.julia` directory (it prints the exact path). We'll now spawn a shell here and work in this directory.

```zsh

cd /home/user/.julia/dev/libcxxwrap_julia_jll/override
rm -rf *

# find the path to Julia yourself
# `where julia` might help you — although be aware that it may be a symlink that you have then follow
# think that'd be `which julia` on Linux
# in my case, I installed with Juliaup, and `where` wasn't much help. I found it here:
julia_prefix=~/.julia/juliaup/julia-1.10.3+0.x64.apple.darwin14

# the path to the clone of libcxxwrap-julia
source_path=~/Desktop/libcxxwrap-julia

# configure and build
cmake -DJulia_PREFIX=$julia_prefix $source_path
cmake --build . --config Release

```

Now you should be able to pick it up from the Julia REPL. Worth a quick check:

```
julia> using CxxWrap
julia> CxxWrap.prefix_path()
```

But we're not done yet — it's time for the most obtuse step of the guide. We need to put a manual override in a config file for reasons. Let's break it down.

```zsh

# use whatever editor suits you
vim ~/.julia/artifacts/Overrides.toml

```

Then make sure you have the following in the file, where the directory used is the directory we were just building from.

```
[3eaa8342-bff7-56a5-9981-c04077f7cee7]
libcxxwrap_julia = "/Users/Sam/.julia/dev/libcxxwrap_julia_jll/override"

```

The OpenCV guide suggests additional tests.

```
pkg> add CxxWrap
julia> using CxxWrap
julia> CxxWrap.CxxWrapCore.prefix_path()
```

The final command outputs `"/Users/Sam/.julia/dev/libcxxwrap_julia_jll/override"`. If you get the path from above, you're all to move on to the next step — building OpenCV.

## Building OpenCV from source with Julia compatibility

<https://docs.opencv.org/4.x/d0/db2/tutorial_macos_install.html>

Note that I used `~/.julia/dev/opencv` for these steps because I had absolutely no idea what was going on. Turns out I was just making a working directory that could be deleted afterwards, and so something like `~/Desktop/temp` would be just fine, or maybe `~/Development/2024/opencv` if I wanted the ability to rebuild later on. Hindsight is clearer than their instructions.

```zsh

cd ~/.julia/dev/
mkdir opencv
cd opencv
git clone https://github.com/opencv/opencv.git
git clone https://github.com/opencv/opencv_contrib.git

mkdir build_opencv
cd build_opencv

cmake -DOPENCV_EXTRA_MODULES_PATH=../opencv_contrib/modules -DWITH_JULIA=ON ../opencv

```

That last one output this:

```
-- Installing opencv_julia bindings at /Users/Sam/.julia/dev/opencv/build_opencv
-- opencv_coreopencv_flannopencv_imgprocopencv_dnnopencv_features2dopencv_imgcodecsopencv_videoioopencv_calib3dopencv_highguiopencv_objdetect
-- Placing Julia tests in /Users/Sam/.julia/dev/opencv/build_opencv/modules/julia/test
-- Found 'misc' Python modules from /Users/Sam/.julia/dev/opencv/opencv/modules/python/package/extra_modules
-- Found 'mat_wrapper;utils' Python modules from /Users/Sam/.julia/dev/opencv/opencv/modules/core/misc/python/package
-- Found 'gapi' Python modules from /Users/Sam/.julia/dev/opencv/opencv/modules/gapi/misc/python/package
-- Performing Test HAVE_CXX_WNO_UNUSED_PRIVATE_FIELD
-- Performing Test HAVE_CXX_WNO_UNUSED_PRIVATE_FIELD - Success
-- Found 'misc' Python modules from /Users/Sam/.julia/dev/opencv/opencv/modules/python/package/extra_modules
-- Found 'mat_wrapper;utils' Python modules from /Users/Sam/.julia/dev/opencv/opencv/modules/core/misc/python/package
-- Found 'gapi' Python modules from /Users/Sam/.julia/dev/opencv/opencv/modules/gapi/misc/python/package
-- 
-- General configuration for OpenCV 4.10.0-pre =====================================
--   Version control:               4.9.0-536-g7e9ef4db86
-- 
--   Extra modules:
--     Location (extra):            /Users/Sam/.julia/dev/opencv/opencv_contrib/modules
--     Version control (extra):     4.9.0-83-g1ed3dd2c
-- 
--   Platform:
--     Timestamp:                   2024-05-31T06:09:23Z
--     Host:                        Darwin 22.6.0 x86_64
--     CMake:                       3.28.1
--     CMake generator:             Unix Makefiles
--     CMake build tool:            /usr/bin/make
--     Configuration:               Release
-- 
--   CPU/HW features:
--     Baseline:                    SSE SSE2 SSE3 SSSE3 SSE4_1
--       requested:                 DETECT
--     Dispatched code generation:  SSE4_2 FP16 AVX AVX2 AVX512_SKX
--       requested:                 SSE4_1 SSE4_2 AVX FP16 AVX2 AVX512_SKX
--       SSE4_2 (2 files):          + POPCNT SSE4_2
--       FP16 (1 files):            + POPCNT SSE4_2 FP16 AVX
--       AVX (9 files):             + POPCNT SSE4_2 AVX
--       AVX2 (38 files):           + POPCNT SSE4_2 FP16 FMA3 AVX AVX2
--       AVX512_SKX (8 files):      + POPCNT SSE4_2 FP16 FMA3 AVX AVX2 AVX_512F AVX512_COMMON AVX512_SKX
-- 
--   C/C++:
--     Built as dynamic libs?:      YES
--     C++ standard:                11
--     C++ Compiler:                /Applications/Xcode.app/Contents/Developer/Toolchains/XcodeDefault.xctoolchain/usr/bin/c++  (ver 15.0.0.15000100)
--     C++ flags (Release):         -fsigned-char -W -Wall -Wreturn-type -Wnon-virtual-dtor -Waddress -Wsequence-point -Wformat -Wformat-security -Wmissing-declarations -Wmissing-prototypes -Wstrict-prototypes -Wundef -Winit-self -Wpointer-arith -Wshadow -Wsign-promo -Wuninitialized -Wno-delete-non-virtual-dtor -Wno-unnamed-type-template-args -Wno-comment -fdiagnostics-show-option -Wno-long-long -Qunused-arguments -Wno-semicolon-before-method-body -ffunction-sections -fdata-sections  -fvisibility=hidden -fvisibility-inlines-hidden -Wno-deprecated-copy -O3 -DNDEBUG  -DNDEBUG
--     C++ flags (Debug):           -fsigned-char -W -Wall -Wreturn-type -Wnon-virtual-dtor -Waddress -Wsequence-point -Wformat -Wformat-security -Wmissing-declarations -Wmissing-prototypes -Wstrict-prototypes -Wundef -Winit-self -Wpointer-arith -Wshadow -Wsign-promo -Wuninitialized -Wno-delete-non-virtual-dtor -Wno-unnamed-type-template-args -Wno-comment -fdiagnostics-show-option -Wno-long-long -Qunused-arguments -Wno-semicolon-before-method-body -ffunction-sections -fdata-sections  -fvisibility=hidden -fvisibility-inlines-hidden -Wno-deprecated-copy -g  -O0 -DDEBUG -D_DEBUG
--     C Compiler:                  /Applications/Xcode.app/Contents/Developer/Toolchains/XcodeDefault.xctoolchain/usr/bin/cc
--     C flags (Release):           -fsigned-char -W -Wall -Wreturn-type -Wnon-virtual-dtor -Waddress -Wsequence-point -Wformat -Wformat-security -Wmissing-declarations -Wmissing-prototypes -Wstrict-prototypes -Wundef -Winit-self -Wpointer-arith -Wshadow -Wsign-promo -Wuninitialized -Wno-delete-non-virtual-dtor -Wno-unnamed-type-template-args -Wno-comment -fdiagnostics-show-option -Wno-long-long -Qunused-arguments -Wno-semicolon-before-method-body -ffunction-sections -fdata-sections  -fvisibility=hidden -fvisibility-inlines-hidden -O3 -DNDEBUG  -DNDEBUG
--     C flags (Debug):             -fsigned-char -W -Wall -Wreturn-type -Wnon-virtual-dtor -Waddress -Wsequence-point -Wformat -Wformat-security -Wmissing-declarations -Wmissing-prototypes -Wstrict-prototypes -Wundef -Winit-self -Wpointer-arith -Wshadow -Wsign-promo -Wuninitialized -Wno-delete-non-virtual-dtor -Wno-unnamed-type-template-args -Wno-comment -fdiagnostics-show-option -Wno-long-long -Qunused-arguments -Wno-semicolon-before-method-body -ffunction-sections -fdata-sections  -fvisibility=hidden -fvisibility-inlines-hidden -g  -O0 -DDEBUG -D_DEBUG
--     Linker flags (Release):      -Wl,-dead_strip  
--     Linker flags (Debug):        -Wl,-dead_strip  
--     ccache:                      NO
--     Precompiled headers:         NO
--     Extra dependencies:
--     3rdparty dependencies:
-- 
--   OpenCV modules:
--     To be built:                 alphamat aruco bgsegm bioinspired calib3d ccalib core datasets dnn dnn_objdetect dnn_superres dpm face features2d flann freetype fuzzy gapi hdf hfs highgui img_hash imgcodecs imgproc intensity_transform julia line_descriptor mcc ml objdetect optflow phase_unwrapping photo plot python3 quality rapid reg rgbd saliency shape signal stereo stitching structured_light superres surface_matching text tracking ts video videoio videostab wechat_qrcode xfeatures2d ximgproc xobjdetect xphoto
--     Disabled:                    world
--     Disabled by dependency:      -
--     Unavailable:                 cannops cudaarithm cudabgsegm cudacodec cudafeatures2d cudafilters cudaimgproc cudalegacy cudaobjdetect cudaoptflow cudastereo cudawarping cudev cvv java matlab ovis python2 sfm viz
--     Applications:                tests perf_tests apps
--     Documentation:               NO
--     Non-free algorithms:         NO
-- 
--   GUI:                           COCOA
--     Cocoa:                       YES
--     VTK support:                 NO
-- 
--   Media I/O: 
--     ZLib:                        build (ver 1.3.1)
--     JPEG:                        build-libjpeg-turbo (ver 3.0.3-70)
--       SIMD Support Request:      YES
--       SIMD Support:              NO
--     WEBP:                        build (ver encoder: 0x020f)
--     PNG:                         build (ver 1.6.43)
--       SIMD Support Request:      YES
--       SIMD Support:              YES (Intel SSE)
--     TIFF:                        build (ver 42 - 4.6.0)
--     JPEG 2000:                   build (ver 2.5.0)
--     OpenEXR:                     OpenEXR::OpenEXR (ver 3.2.4)
--     HDR:                         YES
--     SUNRASTER:                   YES
--     PXM:                         YES
--     PFM:                         YES
-- 
--   Video I/O:
--     DC1394:                      NO
--     FFMPEG:                      YES
--       avcodec:                   YES (61.3.100)
--       avformat:                  YES (61.1.100)
--       avutil:                    YES (59.8.100)
--       swscale:                   YES (8.1.100)
--       avresample:                NO
--     GStreamer:                   NO
--     AVFoundation:                YES
-- 
--   Parallel framework:            GCD
-- 
--   Trace:                         YES (with Intel ITT)
-- 
--   Other third-party libraries:
--     Intel IPP:                   2021.9.1 [2021.9.1]
--            at:                   /Users/Sam/.julia/dev/opencv/build_opencv/3rdparty/ippicv/ippicv_mac/icv
--     Intel IPP IW:                sources (2021.9.1)
--               at:                /Users/Sam/.julia/dev/opencv/build_opencv/3rdparty/ippicv/ippicv_mac/iw
--     Lapack:                      YES (/Applications/Xcode.app/Contents/Developer/Platforms/MacOSX.platform/Developer/SDKs/MacOSX14.2.sdk/System/Library/Frameworks/Accelerate.framework -lm -ldl)
--     Eigen:                       YES (ver 3.4.0)
--     Custom HAL:                  NO
--     Protobuf:                    build (3.19.1)
--     Flatbuffers:                 builtin/3rdparty (23.5.9)
-- 
--   OpenCL:                        YES (no extra features)
--     Include path:                NO
--     Link libraries:              -framework OpenCL
-- 
--   Python 3:
--     Interpreter:                 /usr/local/bin/python3 (ver 3.12.3)
--     Libraries:                   /usr/local/Frameworks/Python.framework/Versions/3.12/lib/libpython3.12.dylib (ver 3.12.3)
--     Limited API:                 NO
--     numpy:                       /usr/local/lib/python3.12/site-packages/numpy/core/include (ver 1.26.4)
--     install path:                lib/python3.12/site-packages/cv2/python-3.12
-- 
--   Python (for build):            /usr/local/bin/python3
-- 
--   Java:                          
--     ant:                         NO
--     Java:                        NO
--     JNI:                         NO
--     Java wrappers:               NO
--     Java tests:                  NO
-- 
--   JULIA:                         YES
--     Julia_EXECUTABLE:            /Users/Sam/.juliaup/bin/julia
--     JlCxx_DIR:                   /Users/Sam/.julia/dev/libcxxwrap_julia_jll/override
-- 
--   Install to:                    /usr/local
-- -----------------------------------------------------------------
-- 
-- Configuring done (139.7s)
-- Generating done (2.2s)
-- Build files have been written to: /Users/Sam/.julia/dev/opencv/build_opencv
```

Now I can build OpenCV from source and install it.

```zsh

# bog standard command
sudo make install

# alternative command
time caffeinate sudo make install

```

The second command invokes `time`, timing the execution time of `caffeinate sudo make install` and printing the key stats at the end. `caffeinate sudo make install` runs `sudo make install` but prevents the computer from going to sleep while that last command runs (at least, that's what it's supposed to do — mine still sleeps occasionally despite this), and `sudo make install` builds and installs the shared libraries.

… and it errors out. Retrying with

```zsh

cmake -DOPENCV_EXTRA_MODULES_PATH=../opencv_contrib/modules -DWITH_JULIA=ON -DJulia_EXECUTABLE=/Users/Sam/.julia/juliaup/julia-1.10.3+0.x64.apple.darwin14/bin/julia ../opencv

time caffeinate sudo make install

```