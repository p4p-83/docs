This has been a long time coming, but I think I've finally got a prototype that is at least ready to proceed to some form of repeatability testing.

![](Image%203.jpeg)

I just need to print a bracket for it and then I can go in this afternoon/evening and try fitting it to the machine. Wouldn't that be a milestone…

---

I need to tidy up a few goofs with this design.

- If I'm still planning on using open timing belt within the thing, I really should offset the top pulley to the right a bit so that the awkward, variable bend is preserved. This should help me to make sure that the total belt length remains constant independent of the slider position.
- ~~Ideally I just switch to a closed belt here.~~ This would make installing the slider a lot easier, but installing the belt itself would become overly difficult, as I'd have to fit the belt first. Fitting the stepper shaft pulley may become arduous, and I'd probably have to fight against any tensioner quite significantly. I also have less flexibility with belt sizes.
- Either way, it'd be nice to add a belt tensioner here. What I'm thinking is that I use the existing pin configuration, but put a slot in the main part instead of the current locating hole. I then add a separate, U-shaped (at minimum) cap that has the required locating holes. I then add a compression spring in between the end of the existing main part and the middle section of the U in order to preload the belt. I think this would be adequate.
- One of the screws that holds down the bushing retainer plate sits in the path of the nozzle rotation/roll stepper. This needs to sit a bit lower.
- The bushing retainer plate doesn't print nicely (on account of the overly large 3D-printer-bed-side fillet).
- Some cable management would be nice (and maybe some clearances so I can better orient the various things with their protruding wires).
- Collision between the belt clip and the platter.

---

![](Screenshot%202024-08-29%20at%2018.47.36.png)

(First time I've leaned on joints to help me put together an assembly — it's worked well, but look at how many damn meaningless numbers are overlayed onscreen!)

I drafted a crude bracket so I can bring the thing in and put it on the frame. It's still on the printer now, but I've already noticed a few things.

- The Raspberry Pi mounting style requires me to tap some holes, but I won't actually be able to get the tap into the corners where two of the holes are.
- Similarly, the Raspberry Pi position interferes with the positions of two of the screws for the rail blocks. I could leave them not fitted for now but the combination of this and the above concern gives me the impression that I need to reconsider the placement of the Pi or the shape of the bracket.
- The camera is pictured in CAD in its ideal position for the current setup. It's way off in no-man's land and it makes the whole assembly ~350 mm long. That's ridiculous, and so I haven't made a bracket for it just yet. Should I perhaps fold the optical axis? Should I change the camera-nozzle offset angle from 90° to something else?
- I've not added a belt tensioner for the stepper, only some slots to allow it to in theory be installed with the belt taut. Does it need a tensioner? (If I do install a tensioner, it must be on the bottom part of the belt, as the top part is load bearing and thus we would end up with an error as the torque on the pulley fights it out with the tensioner spring.)
- Also the footprint of the whole thing is also rather large. Could I perhaps put the second, belt driving stepper above the first stepper? Not my preferred aspect ratio but it would recover a little bit of travel, and it seems like I'm going to have to make more use of the vertical dimension anyway just to bring the B camera back in a bit.
	- This one is mostly inspired by the fact that the nozzle protrudes by a certain amount and that the arc traced by the nozzle is outside the present footprint of the design. Ideally I would move the supporting rail blocks so that the combined footprint is as small as possible, and this means biasing the design in this direction, but the B stepper is currently preventing me from reducing it at the back. Moving the stepper up would allow me to change the stance of the design and have a smaller footprint.

Also I did wonder about using some sort of lens and mirror setup to split the view of the B camera in order to get a 90° shot of the nozzle so that we can align things more easily. Not entirely sure how this would go, but it might be worth just trying for fun if I can find a straightforward place for the mirror.

---

Been to uni and installed it on the machine. Some more issues observed.

- I entirely forgot the clips needed to attach it to the *y* axis belt!
- The linear rail bearing blocks clearly don't match the hole drill pattern that I found online, and so I can't get all the screws in.
- While I'm at it, I should really put slots in to mount to the secondary linear rail bearing blocks — the current design preloads the rail more than it should.
- I forgot to make the B stepper shaft hole into a slot.
- In its current position, the Raspberry Pi's ethernet and USB ports mightn't be accessible if a deeper stepper motor is installed.
- I don't really have any M4 screws of the appropriate length to fix to the rail blocks (given the 5 mm (?) base of the bracket).
- I used woodscrews to secure the Pi to the bracket (given the tapping problem).

There might have been more, but I didn't write them down at the time did I, and so I very much can't remember them now…

I also clean forgot that I'm supposed to be making something up for the **lighting / illumination of components**, and also making a **nozzle cone**. Oops. Better get to that and factor these in before I do the next round of prints.