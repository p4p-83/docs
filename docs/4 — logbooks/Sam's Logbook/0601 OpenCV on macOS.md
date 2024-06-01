The only way that I found to get this to work was to build from source using Homebrew.

```zsh
brew install opencv --build-from-source
```

Having previously tried to build from source manually, I encountered an issue where brew couldn't create the required symlinks. This was because my previous command `sudo make uninstall` deleted the files, but not the directories, and the directories were created by `sudo make install`, which meant that only the superuser had write permissions. Brew couldn't put new files in. This I fixed by repeatedly running `brew link opencv`, observing the path that was not accessible, and deleting the directories myself (using Finder and inputting a password for deletion). This part can be done with a little bit of common sense, and eventually the `brew link opencv` just did its thing with no errors, and OpenCV was installed.

## Using it

CMake can pick it up when build from source. A minimum viable example is reproduced below.

```CMake
# CMakeLists.txt
cmake_minimum_required(VERSION 3.10)
project(example)

find_package(OpenCV REQUIRED)
add_executable(example main.cpp)
target_link_libraries(example ${OpenCV_LIBS})
```

Refer to [this 6 minute YouTube video](https://www.youtube.com/watch?v=Ozc3zWJ_NhQ) for example code and usage (or just use any other OpenCV example code).