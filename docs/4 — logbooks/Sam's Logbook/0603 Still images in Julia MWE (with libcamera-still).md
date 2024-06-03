![](Image.jpeg)

Above: iPhone photo of the setup.

Trying to capture data from the camera directly to Julia.

First, I'm using `v4l2-ctl` to set up the camera frame specification.

```
Sam@rpi5:~/Development/pcbtests $ v4l2-ctl --device /dev/video0 --list-formats-ext
ioctl: VIDIOC_ENUM_FMT
        Type: Video Capture

        [0]: 'YUYV' (YUYV 4:2:2)
                Size: Stepwise 16x16 - 16384x16384 with step 2/1
        [1]: 'UYVY' (UYVY 4:2:2)
                Size: Stepwise 16x16 - 16384x16384 with step 2/1
        [2]: 'YVYU' (YVYU 4:2:2)
                Size: Stepwise 16x16 - 16384x16384 with step 2/1
        [3]: 'VYUY' (VYUY 4:2:2)
                Size: Stepwise 16x16 - 16384x16384 with step 2/1
        [4]: 'RGBP' (16-bit RGB 5-6-5)
                Size: Stepwise 16x16 - 16384x16384 with step 2/1
        [5]: 'RGBR' (16-bit RGB 5-6-5 BE)
                Size: Stepwise 16x16 - 16384x16384 with step 2/1
        [6]: 'RGBO' (16-bit A/XRGB 1-5-5-5)
                Size: Stepwise 16x16 - 16384x16384 with step 2/1
        [7]: 'RGBQ' (16-bit A/XRGB 1-5-5-5 BE)
                Size: Stepwise 16x16 - 16384x16384 with step 2/1
        [8]: 'RGB3' (24-bit RGB 8-8-8)
                Size: Stepwise 16x16 - 16384x16384 with step 2/1
        [9]: 'BGR3' (24-bit BGR 8-8-8)
                Size: Stepwise 16x16 - 16384x16384 with step 2/1
        [10]: 'RGB4' (32-bit A/XRGB 8-8-8-8)
                Size: Stepwise 16x16 - 16384x16384 with step 2/1
        [11]: 'BA81' (8-bit Bayer BGBG/GRGR)
                Size: Stepwise 16x16 - 16384x16384 with step 2/1
        [12]: 'GBRG' (8-bit Bayer GBGB/RGRG)
                Size: Stepwise 16x16 - 16384x16384 with step 2/1
        [13]: 'GRBG' (8-bit Bayer GRGR/BGBG)
                Size: Stepwise 16x16 - 16384x16384 with step 2/1
        [14]: 'RGGB' (8-bit Bayer RGRG/GBGB)
                Size: Stepwise 16x16 - 16384x16384 with step 2/1
        [15]: 'pBAA' (10-bit Bayer BGBG/GRGR Packed)
                Size: Stepwise 16x16 - 16384x16384 with step 2/1
        [16]: 'pGAA' (10-bit Bayer GBGB/RGRG Packed)
                Size: Stepwise 16x16 - 16384x16384 with step 2/1
        [17]: 'pgAA' (10-bit Bayer GRGR/BGBG Packed)
                Size: Stepwise 16x16 - 16384x16384 with step 2/1
        [18]: 'pRAA' (10-bit Bayer RGRG/GBGB Packed)
                Size: Stepwise 16x16 - 16384x16384 with step 2/1
        [19]: 'pBCC' (12-bit Bayer BGBG/GRGR Packed)
                Size: Stepwise 16x16 - 16384x16384 with step 2/1
        [20]: 'pGCC' (12-bit Bayer GBGB/RGRG Packed)
                Size: Stepwise 16x16 - 16384x16384 with step 2/1
        [21]: 'pgCC' (12-bit Bayer GRGR/BGBG Packed)
                Size: Stepwise 16x16 - 16384x16384 with step 2/1
        [22]: 'pRCC' (12-bit Bayer RGRG/GBGB Packed)
                Size: Stepwise 16x16 - 16384x16384 with step 2/1
        [23]: 'pBEE' (14-bit Bayer BGBG/GRGR Packed)
                Size: Stepwise 16x16 - 16384x16384 with step 2/1
        [24]: 'pGEE' (14-bit Bayer GBGB/RGRG Packed)
                Size: Stepwise 16x16 - 16384x16384 with step 2/1
        [25]: 'pgEE' (14-bit Bayer GRGR/BGBG Packed)
                Size: Stepwise 16x16 - 16384x16384 with step 2/1
        [26]: 'pREE' (14-bit Bayer RGRG/GBGB Packed)
                Size: Stepwise 16x16 - 16384x16384 with step 2/1
        [27]: 'BYR2' (16-bit Bayer BGBG/GRGR)
                Size: Stepwise 16x16 - 16384x16384 with step 2/1
        [28]: 'GB16' (16-bit Bayer GBGB/RGRG)
                Size: Stepwise 16x16 - 16384x16384 with step 2/1
        [29]: 'GR16' (16-bit Bayer GRGR/BGBG)
                Size: Stepwise 16x16 - 16384x16384 with step 2/1
        [30]: 'RG16' (16-bit Bayer RGRG/GBGB)
                Size: Stepwise 16x16 - 16384x16384 with step 2/1
        [31]: 'PC1R' (PiSP Bayer Comp 1, compressed)
                Size: Stepwise 16x16 - 16384x16384 with step 2/1
        [32]: 'PC1B' (PiSP Bayer Comp 1, compressed)
                Size: Stepwise 16x16 - 16384x16384 with step 2/1
        [33]: 'PC1g' (PiSP Bayer Comp 1, compressed)
                Size: Stepwise 16x16 - 16384x16384 with step 2/1
        [34]: 'PC1G' (PiSP Bayer Comp 1, compressed)
                Size: Stepwise 16x16 - 16384x16384 with step 2/1
        [35]: 'GREY' (8-bit Greyscale)
                Size: Stepwise 16x16 - 16384x16384 with step 2/1
        [36]: 'Y10P' (10-bit Greyscale (MIPI Packed))
                Size: Stepwise 16x16 - 16384x16384 with step 2/1
        [37]: 'Y12P' (12-bit Greyscale (MIPI Packed))
                Size: Stepwise 16x16 - 16384x16384 with step 2/1
        [38]: 'Y14P' (14-bit Greyscale (MIPI Packed))
                Size: Stepwise 16x16 - 16384x16384 with step 2/1
        [39]: 'Y16 ' (16-bit Greyscale)
                Size: Stepwise 16x16 - 16384x16384 with step 2/1
        [40]: 'PC1M' (PiSP Bayer Comp 1, compressed)
                Size: Stepwise 16x16 - 16384x16384 with step 2/1
```

