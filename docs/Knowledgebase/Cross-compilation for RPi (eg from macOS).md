~~I have got a proof-of-concept working here <https://github.com/p4p-83/cross-compilation-example>~~~~~~>

I do not know how well this method will play with VS Code intellisense. I hear you can run intellisense out of a Docker container these days, which might work, or alternatively using the native compiler could be okay as a fallback. I haven't looked into this yet.

## Building your own cross compiler

Derived from <https://uav-lab.org/2017/02/15/how-to-set-up-an-arm-cross-compiler-toolchain-for-macos/> to get started and <https://www.bootc.net/archives/2012/05/26/how-to-build-a-cross-compiler-for-your-raspberry-pi/> for something old but rpi-specific.

I've gone through the steps myself. It takes a damn long time but I went about it as follows.

```zsh

# following along with https://uav-lab.org/2017/02/15/how-to-set-up-an-arm-cross-compiler-toolchain-for-macos/

cd ~/Development/2024
mkdir crosscompilers
cd crosscompilers
git clone https://github.com/crosstool-ng/crosstool-ng.git
cd crosstool-ng
./bootstrap

# at this point go away and use Disk Utility to make yourself
# a case-sensitive DMG file and mount it
```

Like this:

![](Screenshot%202024-05-05%20at%2019.49.04.png)

Case sensitivity is important! In fact, it's the only reason we're working on a separate disk image. (I'm not sure if the choice between APFS and Mac OS Extended makes too much difference, but I chose the older one on the grounds that it'd probably have fewer compatibility woes.)

Note that I'm not sure if a sparse image really was the best choice in terms of performance. Knowing what I know now (that you can copy the compiler off the disk) I would probably make the thing a fixed read/write DMG file.

Also, it does use a lot of storage space, but 10 GB should be adequate if you're only making a single cross-compiler toolchain (15 GB is not enough if you follow the following steps to the letter and make a dummy toolchain first as a test run — you will have to manually delete the old toolchain's .build files midway through!).

Anyway, mount the disk image and continue. Note that in the following I've run some of the debugging steps in a separate terminal, and these are commented. Some I've "struck out" (with \~\~) because I no longer recommend doing them — I found a better way only a few steps later.

```zsh

./configure --prefix=/Volumes/ct-ng/ct-ng

# got an error
# "configure: error: missing required tool: gobjcopy objcopy"
# ran brew reinstall binutils
# `mdfind -name objcopy` to find its location
# ~~`ln /usr/local/Cellar/binutils/2.42/bin/objcopy /usr/local/bin/objcopy` so that it might be found~~

./configure --prefix=/Volumes/ct-ng/ct-ng

# can't find readelf
# also in /usr/local/Cellar/binutils/2.42/bin/readelf
# ~~backtrack with rm /usr/local/bin/objcopy~~
# then just add /usr/local/Cellar/binutils/2.42/bin to PATH with sudo vim /etc/paths

./configure --prefix=/Volumes/ct-ng/ct-ng

# missing help2man
brew install help2man

./configure --prefix=/Volumes/ct-ng/ct-ng

# finally, we made it…
# to the next steps at least:

make
make install

# now try to use it
# this isn't the compiler we want, but it will help as a slightly drier run

cd /Volumes/ct-ng/ct-ng/bin
./ct-ng arm-unknown-linux-gnueabi

# then we need to make sure it's set up right apparently
./ct-ng menuconfig

# inside Paths and misc options
#                                Local tarballs directory from ${HOME}/src to /Volumes/ct-ng/src
#                                Working directory from ${CT_TOP_DIR}/.build to /Volumes/ct-ng/.build
#                                Similarly, Prefix directory should be changed to start with ${CT_PREFIX:-/Volumes/ct-ng/x-tools}/${CT_HOST … … …

# then exit twice and select save when prompted

ulimit -n 4096
./ct-ng build		# [edit: don't bother building a demo toolchain. I've vetted the steps already; just pretend that this step doesn't exist.]

# and go make a cuppa or something apparently
# you are basically a Linux user now https://youtu.be/gCRzng7LsQI?si=qG8wUUivqtroU9Jj

# NB ct-ng will be using the .build folder for staging (as we instructed above) which is of course hidden by default (starts with a dot)
# in Finder, use command+shift+period to toggle showing of hidden files on and off (if you wish to sniff around)

# you have plenty of time to sniff around
# damn this is slow
# got to love compiling compiler compilation tools from source so that you can then compile your cross-compiler from source
# this is why I did not do software

# also why is the hub.docker.com webpage using 97% cpu in the background
# let me close that tab

# hmm 22 mins and still going string

# 40 odd minutes and my computer is also falling asleep
# time to run `caffeinate` in a second terminal to stop that happening again

# alright, 92 mins and 25 seconds later, we have our toolchain
# let's give it a whirl

alias crossgcc=/Volumes/ct-ng/x-tools/arm-unknown-linux-gnueabi/bin/arm-unknown-linux-gnueabi-gcc
crossgcc --version

cd ~/Desktop
vim test.c

# into which we put the code
#
#	#include <stdio.h>
#	
#	int main(void) {
#	        printf("Hello, world!");
#	        return 0;
#	}
#

crossgcc test.c

# it compiles
# haven't the foggiest as to which platform it can actually be run on, but hey
# it compiles

# now for the real mission
# making a cross compiler for the Pi

cd /Volumes/ct-ng/ct-ng/bin/
./ct-ng menuconfig

# set/verify the following
# in Target options
#                   Endianness: (Little endian)
#                   Bitness: (64-bit)

# and now try again

./ct-ng build

# fingers crossed it's not as bad this time around

# boo! It crashed at the 11th hour (literally…)
# anyway, it made me a compiler regardless

alias crossgcc=/Volumes/ct-ng/x-tools/aarch64-unknown-linux-gnu/bin/aarch64-unknown-linux-gnu-gcc

# usage: just like `gcc` or `clang` except with the above alias you must type `crossgcc`

```

I have found that you can copy the whole /Volumes/ct-ng/x-tools/aarch64-unknown-linux-gnu/ directory elsewhere and invoke the compiler as eg `./aarch64-unknown-linux-gnu/bin/aarch64-unknown-linux-gnu-gcc file.c` You will be blocked during the initial copy operation most likely by a certain xt-connmark.h conflicting in the now non-case-sensitive file system, but I found you can just click OK. It'll stop the copy, and at that point you click the little circled retry arrow next to the greyed out copy that almost got made, and then it will ask if you want to resume the copy. Hit resume; it doesn't complain the second time around.

I am considering putting the compiler in a sensible location on my hard drive and adding it to PATH. I will report back if I do.

I am currently in the process of recompiling my toolchain. As above, it failed at the 65th minute, and while I did get enough of a toolchain to compile a simple test program, I would like to get a clean build so that I can know that my compiler is good going forward. Hopefully it was just a trivial or one-off glitch that caused it to fail — time will tell.

## Using LLVM

Clang can work as a cross compiler provided you have the target's headers and std libraries installed. <https://clang.llvm.org/docs/CrossCompilation.html>.

You can achieve this by setting it all up in a Docker container <https://solarianprogrammer.com/2019/05/04/clang-cross-compiler-for-raspberry-pi/>.

And for testing purposes, it might help to run commands live using `docker run -it --entrypoint /bin/bash` — see <https://stackoverflow.com/questions/30172605/how-do-i-get-into-a-docker-containers-shell#30173220>.

But at this point I might as well just use Raspberry Pi's supplied cross-compilers from apt-get: <https://github.com/raspberrypi/tools>.