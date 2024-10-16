### Supervisor Meeting

29 Feb 1101–1236

#### Attendees

- Dr Nitish Patel (N)
- James Bao (J)
- Sam Skinner (S)

#### Absentees

- Nil

#### Agenda

- Project kickoff

#### Key Takeaways/Future Actions

- Need to determine whether Ethics Committee approval is necessary for our human testing
- Sam to look into solder stencil support
- Solder paste dispensing is definitely out of scope
- Start with MVP, exploratory analysis into gantry system vs 6DOF arm
- Explore the Hoeken linkage/camera crosshair view
- Laser cutter access?
---
- Work out a set of specifications, target two or three things to do in the next few days
- Nitish to share year planner/calendar
- Work out a project timeline/roadmap/Gantt chart
- James & Sam to let Nitish know when we want to work in the lab

#### Minutes

- N : First problem is to clearly quantify the research component
- We need to evaluate *something* technically challenging, scientifically rewarding
- Due to its research project nature
---
- J : Brief has slightly changed, gone from illumination to haptic/shared control
---
- N : What is the input method going to be?
- J : Input is likely going to be something like a joystick & a knob
---
- N : Joystick might be a bit ambitious, we could spend a whole project on this
- Maybe decrease the sensitivity of a joystick the closer you get to the snap point?
---
- S : There is already a existing knob project on GitHub
- We would just use something like an encoder
- N : A potentiometer for reading values, and an actuator to apply torque
---
- N : Induction braking—using eddy currents to brake
- We could use a disc of metal, aluminium
- Use PWM to drive alternating current—the greater the electric field—the more resistive torque on it
---
- N : Need to make sure that the solution is as robust as possible, require minimal maintenance—minimal software updates etc
- Create the smallest, most barebones proof-of-concept
---
- N : Start by getting the gantry stabilised, to get a reference mechanical structure
- Familiarise with the motors, get an idea of the repeatability, accuracy
- Start with simple keyboard interface
- Arrow keys, some numpad numeric entry
- Basic commands operational that becomes a low-level API
- Continuously add complexities
---
- N : In the worst case, you have a command to 'pick up', and can use the arrow keys to move the head
- Need to have replacement, spare components for the drivers/motors/etc.
- A point of research may be in evaluating technologies for making the gantry system reliable
---
- S : From what we have found, the shared joystick control seems to have very little existing research in it
- The human *and* the computer might be simultaneously trying to actuate the joystick, hence the shared control
---
- N : So how would you tell it to go to particular pad?
- J : No knowledge of the PCB artwork/netlist, no assumption around the presence of reference designators
- Joystick control, you take it over to the component that you want to pick up right now, and then ideally when we get to it, we would want that computer vision assistance on the component pick up as well
- So snap to the component, pick it up, and then take it over to whatever pad
- As soon as you get into the neighbourhood of pads, it snaps, progresses, or pulls you in, but then you have the ultimate control to take it to exactly the one that you want
- Machine takes care of the micro, you take care of the macro
- So you get it into the neighbourhood, but then it will take it to the mailbox
- S : Fighter pilot lock analogy
---
- N : It needs to be fast, it needs to be quick, and it needs to be intuitive
---
- N : Can we get it to do component recognition?
- ie this is a 0603 capacitor, with this value
- perhaps can flash on the screen of what has been picked up, and error if the pad is not appropriately sized etc.
- S : To start with, probably not much more recognition than just the footprint by looking at where the leads are
---
- N : End goal is to make the end product usable from different levels of expertise
---
- J : One of our research outcomes was to have some graph of the productivity boost as a result of having this shared control scheme
- N : If there's humans involved, we will likely need Ethics Committee approval
- J : 301 PCBs?
---
- S : Machine with/without CV assistance, tweezers with/without paste/reflow oven
---
- N / J : We will want to create our own test/reference PCB design
---
- N : No solder paste dispensing in this project
- The real thing that brings it down is humans
---
- N : We will make the assumption that the PCB has been placed with solder paste already applied, with a stencil
---
- N : Need to get a bunch of images of boards with/without paste off the machine so that we can evaluate our algorithms
---
- N : What if, one of the things that we add in the hardware aspect of this, is the ability to place a stencil, lower it, raise it, in a purely z-axis motion without any smearing, etc.
---
- Need to look into ways to secure the stencil/board in place
---
- S : 1.6mm walls made of other PCBs for exact height
- L-shaped walls, pins that can slide to locate the PCB
- Either make assumption about rectangular PCB to devise a way to secure any arbitrarily shaped board
---
- Should we read the pad locations from the stencil?
---
- N : How are we going to do the pick-and-place?
- S : That was actually one of my biggest concerns...
---
- N : Another thing to consider is using a 6DOF arm
- Supposedly reasonably precise
- Single biggest problem is that the load bearing capacity is usually quite small
- Lots of software available for free
---
- N : To me, the human factor takes into account the inaccuracy of the machine
- As long as you have the ability of moving to the right place even with slack etc. that is good enough
- Microsteps
- You take it to the right place or roughly there abouts, then the machine vision to lock into place
---
- N : I have used a manual pick-and-place, and it had a camera, but the parallax meant that it was impossible to get one of the dimensions correct
- Three or four cameras, and blend the images so that the nozzle doesn't exist
- S : Yeah, with the Hoeken linkage, when the nozzle lifts up out of the way, you get the picture without the nozzle because it physically isn't there
- N : That's a good idea, when the nozzle is out of the way, you get a very specific image, so you might put a crosshair in it... so I think there is an advantage to this, I think you should explore this
- S : With the linear rod, you have the option of putting a mirror in to get a split view of the footprint/pad together for straightening the component
- N : ... I was imagining that when you pick it up, there is another camera looking up that the component goes over, it sees the orientation, it corrects the orientation
- N : Another research component in the pick-and-place strategies—eg Hoeken linkage vs some linear rail
- Microstepping, but the issue is load—cannot carry as much load as without microstepping
---
- N : 200 steps per revolution
---
- N : What is the smallest package size that is supported?
- What minimum resolution is required?
- We want to start putting some numbers to our specifications
- Lets look at two things first in the coming weeks—one is testing these (gantry) out, evaluating whether any of these electromechanical things need to be tweaked, whether this is the best one or there are other alternatives, repeatability test
- Once we decide on that we will look at the pick-and-place, the 6DOF machine and whether that has any hope or whether that is a non-starter
---
- J : Any chance on getting Lab access before Week 3?
- N : Just tell me, I should be around to let you in
---
- J : Literature review...?
- N : If you want to concentrate on the Literature Review, I am absolutely happy with that
- At the end of the four weeks when you make your project proposal, you are staking the line
- To me, if you don't get this done simultaneously we have no way of saying whether we are using this or we aren't using this
- So we need to do *some* kind of experimental work to be able to draw a line
---
- N : This is called a mechanism, a four bar mechanism—evaluate this, see if you can simulate it, to me the problem that I can imagine these things have is the non-linear relationship between angle and distance
- And we need to find where sensitivity is the worst
- S : Yes, that is also the case with a crank, a crank slider, the alternative would be a belt
- N : So you might want to flip it over, figure out which end is the worst
- S : This one's not perfect, but I think the reason this one is the best is that it also has an almost linear first derivative
- N : So we need to put numbers down as to what precision we need
- Start with an initial set of specifications, then we do some tests, see if we can meet them and which ones we can't, and we can readjust some things
---
- Photogrammetry/occlusion side discussion
#### Amendments

1. N : We should also test the performance when the gantry head is loaded. We need to decide if geared steppers are more appropriate if at all possible.
2. N : The manual operation with a joystick should have a sensitivity knob to tweak sensitivity and hence the precision at the target.
3. N : Let's acquire a USB joystick or two.
