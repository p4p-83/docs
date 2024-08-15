I've about had it with designing and redesigning the head mechanism, but here we are again. I've did a lot of conceptual work to get to this stage, and I've prototyped two designs to see how they perform. Long story short, I'm not convinced. They're both so close, yet so far.

*First, just a note as to why I'm subjecting myself to this torment and still trying to make a head with compound action. We want a live feed of the underside of the component, and a live feed of the top of the board. This is so fundamental to our approach to the CV that I still feel the best option is to just make the mechanical part of it work, despite the grief it's giving me.*

The big, single-motor design is just that. Big, and uses only one motor. The 'one motor' bit was cool on paper, but really it's just been an absolute pain the whole way through. I'm not a mechanical engineer, so I don't really know what I'm doing, and I'm not a machinist or someone with enough time on my hands to properly design it for a machine shop, so it's giving me a lot of grief. Yes, it's not far off, if I can make it a bit smaller and think up some way to securely anchor it. It's not far off, but at this point I'd rather use another motor and just fix it in software. I'm an electronics guy.

Yesterday I designed and prototyped a two-motor concept that's basically an articulating arm, but with 5 mm dowel pins used as linear rails to ensure it always points in the right direction. I designed it very wrong — it's wobbly — but even though that's fixable, I'm concerned that the miniature steppers just won't have enough torque. They're also pretty low-speed. It was better in my head.

Neither design is completely unworkable, but I'd like to try making another concept first before trying to optimise either of the first two. What if I just put speed and motor size and motor count and all other mechanical things on the back burner, and design primarily for ease of manufacture (i.e. ease of 3D printing) and repeatability right from the outset?

**The new approach**

It'll be like the old Geneva wheel / Scotch yoke design, except:

1. There'll be no Geneva wheel. I'll just use a belt drive from a first motor.
2. There'll be no Scotch yoke. I'll just use a belt drive from a second motor.

Replace the Geneva table with a giant pulley. I'll 3D print it. A loop of timing belt goes around the outside and then links it to the first stepper, placed eccentrically. I can put some spring-loaded tensioners in between. I can also put some dowel pins or screws or something of that nature in place to create hard end stops for the motion of the wheel (and rely on the tensioners to take up the slack / give up slack to allow the stepper that drives it to basically load up a bunch of torque near the end stops. I could use the stepper itself for positioning, but I don't have to. I have options with this design, and that's nice.)

The pulley acts as a platter. I'll mount a linearly-acting nozzle motor bracket to this. I'll mount it eccentrically, because trying to put it on centre is unnecessary and guaranteed to be a pain in the butt. Also, if the centre of mass is always eccentric (preferably always on the same side, too) it will create a torque that preloads the thing and takes out any backlash.

I'll drive the linear stage with a belt. There are a few options here but the idea is that there will be a belt, probably running centrally. I'll use two idlers (probably adjustable, so that slack can be removed) to redirect the belt sideways, off to the centre of the pulley/rotary table thing. Unless I want to mount the second stepper to the plate itself (I don't; too heavy), the only possible place (i.e. the only place that's not going to cause this to take 3 months to design and make) is to put that second stepper with its shaft concentric with the axis of rotation of the rotary table.

Because I can't be bothered with anything else, I'll use the shaft of that second stepper as the "fixed" reference for the bearings of the rotary table. I'll make a little stack. This is probably the key insight that makes this design feasible. I'll put two bearings (for the platter) either side of a short pulley that engages with the linear motion belt. The stepper will be about as rigid as any dinky plastic thing even despite the fact that the whole assembly is going to be cantilevered. (Although the fact that it'll be cantilevered is actually an advantage, provided rigidity is not impaired.)

Basically, it's the design that I wanted from the start, except I solve the myriad problems with it by adding a second stepper (that lets me do the counterrotating-with-a-different-gear-ratio-and-timing bit with software, not hardware). I've been trying to use a "just buy the precision" mindset in designing these things, but I forgot that the stepper factored into that. In this case, I'll be buying some rigidity and the ability to get my compound action with much less mechanical complexity.

While the first iteration of this probably still won't be quite what I need, I'm hoping that it will at least have better repeatability than either of the two prototypes I currently have. It'll be more modular and more workable and hopefully will let me converge on a final design much more quickly.

---

While it's not super critical to the core of this concept, I do want to note down the lessons I've learned from making linear slides.

- The rigid coupling to the rails should really be next to the stepper. This means that the rails move with the stepper and never get in the way. Not doing this is possible but would require a very long, ascending motor bracket.
- Even the precision bearings have a little bit of play in them that does cause issues. There really needs to be at least a second bearing to constrain this placed with as much separation as possible (this applies to rotary bearings too). I think the best metric for the degree of separation is to consider how far apart the bearings are, relative to the spacing between the first bearing and the nozzle tip — it's this lever ratio that will define how much any play is amplified.
- Plastic-on-metal parts can be made, and they're passable. Not great (PLA isn't known for any low-friction properties), but it's also not unworkable if designed right and reamed to size with a few test fits along the way.

For all designs going forward, I'm thinking that the stepper should hard mount to the rails, but that the rails should have the maximum depth of embedment / spacing between the bearings when the head is in the fully extended position. Exactly how I do that, I am less sure, but it does at least give me some sort of idea of what I should be optimising for whenever possible.