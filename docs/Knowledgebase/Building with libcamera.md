# Natively

You will need libcamera installed. Installation requires you have Meson and Ninja installed.

```bash
# start in a convenient directory
git clone https://git.libcamera.org/libcamera/libcamera.git
cd libcamera
sudo apt-get install meson ninja
meson setup build            # makes build directory `build`
ninja -C build               # build within `build`
sudo ninja -C build install  # install built library

# and if you really want…
cd ..
rm -r libcamera
```

You can now include libcamera in C++ using `#include <libcamera/libcamera.h>`. The (minimum) command to compile binaries is therefore:

```bash

# compile main.cpp to a.out (or whatever the default name is)
g++ -I/usr/local/include/libcamera/ main.cpp /usr/local/lib/aarch64-linux-gnu/libcamera.so

```

# Cross-compiling

Yeah, this is a can of worms, and I haven't yet fully got this working. Still, it is perhaps only useful to check syntax or something. It also requires you to have [prepared yourself a cross compiler](<Cross-compilation for RPi (eg from macOS)>) in advance.

If you really want to go down this route, you can tell Meson to cross-compile in the following way:

1. Create a config file, eg meson-cross.txt:

```
[binaries]
c = 'aarch64-unknown-linux-gnu-gcc'
cpp = 'aarch64-unknown-linux-gnu-g++'
ar = 'aarch64-unknown-linux-gnu-ar'
strip = 'aarch64-unknown-linux-gnu-strip'
exe-wrapper = 'exec-arm64-binary'

[host_machine]
system = 'linux'
cpu_family = 'aarch64'
cpu = 'arm8'
endian = 'little'
```

1. (Re)configure the project using `meson setup --cross-file meson-cross.txt -Dprefix=/Users/Sam/Development/cross-compilers/aarch64-unknown-linux-gnu/aarch64-unknown-linux-gnu/sysroot/usr --wipe build`, replacing the prefix as necessary. (Mine puts into the cross-compiler's search path.)
2. Install "as usual" with `ninja -C build install`. Note this is the usual syntax but the install location should in theory not be the usual one, but rather the one selected when setting up Meson.

Once you've done this, you should be able to compile binaries that link with libcamera using … a command very similar to the native command. However, you'll need to

- make sure you use the cross-compiler (not just `g++` — you won't have any luck with that on an x86 processor or on macOS)
- make sure you sub in the correct library and include search paths.

This method will allow compilation, but **the compiled binary will not run on the Raspberry Pi**. I still don't really understand why. Perhaps my cross-compiler uses standard C and standard C++ version that's different to the one on the Pi. Perhaps the linked shared library paths really don't have much luck. The best I've done is to copy my .so files to the Pi and shove them when running, eg `./vision`, by changing the command to `LD_PRELOAD=$LD_PRELOAD:<shared libs> ./vision`.

The result? The libraries were loaded, and I got a segfault instead.

I have had success setting up a Docker container that will run the cross-compiled binaries (it loads the libraries and runs the code without a segfault) but it is of limited practical utility (for one, it doesn't have access to any cameras). Documentation (in the form of an example that worked on my machine) is in a repo on [our p4p-83 GitHub](https://github.com/p4p-83/exec-arm64-with-docker).

A 'working' Makefile used in a past build attempt goes as follows: (note that Obsidian might have mangled the formatting)

```make
CC = aarch64-unknown-linux-gnu-g++
LDFLAGS =
BLDDIR = .
INCDIR = $(BLDDIR)/inc
SRCDIR = $(BLDDIR)/src
OBJDIR = $(BLDDIR)/bin
CFLAGS = -c -Wall -I$(INCDIR) -I/Users/Sam/Development/cross-compilers/aarch64-unknown-linux-gnu/aarch64-unknown-linux-gnu/sysroot/usr/include/libcamera
SRC = $(wildcard $(SRCDIR)/*.cpp)
OBJ = $(patsubst $(SRCDIR)/%.cpp, $(OBJDIR)/%.o, $(SRC))
EXE = $(OBJDIR)/vision

all: clean $(EXE)

$(EXE): $(OBJ)
	$(CC) $(LDFLAGS) $(OBJDIR)/*.o /Users/Sam/Development/cross-compilers/aarch64-unknown-linux-gnu/aarch64-unknown-linux-gnu/sysroot/usr/lib/libcamera.so -o $@

$(OBJDIR)/%.o : $(SRCDIR)/%.cpp
	@mkdir -p $(@D)
	$(CC) $(CFLAGS) $< -o $@

clean:
	-rm -f $(OBJDIR)/*.o $(EXE)
```

# Python pain

`pip3` is a pain in the butt, because it doesn't work when Python is managed by a package manager.

On macOS, if you've used Homebrew to install Python, you will have to run a virtual environment. Sam's is re-activated using `source ~/Development/2024/python3/venv/bin/activate` but the path obviously varies from one virtual environment setup to the next.

On Linux, apt-get seems to be clever enough to actually manage Python packages, so you don't need a virtual environment. You can just use `sudo apt-get install python3-[insert package name here]` (without the brackets) to install a dependency.
