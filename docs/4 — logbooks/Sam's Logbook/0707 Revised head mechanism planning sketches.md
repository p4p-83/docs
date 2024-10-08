I'm going to draw the head mechanism up in CAD and 3D print most of the inner parts. These planning sketches should help with that and let me decide on a direction earlier.

![[IMG_1411.jpeg]]

I don't see any reason to deviate significantly from the previous plan (back when we were going to use an RC car gearbox), and so that means keeping the general idea of cantilevering the guards at the bottom and putting the railblock(s) at the *z*-axis fold pivot. I've also quickly decided that I'll try for the most lightweight option possible, so I'll continue with the single-guide idea. (Two struts looks goofy anyway.)

![[IMG_1412.jpeg]]

Here's a slightly more refined version of the plan. This is sort of a minimum viable design, so I'll draught this first and then go from there I think.

(It's not actually fully complete — the guide rail is still free to rotate about its axis, which is undesirable — but I'd prefer to keep things simple on paper for now. I already know in my head how I'm going to add that constraint (a simple guide rail, same as the last design), I just don't know exactly where I'll put it yet, and I'd prefer to fiddle around with that in CAD after I get the more core aspects right.)

---

**A few notes on design decisions**

*Why am I not just using two rails to create the rotational constraint?* Firstly, it would add a few too many constraints and maybe encourage the thing to bind up. Secondly, I'd rather not add a second steel rail, as the thing is already way too heavy. I'll probably use an aluminium extrusion of some sort (probably just some tube of adequate stiffness) as the rail (fixed to and rising upwards from the pivot adaptor) and a simple plastic tuning-fork-shaped clip that hangs off the top of the primary linear guide rail.

*Why does it look like this at all?* A semester of (haphazardly documented) iteration. Mostly rough, very rough, sketches on loose paper and musing while in transit etc. This is the simplest, most direct idea I could come up with. Yes, it still looks weird to me, but I've also studied the the needle mechanism of a torn down sewing machine and this isn't a far cry from it, so I'm not too far off the beaten path. (Yes, the more conventional option would have been to use that rectangular profile guide rail, but I didn't have access to any and it also didn't solve the folding issue much.)

*Why is the z travel folded like that?* It's the best option, given the design constraints I'm imposing on the vision setup. The camera has to be ~120 mm from the PCB. I want a camera pointing at the bottom of the component (when it's lifted) and a second view of the PCB itself (at least while the component is lifted). I also want both cameras to be focused at the same distance (lens distortion and FOV etc is usually a function of focus distance. Distortion is easier to deal with when it's identical for both cameras, hence why I want to keep it the same between both cameras. Also, if the objects are at the same distance the perspective distortion and scale in general should be the same. Basically, I just want a matched system). Consequently if the PCB is 120 mm from the footprint camera, I need to get the component leads that same 120 mm from the component camera. I would like to keep both cameras next to each other on the gantry for several reasons (speed of operation and wiring considerations being the most prominent). Doing the maths, 120 mm from PCB to gantry and 120 mm more from gantry to component footprint means that I would have to lift the component 240 mm vertically. To me, this is stupid. I only need 50 mm clearance, so tops 100 mm lift — 240 mm is therefore unreasonable. With the "just lift it high enough" approach I'd end up with a gantry that looks like an oil rig. All things considered, folding the optical path (by folding the *z* axis motion) is actually the simpler option mechanically.

*Have any other mechanisms been considered for the z axis folding?* Might as well document this at least superficially while I'm here. The answer is yes. At first I was approaching the problem very much differently, thinking I could use a Hoeken linkage to give the head linear travel with a degree of sideways retraction on the second half of the stroke. However, the retraction wasn't going to help me to get a camera underneath for the component lead photo, and it the linkage was also a bit awkward to fit in given its proportions. (Also, it a single Hoeken linkage was not going to be enough to keep the nozzle vertical. It might move vertically, but I had a second thing coming if I wanted it to also be pointed on axis.) I then ditched that idea and tried using a Scotch yoke to do the up/down when paired with a Geneva mechanism to fold the thing. However, that was not going to give me the correct sequence of motion unless I added multiple motors or an idler gear to invert the motion of one of them (the Geneva mechanism has two rotating parts, and just as gears do, they rotate in opposite directions). I didn't like that idea at the time, hence why I'm trying a belt, to see if that will do the job well enough (if it does, it makes things easy!).

Right, enough walls of text. I'll leave it as an exercise to the reader to split the above into proper paragraphs. I'm going to draw this up in CAD.

---

Another addendum: here's where it's at presently. This should make the location of the secondary rail slightly clearer. Sorry about the not so wonderful colour scheme.

![[Screen Shot 2024-07-07 at 19.23.51.png]]The teal rod will be 8 mm Ø steel linear rail (nominally precision ground, but who knows what the supplier will really do) and the darker blue one is probably going to be 10 mm OD aluminium tube (the size depends on what the hardware store carries — I have yet to double check. And yes, I forgot to model the hollow cross section, but that will make zero difference for the 3D printing). The other two funky parts are to be 3D printed, probably out of PLA, or whatever I have on hand for prototyping.

Might try a light interference fit to get the linear bearing and rail to stay in the purple part. That and/or I can try to pin the rail and use a set screw to lock into a groove on the bearing.