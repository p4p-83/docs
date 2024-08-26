… and hopefully for the last time!

![](Screenshot%202024-08-26%20at%2020.50.21.png)

This is going slightly better than expected. My CAD approach has been as follows:

- Draw the nozzle assembly in a seperate file.
- Draw the main stepper in a separate file.
- Start in the main file with the first platter bearing. This is grounded at the origin and therefore provides the reference for the rest of the work.
- Place the stepper that doubles as the shaft. I'm going to put a spring on the shaft to take out any slop, so I offset the joint accordingly. (I've done all of the alignment between components using joints this time around.)
- Duplicate the bearing and fit it to the opposite end of the shaft.
- Fill in a spacer and the small, short pulley in the remaining space on the shaft. (The best part is that because I'm working from a reasonably accurate model of the stepper motor, I can actually see if these are going to fit and how well, just by looking at the CAD. Don't know why it took me this long to work out that 5 minutes drawing up can make life so much easier.)
- Draw the pulley platter and the parts that connect around the bearing.
- Piggy back off the main platter sketch to rough out the position of the nozzle carriage thing.
- Build up the nozzle carriage and then place in the nozzle subassembly.
- Add joints to a reference "floor" (positioned at the approximate offset of the PCB relative to the head) and then use an as-built joint and a planar joint to move the nozzle carriage down to its fully extended, touching-the-floor position.
The great part about this approach is that I can now see where everything currently falls, evaluate the clearances and whatnot and just generally flesh out future parts, and then go back through and add dimensions and constraints to the sketches to improve the positions of things. I might have shot my self in the foot somewhere with the precedence of the modelling and might thoroughly regret this approach in the future, but so far this is going great.

I'm actually pretty excited with how this is turning out. The most visible thing is that the amount by which the head extends is big enough to give me some breathing room around the platter, but is not so big as to be cantilevered to a concerning degree. The best part is that it looks like I'm going to be able to put the belt drive out of the top of the platter, and extend a clip off the top of the linear rods to meet it. This will make the design *so much cleaner* and it'll be waaaay easier to get the camera mount in, as there won't be a belt underneath it as in my original plan. It might even save me having to split the thing into two parts. (This is not something that I could see in advance with my original modelling approach, so maybe I am doing something right here.)

I'm going to call it here for the day, but hopefully things continue reasonably smoothly tomorrow. Roughly, I'll:

- Start fleshing out the positions of the linear bushings on the platter.
- Make those parts.
- Undo the as-built joint on the nozzle carriage and replace it with the proper joints through the bushings.
- Model the belt path and belt clip.
- Parameterise the camera position / offset and then model a camera bracket in the right place.
- Maybe adjust some of the magic numbers that I've added in (e.g. the 120 mm offset of the PCB from the stepper's rotation axis etc.) and try to come to a more refined looking design with a bit more tolerance for slight errors.
- Work out how I'm going to hold the whole thing (probably from the back of the stepper motor) and maybe CAD something up to do this. This will have to account for the secondary camera that will look at the footprint.
- Add a nozzle cone and limit switch. Hopefully this won't be too difficult though, given that there's a subassembly already there that I can pretty much edit in place to do this.
- Add provision for lighting. (Yeah, that elephant in the room…)
I imagine that these last few steps are going to end up making a mounting bracket that 100% should be made either from sheet metal or perhaps plywood (given the probable thinness and overall size it'll be) but that I'll probably have to 3D print regardless, at least for now. (But who knows, maybe I'll have more ideas when I start to see it emerging onscreen.)
---

For reference, this platter design originated from the following sketch (lower half of page) in my "logbook". (One of these days I will have to collect up all of my physical sketches done for this project and record them digitally…)

![](Image%202.jpeg)