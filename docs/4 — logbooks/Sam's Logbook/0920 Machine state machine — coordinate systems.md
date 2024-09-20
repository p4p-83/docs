I've decided we really need to run something akin to a 'digital twin' in realtime to circumvent several problems incurred by the open loop nature of several components, and also to maintain tracking of the reference lead of the component and so forth. I have many of the algorithms for CV and the like working in isolation, but bringing them together is going to require standardising on a particular coordinate system convention. In particular, I need to set reference datums.

In developing the algorithms for the rotation and wicking, I noticed three things relevant here.

- Firstly, both require a correctional translation to account for the fact that we are not able to spin the component about the desired rotational axis — we can only rotate about the nozzle axis, wherever that already happens to be. Presently I have duplicate code to perform these calculations, so I should also probably refactor this so the code is in one central place.
- Secondly, in both of these calculations I make the assumption that the true axis of rotation is at the origin of the image coordinates. I should probably check that this is a sensible assumption to make (or does it impose stupid requirements on the code beforehand?)
- Thirdly, I have largely treated nozzle rotations separately to pad translations. While the machine is technical a rotation stage stacked on a z stage stacked on a y stage stacked on an x stage, that's not how it looks to the user and to the vision code. We see things through the cameras, and thus the nozzle rotation is fixed in position from our frame of reference. Similarly, from our frame of reference, we are stationary and the bed is moving. This subtle shift in perspective means that it makes a lot of sense to reference things to the nozzle rather than some fixed point on the machine bed.

From this, I therefore conclude that the best primary datum axis the nozzle's axis of rotation. Technically we need a point, so we can say that the datum point is just where the nozzle's axis of rotation intersects a camera's focal plane. As we have two cameras, we could have two datums, but we can just define them as being the same and calibrate the cameras accordingly.

We do still need a second x-y "home" position, but we can see this more as a point that moves relative to our datum. We can therefore imagine a rectangle bounding all valid datum positions, located at one corner by the home point, and this rectangle moves relative to our datum and we must not let the datum leave the rectangle.

This is all a bit of a backwards way of describing it, but it's mostly to get the point across that we need to use a different frame of reference compared to most other machines. Intuitively you would strap the PCB to the bed, and then use the bed (and thus PCB) as your frame of reference without a second thought. We shouldn't.

## Implications

We have now effectively isolate rotation from translation. (Yes, we will still need to coordinate the two, particularly in the above case where we wish to rotate about some virtual axis, but the idea is that we can keep track of them separately.) This means that we can write routines for them separately.

### The cameras

We get vision data from the cameras, supplied as x,y pixel coordinates. We will assume that this camera data exhibits some degree of radially-symmetric distortion (i.e. barrel-, pincushion-, or moustache distortion) that is centred about some centre of distortion. This centre of distortion is located with respect to the 0,0 pixel coordinate. We are thus still in the pixel coordinate space.

After correcting the distortion, we now have some coordinate space ambiguity — we have multiple possible references; which do we choose? We could try stay relative to 0,0 (but what does that even physically correspond to?), we could try and put things relative to the centre of frame (but then we need a definition for this?) or we could leave coordinates relative to the centre of distortion. I'm going to say we should opt for this latter option, and in the process essentially define the centre of distortion as the centre of frame for all practical intents and purposes. The centre of distortion is nice in that it will be the one point guaranteed to stay in the same place when correcting or un-correcting for distortion, so hopefully this simplifies some of the calibration math a little.

We therefore have image coordinates relative to this so-called centre of distortion, which we assume will be roughly centred in the frame. Our single missing link therefore becomes the displacement between the centre of distortion and the nozzle's rotation axis.

The one mild complication/gotcha here is that we can measure the distance between the centre of distortion and the nozzle's axis of rotation *optically*, but that's not quite what we want because we have corrected distortion and now working in *true units* — whatever the hell those are.

