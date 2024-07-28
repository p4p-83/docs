Redesigning the head in CAD. I did make an earlier prototype just to get an idea of the scale/rigidity of things (seems okay) and to aid me to work out how I'll mount the various metal parts (interference/press fits will do just fine for now, and the 3D printer tends to get a decent press fit if you just set the hole size to the exact dimension measured off of the mating part).

Now, I'd like to make up all the parts (including attaching the lighting/limiting cone) and ideally fix a few collisions. I'm drawing up a new file for this.

I'm using one single sketch as a template for the plastic adaptors at all three levels (foot/stepper bracket, pivot block, and drive block). They're all pretty much the same anyway, so this speeds the CAD up.

![](Screenshot%202024-07-28%20at%2014.08.18.png)

Doing so does make for a slightly scarily dense drawing though. From this (and a couple of other auxiliary sketches) I produce the three adaptor blocks.

![](Screenshot%202024-07-28%20at%2014.08.56.png)

It can be noted that I've adjusted the design to now expect two linear rails and two linear bearings. The previous iteration only used one proper linear component, and was just going to use some sort of plastic-on-metal grip to constrain rotation. Turns out that there is a little slop in the linear bearing though (angular play, i.e. the rod is not constrained perfectly to the intended axis of travel), so I'm adding a second one with a bit of an offset in the hopes of removing that slop. It'll increase the weight but I've kind of given up on that at this point anyway. At least the BOM will have fewer lines.

It can also be noted that I've had to cap the driving block (the top one) to put the belt clip on axis with the major rail. (Whether or not this is strictly necessary is a different story.) At any rate, this does mean that the major rail is going to have to be trimmed to size. I'm not so impressed by this, but I'll go along with it for now.

The overall action is defined by the auxiliary sketch on the pivot block (yes, terrible CAD practice, but whatever).

![](Screenshot%202024-07-28%20at%2014.20.44.png)

The belt lines follow the *centre* of the belt; they don't trace the inside as you might initially expect. (The pulley circles have all been bumped up in size by the appropriate amount to achieve this.) The folding arc is linearly interpolated and comes out sufficiently close to true. Perhaps if I really wanted, I could switch the centre interpolation pulley to something spring loaded to add a bit of tensioning (its exact position isn't overly critical).

The camera module will be located on the downwards projection. The 14.40 mm dimension refers to the bottom-edge-to-optical-axis dimension of the CM3. Dimensions for the CM3 are based on a technical drawing published by Raspberry Pi [here](https://datasheets.raspberrypi.com/camera/camera-module-3-standard-mechanical-drawing.pdf).

The z axis stepper motor is drawn as a circle. It's still a square (I haven't somehow squished it or sanded it down) but I only really care about modelling the clearance between it and the pivot axis (I've allowed 16 mm, enough to get a 10 mm spanner in there for M6 pivot hardware). I intend to mount it at an oddball angle to maximise this clearance.

The parts don't look particularly polished, but they're probably only temporary. I just want to get an iteration out as early as possible to see how well they'll work.

---

Currently working on adding in the nozzle cone clips/contacts. The section views look kind of cool.

![](Screenshot%202024-07-28%20at%2014.39.54.png)

The indent for the camera cable can be seen pretty clearly.

And an overview of the whole thing:

![](Screenshot%202024-07-28%20at%2015.03.38.png)

---

And a preview in the slicer:

![](Screenshot%202024-07-28%20at%2017.10.55.png)

I'm printing it Creality Hyper PLA in white. Looks good on the bed, aside from the fact that the cone is ridiculously large. Clearly I have a bit of free space on my hands to come up with a better limit switch contacts technique.

**Limit switch contacts and quick release.** I've been thinking about this a little bit. Really, we need a quick release system on the cone, so that the nozzle can be changed. Because the cone is so big, I could afford to have external contacts. I could move the contacts back and forth so that they get out of the way and allow the cone to be removed axially. If I put some dowel pins in or similar, I could constrain the cone axially so it couldn't slide off the contacts, and then I could point all four contacts in the same direction (thus they can all be attached to the same sliding plate and all be released at once). This sliding plate could be a PCB, in the case where I am using gold contacts.

I can also stop the taper on the cone and just go up vertically at some point along. I don't need a cone the whole way, provided there is some overhang. That way I can slot the cone into a fixed, white seamless background and have that do the rest of the work for large fields of view.

**Really, really left-field limit switch idea.** With two mirrors, I could redirect the view of the component footprint camera to instead point at the board, if I wished to use CV to detect when the limit is reached (when the nozzle begins to be compressed). Probably this isn't needed anymore — that's what the cone does, in an electrically and computationally much simpler manner — but I guess this is how I could have achieved the CV approach if the cone idea hadn't come to me first / ends up falling flat.