I have no idea what half of this means.

```
Sam@rpi5:~/Development/pcbtests $ v4l2-ctl --device /dev/video0 --all
Driver Info:
        Driver name      : rp1-cfe
        Card type        : rp1-cfe
        Bus info         : platform:1f00110000.csi
        Driver version   : 6.1.73
        Capabilities     : 0xaca00001
                Video Capture
                Metadata Capture
                Metadata Output
                Streaming
                Extended Pix Format
                Device Capabilities
        Device Caps      : 0x24a00001
                Video Capture
                Metadata Capture
                Streaming
                Extended Pix Format
Media Driver Info:
        Driver name      : rp1-cfe
        Model            : rp1-cfe
        Serial           : 
        Bus info         : platform:1f00110000.csi
        Media version    : 6.1.73
        Hardware revision: 0x00114666 (1132134)
        Driver version   : 6.1.73
Interface Info:
        ID               : 0x03000017
        Type             : V4L Video
Entity Info:
        ID               : 0x00000015 (21)
        Name             : rp1-cfe-csi2_ch0
        Function         : V4L2 I/O
        Pad 0x01000016   : 0: Sink, Must Connect
          Link 0x02000037: from remote pad 0x1000006 of entity 'csi2' (Video Interface Bridge): Data
Priority: 2
Video input : 0 (rp1-cfe-csi2_ch0: ok)
Format Video Capture:
        Width/Height      : 640/480
        Pixel Format      : 'pRAA' (10-bit Bayer RGRG/GBGB Packed)
        Field             : None
        Bytes per Line    : 800
        Size Image        : 384000
        Colorspace        : Raw
        Transfer Function : None
        YCbCr/HSV Encoding: ITU-R 601
        Quantization      : Full Range
        Flags             : 
Format Metadata Capture:
        Sample Format   : 'SENS' (Sensor Ancillary Metadata)
        Buffer Size     : 8192
```

Nor any of that.

Heck, none of this is intuitive. Let's try `libcamera-still` for the meantime. It was difficult to get any image to be output to std out, but it turns out that even once the default command is started, it will wait for a keypress on std in or a signal in order to actually fire the shutter. Not so much what I'm after here.

So, here's the trick. We use `--output -` to send output to std out, and we use `--immediate` to get that output immediately.

A minimum working Julia script to read a single frame and display it is as follows.

```julia
w = 4608
h = 2592
channels = 3 # RGB
@time raw = read(`libcamera-still --width $w --height $h --autofocus-on-capture --immediate --encoding=rgb --output -`)
@time mat = reshape(raw, channels, w, h)
@time img = colorview(RGB{N0f8}, mat)
```

Observe that I've set it up to time the execution of the three main operations. The results are as follows:

| step               | purpose                                                 | stats                                                             |
| ------------------ | ------------------------------------------------------- | ----------------------------------------------------------------- |
| `raw = read(`      | invoke libcamera-still                                  | 1.042190 seconds (14.56 k allocations: 41.370 MiB, 1.69% gc time) |
| `mat = reshape(`   | convert result to the 3D array expected by `colorview(` | 0.000009 seconds (3 allocations: 128 bytes)                       |
| `img = colorview(` | turn that 3D array into an Julia image                  | 0.000008 seconds (1 allocation: 80 bytes)                         |