I guess defining these 'true' units becomes a really important decision.
- For the translation, I would prefer to use steps as the true unit. However, steps are not really so obvious here. Also, steps may not be isotropic.
- I could use pixels here, but how exactly do you define the size of a pixel? The underlying concept would be that you are know the centre of distortion stays fixed, but you also have to chose a second reference radius at which pixels are again "in alignment" and have the same value both before and after the correction. (I guess if you had to use a scheme like this, you could actually use the nozzle axis of rotation as this value — which I guess isn't my worst idea ever.)
- I could use polar/spherical coordinates, because the angle is tied to the field of vision of the camera and is not related to the camera's distance from the board, so is pretty isolated from all of the other factors as far as these things go. However, it's so isolated that it would be pretty computationally intensive — we work with flat PCBs and so calculations need to happen on flat planes! (We also assume that the image plane is flat after distortion correction, whereas this would cause the distortion correction to do the complete opposite!)
- We could use millimetres, but I'd want a pretty good reason to introduce another unit if it wasn't going to be used elsewhere. Using millimetres also relies on us knowing (and fixing) the image plane's distance from the camera in our calibrations, which may or may not be acceptable.

I guess all three of these could technically work; I just have to pick one and see how well it works for the rest of the setup.

I'm going to try picking millimetres.
- The machine is designed to pick and place components on a board at a given height. Moving the board closer or further away will require recalibration (or at the very least, re-measurement) no matter what. Additionally, the distortion may actually be a function of the image plane distance (because the lens elements physically move relative to one another in order to set a given focus) and therefore the calibration itself is kind of inherently tied to the distance at which it was made.
- We'll probably use a printed test pattern to do the calibration, which we will place at the image plane and which we will have measured in millimetres. This saves us calculations.
- GRBL expects values in millimetres and does a conversion to steps internally. This applies more to translation than to rotation, and we might be able to bypass it by setting the internal calibration constant to 1 or some arbitrary factor, but the point is that millimetres shows up here too.

My biggest gripe with using something with the focus distance baked in is that it's difficult to exactly measure the distance between the camera reference plane and the PCB in order to actually know at which distance the calibration was made, which is information we'd need if we were to try to scale the calibration in place, but I'm not sure that this problem is unique to this unit of measurement, nor particularly exacerbated by it.
- My interpretation just at the moment is that there isn't really a compelling alternative, so it's probably easier to fix this more minor limitation than it is to use some other metric or deliberate on this further.
- I will need to recalibrate whenever there are significant changes to this distance anyway, for example if I move the bed. The concern is more for small deviations, such as using a slightly thicker or thinner PCB, but I guess that this is something that can be largely mitigated by good board holder designs and any residual error can be taken out by an approximate or empirical multiplicative factor if it's problematic (we will have an approximate distance to the board, so for small misalignments this should be more than accurate enough.)
- Harder to deal with bed deviations from flatness or gantry sag, but this isn't something I intend to do at the moment and might require a little bit more work than just choice of limits.

All in all, millimetres seems to be something that might work all around, as much as I've been telling James we'll just use steps as there's no need to introduce another unit. Oops.

Let's take a step back and see how this will work from a calibration perspective.

1. Print a test pattern with a grid. You need to know the grid pitch in millimetres.
2. You point the camera at the grid and take a photo.
3. You read off the pixel coordinates of each corner of the grid. You then make a list of all of the grid corner coordinates and a list of all of the measured pixel coordinates.
4. You find the centre of distortion as the point about which these deviations are symmetric.
5. You convert to polar coordinates (radius, angle) about the centre of distortion and then use your recentred grid and pixel coordinates to program the linear interpolation function (which has an exact inverse when you simply switch the order of reference radius vectors — you'll probably need this property as most conversions go the other way around, from pixels to millimetres).
6. You measure the location of the nozzle centre of rotation (at the right distance) in pixels and use the linear interpolation function to convert that to millimetres. Probably you do the pixel measurement by finding the pixel coordinate of the nozzle in a still image, then subtracting the known pixel coordinate of the centre of distortion as found earlier.
7. You know your optical to true coordinates function, and you know the offset of the nozzle's centre of rotation in true coordinates.

Okay, so clearly this method is practically achievable, at least for the rotation axis. How about the translation axis?

Well, we can do a translation calculation by pointing the camera at the grid and then doing trial and error and some math to align the movements to be exactly in grid step when the controller thinks they should be. James already has this working from the interface, although for the gantry controller this conversion factor must be done on the Julia side of things. No matter, we can treat this as the millimetres-to-steps conversion factor (or factors — they may be slightly different between the axes) and proceed with what we have already got there.

The only other calibration is that we need to work out where the nozzle will land on the PCB — that's the datum for the downwards-facing camera. We'll know the centre of distortion and we'll need to measure that with reference to the centre of distortion of the downwards facing camera. The most basic way of achieving this would be to draw a dot or perhaps crosshair/circle with centre dot on a piece of paper, lower the nozzle onto it, centre the reference mark under the nozzle's rotational axis, and then switch back to the downwards camera to get the pixel value, doing the equivalent of step (6) above.

Unfortunately the nozzle is not perfectly concentric, but … not much I can think to do about that just at the moment. I don't think it'll make too much difference just at the moment, particularly if we don't rotate the nozzle between calibrating the two cameras (that will hopefully cause the concentricity error to cancel itself as far as the live feeds are concerned, although the math will not be saved by that trick).

At this point, the whole system works pretty sensibly using millimetres. The calibration routines aren't as straightforwards as I hoped, but they're also not awful.

The optical calibrations can be done first. The differences between the centres of distortion and nozzle rotational axis do depend on this calibration, but not to an unreasonable degree. Scale issues are not affected by the calibration directly and only rely on the cameras being at the same distances from their respective targets. The translation calibration is completely independent to the optical calibration as you are always trying to align the same pixel with any given grid corner.

### Procedure for live data

We've already identified that we will take all live data and then subtract the optical (pixel) coordinate of the centre of distortion from it. We then apply the optical-to-true (pixel→mm) mapping function to remove distortion. We then subtract the true (mm) coordinate of the nozzle's centre of rotation, meaning that all of our points are now centred about the point that we earlier decided was the datum.

We do this for both the pad centroid data and lead centroid data, and we can then run all the maths assuming zero distortion and zero misalignment.

Was that earlier than I was expecting to do the correction? Yes. Is this a problem worth worrying about on the grounds that there may be a more efficient way? Probably not. This is comparatively simple in that it reduces the thought required for future steps.

---

This is all well and good for getting the centroids into a palatable form. However, if the datums of the two cameras are different (when mapped into pixel coordinates) then the placement preview as shown onscreen will be misleading.

There's no perfect way to correct this (as I refuse to do distortion correction for both live video feeds) but provided the distortion is pretty similar between the cameras, not overly extreme, and provided the misalignment is small enough, I don't see any reason why I can't just bump one of the video feeds over a little bit in the compositing loop.

- Let's say we use the downwards (pads, PCB) feed as the fixed feed. We calculate the datum coordinate in pixels, and know that we have to translate the upwards (leads, component) feed to align this.
- We therefore also calculate the datum coordinate in pixels as seen by the upward feed and measure the difference.
- We then program the composing loop to correct for this difference in datums by this amount. (We also write some bounds checking code. Probably we just repeat edge pixels of the upward image when we don't actually have them, and clip off the opposite edge pixels where they don't fit on top of the fixed downward image. That, or we just crop both images by a suitable degree, but I see that being a bit too messy.)
- We interpret click events from the UI as being clicks on the PCB, and use the same optical→true (pixels→mm) conversion routine to move the machine.

This calibration is pretty system-level, in the sense that it also accounts for misalignments of the head itself.

## "Summary"

Ah, yeah, so basically we just map everything into millimetres as far as the math is concerned. We have to approach the live view and the math slightly differently, but basically everything treats the datum as being the nozzle centre and flows from there.

I think I'll start by templating the calibration routines (these are still needed for the placement preview feed) and then get the placement preview feed offsets "working" — not that I can test them before getting those cameras installed.

## Alternatives

It's worth mentioning that there is an obvious alternative of just getting the calibration right mechanically and saving a job in software. You'd probably just have to spend a while with a screwdriver fine-tuning the cameras on their mounts.

That isn't my current plan as I'd prefer to just get it as close as possible with the 3D printer, and hope that that's sufficiently close to just fix the slight misalignment in software. I don't know. Neither is going to be fun to calibrate but I think that designing properly adjustable mounts may be a step too far for me, leaving a software fix the only reasonable option (at least this close to the deadline).
