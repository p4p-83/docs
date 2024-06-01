Dependencies

```sh
sudo apt-get update
sudo apt-get install libevent-pthreads-2.1-7 # not sure if this is 100% needed

git clone https://github.com/libevent/libevent.git
cd libevent
mkdir build && cd build
cmake ..     # Default to Unix Makefiles.
make
make verify  # (optional)
```

The MWE

```sh
git clone https://git.libcamera.org/libcamera/simple-cam.git
meson setup build
```