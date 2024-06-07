#bf5af2/university 

# James' Logbook

## Wed 28 Feb

- 700A lecture with Morteza to discuss project expectations/brief
- Informal meeting with Sam afterwards to discuss immediate next steps, things to prepare before tomorrow's meeting with Nitish
	- I will create a new GitHub organisation to house our main repository(ies)/any forks we require
	- We will adopt a *polyrepo* approach, rather than a monorepo approach.
		- Although [monorepos](https://monorepo.tools/) have their benefits and are the direction in which industry is trending (back to), we believe at this point that it would make the most sense for our disparate components to each have their own, separately versioned repositories with their own distinct CI/CD pipelines, and `git` submodules used when necessary to share code.
	- We will sit down tomorrow to get on the same page re: Obsidian.

- Updating Obsidian configuration
- Updating repository settings
- Creating new GitHub organisation

## Thu 29 Feb

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

- Work out a set of specifications, target two or three things to do in the next few days
- Nitish to share year planner/calendar
- Work out a project timeline/roadmap/Gantt chart
- James & Sam to let Nitish know when we want to work in the lab

#### Minutes

- N : First problem is to clearly quantify the research component
- We need to evaluate *something* technically challenging, scientifically rewarding
- Due to its research project nature

- J : Brief has slightly changed, gone from illumination to haptic/shared control

- N : What is the input method going to be?
- J : Input is likely going to be something like a joystick & a knob

- N : Joystick might be a bit ambitious, we could spend a whole project on this
- Maybe decrease the sensitivity of a joystick the closer you get to the snap point?

- S : There is already a existing knob project on GitHub
- We would just use something like an encoder
- N : A potentiometer for reading values, and an actuator to apply torque

- N : Induction braking—using eddy currents to brake
- We could use a disc of metal, aluminium
- Use PWM to drive alternating current—the greater the electric field—the more resistive torque on it

- N : Need to make sure that the solution is as robust as possible, require minimal maintenance—minimal software updates etc
- Create the smallest, most barebones proof-of-concept

- N : Start by getting the gantry stabilised, to get a reference mechanical structure
- Familiarise with the motors, get an idea of the repeatability, accuracy
- Start with simple keyboard interface
- Arrow keys, some numpad numeric entry
- Basic commands operational that becomes a low-level API
- Continuously add complexities

- N : In the worst case, you have a command to 'pick up', and can use the arrow keys to move the head
- Need to have replacement, spare components for the drivers/motors/etc.
- A point of research may be in evaluating technologies for making the gantry system reliable

- S : From what we have found, the shared joystick control seems to have very little existing research in it
- The human *and* the computer might be simultaneously trying to actuate the joystick, hence the shared control

- N : So how would you tell it to go to particular pad?
- J : No knowledge of the PCB artwork/netlist, no assumption around the presence of reference designators
- Joystick control, you take it over to the component that you want to pick up right now, and then ideally when we get to it, we would want that computer vision assistance on the component pick up as well
- So snap to the component, pick it up, and then take it over to whatever pad
- As soon as you get into the neighbourhood of pads, it snaps, progresses, or pulls you in, but then you have the ultimate control to take it to exactly the one that you want
- Machine takes care of the micro, you take care of the macro
- So you get it into the neighbourhood, but then it will take it to the mailbox
- S : Fighter pilot lock analogy

- N : It needs to be fast, it needs to be quick, and it needs to be intuitive

- N : Can we get it to do component recognition?
- ie this is a 0603 capacitor, with this value
- perhaps can flash on the screen of what has been picked up, and error if the pad is not appropriately sized etc.
- S : To start with, probably not much more recognition than just the footprint by looking at where the leads are

- N : End goal is to make the end product usable from different levels of expertise

- J : One of our research outcomes was to have some graph of the productivity boost as a result of having this shared control scheme
- N : If there's humans involved, we will likely need Ethics Committee approval
- J : 301 PCBs?

- S : Machine with/without CV assistance, tweezers with/without paste/reflow oven

- N / J : We will want to create our own test/reference PCB design

- N : No solder paste dispensing in this project
- The real thing that brings it down is humans

- N : We will make the assumption that the PCB has been placed with solder paste already applied, with a stencil

- N : Need to get a bunch of images of boards with/without paste off the machine so that we can evaluate our algorithms

- N : What if, one of the things that we add in the hardware aspect of this, is the ability to place a stencil, lower it, raise it, in a purely z-axis motion without any smearing, etc.

- Need to look into ways to secure the stencil/board in place

- S : 1.6mm walls made of other PCBs for exact height
- L-shaped walls, pins that can slide to locate the PCB
- Either make assumption about rectangular PCB to devise a way to secure any arbitrarily shaped board

- Should we read the pad locations from the stencil?

- N : How are we going to do the pick-and-place?
- S : That was actually one of my biggest concerns...

- N : Another thing to consider is using a 6DOF arm
- Supposedly reasonably precise
- Single biggest problem is that the load bearing capacity is usually quite small
- Lots of software available for free

- N : To me, the human factor takes into account the inaccuracy of the machine
- As long as you have the ability of moving to the right place even with slack etc. that is good enough
- Microsteps
- You take it to the right place or roughly there abouts, then the machine vision to lock into place

- N : I have used a manual pick-and-place, and it had a camera, but the parallax meant that it was impossible to get one of the dimensions correct
- Three or four cameras, and blend the images so that the nozzle doesn't exist
- S : Yeah, with the Hoeken linkage, when the nozzle lifts up out of the way, you get the picture without the nozzle because it physically isn't there
- N : That's a good idea, when the nozzle is out of the way, you get a very specific image, so you might put a crosshair in it... so I think there is an advantage to this, I think you should explore this
- S : With the linear rod, you have the option of putting a mirror in to get a split view of the footprint/pad together for straightening the component
- N : ... I was imagining that when you pick it up, there is another camera looking up that the component goes over, it sees the orientation, it corrects the orientation
- N : Another research component in the pick-and-place strategies—eg Hoeken linkage vs some linear rail
- Microstepping, but the issue is load—cannot carry as much load as without microstepping

- N : 200 steps per revolution

- N : What is the smallest package size that is supported?
- What minimum resolution is required?
- We want to start putting some numbers to our specifications
- Lets look at two things first in the coming weeks—one is testing these (gantry) out, evaluating whether any of these electromechanical things need to be tweaked, whether this is the best one or there are other alternatives, repeatability test
- Once we decide on that we will look at the pick-and-place, the 6DOF machine and whether that has any hope or whether that is a non-starter

- J : Any chance on getting Lab access before Week 3?
- N : Just tell me, I should be around to let you in

- J : Literature review...?
- N : If you want to concentrate on the Literature Review, I am absolutely happy with that
- At the end of the four weeks when you make your project proposal, you are staking the line
- To me, if you don't get this done simultaneously we have no way of saying whether we are using this or we aren't using this
- So we need to do *some* kind of experimental work to be able to draw a line

- N : This is called a mechanism, a four bar mechanism—evaluate this, see if you can simulate it, to me the problem that I can imagine these things have is the non-linear relationship between angle and distance
- And we need to find where sensitivity is the worst
- S : Yes, that is also the case with a crank, a crank slider, the alternative would be a belt
- N : So you might want to flip it over, figure out which end is the worst
- S : This one's not perfect, but I think the reason this one is the best is that it also has an almost linear first derivative
- N : So we need to put numbers down as to what precision we need
- Start with an initial set of specifications, then we do some tests, see if we can meet them and which ones we can't, and we can readjust some things

- Photogrammetry/occlusion side discussion

#### Amendments

1. N : We should also test the performance when the gantry head is loaded. We need to decide if geared steppers are more appropriate if at all possible.
2. N : The manual operation with a joystick should have a sensitivity knob to tweak sensitivity and hence the precision at the target.
3. N : Let's acquire a USB joystick or two.

### Meeting with Sam

- Meeting to discuss the Obsidian/organisation set up
- Block diagrams
- Gantt chart
- Talking about the overall software message passing/post office/synchronous block/object architecture

- My immediate tasks are:
	1. Finish the meeting minutes
	2. Start the literature review, and hook up the RPi + camera
	3. Gantt chart
	4. Come back together with Sam and evaluate the existing gantry vs the 6DOF arm

### GitHub Project

- Setting up the GitHub project by adding a Board & Timeline view for the Gantt chart
- Copying across the major deliverables into the project

## Wed 6 Mar

- Collecting the Raspberry Pi 5s, ArduCam, and HQ Camera from Akshat

## Wed 13 Mar

- Getting started with the Pi + camera
- Flashing the Raspberry Pi OS on the microSD card
- Updating system packages
- Using `rpicam-hello` and `rpicam-jpeg` to test the camera is working

- Installing XQuartz to allow X forwarding of the preview window over SSH
```sh
# Verify XQuartz has configured $DISPLAY correctly
echo $DISPLAY

# ssh with X forwarding
ssh -X james@rpi.local

# start the camera with X forwarding
rpicam-vid -t 0 --qt-preview
```

- It works!!
- It's just far from fast, there's easily a 6+ second delay before the preview window updates
- Will try [network streaming](https://www.raspberrypi.com/documentation/computers/camera_software.html#network-streaming) now

- Can get a connection with TCP, but it's:
	- Not any lower latency, and
	- Still finnicky—the stream doesn't seem to start until I ping `rpi.local` in a separate terminal window??

- Installing `ffmpeg` to try using `ffplay` for the 'low-latency' solution as documented in the [docs](https://www.raspberrypi.com/documentation/computers/camera_software.html#tcp)
- Hey! This is pretty good!
```sh
# On server (Pi)
rpicam-vid -t 0 --inline --listen -o tcp://0.0.0.0:2121 --nopreview

# On client
ffplay tcp://rpi.local:2121 -vf "setpts=N/30" -fflags nobuffer -flags low_delay -framedrop
```

## Thu 14 Mar

### Mounting Raspberry Pi SD Card

#### macOS

- Note: requires `macfuse` and `ext4fuse`.
- If homebrew complains with `brew install ext4fuse`, try `brew install gromgit/fuse/ext4fuse-mac`.

```sh
diskutil list

sudo mkdir /Volumes/rpi

sudo ext4fuse /dev/disk<...> /Volumes/rpi -o allow_other

# unmount
sudo diskutil unmount /Volumes/rpi
```

> [!fail]
> Oh... `ext4fuse` on macOS is an _only_ read implementation...

#### Ubuntu

- Okay, I will try installing Ubuntu as a guest OS through Parallels
- After lots of frustration... it works if you:
	1. Eject `bootfs` from Finder in macOS
	2. Explicitly pass `bootfs` into Parallels as a USB device
	3. `rootfs` appears!
- [hilarity](https://share.cleanshot.com/VXpVKmMK)

### Changing WiFi SSID Without Remote Access

- Open `etc/wpa_supplicant/wpa_supplicant.conf`
- Add
	```
	network={
		ssid="Wifi1"
		psk="passphrase1"
		priority=100
	}

	network={
		ssid="UoA-WiFi"
		key_mgmt=WPA_EAP
		identity="jbao577"
		password=""
	}
	```

## Fri 15 Mar

- Hm... the `wpa_supplicant.conf` changes yesterday don't seem to work
- Neither on `UoA-WiFi`, neither on my personal hotspot
- I will follow the steps in [this guide](https://learn.sparkfun.com/tutorials/headless-raspberry-pi-setup/wifi-with-dhcp)

- Still didn't work.
- Caved and bought a micro HDMI cable from PB Tech
- Connected to the network through the OS, and managed to SSH into it using the IP address
- Nice!
- `hostname -I | awk '{print $1}'`

- Reading through Sam's _The First Quarter_ proposal

- I should probably start looking at literature...

## Sat 16 Mar

- Starting to work on the Project Risk Assessment

- Discussion with Sam re: using a game engine for the user interface

## Sun 17 Mar

- Continuing to work on the Project Risk Assessment
- Completed most of the hazard identification & risk control, but will want to check the risks posted on the door to the lab for anything I've missed

- Starting to work towards the literature review
- I will start looking into:
	- Image processing algorithm/keying
		- Astronomical image processing? Counting stars based on pixel values may be relevant/interesting.
	- Integration of machine assistance with human input—shared control
	- General fly-by-wire control

### Literature Review

**Relevant Knowledge Areas/Keywords**

- Image processing algorithms
- Image keying
- Machine vision
- Computer vision
- Gradient ascent computer vision

- Shared control
- Human-in-the-loop
- Cooperative control
- Human computer interaction

- Input multiplexing
- Input mixing
- Cooperative machine vision algorithm
- Human-centric machine vision

- Fly-by-wire control
- Human-centric automation

- Smart pick-and-place machine
- Pick-and-place for rapid prototyping
- Pick-and-place with machine vision snapping

- Image decluttering (for lit review only)
	- A reference clean PCB from the PDF, overlaid onto a location, and matching the camera data onto that PDF image

## Mon 18 Mar

### Supervisor Meeting

- Applying some classic AI techniques for decluttering
	- Reference PCB image (clean, from PDF)
	- Overlay on location
	- What it sees, it tries to match with the PDF
	- Just for the literature review

- 6DOF coordinate control

- ufactory.cc uArm Swift Pro


18 Mar 1143–1352

#### Attendees

- Dr Nitish Patel (N)
- James Bao (J)
- Sam Skinner (S)

#### Absentees

- Nil

#### Agenda

- Progress check in
- Literature review topics
- Risk assessment form
- Dismissal of joystick

#### Key Takeaways/Future Actions

- Joystick dropped... (cool idea for a future project!)

- James to come in and start working on gantry & robotic arm
- James to begin creating test PCB to allow for benchmarking performance

- Sam to write down the various scenarios, pros and cons of each, and we explore one of them before the submission date
- Sam to begin producing prototype of mechanical head assembly
- Nitish recommends to use off-the-shelf components where possible

- To look into getting a lens protector for the camera
- Need to get the 27W Raspberry Pi 5 power supplies
- Sam to provide a full list of other mechanical/pneumatic parts to buy

- Risk assessment essentially completed, James and Sam just to finish the risk assessment matrix and submit to Canvas

#### Minutes

- James has completed an exploratory investigation into the Raspberry Pi & high quality camera
- Can stream output to laptop, mount Linux `rootfs` SD card on laptop, etc.
- SSH into the Pi, including on the University network
- Look into getting a lens protector

- James and Sam need the Raspberry Pi 27W (5V, 5A) USB PD power supplies ([$22 from PB Tech](https://www.pbtech.co.nz/product/SEVRBP0511/Raspberry-Pi-Official-27W-Power-Supply-White-AU-Pl))

- Dropped joystick ideas in favour of keyboard due to time management/other implementation considerations like zero point, project scope
- Gone more down a video game direction for user input/interface/visual feedback
- eg field of view feedback

- Sam recently working on the pickup head, with a small mockup

- In terms of literature, James intending to focus into:
	- Image processing algorithms; keying
	- Integration of machine vision with human input; shared control
	- General fly-by-wire controls
- Nitish suggested looking into AI techniques for decluttering
- Purely for the literature review
- A reference clean PCB image from the PDF
- Overlaid onto a location, and matching the camera data onto the PDF image

- Also algorithms required for the 6DOF control
- The robot itself is actually 4DOF, but look into the relevant algorithms/coordinate transforms

- Sam has been thinking about lots of ideas for the vacuum head
- Hoeken linkage
- Need to start laser cutting a prototype and looking at feasibility
- Operate in a horizontal plane rather than vertically

- Sam discussing force sensing considerations/design
- To provide Nitish with a list of parts to buy—vacuum pump, and other needs

- Laser targeting of the nozzle/head

- Sam to write down the various scenarios, pros and cons of each, and we explore one of them before the submission date

- Discussion about benchmarking the performance of the two mechanisms
- Frosted glass to act as an optical filter, with a bottom-mounted camera that is looking up to see where the head is (when it is lowered to touch the glass)
- Some electrical solution with a test PCB that implements continuity tests
- Varying pad sizes, etc., to benchmark repeatability & precision
- We want some automated benchmark method

- Sam asking about the University's preferred suppliers for mechanical parts
- Not a hard and fast rule

- James to look into ROS (Robot Operating System)
- Gold standard in robotics/research for a while now

- Risk assessment essentially completed, James and Sam just to finish the risk assessment matrix and submit to Canvas

- Discussion about final project requirements re: connected computer
- Eventually have the two Raspberry Pis running everything standalone, for now use our computers for development

- Discussion about network switch/router to access Pi/VNC
- James has managed to get SSH access to the Pi over WiFi though

- More of Sam's head discussion
- Geneva mechanism, crank slider
- Two brass tubes
- Nitish recommends to use off-the-shelf components where possible
- Profile the wear of the tubes
- Buy a few of the pumps

## Tue 19 Mar

- Starting to play with the robotic arm
- Have connected it to my phone with uArm Play
- Motion is more jittery than I thought... the repeatability looks alright though

- uArmStudio is not supported on ARM silicon, installing it on Windows instead

- The gantry is a Makeblock XY plotter 2.0
- Have to install CH340 drivers
- Installing from the [manufacturer's website](https://www.wch.cn/downloads/CH341SER_MAC_ZIP.html)
- Nice! Detected now!
- But it dies as soon as I connect to the port...
- Oh, GRemote is just a Java applet... there is also a `.sh` to launch it on macOS
- This doesn't work either? It sees the ports, but none of the open the remote interface
- Hmm... I want to at least get this remote working before I try to do anything else

- I will need to verify that the drivers were correctly installed on macOS

## Sat 23 Mar

- Converting logomark type to outlines
- Creating variant with transparent background

## Sun 24 Mar

- Using transparent background logomark to create Altium templates
- Creating a shortlist of potential project logotypes

- [Encode Sans SC](https://fonts.google.com/specimen/Encode+Sans+SC?preview.text=0O1Il%20a%20pick%20and%20place%20rapid%20prototyping%20%7C%20Raspberry%20Pi%20%7C%20Part%20IV%20Project%20%2383&preview.text_type=custom&stroke=Sans+Serif)
- [Varta](https://fonts.google.com/specimen/Varta?preview.text=0O1Il%20a%20pick%20and%20place%20rapid%20prototyping%20%7C%20Raspberry%20Pi%20%7C%20Part%20IV%20Project%20%2383&preview.text_type=custom&stroke=Sans+Serif)
- [Encode Sans Semi Expanded](https://fonts.google.com/specimen/Encode+Sans+Semi+Expanded?preview.text=0O1Il%20a%20pick%20and%20place%20rapid%20prototyping%20%7C%20Raspberry%20Pi%20%7C%20Part%20IV%20Project%20%2383&preview.text_type=custom&stroke=Sans+Serif)
- [Rambla](https://fonts.google.com/specimen/Rambla?preview.text=0O1Il%20a%20pick%20and%20place%20rapid%20prototyping%20%7C%20Raspberry%20Pi%20%7C%20Part%20IV%20Project%20%2383&preview.text_type=custom&stroke=Sans+Serif)
- [Alegreya Sans SC](https://fonts.google.com/specimen/Alegreya+Sans+SC?preview.text=0O1Il%20a%20pick%20and%20place%20rapid%20prototyping%20%7C%20Raspberry%20Pi%20%7C%20Part%20IV%20Project%20%2383&preview.text_type=custom&stroke=Sans+Serif)
- [Ubuntu](https://fonts.google.com/specimen/Ubuntu?preview.text=0O1Il%20a%20pick%20and%20place%20rapid%20prototyping%20%7C%20Raspberry%20Pi%20%7C%20Part%20IV%20Project%20%2383&preview.text_type=custom&stroke=Sans+Serif)

## Tue 26 Mar

- Raiding Makerspace for some M4 screws & nuts
- Fitted the gantry feet finally
- Need to get some shorter screws though

- Compressed Air induction

- Quick catch-up/demo of the robotic arm with Nitish

## Sun 31 Mar

- Finally starting literature review

### Literature Review

- Sam has found an interesting paper: [[A policy-blending formalism for shared control]]
- Other interesting papers he has found:
	- [[Haptic shared control - smoothly shifting control authority?]]
	- [[Cooperative Human and Machine Perception in Teleoperated Assembly]]

## Mon 1 Apr

- Setting up Mendeley & browser extension
- Huh, it's quite nice!!! Auto-citations!!
- Way nicer than spending 5 minutes per paper...
- The PDF viewer isn't ProMotion though :(

- Also looking into the references cited by papers I have found and collecting them too

### Searched Keywords

- image processing algorithm
- image processing algorithm locating location
- image processing fiducial
- image processing pcb footprint
- image processing fiducial auto-alignment
- computer vision footprint
- computer vision electronics footprint
- computer vision circuit board footprint
- computer vision circuit board
- computer vision circuit board pad

## Thu 4 Apr

- Continuing to collect literature

### Searched Keywords

- circuit board machine vision

## Sat 6 Apr

- I've been so ineffective this week...
- I will try to finally finish collecting literature today...

### Searched Keywords

- Image keying

## Wed 10 Apr

- Wow, I really am so bad at this...

### Searched Keywords

- Gradient ascent computer vision

## Tue 23 Apr

- It's been two weeks...
- Screw 723 and 770 -.-

### Call with Nitish

- One section in the Literature Review for each topic of research

- For each paper, look at the Abstract, see what they have discovered, see their techniques, read their conclusions
- Should give a very good idea about whether that methodology is appropriate for our use
- If determine that its relevant, can also look at the papers that they have referred to

- Statement of research intent/'project proposal' is to construct a pathway as a consequence of the literature review
- not a full project proposal
- some stated research questions
- some intended methodologies
- some distilled summary of the lit review

- don't bother with going into great depth (when I asked about whether to focus on a little number of papers in depth, or more papers for breadth)
- take the papers on face value
- remember that its unverified
- distill as much information as I can from them in the literature review
- keep in mind that these need to be validated and replicated

- breadth is better than depth

- goals, objectives to come first
- set of specifications
- literature review
- research question & methodology (the few challenges that you can identify)

### Searched Keywords

- Stochastic gradient descent algorithm computer vision

- Shared control

### Reference Manager

- Mendeley is pissing me off wayyyy too much
- Looking into alternatives
- Bookends (macOS exclusive!!), Papers, Zotero, Petal

#### Bookends

- Nice!!!
- Workflow is quite unfamiliar though, there is no browser extension
- Testing the Bookends Browser for a bit, but I'm not the biggest fan
- This also doesn't import the abstract and things
- PDF notes are ProMotion, but the tools aren't the best

- Oh, I can import RIS from the clipboard! This should work!
- I can also just install a nicer system PDF editor...

### Petal

- Huh, cool AI stuff
- Smooth PDF editing, and nice annotation features (arbitrary square annotations, highlighting)
- Opens in a new tab though...
- Browser-based, a bit slow, lots of AI...

### Zotero

- Nice browser connector, but still not the fastest
- Imports the abstract at least
- At least for IEEE PDFs, it tries to search for it from another source rather than taking it directly from Xplore
- Annotation is 60 Hz and not the best featured anyway...

## Wed 24 Apr

- Still just collecting more literature...

- Loving Bookends though!
- Have also installed Highlights as a PDF editor, it seems quite nice!
- Also tried Skim, but didn't like it as much

- Finally happy with the amount of literature that I have collected!
- Time to grind 💀

### Call with Nitish

- Demonstrate the problem—for example, take a picture of resistors and ICs to identify the problem / show what the outcome must do
	1. identify the component
	2. read the markings
	3. etc.
 - How are you going to address the issues with repeatability?
	 - how are we going to achieve precision? repeatability? how are we going to set the specification for precision? (parameterise with minimum pad size)
 - Why did prior work pick their specific configurations? ie gantry vs arm, gantry components
	 - eg. the Neoden—why two runners vs one runner?
		 - rigidity etc
 - Which type of Actuation? Hydraulic vs pneumatic vs electrical
 - What is your placement of cameras?
 - How do you plan to solve the head snapping to a component—not all components are going to be aligned, so how does it identify the correct orientation?

- Can refer to Sam's document, use his figures, diagrams, etc., must my own submission must be complete

- Reasons for choosing the gantry over the arm
	- xy is fully functional, can go to any (x,y,z)
	- but there _are_ lots of manufacturers of 6dof arms—the implementation part is done
		- but rigidity problem? potentially exacerbated once we mount a camera, additional hardware to the head

- Look, at this stage it looks like we are leaning towards x because of ease of software tools, already has a graphical software that operates the plotter, ...

### Searched Keywords

- shared control pick and place
- human in the loop pick and place
- human in the loop control
- human-centric automation
- rapid prototyping

#### Fruitless Keywords

- human in the loop
- cooperative control
- cooperative control pick and place
- input multiplexing
- cooperative machine vision algorithm

## Thu 25 Apr

- Yesterday, I finished importing the last of my papers (in this round of searching) into Bookends, then called it a night

- Anzac Day
- Another (very quick) call with Nitish
- Oh. he was an NCC cadet??
- He reiterates to not get overwhelmed...

- Well, I've just spent the entire rest of the day doing 'Anzac Day aftermath'...
- Fark, definitely overwhelmed.
- Do I shower and sleep for a few hours again, or just push through?

- Okay, well, there is no point doing _anything_ until I start to read the actual literature... so I'll start doing that first I guess.
- One on side, it's nice that I collected as much literature as I have—I don't feel like I'm missing crucial information.
- On the other hand, 43 is _intimidating_.
- I also already have a *start*, from my project pitch last year—and also the comments that Sam made on it! I'd say that those are probably fair game.
- Also my meeting minutes and things... I do have information in places!

- I just need to remember what I said to Nitish—a lot of these papers _aren't_ going to be directly relevant, so I should be able to skim over non-relevant sections reasonably quickly.

- Man, I can't even keep my eyes open lol
	- This would be the equivalent of 2.30am, assuming I woke up at 9am... (it's 8.30pm)
	- Maybe I sleep until midnight or something... that gives me 17 hours?
	- Realistically, I don't think I'll be productive if I keep on in this state.
	- When I wake up, I just need to _remember_ that I need to be judicious, efficient, and deliberate about what I do.

- I've just completely forgotten to research the video game display stuff for  user input/interface/visual feedback...
	- That's fine. I just won't include it. I need to be judicious right now. Just get something in.

- I really have approached this literature review wrong... rather than going ahead and collecting 43 papers on random things, I should stopped at the first useful one, read it, then just gone from there... This continuous role adaption paper looks like it would have been such a good place to start... but now I have no time...
- Like, I'm actually starting to be convinced that this project is just impossible... it looks like a whole team of researchers is working on just looking at how to gradually improve the way that a human and a robot can work with each other... while we are doing that, and 1000 other things...

- Hm... I guess a counter-point is that real research doesn't drill down into everything—nothing would _ever_ get done—instead, they just give their experimental set up and leave it at that.
- The main issue follows that I still do not concretely understand what my research focus will be, as I seem to be under the pretense that _everything_ will be research. I think the only realistic way to go about this project is to accept that half of it is to build the *experimental set-up* for the *thing we actually want to research*, something that I think Sam came to understand many moons ago.
- This means that all of the exploratory work Nitish was having us do wasn't to skip the step of consulting literature—rather, purely so we could acquire a better grasp on what we had available to us.

### Literature Review Goals

> [!quote] Canvas Announcement
> Students should present the problem that they are trying to solve, the important research questions, the objectives, and explain why this research is important. The purpose of the literature review is twofold:
> 
> - to demonstrate an awareness of the research relevant to your project, and
> - to summarise the important information or findings that will form the foundations of the work to be undertaken.
> 
> The literature review will also reveal if the research to be carried out in the project is something that has not been done before and is worth pursuing in order to advance knowledge in the particular area. The review should show the breadth of the research area and some depth in particular areas.

## Fri 26 Apr

- A mad dash to submit my LRSRI.

## Mon 29 Apr

- Talking with Sam after our Power E test... he has a new idea (vim-like navigation; enter `w` to move to the next pad above, `d` to move to the next pad to the right, etc.
- Hm.... this is actually a good point... why are we making the user need to 'drag' (in his words), or joystick-like-navigate to the pad of interest at all?
- I suppose it initially made sense when we were dealing with a joystick, and particularly when we were dealing with a _haptic_ joystick.
- Now that we've ditched the keyboard, however, does this really make sense?
- The analogy of vim-like `f`, `x` navigation is _much_ closer to what we are trying to achieve, than a Minecraft-boat-like video game analogy—we necessarily have a finite set of discrete points to which the user may be trying to place the component down on to—whereas Minecraft boat navigation is (pseudo-)continuous. Yes, there may be flowing water attempting to pull the user into a steady-state position, but, in our case _we aren't interested in all of the intermediate positions_—the user would be wanting to simply teleport directly to the pad of interest.
- This does eliminate the aspect of 'shared control' that previously existed, though, I don't think that this is an issue. It means that our project is going to pivot yet again, however, I think the shared control-approach (particularly with a keyboard) was always going to be a bit of a graft.

## Sat 4 May

- Having more success than before, at least the Windows `.bat` launches the full remote
- I can click the buttons, and see the GCode being transmitted... and supposedly it responds with an `ok`
- Nothing moves though?
- (Shame I forgot to get screenshots)

- Now it is refusing to connect to my computer...
- I see there is a code comment in `GCodeParser.ino` that 
```c
// Port 6 Uno/Orion
// Note instructions say port 8
// but the X Limits dont work with pins A0 and A7, they only work in port 
```
- I will correspondingly move the limit switch to Port 6

- I will try to re-flash the firmware...
- Trying to do it from macOS, I don't know which port to be using?

- We have an Orion board, so it's not the same process as an Arduino Leonardo

- Hm... it seems that I _don't_ want the CH340 third-party drivers?
- https://discussions.apple.com/thread/254867571?sortBy=best
- https://community.platformio.org/t/macos-ventura-and-ch340/30616/19
- https://github.com/adrianmihalko/ch340g-ch34g-ch34x-mac-os-x-driver
- It sounds like Apple now provides first-party drivers... which would check out, as the device was showing up as both `/dev/cu.usbserial-10` and `/dev/cu.wchusbserial10`
- Uninstalling the drivers, turning off the Orion board, and plugging it into my Mac, I no longer get the duplicate!
- (This process was finnicky... it seems to work only if I either turn off the board then plug it in, or, turn on the board, hit the reset, then plug it in)

- Fuck yes! With usbserial-10 only, I can program it!

- Got it working with Arduino IDE, programming it as an Uno board
- Doesn't seem to play ball with Windows, but rather than trying to get that to work, I'll just set it up as a PlatformIO project

- Now lots of refactoring...

## Sun 5 May

- Continuing to refactor
- Testing polarity of limit switches

## Mon 6 May

- Continuing to refactor... almost there

- Fixing alignment of the gantry with Sam
- Loose driver connector was such a red herring...

- Discussing `gantry` place in the project
 - This firmware should basically just take a steps count (not distance units), and go there
- 'MoveToZero' should just be 'move 1000000000'

- First I will finish refactoring and write a basic testbench

## Thu 11 May

- Meeting with Sam to discuss next steps, project plan, etc.
- ![[Pasted image 20240511221503.png]]

## Tue 4 Jun

- I don't think I've touched this for a month...
- Cleaned the desk today!

- Will figure out the next step...
- I'm not actually sure if it's worth doing a keyboard API?

## Wed 5 Jun

- Adding a very primitive `KeyboardApi`
- Ripped the Micro B head off 😂
- Meeting with Sam to
	- Get Julia set up
	- Discuss the next steps
	- Set up remote VS Code + Julia extension
	- Set up ngrok tunnelling

### Next Steps

- Re-write gantry to use fixed time slices

- Start with fly-by-wire
	- User clicks on a screen interface, the gantry drives to that position
		- Protocols?
	- Start working on interface
- _After_ fly-by-wire, next step is to add snapping

- Will need to add some handshaking mechanism between the components
	- Use EOF for synchronisation with a `read()`?

- Use camera to take photos... then do some onion-skin type thing to see how consistent it is

## Thu 5 Jun

- Working on next steps

- Running repeatability tests
	- Connecting HQ camera to Pi, using `rpicam-vid` as a viewfinder, and moving the gantry with Julia
	- Then using Julia to take still shots with `libcamera-still`
	- Still need to update the gantry firmware though

```julia
using LibSerialPort
using Images

##

function capture(shutter=12e-3)
    w = 4056
    h = 3040
    channels = 3 # RGB
    raw = read(`libcamera-still --nopreview --shutter=$(shutter)s --denoise=cdn_fast --immediate --encoding=rgb --output=-`)
    mat = permutedims(reshape(raw, channels, w, h), (1, 3, 2))
    colorview(RGB{N0f8}, mat)
end

##

# run(`echo "a" > /dev/tty.usbserial-10`)
f = open("/dev/ttyUSB1", 115200)

##

write(f, "G28\n")

##

# positions = 10000 .* [
#     10 10 0
#     12.5 10 0
#     15 12.5 0
#     15 15 0
#     12.5 17.5 0
#     10 17.5 0
#     7.5 15 0
#     7.5 12.5 0
# ]
positions = [
    14.3 11.4 0
    15.7 11.5 0
    16.4 10.2 0
    16.2 13 0
    16 15.8 0
    17.6 20.9 0
    13.2 23 0
    13.2 21.9 0
    12.1 14 0
] .* 10000
positions = Int.(positions)

write(f, "G0 X$(positions[1,1]) Y$(positions[1,2]) Z$(positions[1,3])\n");
sleep(5)

##

for j = 1:3
    for i = 1:30
        for coord in eachrow(positions)
            @time write(f, "G0 X$(coord[1]) Y$(coord[2]) Z$(coord[3])\n")
            sleep(6)
            @time img = capture(1 / 3)
            save("Repeatability/$(j)-$(i)-X$(coord[1])-Y$(coord[2]).png", img)
        end
    end
    write(f, "G28\n")
    sleep(10)
end

##

# @time img = capture(1 / 3)

##

close(f)
```

- Talking to Nitish about our progress
	-  Looked at the microstepping DIP switches
		- We are somewhere in the middle right now, neither zero microstepping nor max microstepping
	- He suggested buying a new gantry if needed rather than trying to rebuild this one

### Repeatability Tests

> [!summary]
> These are pretty good!!!
> There is definitely noticeable drift, however, these tests were taken without securing the gantry—and there was obvious and noticeable movement caused by the deceleration of the steppers.
> 
> This is an encouraging result however, and does not immediately prove non-viability.

![[s-X169000-Y110000.png]]

![[Pasted image 20240606205324.png]]

![[Pasted image 20240606205514.png]]

![[Pasted image 20240606205556.png]]

![[Pasted image 20240606205404.png]]

![[Pasted image 20240606205416.png]]

![[Pasted image 20240606205306.png]]

![[Pasted image 20240606205614.png]]

#### Colour Composites

```julia
using Images
using ImageIO

# Function to adjust opacity of an image
function adjustOpacity(img, alpha)
    return map(c -> RGB{N0f8}(c.r * alpha, c.g * alpha, c.b * alpha), img)
end

# Base path of the images
basePath = "/Users/james/Downloads/Repeatability/"

positions = [
    16.9 11 0
    18 11 0
    19 10 0
    19 12.5 0
    18.75 15 0
    18.75 20 0
    17.25 19.50 0
    14.5 13.6 0
] .* 10000
positions = Int.(positions)

for coord in eachrow(positions)

    # Initialize the stacked image
    stackedImage = nothing

    samples = 19
    # Read and stack each image with 20% opacity
    for n in 1:samples
        # Construct the filename
        filename = "$basePath/$n-X$(coord[1])-Y$(coord[2]).png"

        # Read the image
        img = load(filename)

        # Adjust opacity
        opacity = adjustOpacity(img, (1 / samples))

        # Stack the image
        if isnothing(stackedImage)
            stackedImage = opacity
        else
            stackedImage .= stackedImage .+ opacity
        end
    end

    # Ensure the stacked image is normalized between 0 and 1
    stackedImage = clamp01.(stackedImage)

    # Save the resulting stacked image
    save("s-X$(coord[1])-Y$(coord[2]).png", stackedImage)

end
```

#### Sam's Greyscale Range Composites

> [!info]
> See [[0606 Repeatability testing composites]]

![[Pasted image 20240606210021.png]]

```julia
using Images
using ImageIO

function threshold(img, lower, upper)

    # extract underlying
    mat = reinterpret.(UInt8, N0f8.(channelview(img)))

    # red channel only
    r = mat[1, :, :]

    # threshold it
    a = [
        if px < lower
            UInt8(0)
        elseif px > upper
            UInt8(0)
        else
            UInt8(255)
        end
        for px in r]

    # reinterpret as fixed-point
    b = reinterpret.(N0f8, a)

    # get as an image
    colorview(Gray{N0f8}, b)
end

# Base path of the images
basePath = "/Users/james/Downloads/Repeatability 2/"
basePath = "/Users/james/Desktop/Repeatability/"

# positions = [
#     16.9 11 0
#     18 11 0
#     19 10 0
#     19 12.5 0
#     18.75 15 0
#     18.75 20 0
#     17.25 19.50 0
#     14.5 13.6 0
# ] .* 10000
positions = [
    14.3 11.4 0
    15.7 11.5 0
    16.4 10.2 0
    16.2 13 0
    16 15.8 0
    17.6 20.9 0
    13.2 23 0
    13.2 21.9 0
    12.1 14 0
] .* 10000
positions = Int.(positions)

for coord in eachrow(positions)

    # get image size
    filename = "$basePath/1-1-X$(coord[1])-Y$(coord[2]).png"
    img = load(filename)
    sum = zeros(Int, size(channelview(img))[2:3]...)

    # set total number available
    count = 5

    mask = nothing
    for n = 1:count

        println("adding $n")

        img = load(filename)
        mask = threshold(img, 155, 250)

        sum .+= channelview(mask)

    end

    composite = clamp01.(sum) ./ 2
    composite .+= mask ./ 2
    compimg = colorview(Gray, composite)

    save("docs/4 — Logbooks/.vs/u-X$(coord[1])-Y$(coord[2]).png", compimg)

end
```

---

- Using tape to secure the gantry down

- i’ve left it set up for a 3x of (go-home + (30x iterations of (9 points-of-interest)))… but no point leaving it running cause the lights will turn off
- so tomorrow morning:
```sh
ssh -p 13874 0.tcp.au.ngrok.io

cd Desktop
julia test.jl & # in the background so it doesn’t die if i disconnect from ssh
```

- before I left though, I did manage to take 5 iterations, which produced this ([[0606 Repeatability testing composites|Sam's range code]]):
	- ![[Pasted image 20240607003105.png]]
	- ![[Pasted image 20240607003112.png]]
	- ![[Pasted image 20240607003117.png]]
	- ![[Pasted image 20240607003127.png]]
	- ![[Pasted image 20240607003135.png]]
	- ![[Pasted image 20240607003144.png]]
	- ![[Pasted image 20240607003149.png]]
	- ![[Pasted image 20240607003154.png]]
	- ![[Pasted image 20240607003202.png]]
	- encouraging! though I'm now interested in what error accumulates each time the machine zeroes... which I'll find out in the full test tomorrow
