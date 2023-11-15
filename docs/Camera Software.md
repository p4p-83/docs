# Resolution

Early indications were that taking still images using the usual method (rasbpistill or the libcamera emulation) are slow, and limit you to maybe 1-2 fps. Worse, it seemed that video was limited in resolution to a mere 1080p even with lowered framerates.

Apparently you can dodge both issues by specifying custom video parameters and choosing an uncompressed encoding method (not H.264).

## 15 fps at high resolution

The following snippet kind of combines both steps (Y'UV 420 is used instead of H.264).

```bash
sudo modprobe bcm2835-v4l2 max_video_width=2592 max_video_height=1944
v4l2-ctl -p 15
v4l2-ctl -v width=2592,height=1944,pixelformat=I420
v4l2-ctl --stream-mmap=3 --stream-to=/dev/null --streamcount=1000
```

[source: Raspberry Pi Forum thread](https://forums.raspberrypi.com/viewtopic.php?t=82691)

## The 1080p limit

Apparently the 1920x1080 limit applies only to H.264 compression. Other compression methods don't have hard limits (or, at very least, they're much looser).

> MJPEG, being a video codec, has a target bitrate rather than specifying the normal Q-factor. It tweaks the Q-factor per frame based on the bitrate achieved so far.
> As long as you set the bitrate high enough(\*), then the image quality should be maintained reasonably. MJPEG is not an efficient codec, but it's the only option as H264 on Pi maxes out at 1920x1080.
> 
> (\*) Sorry, can't remember the v4l2-ctl command off the top of my head. It'll be v4l2-ctl --set-ctrl=\<something\> =\<value\> . v4l2-ctl --list-ctrls would list all the values allowed for \<something\> , one of which will be video bitrate.
[source: Raspberry Pi Forum thread](https://forums.raspberrypi.com/viewtopic.php?t=82691)

The best part of this is that I wanted to shoot uncompressed anyway.