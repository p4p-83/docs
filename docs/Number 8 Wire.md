A notepad of ideas that could inspire simple designs using readily available materials for otherwise tricky pick & place parts.

## Two axis vacuum coupling

The nozzle needs to go up and down, and rotate, yet we still need to maintain vacuum. Consider creating a coil (like an extension spring) out of [automotive vacuum hose](https://www.repco.co.nz/search/?text=vacuum+tubing). This stuff is pretty flexible, and more so with a few turns of slack. One end can be fixed (or maybe run along the gantry and out the end of the machine) and the nozzle itself can be tied to the other end. With some way to grip it, you can spin the nozzle around maybe a turn, the slack provided by the constricting or expanding coil. The nozzle can lift and fall and the coil takes up that slack by changing in pitch.

So, a length of automotive vacuum hose with a nozzle [hose clamp](https://www.bunnings.co.nz/search/products?page=1&q=hose+clamp&sort=BoostOrder)ed on the end. How do you drive the degrees of freedom?

I say gearwheels. What of a 3D-printed part that looks like a worm screw in the axial direction and a normal (albeit long) spur gear in the radial direction. Essentially a knobbly sleeve for the hose just above the nozzle. Then you have two servos to drive these two degrees of freedom, each with an overgrown gearwheel attached to the shaft. Sure, it maybe slightly imprecise, but it could work.

Otherwise you go the usual route with a plain bearing and a metal sleeve around the tube, then use a linkage to move the tube axially. Then you do the usual scratching of the head to work out how to add the other movement in compound without an extraordinary number of additional parts.