Clearly the Julia matrix operations are not the bottleneck. I suspect these aren't really doing much at all — they'll just be changing how Julia reads out the data or how it is otherwise represented, and not modifying the data itself. The first operation is the slow one, but I do believe that this execution time includes the execution time of libcamera-still itself. That program makes a cold start each time (including, ostensibly, an auto-focus manoeuvre) and is hardly fast at all.

I initially tried this using the generic `RGB` type (the one based on floats, not `RGB{N0f8}`, this latter being based on the fixed-point 0 integer, 8 fractional native to the return type of libcamera-still). This increased performance significantly: performing a floating-point division on all of those pixels when preparing `mat` was taking 0.080457 seconds. Clearly the above code is more performant now, but I'm not sure how independent this is of subsequent code. (If this subsequent code needs to convert to a floating-point representation, then the above saving came at the expense of a following step's performance.)

I've been doing all of this in VS Code, and the Julia extension automatically displays the image output from the `colorview` step. I've been using this for debugging, and I can drag the image out and put it here. Here's the PCB with a red light shone on it as captured by the above code.

![](download.png)

Clearly the autofocus is not doing the advertised job… but that's on libcamera-still, not my code per se. (But I'm probably also inside the minimum focusing distance, which also won't help.) The white balance is similarly out of whack, attempting to fully compensate for the pure red light. I've tried fixing these by modifying the `libcamera-still` args to include things like `--lens-position 0.9` and `--awb tungsten` to no avail. It's not worth my time to diagnose these as I'll be replacing `libcamera-still` soon enough anyway, but this has been good enough to test out the minimum viable Julia code to take a raw stream of RGB bytes from the camera and get a workable image out.^[arrayorder]

[arrayorder]: As suggested by the `reshape` call, the array is formatted with the RGB values of each pixel recorded, then the tuples for the pixels along the row, and then the scanning of subsequent rows. This is what `libcamera-still` provides by default, and what Images.jl seems to be written to expect internally. How convenient!

Also, yes, the image is rotated a lot. I can fix this with `mat = permutedims(reshape(raw, channels, w, h), (1, 3, 2))` (which is then upside down, but there's a `libcamera-still` arg for that I suppose) but I won't: this takes the `@time` stat to 0.133606 seconds (5 allocations: 34.172 MiB). Not ideal: I'd rather physically rotate the camera! (In the event that isn't possible, I could just do all the processing on the rotated image, and then just rotate the results about 90 degrees. I'd prefer this, as the results will probably just be a small and manageable collection of points or vectors.)

— A bit of an afterthought, but I have only just realised that the image is flipped (the 'Algovision' text reads backwards). This is mildly embarassing, but falls in the same category of the rotation error. This is also fixable with a `libcamera-still` argument, but could equally just be ignored and addressed with a final transform on the computed CV results.

— Another addition: a sample command line output. Here's what I get when I run the relevant lines of script in the REPL.

```
julia> w = 4608
       h = 2592
       channels = 3 # RGB
       @time raw = read(`libcamera-still --width $w --height $h --lens-position 1.0 --awb tungsten --immediate --encoding=rgb --output -`)
       @time mat = reshape(raw, channels, w, h)
       @time img = colorview(RGB{N0f8}, mat)
[5:02:34.394801292] [371320]  INFO Camera camera_manager.cpp:284 libcamera v0.2.0+46-075b54d5
[5:02:34.403869617] [371327]  INFO RPI pisp.cpp:662 libpisp version v1.0.4 6e3a53d137f4 14-02-2024 (14:00:12)
[5:02:34.419988760] [371327]  INFO RPI pisp.cpp:1121 Registered camera /base/axi/pcie@120000/rp1/i2c@88000/imx708@1a to CFE device /dev/media0 and ISP device /dev/media1 using PiSP variant BCM2712_C0
Preview window unavailable
Mode selection for 4608:2592:12:P
    SRGGB10_CSI2P,1536x864/0 - Score: 10600
    SRGGB10_CSI2P,2304x1296/0 - Score: 8200
    SRGGB10_CSI2P,4608x2592/0 - Score: 1000
Stream configuration adjusted
[5:02:34.424203329] [371320]  INFO Camera camera.cpp:1183 configuring streams: (0) 4608x2592-BGR888 (1) 4608x2592-BGGR16_PISP_COMP1
[5:02:34.424301292] [371327]  INFO RPI pisp.cpp:1405 Sensor: /base/axi/pcie@120000/rp1/i2c@88000/imx708@1a - Selected sensor format: 4608x2592-SBGGR10_1X10 - Selected CFE format: 4608x2592-PC1B
Still capture image received
  1.050392 seconds (14.27 k allocations: 41.367 MiB)
  0.000009 seconds (3 allocations: 128 bytes)
  0.000008 seconds (1 allocation: 80 bytes)
```

# Next steps

I think I can somewhat park this particular part of the process (improving capture speed) until after the camera is on the gantry. This step works quite well, and I've already seen examples of much higher Julia throughput working fine. No, there are two higher priorities that this simple and slow method opens the door to:

1. Profiling the gantry performance. We can now use a Julia script to take images and do with them whatever we please. This is useful, because that same Julia script can control the gantry movements using James's code. (We can therefore do some optical repeatability testing if we wish.)