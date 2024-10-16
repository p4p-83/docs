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
- Now that we've ditched the joystick, however, does this really make sense?
- The analogy of vim-like `f`, `t` navigation is _much_ closer to what we are trying to achieve, than a Minecraft-boat-like video game analogy—we necessarily have a finite set of discrete points to which the user may be trying to place the component down on to—whereas Minecraft boat navigation is (pseudo-)continuous. Yes, there may be flowing water attempting to pull the user into a steady-state position, but, in our case _we aren't interested in all of the intermediate positions_—the user would be wanting to simply teleport directly to the pad of interest.
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

        img = load("$basePath/1-$n-X$(coord[1])-Y$(coord[2]).png")
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

## Fri 7 Jun

- Grabbing results of repeatability test

### Updated Test

```julia
using LibSerialPort
using Images, Dates

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

coldstarts = 5
spins = 30

for j = 1:coldstarts
    for i = 1:spins
        for k = 1:size(positions)[1]
            coord = positions[k, :]
            @time write(f, "G0 X$(coord[1]) Y$(coord[2]) Z$(coord[3])\n")
            sleep(6)
            @time img = capture(1 / 3)
            save("/media/james/STRONTIUM/Repeatability/$j-$i-$k.png", img)
            open("/media/james/STRONTIUM/Repeatability/testing_log.txt", "a") do logfile
                println(logfile, "$(Dates.format(now(), "d u yy HH:MM:SS")): captured with j=$j, i=$i, and k=$k")
                println(logfile, "G0 X$(coord[1]) Y$(coord[2]) Z$(coord[3])\n")
            end
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

- Some minor changes:
  - Writing directly to my USB so I don't run out of disk space on my 16GB SD card
  - Naming the images with `k` rather than the coordinate
  - Sam also added a `testing_log.txt`
  
### Analysing Incomplete 'Fixed' Tests

#### A note

Previously, I was saving images as `{coldStart}-{spin}-X{xCoord}-Y{yCoord}.png`.
This is kinda stink from a code perspective, as it means that the processor must be aware of the `positions` matrix.
I've consequently changed the test script directly above to index the `positions` matrix with an incrementor `k`, and saving the image files as `{coldStart}-{spin}-{positionOfInterest}.png`.

This change has been applied for the full test run that I began once I got into the lab at noon, but, this naming change wasn't applied to the partial test run that Sam began earlier in the morning.
Hence, there is a need for the below script to rename the image files.

```julia
using Images
using ImageIO

# Base path of the images
basePath = "/Users/james/Desktop/Repeatability"

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

coldStarts = 2
spins = 30
pointsOfInterest = 9

for i = 1:coldStarts
	for j = 1:spins
		for k = 1:pointsOfInterest
			coord = positions[k, :]
			# get image size
			img = load("$basePath/$i-$j-X$(coord[1])-Y$(coord[2]).png")
			save("$basePath/$i-$j-$k.png", img)
		end
	end
end
```

#### Therefore

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
basePath = "/Users/james/Desktop/Repeatability"

coldStarts = 2
spins = 30
pointsOfInterest = 9

for i = 1:coldStarts
	for k = 1:pointsOfInterest

		# get image size
		filename = "$basePath/$i-$k-1.png"
		img = load(filename)
		sum = zeros(Int, size(channelview(img))[2:3]...)

		mask = nothing
		for n = 1:spins

			println("adding $n")

			filename = "$basePath/$i-$n-$k.png"
			img = load(filename)
			mask = threshold(img, 155, 250)

			sum .+= channelview(mask)

		end

		composite = clamp01.(sum) ./ 2
		composite .+= mask ./ 2
		compimg = colorview(Gray, composite)

		save("docs/4 — Logbooks/.vs/$i-s-$k.png", compimg)

	end
end
```

#### Rainbow Analysis

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
basePath = "/Users/james/Desktop/Repeatability"

coldStarts = 2
spins = 30
pointsOfInterest = 9

for i = 1:coldStarts
	for k = 1:pointsOfInterest

		# get image size
		filename = "$basePath/$i-$k-1.png"
		img = load(filename)
		sum = zeros(Int, size(channelview(img))[2:3]...)

		hero = spins
		bg = HSV(0,0,0)

		w = 4056
		h = 3040
		colours = fill(bg, h, w)

		# mask = nothing
		for n=1:spins

			println(n)

			filename = "$basePath/$i-$n-$k.png"
			mask = threshold(load(filename), 155, 250)
			hue = (n-1)*360/spins
			
			colours[mask .> 0] .= HSV(hue, 0.8, 0.8)

		end

		heromask = threshold(load("$basePath/$i-$hero-$k.png"), 155, 250)
		colours[heromask .> 0] .= HSV(0, 0, 1)

		# display(colours)
		save("docs/4 — Logbooks/.vs/$i-c-$k.png", colours)

	end
end
```

### My Next Steps

- My next steps are to
	- Begin working on the web interface
	- Re-write the firmware (can wait)

- I will also detach the fork of `p4p-83/gantry`

### Interface

- Most likely to be built using the [`<canvas>`](https://developer.mozilla.org/en-US/docs/Web/API/Canvas_API) API?
- Maybe [WebGL](https://developer.mozilla.org/en-US/docs/Web/API/WebGL_API) so I can utilise hardware acceleration

- Do I want to use React or something?

- Collecting interesting links
- Could be a play to use a tiling map interface, composed of still images...

> [!todo]
> What do existing PnP interfaces look like?

#### Video Streaming

- I'll park the interface research for now, and just get a barebones link working so we can start quantifying the round-trip latency

https://codecalamity.com/a-raspberry-pi-streaming-camera-using-mpeg-dash-hls-or-rtsp/

- Running the script
- Hm, the `stream_camera` service does not start correctly... `ffmpeg` seems to complain about the `-i` argument

- I've just been completely stabbing in the dark... I should probably do some research into codecs/containers/formats etc.
- https://www.youtube.com/watch?v=-4NXxY4maYc
- https://www.youtube.com/watch?v=0Mds4-ggpNI

- It seems like the best option might actually be the `rpicam-vid` streaming I figured out at the very beginning (if we choose to do livestreaming at all).
- This latency is about as good as the RTSP blog post claims, and I know that it works

- Researching other people who have tried to reduce latency
	- https://forums.raspberrypi.com/viewtopic.php?t=335940
	- https://forums.raspberrypi.com/viewtopic.php?p=1695053#p1695053
	- https://stackoverflow.com/questions/16750395/raspberry-pi-no-delay-10ms-video-stream

https://gektor650.medium.com/comparing-video-stream-latencies-raspberry-pi-5-camera-v3-a8d5dad2f67b

## Sat 8 Jun

### Video Streaming

- Trying some of the things I saved last night...

#### Flushing after write (TCP)

```sh
rpicam-vid -t 0 --inline --listen -o tcp://0.0.0.0:2121 --nopreview --flush --width 800 --height 600 --framerate 10

ffplay -probesize 32 -analyzeduration 0 -fflags nobuffer -fflags flush_packets -flags low_delay -framerate 10 -framedrop tcp:/172.23.126.145:2121
```
- Still rubbish

#### rpicam-vid + FFmpeg RTSP

```sh
rpicam-vid -t 0 --camera 0 --nopreview --codec yuv420 --width 800 --height 600 --inline --listen -o - | ffmpeg -f rawvideo -pix_fmt yuv420p -s:v 800x600 -i /dev/stdin -c:v libx264 -preset ultrafast -tune zerolatency -f rtsp rtsp://localhost:8554/stream

# IINA to receive RTSP
```
- Doesn't work?
	- Edit later: Sam mentioned that YUV is somewhat particular about resolutions, I discovered later that it _did_ work with 640x480

#### rpicam-vid + FFmpeg RTSP + mediaMTX

See https://arc.net/l/quote/brjohqmq

- There is over 6 seconds of latency...

#### rpicam-vid + FFmpeg RTSP + mediaMTX (WebRTC)

- Holy shit!
- https://arc.net/l/quote/elmpojaw

- https://discord.com/channels/1154647250144870412/1154647251193434165/1248794096412266658
- This is pretty freaking good!

- Can change resolution to 1080p, no perceivable additional delay
- Also 60fps!

- I'll get the `.yml` added to a git repo so it can be reproduced easily in the future

- Probably a good idea to go through and remove the stuff that isn't needed

### SSH Forwarding

- To clone private repositories from the Pi, I can use SSH agent forwarding to use my local SSH keys such that I don't need to add/create new ones on the Pi.
- This _should_ have already been enabled (ie `ForwardAgent yes`) was already in my `~/.ssh/config` for the respective host
- However, the key was not listed by `ssh-add -L`, ie it was not added
- This is because `ssh-agent` forgets keys once restarted on reboot on macOS
- Fixed by running
	```sh
	ssh-add --apple-use-keychain ~/.ssh/jamesnzl.key
	```
- See https://docs.github.com/en/authentication/connecting-to-github-with-ssh/using-ssh-agent-forwarding#your-key-must-be-available-to-ssh-agent

### Video Streaming

- Now that it works on the Pi, I can go home and just use MediaMTX to display a video stream of my laptop webcam

- I tried to use `ffmpeg` to read the `avfoundation` FaceTime webcam, but it wouldn't play nicely with Arc for some reason
- Not to fret, I can [stream to the server from my browser](https://github.com/bluenviron/mediamtx?tab=readme-ov-file#web-browsers)
- This works well!

### Interface

I'll start by trying to get this video stream into a basic HTML shell, similar to the WebRTC MediaMTX endpoint?

![[Pasted image 20240608180543.png]]
![[Pasted image 20240608180549.png]]

- It looks like there's an empty `<video>` tag, that has JavaScript code to populate it?
- I will need to read more into WebRTC
- https://cloudinary.com/blog/guest_post/stream-videos-with-webrtc-api-and-react

- You can embed the WebRTC feed in an `iframe`
- Oh cool, https://arc.net/l/quote/spnpcwtc MediaMTX gives you the option to read it out directly as a WHEP thing so you don't need the `iframe`
- https://github.com/Eyevinn/webrtc-player this works quite well ([demo](https://webrtc.player.eyevinn.technology/))

- I wonder if I can use the stats/other mechanism to pass position information along with the video?

- Cool, I've now got a basic prototype working. I do want to bootstrap it as part of a Next.js project, for dev-ex + extensibility
- Using shadcn/ui because it's objectively goated

![[Pasted image 20240609025717.png]]
![[Pasted image 20240609025731.png]]

- I've been fairly clever :^) with the overlay! The overlay is also what is used for the click events, so the user cannot click out of bounds!
- The overlay automatically re-sizes to the real-size of the video feed (ie not the `<video>` element's bounding box). This means that the overlay `<div>` perfectly aligns with the video feed, allowing me to apply a `cursor: crosshair` only within the valid clickable range.
- Similarly, I can apply a `'click'` event handler, and know that it will only work atop the video feed.

![[Capture 2024-06-09 at 03.12.41.png]]
- Normalising the click to be centred about the head position

https://discord.com/channels/1154647250144870412/1214522805123682314/1249022702283522069

- Okay, it's 4am.
- I should sleep.

- Tomorrow (today 💀) I want to look at trying to pass data back and forth alongside the video... ie can I piggy-back the WebRTC connection to pass head position/target position?

## Sun 9 Jun

### Interface

- First adding a loading bar for the `WebRtcVideo` component ✨

- Hm, okay
	- `webrtc-player` [once](https://github.com/Eyevinn/webrtc-player/issues/5) had support for data channels between the broadcaster and player, but [this has been removed](https://github.com/Eyevinn/webrtc-player/pull/13)
	- `mediamtx` [does not](https://github.com/bluenviron/mediamtx/discussions/3052) have support for data channels (also [this](https://github.com/bluenviron/mediamtx/discussions/1456)).

- If we choose to use WebRTC too, I will need to open a separate connected in parallel...

- I suppose this is fine—ultimately we will have our (Julia?) controller, which will be streaming the interface-addressed video feed into MediaMTX anyway—so we really just achieve the bypassing of MediaMTX as a middleman.
- This means that, for now, I can probably look at trying to use Julia to create the WebRTC connection with my frontend, and passing the position information back-and-forth.

- https://developer.mozilla.org/en-US/docs/Web/API/WebRTC_API/Using_data_channels

- I should probably start by reading properly into WebRTC, if I'm signing the dotted line...
	- https://developer.mozilla.org/en-US/docs/Web/API/WebRTC_API/Protocols

### WebRTC

- Built atop lots of protocols!

#### ICE

- Interactive Connectivity Establishment

- Allows web browser to connect with peers
- Commonly used for interactive media scuh as VoIP, p2p, video, IM
- Communicating through a central server would be slow and expensive

- Direct communication between clients on the Internet is tricky
	- Network address translators (NATs)
		- Delayed exhaustion of IPv4 address pool (~4bil unique addresses)
	- Firewalls that may prevent connections
	- Needs to give you a unique public address (not usually pre-existing)
	- Relay through a server if your router doesn't allow you to directly connect with peers
	- Other network barriers
- Published as RFC 8445
- August 2018

- Provides a framework for a communicating peer to discover and communicate its public IP address for other peers to reach it

- Uses STUN and/or TURN servers for this

#### STUN

- Session Traversal Utilities for NAT

- Protocol to discover your public address and determine any restrictions in your router that would prevent a direct peer connection

- Client sends a request to a STUN server on the Internet
- Server replies with the client's public address and whether the client is accessible behind the router's NAT

![[Pasted image 20240609163620.png]]

#### NAT

- Network Address Translation

- Gives your device a public IP address
- A router has a public IP address
- Each device connected to the router has a private IP address
- Requests are translated from the devices' public IP to the router's public IP with a unique port
- Devices don't need a unique public IP, but are still discoverable on the Internet

- Some routers have restrictions on who can connect to devices on the network
- Even though we have the public IP found by the STUN server, we might not be able to connect
- We therefore need TURN

#### TURN

- Traversal Using Relays around NAT

- Some routers have a restriction called 'Symmetric NAT'
- Router will only accept connections from peers you have previously connected to

- TURN bypasses Symmetric NAT restrictions
- Opens a connection with a TURN server and relays information through it
- You create a connection with a TURN server and tell peers to send packets to the TURN server, which forwards it in turn
- Has overhead, so is a last resort

#### SDP

- Session Description Protocol

- Standard for describing the multimedia content of the connection
	- Resolution
	- Formats
	- Codecs
	- Encryption
	- etc.
- Used so peers can understand each other
- Metadata

- Not a protocol, but a data format used to describe a connection that shares media between devices

- Consists of one or more lines of `UTF-8` text
- Each line begins with a one-character type
- Then an `=` sign
- Then structured text of a value or description
	- Format depends on the type

> [!quote]
> https://en.wikipedia.org/wiki/Session_Description_Protocol
> 
> Below is a sample session description from RFC 4566. This session is originated by the user "jdoe", at IPv4 address 10.47.16.5. Its name is "SDP Seminar" and extended session information ("A Seminar on the session description protocol") is included along with a link for additional information and an email address to contact the responsible party, Jane Doe. This session is specified to last for two hours using NTP timestamps, with a connection address (which indicates the address clients must connect to or — when a multicast address is provided, as it is here — subscribe to) specified as IPv4 224.2.17.12 with a [TTL](https://en.wikipedia.org/wiki/Time_to_live "Time to live") of 127. Recipients of this session description are instructed to only receive media. Two media descriptions are provided, both using RTP Audio Video Profile. The first is an audio stream on port 49170 using RTP/AVP payload type 0 (defined by RFC 3551 as [PCMU](https://en.wikipedia.org/wiki/PCMU "PCMU")), and the second is a video stream on port 51372 using RTP/AVP payload type 99 (defined as "dynamic"). Finally, an attribute is included which maps RTP/AVP payload type 99 to format h263-1998 with a 90 kHz clock rate. [RTCP](https://en.wikipedia.org/wiki/RTCP "RTCP") ports for the audio and video streams of 49171 and 51373, respectively, are implied.
> ```
> v=0
> o=jdoe 2890844526 2890842807 IN IP4 10.47.16.5
> s=SDP Seminar
> i=A Seminar on the session description protocol
> u=[http://www.example.com/seminars/sdp.pdf](http://www.example.com/seminars/sdp.pdf)
> e=j.doe@example.com (Jane Doe)
> c=IN IP4 224.2.17.12/127
> t=2873397496 2873404696
> a=recvonly
> m=audio 49170 RTP/AVP 0
> m=video 51372 RTP/AVP 99
> a=rtpmap:99 h263-1998/90000
> ```

#### A question

- Hmm... is WebRTC necessary here?
- Is a standard WebSocket sufficient?
	- There doesn't look to be much existing support for WebRTC in Julia
	- I'm not _really_ trying to do media transfer here
	- Looks like WebRTC is only really designed for browser <-> browser?
	- I don't really want to need to create a whole TURN server

- https://www.youtube.com/watch?v=p2HzZkd2A40 a _great_ video on WebRTC by Google.
- https://www.youtube.com/watch?v=WmR9IMUD_CY Fireship WebRTC
- https://www.youtube.com/watch?v=1BfCnjr_Vjg&t=128s Fireship WebSockets
- https://www.youtube.com/watch?v=pnj3Jbho5Ck

- Some YouTube research later—yeah, basically hit it on the nose.
- I'll use WebSockets for real-time communication between the web browser frontend interface (the client) and the Raspberry Pi (the server), and WebRTC for low-latency video streaming.

### WebSocket

#### Julia Server

```julia
using HTTP.WebSockets

server = WebSockets.listen("0.0.0.0", 8080) do ws
    println("Client connected")
    for msg in ws
        println("Received message: ", msg)
        send(ws, msg)
    end
end

close(server)
```

- And it seems we have communication!
- https://discord.com/channels/1154647250144870412/1214522805123682314/1249311422601625623

#### Interface

- Adding `sonner` toasts
- https://discord.com/channels/1154647250144870412/1214522805123682314/1249358460269891759

#### Server

- Adjusting server to mimic some form of returned data
- Not fully certain what this would end up looking like (where are the targets going to be added? Probably makes sense for FE?)
- But for now, I will just try to get the click target to move towards the middle

- Parse the received payload, and slowly write back the delta tending to 0

```julia
using HTTP.WebSockets

server = WebSockets.listen("0.0.0.0", 8080) do socket
    println("Client connected")
    for data in socket

        println("Received data: ", data)

        if length(data) % 2 == 0 
            deltas = reinterpret(Int16, data)

            while deltas[1] != 0 || deltas[2] != 0
                step = Int16[0, 0]
                for i in 1:2
                    if deltas[i] > 0
                        actual_step = min(1000, deltas[i])
                        deltas[i] -= actual_step
                        step[i] = actual_step
                    elseif deltas[i] < 0
                        actual_step = min(1000, -deltas[i])
                        deltas[i] += actual_step
                        step[i] = -actual_step
                    end
                end
                
                sleep(0.1)
                send(socket, reinterpret(UInt8, step))
            end
        end

        send(socket, data)
        # send(socket, "ok")

    end
end

close(server)
```

#### Data Payload

- I have fixed the issue where the click position passed back to the server depends on viewport width by mapping it to the `videoHeight`/`videoWidth`
- However, this isn't perfect, as it assumes that the video will not be scaled
- I will instead create an invariant by parameterising a fixed `deltaRange`, say the range of an `int16_t`, where the delta position is mapped into that range
- The controller can then convert that _known_, invariant range into its camera pixel distance

```ts
/*
 * This normalises the delta -> the clicked target to be on a Int16 coordinate space that is
 * - Centred at (0, 0), which should reflect the present position of the head
 * - Maximally negative at -32,768
 * - Maximally positive at 32,767
 *
 * This is done to ensure that the delta information passed back to the controller via
 * the WebSocket is agnostic of both the client viewport and the streamed video size
 * In other words, the burden is left to the controller to map the Int16 delta into real camera pixels
*/
}
```

## Mon 10 Jun

### WebSocket Data Interface

- I will extract the messaging logic from the `PlaceOverlay` component into its own `@/lib/socket` file
- Command specification will be documented in code

- Nice! I'm pretty happy with my messaging implementation now...
![[Capture 2024-06-10 at 20.19.57.png]]

- I have also customised the `heartbeat` message to also be a binary message (1 byte!) rather than a string
- Also type safety 😎

- I will now start improving the Julia server to better mimic the real one...

https://discord.com/channels/1154647250144870412/1214522805123682314/1249641977083854908

### Video Player

- Oh, I need to handle the case where the player fails to connect
- ![[Capture 2024-06-10 at 21.18.30.png]]

### Julia Server

```julia
using HTTP.WebSockets

@enum MessageType begin
    HEARTBEAT
    TARGET_DELTAS
    MOVED_DELTAS
end

const MESSAGE_TAGS = Dict(
    HEARTBEAT => 0x00,
    TARGET_DELTAS => 0x01,
    MOVED_DELTAS => 0x02,
)

function send_message(socket::WebSocket, type::MessageType, payload::AbstractArray{UInt8})
    if !haskey(MESSAGE_TAGS, type)
        println("Invalid message type: ", type)
        return
    end

    message = Vector{UInt8}(undef, length(payload) + 1)
    message[1] = UInt8(MESSAGE_TAGS[type])
    if length(payload) > 0
        message[2:end] .= payload
    end

    println("Generated message: ", message)
    WebSockets.send(socket, message)
end

function parse_message_type(tag::UInt8)::Union{MessageType, Nothing}
    for (key, value) in MESSAGE_TAGS
        if tag == value
            return key
        end
    end
    return nothing
end

function process_message(socket::WebSocket, data::Any)
    println("Non-UInt8[] data received: ", data)
    return nothing
end

function process_message(socket::WebSocket, data::AbstractArray{UInt8})
    message_tag = data[1]
    message_type = parse_message_type(message_tag)

    if isnothing(message_type)
        println("Received invalid message tag: ", message_tag)
        return nothing
    else
        println("Received message: ", message_type)
    end

    if message_type == HEARTBEAT
        send_message(socket, HEARTBEAT, UInt8[])

    elseif message_type == TARGET_DELTAS
        payload = reinterpret(Int16, data[2:end])
        println("Payload: ", payload)
        
        deltas = payload
        while deltas[1] != 0 || deltas[2] != 0
            step = Int16[0, 0]

            for i in 1:2
                if deltas[i] > 0
                    actual_step = min(1000, deltas[i])
                    deltas[i] -= actual_step
                    step[i] = actual_step
                elseif deltas[i] < 0
                    actual_step = min(1000, -deltas[i])
                    deltas[i] += actual_step
                    step[i] = -actual_step
                end
            end

            println("Stepped: ", step)
            send_message(socket, MOVED_DELTAS, reinterpret(UInt8, step))
        end

    end

end

WebSockets.listen("0.0.0.0", 8080) do socket
    println("Client connected")

    for data in socket
        println()
        println("Received data: ", data)

        process_message(socket, data)
    end

end
```

- FUCK. YES. THIS. IS. SO. COOL.
https://discord.com/channels/1154647250144870412/1214522805123682314/1249711510901493822

This probably(?) isn't logic that actually needs to exist in the final, but, it shows that the entire messaging link between the interface <-> server works, and has the capacity to do work.

1. The user clicks on a point in the video bounding box
2. The mouse event is captured
3. The clicked pixel location is converted to a delta from (0,0), defined as the middle of the screen
4. The delta is normalised into an `Int16` range
5. That delta is messaged to the server as `TARGET_DELTAS`
6. The server receives the message
7. The server parses the message tag to identify it as a `TARGET_DELTAS` message
8. The server uses that knowledge to parse the payload
9. The server steps in maximum increments of 1000 `Int16` coordinate space points until the delta away from (0,0) is zero
10. While stepping, the server messages the interface with the amount of steps taken as `MOVED_DELTAS`
11. The interface receives the messages
12. The interface parses the message tag...
13. The interface parses the payload...
14. The interface denormalises the deltas from the `Int16` range to the real viewport size
15. The interface moves the target circle the according number of moved steps

- Jobs for tomorrow:
	- Remove some of my useEffects if I can!
	- Hide the overlay if the video errors...
	- `setIsVideoStreaming?.()`
	- `setVideoSize()`
	- remove forwardRef

## Tue 11 Jun

- Wow, this diff is insane 😂
- I'm glad that at least the diff goes bad -> good 😂

```
feat: :art: significantly improve `PlaceVideo` implementation

- properly decouples `PlaceOverlay` from the `video` element
- fixes overlay still showing if video errors
- removes `forwardRef`
- removes unnecessary `useState`
- improves component lifecycle of `PlaceOverlay`
- uses conditional rendering instead of `display: none`
```

### Catching up with Sam

- Showing him the frontend / architecture / design / React
- Talking about repeatability in setup, deployment, etc.
- Talking about the state of the mechanics
- Next steps

#### Next steps

- I can start working on the CV
	- Just read Sam's logbook
	- Can we make a paste mask?
		- If yes, then our CV can be modular—we develop with the copper mask, knowing that the paste mask can be substituted in
	- Does 'cleaning the data' have a technical meaning?
	- What is 'useful' centroid information?
		- Centres of pads =/= centres of leads, so not always

- For open loop:
	- Sending target locations to the frontend
	- Calibration functions / logic

- Keyboard input

### Afterwards...

- Small interface bug fixes and tweaks...
- Fixing some lifecycle things/error handling for `PlaceVideo`

- Deciding the italics on the home page...
- Adding dynamic subtitles...
- Picking a font...
- All mission critical 😭😂

- Font shortlist:
	- Ubuntu
	- Maven Pro
	- Encode Sans

## Wed 12 Jun

- More font selection apparently... I thought I settled on Encode Sans last night, but Ubuntu is speaking to me today

## Thu 13 Jun

- Talking to Jin about potential improvements

> [!TODO]
> - [x] Need to add keyboard input to overlay
> - [x] Use protobufs for messaging
> - [ ] Look at making the web interface a native Electron app
> 	- Solves the issue of browser stability
> - [ ] Tauri go fast brrrt 🦀
> 	- ⚡blazingly fast⚡
> 
> - Adding a proper 'error' prompt if video fails to load

## Fri 21 Jun

- Refactoring `overlayTargetPosition` from client viewport offset to percentage offset
- Can remove the logic to clear the target if the overlay is resized
- Getting ready for a potential move to `<canvas>`!

- Looking into protobufs
- https://ieeexplore-ieee-org.ezproxy.auckland.ac.nz/document/6784954
- https://ieeexplore-ieee-org.ezproxy.auckland.ac.nz/document/5982183
- https://ieeexplore-ieee-org.ezproxy.auckland.ac.nz/document/8337257

## Sat 29 Jun

- Looking at what Sam has dropped off for me
- Nice! It looks solid
- Lashing the Raspberry Pi to the top of the head plate
- Re-wiring/re-connecting the modules
- Mounting the CM3

- Gantry seems fine—limit switches are all working
- Will connect the motor driver back to the Raspberry Pi, and set up the WebSocket listener for open-loop

> [!TODO]
> - My other to-dos:
> 	- Target locations -> frontend
> 		- Canvas?
> 	- Calibration functions/logic
> 	- Settings page
> 	- CV
> 	- Protobufs
> 	- Electron

### Julia Server with Gantry Logic

```julia
using HTTP.WebSockets
using LibSerialPort

@enum MessageType begin
    HEARTBEAT
    TARGET_DELTAS
    MOVED_DELTAS
end

const MESSAGE_TAGS = Dict(
    HEARTBEAT => 0x00,
    TARGET_DELTAS => 0x01,
    MOVED_DELTAS => 0x02,
)

gantryPosition = [ 0 0 0 ]

function send_message(socket::WebSocket, type::MessageType, payload::AbstractArray{UInt8})
    if !haskey(MESSAGE_TAGS, type)
        println("Invalid message type: ", type)
        return
    end

    message = Vector{UInt8}(undef, length(payload) + 1)
    message[1] = UInt8(MESSAGE_TAGS[type])
    if length(payload) > 0
        message[2:end] .= payload
    end

    println("Generated message: ", message)
    WebSockets.send(socket, message)
end

function parse_message_type(tag::UInt8)::Union{MessageType, Nothing}
    for (key, value) in MESSAGE_TAGS
        if tag == value
            return key
        end
    end
    return nothing
end

function process_message(socket::WebSocket, data::Any)
    println("Non-UInt8[] data received: ", data)
    return nothing
end

function process_message(socket::WebSocket, data::AbstractArray{UInt8}, gantry::SerialPort)
    message_tag = data[1]
    message_type = parse_message_type(message_tag)

    if isnothing(message_type)
        println("Received invalid message tag: ", message_tag)
        return nothing
    else
        println("Received message: ", message_type)
    end

    if message_type == HEARTBEAT
        send_message(socket, HEARTBEAT, UInt8[])

    elseif message_type == TARGET_DELTAS
        payload = reinterpret(Int16, data[2:end])
        println("Payload: ", payload)
        
        gantryPosition[1] = gantryPosition[1] + payload[1]
        gantryPosition[2] = gantryPosition[2] + payload[2]
        
        write(gantry, "G0 X$(gantryPosition[1]) Y$(gantryPosition[2]) Z$(gantryPosition[3])\n");

    end

end

##

gantry = open("/dev/ttyUSB1", 115200)

write(gantry, "G28\n")
gantryPosition = [ 0 0 0 ]

##

WebSockets.listen("0.0.0.0", 8080) do socket
    println("Client connected")

    for data in socket
        println()
        println("Received data: ", data)

        process_message(socket, data, gantry)
    end

end

##

close(gantry)

```

- Open loop!
- https://discord.com/channels/1154647250144870412/1154647251193434165/1256537119090802688

## Sun 30 Jun

- Working on the protobufs now

```julia
using ProtoBuf
protojl("pnp.proto", ".", ".")
```

> [!warning] Careful!
> `ProtoBuf.jl` generates this:
> ```julia
> function PB.encode(e::PB.AbstractProtoEncoder, x::Message)
>    initpos = position(e.io)
>    x.tag != var"Message.MessageTags".HEARTBEAT && PB.encode(e, 1, x.tag)
>    !isnothing(x.deltas) && PB.encode(e, 2, x.deltas)
>    return position(e.io) - initpos
> end
> ```
> 
> The `x.tag != var"Message.MessageTags".HEARTBEAT` means that a plain heartbeat message will not be encoded; ie the buffer will not be touched; ie the encoding does not happen!
> 
> The guard must be removed, ie always encode the tag.

EDIT: Hm, actually, see [Field Presence](https://arc.net/l/quote/lxfqapjg)
- What if there are _no_ bytes in that case 🤔
- Maybe have `INVALID` as the default?
- Have done that, and added proper field checking logic

### Julia Server with protobufs

- See https://github.com/p4p-83/interface/tree/main/stream/socket.jl

## `p4p-83/protobufs`

- Moving the protobuf definitions into its own repository

## Mon 1 Jul

- Finished implementing the protobufs yesterday

- I will start working on adding the ability to draw targets on the interface overlay

- Oh... I should be using the `onClick`-type JSX prop, rather than adding a `useEffect()` and `addEventListener`...
- I completely forgot 😅

- https://github.com/p4p-83/protobufs/commit/9980369269588cae5884df8d3850981da55e6c4d
- https://github.com/p4p-83/interface/commit/a30b7e653cee02283d8dee2f212212a9674f29a7
- https://discord.com/channels/1154647250144870412/1154647251193434165/1257136479499587704
- Whenever the server receives a heartbeat from the frontend (ie every 5 seconds), it now randomly generates target positions and sends it back—which the frontend is now able to display 😎
- When the user clicks on the target, it acts as if the user has clicked bang on the middle of the target

- I will now start looking into the Canvas API
	- https://developer.mozilla.org/en-US/docs/Web/API/Canvas_API/Tutorial
	- https://developer.mozilla.org/en-US/docs/Web/API/Canvas_API/Manipulating_video_using_canvas
	- https://code.pieces.app/blog/draw-interactive-shapes-react-canvas-api
	- https://joshondesign.com/p/books/canvasdeepdive/title.html

- Nevermind.
	- https://joshondesign.com/p/books/canvasdeepdive/chapter03.html#events
	- This sounds like a PITA...
	- I will see if this is made any easier by some libraries...
		- Seems so. https://konvajs.org/docs/styling/Mouse_Cursor.html
		- Ultimately, for the time being, the current implementation is more than enough.
		- If, in due course, we find that we _do_ need to draw more advanced shapes and graphics, it might pay to look at migrating then—but, for the time being, I don't think it's wise.

> [!quote]
> Canvas doesn't define any new events. You can listen to the same mouse and touch events that you'd work with anywhere else. This is both good and bad.
>
> The Canvas just looks like a rectangular area of pixels to the rest of the browser. The browser doesn't know about any shapes you've drawn. If you drag your mouse cursor over the canvas then the browser will send you standard drag events to the canvas as a whole, not to anything within the canvas. This means that if you want to do special things like making buttons or a drawing tool you will have to do the event processing yourself by converting the raw mouse events that the browser gives you to your own data model.

- I will now start working on jumping to the next best target with the keyboard

- Have added Shift+direction to step the gantry a fixed amount in the specified direction (eg `"a"`) over the serial port
- Now adding teleportation to targets
	- _What_ is the nearest target?

### Nearest Target
    
- My naïve implementation is to:
    1. Filter for target positions that fall beyond the reference line
    2. Sort for the nearest target in that subset
- For example, if I am looking to the left, I will:
    1. Filter for all target positions where `.x` is less than my current `.x`
    2. Sort based on `.x` to find the _greatest_ one
- This 'works', but the 'nearest' one found might be basically directly up—it is just slightly displaced leftwards too.
- What algorithm do I want to use?
- Perhaps some vector/Pythagoras, which takes into account the non-major axis component too?
- Or perhaps include the angle in my sorting?
- Put it in polar coordinates?

- Working in my physical notebook

![[Pasted image 20240701160921.png]]

#### Hypotenuse

$$
\Delta x^2 + \Delta y^2
$$

```julia

deltas = [
    -4 1
    -3 5
    1 8
    0 15
    4 12
]

for point in eachrow(deltas)
    result = sum(point .^ 2)
    
    println(result)
end

```

```
17
34
65
225
160
```

- This just finds the hypotenuse
- (1) shouldn't be the best.

#### Scaled? Hypotenuse

$$
2\Delta x^2 + \Delta y^2
$$

```julia

deltas = [
    -4 1
    -3 5
    1 8
    0 15
    4 12    
]

for point in eachrow(deltas)
    result = (2 * point[1] ^ 2) + (point[2] ^ 2)
    
    println(result)
end

```

```
33
43
66
226
176
```

- The scaling factor is kinda arbitrary, and doesn't do a whole lot (linear)...

#### Exponential Runaway on Non-Major Axis

$$
\Delta x + \Delta y^2
$$

```julia

deltas = [
    -4 1
    -3 5
    1 8
    0 15
    4 12    
]

for point in eachrow(deltas)
    result = (point[1] ^ 2) + point[2]
    
    println(result)
end

```

```
17
14
9
15
28
```

- Seems promising!
- Also somewhat makes sense, has an inverse exponential relationship with how far it deviates from the desired axis.
- Would be difficult to get it to work nicely if I choose to add (`Q`,`E`,`Z`,`C`) inputs to move diagonally though... no direct relationship with angle
  - I guess this just means you would take the raw hypotenuse...?

#### Polar Coordinates

- I'm going to stop faffing around in cartesian coordinates... it really is a polar problem. The head is centred at (0, 0) at the middle of the screen, and we are moving out in rays from that...
- I suspect we'll be best to work in a polar coordinate space for the rotation picking ~~space~~ step. Also polar space works just fine because the origin would be relative to the current head position in theory, and the lens uses circular elements so distortion is probably best corrected for with a polar space etc etc. Back converting to Cartesian would only be required for driving the gantry, the last step (unless I'm missing something)

$$
r * ((\theta - 0^{\circ})^2 + 1)
$$

```julia

deltas = [
    -4 1
    -3 5
    1 8
    0 15
    4 12    
]

for point in eachrow(deltas)
    r = sqrt(sum(point .^ 2))
    theta = -atan(point[1] / point[2])
    
    angle = 0
    
    thetaFactor = ((theta - angle)^2 + 1)
    result = r * thetaFactor
    
    println("$result \t $r \t $theta \t $thetaFactor")
end

```

```
11.37
7.53
8.17
15.0
13.96

```

- When working with axes aligned with $90^{\circ}$, this means that the maximum angle that a point may have is $90^\circ = \pi/2 = 1.57$
- ie a point that is maximally misaligned will have its radius multiplied by $1.57^2 + 1 = 3.46$
- This feels reasonable

---

- I will implement the latter two and see how they behave

- Gah! The fractions squaring giving smaller numbers 😑

```julia
function exponential_runaway(deltas)
    return (deltas[1] ^ 2) + deltas[2]
end

function polar(deltas)
    r = sqrt(sum(deltas .^ 2))
    theta = -atan(deltas[2] / deltas[1])
    
    angle = 270
    
    thetaFactor = ((theta - angle)^2 + 1)
    return r * thetaFactor
end

points = [
    0.5512626840619517 0.558434424353399;
    0.30038910505836575 0.5775082017242694;
    0.4457770656900893 0.7488212405584802;
]

# ! Important!
points = points .* 100

deltas = [
    [points[2,:] .- points[1,:]]
    [points[3,:] .- points[1,:]]
]

println("---")

for delta in deltas
    println()
    println(delta)

    println("Exp Runaway: $(exponential_runaway(delta))")
    println("Polar: $(polar(delta))")
end

```

---

- Sam has clearly already thought about this: [[0611 Directed pad finding and centroid calculation]] (I did know this at one point...)
- And with far more logical reasoning.
- I think we've sort of arrived at the same thing, though?

- Ideally, the target positions would be provided by the Raspberry Pi in an ordered manner.
	- In theory, the CV would have already ran some algorithm to detect the pads, through which it probably already has some metric of distance.
	- On second thought. how would it know which order to put it into, for the user's unknown input?
	- Perhaps have a `Map`, where there are multiple arrays that are ordered under a direction key?

- I'm still not sure _where_ this logic would be running. I seem to be charging ahead under the assumption that this will be in the frontend interface—the target positions are passed along, the frontend draws nice clickable targets, and the frontend consumes keypresses to find the best target position and sends that over the wire as a `TARGET_DELTAS` message. From memory, I recall the initial discussions being that the frontend would instead pass the raw keycode (or maybe an abstracted direction) to the controller, which would then presumably search for the best target. I suspect this is the understanding that Sam has—should probably clear it up. I'd personally be partial to keeping this frontend implementation—there's more flexibility & power imo...

- Perhaps I should think about how to implement his sweeping search—it may be possible to do that without a full linear search—or maybe I just call it done 😀

- Talking to Sam, I realise
> > Perhaps I should think about how to implement his sweeping search—it may be possible to do that without a full linear search—or maybe I just call it done 😀
> Yeah, this is a good point. I think you already answered this above (polar coords space with origin at camera centre, filter by angle within a 90° range, pick shortest radius) but if you've got something working we should stick with that for now

- That is such a better idea than my faffing about with these exponential terms 😂

### For Tomorrow

> [!TODO]
> - Start collecting inspiration for HUDs
> 	- Video games
> 	- Neovim
> 	- Vehicles
> 	- AR
> 	- CleanShot
> 	- iOS
> 	- YouTube / IINA
> 	- Miro
> 
> 	- WASD sector guide lines!
> 
> - Gantry protobufs
> - Gantry FW
> 	- Old
> 		- Incremental mode?
> 	- New
> 		- Is this actively being worked on?
> 		- Should I go ahead and finish it?
> - Controller in Cpp (and media server)
> 
> - Nearest target visualisation
> 
> - Open loop testing

## Tue 2 Jul

- Giving a demo to Nitish
- Updating the Julia server to work with the gantry, in a better manner (ie using protobufs, better code quality with a `Position` `struct` etc)

- Improving fixed-step implementation (Shift+direction) by having the frontend determine the fixed step size and just sending it as `TARGET_DELTAS`
	- Interface controls the step size
	- Reduces duplication of logic
	- The frontend+server already have the logic to track the moved delta (ie the controller can keep track of the updated position), whereas the previous `STEP_GANTRY` implementation just sent an `"a"` step which was entirely dependent on the hard-coded step size in the firmware—ie the controller has no idea how much the gantry actually moved

- Taking the gantry home

## Wed 3 Jul

- Updating mediamtx on the Pi
- Increasing the mediamtx write buffer
  - This should hopefully help with the frame corruptions/freezing!

### Nearest target visualisation

I'm going to continue toying with the idea of placing some additional weighting wrt angle deviation when determining the nearest target.

It hasn't felt quite right that the 'nearest target' is chosen to be the target up at an angle of 43deg just because it beats out something straight to the right by a tiny bit on its radius—I have suspicion that we'd want to prioritise things aligned with the cardinal directions to take less 'steps' in the standard case.

This would obviously be something to properly A/B test at some point if we get around to that (ethics...?), or it'll just be an 'the engineer thought so'.

#### Nearest Radius

```julia
using Plots
using Colors

# Define the number of sectors and the angle for each sector
num_sectors = 4
θ_start = -π / num_sectors
θ_stop = 2π - π/num_sectors

sector_limits = range(θ_start, stop=θ_stop, length=num_sectors + 1)

# Create the polar plot data
r_max = 1  # Maximum radius
num_r_points = 150  # Number of points along each ray
num_θ_points = 750  # Number of points along each sector arc
# num_r_points = 40   # Number of points along each ray
# num_θ_points = 125  # Number of points along each sector arc

# Arrays to hold polar coordinates and colors
r = range(0, stop=r_max, length=num_r_points)
θ = range(θ_start, stop=θ_stop, length=num_θ_points)

# Create a gradient for each sector
gradients = [
    range(RGB(1.0, 0.7, 0), stop=RGB(0.0, 0.7, 1), length=num_r_points),
    range(RGB(0, 1.0, 0), stop=RGB(1, 0.7, 1), length=num_r_points),
    range(RGB(1.0, 0.7, 0), stop=RGB(0.0, 0.7, 1), length=num_r_points),
    range(RGB(0, 1.0, 0), stop=RGB(1, 0.7, 1), length=num_r_points),
    range(RGB(0, 0, 1), stop=RGB(1, 0, 1), length=num_r_points),
    range(RGB(1, 0, 1), stop=RGB(0, 0, 0), length=num_r_points),
    range(RGB(0.0, 0.0, 0.0), stop=RGB(0.3, 0.6, 0.9), length=num_r_points),
]

# Plot each sector with a different gradient
plot()
for i in 1:num_sectors
    θ_sector = θ[(θ .>= sector_limits[i]) .& (θ .< sector_limits[i + 1])]

    for j in 1:num_r_points
        scatter!(θ_sector, [r[j]], markercolor=gradients[i][j], markersize=3, markerstrokewidth=0, markershape=:diamond, leg=false, proj=:polar)
    end

end

# Display the plot
p = plot!(size=(1250, 1250), dpi=240, background_color=:transparent, foreground_color=:gray)
savefig(p, "../interface/client/src/app/learn/static-images/targetPlotUnweighted.png")
```

![[Capture 2024-07-03 at 14.43.20.png]]

![[Capture 2024-07-03 at 15.19.29.png]]

![[Capture 2024-07-03 at 15.29.50.png]]

#### Nearest Radius with Angle Relationship

```julia
using Plots
using Colors
using Statistics

# Define the number of sectors and the angle for each sector
num_sectors = 4
θ_start = -π / num_sectors
θ_stop = 2π - π/num_sectors

sector_limits = range(θ_start, stop=θ_stop, length=num_sectors + 1)

# Create the polar plot data
r_max = 1  # Maximum radius
num_r_points = 150  # Number of points along each ray
num_θ_points = 750  # Number of points along each sector arc

# Arrays to hold polar coordinates and colors
r = range(0, stop=r_max, length=num_r_points)
θ = range(θ_start, stop=θ_stop, length=num_θ_points)

# Create a gradient for each sector
gradients = [
    range(RGB(1.0, 0.7, 0), stop=RGB(0.0, 0.7, 1), length=num_r_points),
    range(RGB(0, 1.0, 0), stop=RGB(1, 0.7, 1), length=num_r_points),
    range(RGB(1.0, 0.7, 0), stop=RGB(0.0, 0.7, 1), length=num_r_points),
    range(RGB(0, 1.0, 0), stop=RGB(1, 0.7, 1), length=num_r_points),
    range(RGB(0, 0, 1), stop=RGB(1, 0, 1), length=num_r_points),
    range(RGB(1, 0, 1), stop=RGB(0, 0, 0), length=num_r_points),
    range(RGB(0.0, 0.0, 0.0), stop=RGB(0.3, 0.6, 0.9), length=num_r_points),
]

# Plot each sector with a different gradient
plot()
for i in 1:num_sectors
    θ_sector = θ[(θ .>= sector_limits[i]) .& (θ .< sector_limits[i + 1])]

    θ_mean = mean(θ_sector)
    θ_range = 2π/num_sectors
    println("Middle: $θ_mean, Range: $θ_range")
    # for θ_val in θ_sector
        θ_deviation = abs.(θ_mean .- θ_sector)
        pct_deviation = θ_deviation / (θ_range / 2)
        println("Deviation: $θ_deviation, $pct_deviation")

        for j in 1:num_r_points
            index = trunc.(Int, (pct_deviation) * j) .+ 1
            scatter!(θ_sector, [r[j]], markercolor=gradients[i][index], markersize=3, markerstrokewidth=0, markershape=:diamond, leg=false, proj=:polar)
        end
    # end

end

# Display the plot
p = plot!(size=(1250, 1250), dpi=240, background_color=:transparent, foreground_color=:gray)
savefig(p, "../interface/client/src/app/learn/static-images/targetPlotWeightedSimple.png")
```

- Testing different relationships on Desmos
	- https://www.desmos.com/calculator/ece9m9uww6

![[Capture 2024-07-03 at 16.08.08.png]]

- This looks reasonable! I will implement it for now, and maybe keep it as a 'strategy' that we can switch out later

- Some useful discussion for the report + user A/B testing: https://discord.com/channels/1154647250144870412/1154647251193434165/1257912613615046668

> [!TODO]
> Perhaps a dumb idea but you could implement a modifier key that rotates everything 45°. So the image just spins a bit temporarily and now W is more NE than N 😂
> 
> Also from my lit review I'm still wondering if we should break the image into tiles. Or have a modifier key that will basically long jump in a given direction so you don't have to hit all the intermediate pads

> [!TODO]
> Test the gantry without the selection with WASD—how does that feel?
> 
> 0 = last position on the board, and 1, 2, 3, 4, etc used to jump to parts bins

---

- Hm, implementing it in the interface, it doesn't feel right.
- It seems to be preferring _really_ far points, as long as it lies basically on the search angle.
- This somewhat makes sense, as the radius is being multiplied by a tiny number.

- This isn't good!
- Do I want to ensure the radius scaling factor is capped at some value that it will never go below?

- Refactoring the Julia visualisation code to use matrix operations
- It is comically faster 😂
- It's now < 1 second for 150 radius points and 750 angle rays. Previously, it was almost a whole minute for 15 radius points and 200 rays. Yikes.

![[Capture 2024-07-03 at 23.59.00.png]]

- Well, I can now see that it clearly far-prefers the 'aligned' angles.

- This wasn't quite the chevron I wanted, and that I thought I was seeing in the original screenshot (ie with fewer points).

- I'll see if I can get this closer to what I intended...

```julia
using Plots
using Colors
using Statistics

# Define the number of sectors and the angle for each sector
num_sectors = 4
θ_start = -π / num_sectors
θ_stop = 2π - π/num_sectors

sector_limits = range(θ_start, stop=θ_stop, length=num_sectors + 1)

# Create the polar plot data
r_max = 1  # Maximum radius
num_r_points = 150  # Number of points along each ray
num_θ_points = 750  # Number of points along each sector arc

# Arrays to hold polar coordinates and colors
r = range(0, stop=r_max, length=num_r_points)
θ = range(θ_start, stop=θ_stop, length=num_θ_points)

# Create a gradient for each sector
gradients = [
    range(RGB(1.0, 0.7, 0), stop=RGB(0.0, 0.7, 1), length=num_r_points),
    range(RGB(0, 1.0, 0), stop=RGB(1, 0.7, 1), length=num_r_points),
    range(RGB(1.0, 0.7, 0), stop=RGB(0.0, 0.7, 1), length=num_r_points),
    range(RGB(0, 1.0, 0), stop=RGB(1, 0.7, 1), length=num_r_points),
    range(RGB(0, 0, 1), stop=RGB(1, 0, 1), length=num_r_points),
    range(RGB(1, 0, 1), stop=RGB(0, 0, 0), length=num_r_points),
    range(RGB(0.0, 0.0, 0.0), stop=RGB(0.3, 0.6, 0.9), length=num_r_points),
]

# Plot each sector with a different gradient
plot()
for i in 1:num_sectors
    θ_sector = θ[(θ .>= sector_limits[i]) .& (θ .< sector_limits[i + 1])]

    θ_mean = mean(θ_sector)
    θ_range = 2π/num_sectors
    println("Middle: $θ_mean, Range: $θ_range")
    # for θ_val in θ_sector
        θ_deviation = abs.(θ_mean .- θ_sector)
        pct_deviation = min.(θ_deviation / (θ_range / 2) .+ 0.5, 0.999)
        println("Deviation: $θ_deviation, $pct_deviation")

        for j in 1:num_r_points
            index = trunc.(Int, (pct_deviation) * j) .+ 1
            scatter!(θ_sector, [r[j]], markercolor=gradients[i][index], markersize=3, markerstrokewidth=0, markershape=:diamond, leg=false, proj=:polar)
        end
    # end

end

# Display the plot
plot!(size=(1250, 1250))
```

![[Capture 2024-07-04 at 01.54.24.png]]

- Shifting the angle deviation factor by $0.5$, and limiting the total impact to $1$ (because I only have finite elements in the gradient array; I can't just index it to infinity. The interface doesn't have the same limit, as JavaScript's sort function just compares numbers—but.), I see that I have definitely changed the behaviour.
	- I do suspect that this is because of the hard cap (causing a 'saturation' though, and not because of the linear shift.)

```julia
using Plots
using Colors
using Statistics

# Define the number of sectors and the angle for each sector
num_sectors = 4
θ_start = -π / num_sectors
θ_stop = 2π - π/num_sectors

sector_limits = range(θ_start, stop=θ_stop, length=num_sectors + 1)

# Create the polar plot data
r_max = 1  # Maximum radius
num_r_points = 150  # Number of points along each ray
num_θ_points = 750  # Number of points along each sector arc

# Arrays to hold polar coordinates and colors
r = range(0, stop=r_max, length=num_r_points)
θ = range(θ_start, stop=θ_stop, length=num_θ_points)

# A damping factor. Influence of deviation from the search angle is inversely proportional to this value.
damping = 0

# Create a gradient for each sector
gradients = [
    range(RGB(1.0, 0.7, 0), stop=RGB(0.0, 0.7, 1), length=trunc(Int, (1+damping)*num_r_points)),
    range(RGB(0, 1.0, 0), stop=RGB(1, 0.7, 1), length=trunc(Int, (1+damping)*num_r_points)),
    range(RGB(1.0, 0.7, 0), stop=RGB(0.0, 0.7, 1), length=trunc(Int, (1+damping)*num_r_points)),
    range(RGB(0, 1.0, 0), stop=RGB(1, 0.7, 1), length=trunc(Int, (1+damping)*num_r_points)),
    range(RGB(0, 0, 1), stop=RGB(1, 0, 1), length=trunc(Int, (1+damping)*num_r_points)),
    range(RGB(1, 0, 1), stop=RGB(0, 0, 0), length=trunc(Int, (1+damping)*num_r_points)),
    range(RGB(0.0, 0.0, 0.0), stop=RGB(0.3, 0.6, 0.9), length=trunc(Int, (1+damping)*num_r_points)),
]

# Plot each sector with a different gradient
plot()
for i in 1:num_sectors
    θ_sector = θ[(θ .>= sector_limits[i]) .& (θ .< sector_limits[i + 1])]

    θ_mean = mean(θ_sector)
    θ_range = 2π/num_sectors
    println("Middle: $θ_mean, Range: $θ_range")
    # for θ_val in θ_sector
        θ_deviation = abs.(θ_mean .- θ_sector)
        pct_deviation = θ_deviation / (θ_range / 2) .+ damping
        println("Deviation: $θ_deviation \n\n $pct_deviation")

        for j in 1:num_r_points
            index = trunc.(Int, (pct_deviation) * j) .+ 1
            scatter!(θ_sector, [r[j]], markercolor=gradients[i][index], markersize=3, markerstrokewidth=0, markershape=:diamond, leg=false, proj=:polar)
        end
    # end

end

# Display the plot
p = plot!(size=(1250, 1250), dpi=240, background_color=:transparent, foreground_color=:gray)
savefig(p, "../interface/client/src/app/learn/static-images/targetPlotWeightedDamping0.png")
```

- Increasing the length of the gradient array wrt the maximum radius scaling factor so that I can pretend like I can index it 'infinitely'.
- Hey! This is bloody great!

**damping = 0**
![[Capture 2024-07-04 at 01.14.28.png]]

**damping = 1**
![[Capture 2024-07-04 at 01.15.14.png]]

**damping = 5**
![[Capture 2024-07-04 at 01.15.48.png]]

**damping = 100**
- At a very big number, the influence becomes minimal—so it's as if the factor doesn't exist.
- Can be seen as a damping factor in a sense.
![[Capture 2024-07-04 at 01.16.56.png]]

#### Interface Testing

- Testing these on the interface, a value 0.2 < damping < ~8 really does feel more natural.

```ts
  const [targetPositionOffsets, setTargetPositionOffsets] = useState<Position[] | null>([
    {
      'x': 0.09790188448920424,
      'y': 0.9416189822232395,
    },
    {
      'x': 0.24185549706263829,
      'y': 0.9050278477149615,
    },
    {
      'x': 0.8876020447089341,
      'y': 0.8755168993667506,
    },
    {
      'x': 0.7434653238727398,
      'y': 0.4303349355306325,
    },
    {
      'x': 0.1125810635538262,
      'y': 0.5462272068360418,
    },
    {
      'x': 0.15208667124437322,
      'y': 0.37766079194323643,
    },
    {
      'x': 0.5499656672007325,
      'y': 0.5133745326924544,
    },
    {
      'x': 0.925963225757229,
      'y': 0.7672541390096895,
    },
    {
      'x': 0.5512626840619517,
      'y': 0.558434424353399,
    },
    {
      'x': 0.4457770656900893,
      'y': 0.7488212405584802,
    },
    {
      'x': 0.9060502021820401,
      'y': 0.03494316014343481,
    },
    {
      'x': 0.7314259555962462,
      'y': 0.05574120698863203,
    },
    {
      'x': 0.5562523842221714,
      'y': 0.3819943541618982,
    },
    {
      'x': 0.30038910505836575,
      'y': 0.5775082017242694,
    },
    {
      'x': 0.4777904936293584,
      'y': 0.43222705424582286,
    },
    {
      'x': 0.30475318532082096,
      'y': 0.2430304417486839,
    },
    {
      'x': 0.1262073701075761,
      'y': 0.24333562218661783,
    },
    {
      'x': 0.12397955291065843,
      'y': 0.3066453040360113,
    },
    {
      'x': 0.7494621194781415,
      'y': 0.028595407034409093,
    },
    {
      'x': 0.7555809872587167,
      'y': 0.5491416800183109,
    },
    {
      'x': 0.8394750896467537,
      'y': 0.684290836957351,
    },
    {
      'x': 0.03205920500495918,
      'y': 0.861326009002823,
    },
    {
      'x': 0.8349889372091249,
      'y': 0.7737087052719921,
    },
    {
      'x': 0.5819943541618983,
      'y': 0.4532539864194705,
    },
  ])
```

- Some test data for target locations

##### With no influence

- ie just nearest radius (or very large damping)

![[Capture 2024-07-04 at 01.21.30 1.png]]

- `a` goes to the first target on the left
- While the human operator is likely to expect the next `d` to go to the target labelled (3), it instead goes to (2).
- This doesn't feel natural.

![[Capture 2024-07-04 at 01.26.37.png]]

- Also goes from (2) to (3), rather than (2) to (4)—which feels unnatural.

##### With undamped influence

- ie straight radius * deviation factor

![[Capture 2024-07-04 at 01.23.46.png]]

- Skips straight from (2) to (3), without stopping at (4) or (5)—because (3) has such a low deviation error, so the radius is multiplied by a very small factor

##### With damped influence (0.5)

![[Capture 2024-07-04 at 01.25.04 1.png]]

- Now goes to (3), instead of the higher (4)!
- This is great!

![[Capture 2024-07-04 at 01.27.34.png]]
- Also correctly goes from (1) to (2), rather than from (1) to (3). Nice!

##### With damped influence (2)

![[Capture 2024-07-04 at 01.29.55.png]]

- Going from (3) to the right, a damping of 2 will take it to (4). A damping of 0.5 will take it to (5) instead—which I would probably say is more natural.
- There is a concern though—if (3) goes to (5), how would (4) get reached if those top-left targets were not there?
	- You can't!
	- Note: I _can_ get to it from the targets in the top right (going left), and I can get to it from (1) going up. However, if (1) were not there, (3) and the target to the upper-right of (1) would go back and forth between each other, causing (4) to be unreachable.
	- We can probably assume that there will be enough pads that all pads are reachable in a real scenario, though.

> [!WARNING]
> The damping CANNOT be too small!
> If the influence causes the 'nearest' target to jump too far into the distance and disregards something closer, you run the danger of not being able to get to a target.
> This is not to say that this problem does not exist if the system is nearest-radius-only—that must still be proved—but I have proven (through discovery of a case that breaks) that this damped algorithm can fail.

- We might even be able to expand the search arc with this—if targets that are very far off the search arc are 'attenuated' heavily, then it should only be useful in cases where the field is sparse.

- I'll roll with a damping factor of 1.5 for now.

![[Capture 2024-07-04 at 01.46.40.png]]

## Thu 4 Jul

- Talking to Sam about potential settings for the damping
	- https://discord.com/channels/1154647250144870412/1154647251193434165/1258221907585531904
	- Perhaps overlay the polar diagram (a representation of?)

> [!TODO]
> - Want to work on adding the guide lines
> 	- Search for 'React HUD' and see how people do it... can I do it without Canvas?
> 	- Calibration page
> 	- Settings page
> 		- Browser local storage?

- First looking at `notion-assignment-import` though...

- Welp. It's now 1am, and I didn't get around to any of this.
- Tomorrow, I guess.

## Fri 5 Jul

### HUD

- Researching code examples for heads up displays
	- https://github.com/bronz3beard/react-hud
	- Ooh, I like the `backdrop-blur-lg`

- Yeah, I don't want to use Canvas.
	- No text aliasing
	- No 'element' awareness, have to just check if a click location overlaps with a pixel.
	- https://docs.nanos.world/docs/getting-started/tutorials-and-examples/basic-hud-canvas
	- Compare the above to https://docs.nanos.world/docs/getting-started/tutorials-and-examples/basic-hud-react

- I will want icons/sprites for graphical elements

### Settings

- I'll first quickly switch to the settings page

- Cool, finally learning how to do a React context (and link to `window.localStorage`)
- `zod` for input validation again
- Fixing `WebRtcVideo` error if the supplied host is unresolvable/unreachable

- 'Quickly', he says.
- Four and a half hours later: https://discord.com/channels/1154647250144870412/1154647251193434165/1258702487662366840

### Navbar

- Now adding a navbar

![[Capture 2024-07-06 at 00.22.07.png]]

- Nice 😎

- Also added `Shift`-`(P|S|C|L)` navigation from the home screen to quickly go to `/place`/`/calibrate`/`/settings`/`/learn`

### Learn Page

- Quickly add a `/learn` page now that I have the template components

![[Capture 2024-07-06 at 02.31.00.png]]

- Sick!

![[Capture 2024-07-06 at 02.31.10.png]]

- Added a `globals.ts` file for global strings/page configurations/etc.
- In theory, if we ever need to update a page path, personal website URL, etc., it will now be easy...

## Sat 6 Jul

> [!TODO]
> - Today:
> 	- Think more about the research direction. Revisit papers in Bookends.
> 	- Look at adding cards/collapsibles to the 'Constituent Pages' section.
> 	- Finish documenting all implemented features on `/learn`.
> 	- Right-align page path on Card heading (if I do that).
> 	- Gantry pouncing without space confirmation.
> 

### Serial Port Issues

- Man, why does the `open("/dev/tty.usbserial-10", 115200)` keep failing so often with
```
OS error code 6: Device not configured
ERROR: libserialport returned SP_ERR_FAIL - Host OS reported a failure.
Stacktrace:
 [1] error(s::String)
   @ Base ./error.jl:35
 [2] check(ret::LibSerialPort.Lib.SPReturn)
   @ LibSerialPort.Lib ~/.julia/packages/LibSerialPort/i1kHh/src/wrap.jl:299
 [3] sp_open(port::Ptr{LibSerialPort.Lib.SPPort}, mode::SPMode)
   @ LibSerialPort.Lib ~/.julia/packages/LibSerialPort/i1kHh/src/wrap.jl:341
 [4] open(portname::String, baudrate::Int64; mode::SPMode, ndatabits::Int64, parity::SPParity, nstopbits::Int64)
   @ LibSerialPort ~/.julia/packages/LibSerialPort/i1kHh/src/LibSerialPort.jl:474
 [5] open(portname::String, baudrate::Int64)
   @ LibSerialPort ~/.julia/packages/LibSerialPort/i1kHh/src/LibSerialPort.jl:467
 [6] top-level scope
   @ REPL[3]:1
```

- I don't even know how I fixed it previously... I literally just keep resetting the board/unplugging the USB/restarting the REPL until it works...
- I can't `screen` to it either... surely there is something that I'm doing wrong?
- It claims its an OS error...

	- It's _not_ just `screen -wipe`
	- Nor flipping the USB adaptor
	- Nor plugging my MacBook in
	- Nor starting the gantry away from (0, 0)

- Can I program it using PlatformIO?
	- No... Device not configured????????
	- Okay, unplugging it worked this time
	- And now I can monitor it...
	- Inch resting.
	- And now everything works.

- Hm... the camera feed is freezing still...

### Pouncing

- Yeah, at least until proper handshaking is implemented where the gantry notifies the server that it has reached the destination (at which point the frontend resets), it really isn't intuitive to automatically pounce whenever a target has been selected, without the use of a `Space` 'select' input.

### Interface on Pi

- Running the interface on the Raspberry Pi
- Installing `nvm`
```sh
curl -o- https://raw.githubusercontent.com/nvm-sh/nvm/v0.39.7/install.sh | bash
```

- Wow, installing the packages is painful. I'm even on my home WiFi, so I don't think it's the network.

- It's slightly tolerable with
```
npm install --prefer-offline --no-audit
```
but now my Next scripts don't work... `npm run start` complains that there isn't a valid build in `.next` even after running `npm run build` (which exits immediately), as does `npm run dev`... what gives?

- People online suggest to try running `npm install next@latest`
	- This is taking forever...

### Interface

- I want to try adding an accordion for the Constituent Pages section

- Sick!!!

![[Capture 2024-07-06 at 17.51.09.png]]
![[Capture 2024-07-06 at 17.51.39.png]]

- I really like the page path on the right... love it

- Adding a border, drop shadow, and backdrop blur to the navbar
![[Capture 2024-07-06 at 19.53.40.png]]
![[Capture 2024-07-06 at 19.54.01.png]]
![[Capture 2024-07-06 at 19.54.15.png]]

- This looks so dang good 😍

### Learn Page

- Continuing to write documentation
- Writing a lot of the system architecture
- Starting the Place page

- I'm going to bed 😂 at a 'sane' time... 12.37pm

## Sun 7 Jul

- Significantly improving mobile interface
	- Making text way smaller to display at a suitable size
	- Adding a mobile navigation menu
	- Improving text tracking

- This is cool, I can just go to `James-MacBook-Pro.local:3000`

![[Pasted image 20240707142014.png]]

![[Pasted image 20240707142025.png]]

- Continuing to make ground on writing up the `/learn` content

- Hm, could I proxy the `/place` URLs through a server action, such that they can be accessed anywhere that the interface can?
	- I suppose, if the user has access to the interface, they'll have access to the endpoints directly...

- Highlighting the page fragment when navigated to by hash
	- https://github.com/vercel/next.js/discussions/49465

## Mon 8 Jul

- Adding instant key navigation tips to link cards
![[Capture 2024-07-08 at 03.29.34.png]]

- This is _so_ cool.
https://discord.com/channels/1154647250144870412/1154647251193434165/1259582286253522985

- I also guess I am very much not sleeping today... considering it's now 7 am.

- Just lots of small interface things...
- Adding a custom hook for `useKeyPresses`

- Adding some `useCallback()`s
> [!TODO]
> - I want to look at where `useMemo()` would make sense, and other places for `useCallback()`
> - Need to search for all function defs
> - Look at dependency arrays
> - Add exponential backoff to progress bar

## Tue 9 Jul

- Working on progress bar increment function
	- https://prog21.dadgum.com/227.html
	- https://www.desmos.com/calculator/yggantskdu

![[Capture 2024-07-09 at 12.17.02.png]]

- This looks reasonable...
- Currently it's a linear increment of $0.5$, which progresses $55\%$ after 5.5 seconds
- Instead use a $1/n$ relationship, which varies its speed over time

- Tuning it a bit more
- https://www.desmos.com/calculator/4pelacztnu

- Updating screenshots and adding demo video
- Adding captions to image carousels

- Adding new `TypographyKeyInput`
- Fixing accordion hashes

> [!TODO]
> - Huh, this is interesting
> - https://github.com/djkepa/custom-react-hooks/blob/8d254ee7d2d74651dd7b4d50932c38b58544ee8b/packages/use-element-size/src/index.tsx

## Wed 10 Jul

- Addng a 'View a demo' button to the place error

![[Capture 2024-07-10 at 17.39.30.png]]

- Add more system architecture writeup

- Ignore `useKeyPresses()` inside of input elements

## Thu 11 Jul

- Cleaning up `useState`s
- Move growing progress bar to own component
- Perf optimisation for `fragmentIdMap`

- I've been doing a lot more than I've been documenting here...
- It's now deployed on Vercel @ https://p4p.jamesnzl.xyz.
- Although, this now means that the `http://rpi.local...` doesn't work anymore, as you can't serve an HTTP resource from an HTTPS page.
- Bugger.
- I'll look into how to set up an HTTPS proxy for the HTTP service?

- Still need to document things from Discord, and document the nearest target algorithm.
- But, it's also 1am, and I'm losing my mind 🙃

## Fri 12 Jul

> [!TODO]
> Jobs for today:
> - Add missing logbook entries
> - Document Discord + Logbook
> 	- Nearest target algorithm
> 	- Future ideas
> - HTTPS proxy
> 
> - Start the proper mid-year report

- Trying https://raspberrypi.stackexchange.com/a/61801

```sh
# Install Apache
sudo apt-get install apache2

# Enable Apache2 Modules for Proxying & SSL
sudo a2enmod proxy
sudo a2enmod proxy_http
sudo a2enmod ssl

# Apply configurations
sudo systemctl restart apache2

# Create directory and SSL Cert
sudo mkdir /etc/apache2/ssl/

sudo openssl req -x509 -nodes -days 7300 -newkey rsa:2048 -keyout /etc/apache2/ssl/whep.key -out /etc/apache2/ssl/whep.crt

# Fill in Distinguished Name
Country Name (2 letter code) [AU]:NZ
State or Province Name (full name) [Some-State]:Auckland
Locality Name (eg, city) []:Auckland
Organization Name (eg, company) [Internet Widgits Pty Ltd]:p4p-83
Organizational Unit Name (eg, section) []:controller
Common Name (e.g. server FQDN or YOUR name) []:controller
Email Address []:p4p@jamesnzl.xyz

# Create VirtualHost file
sudo vim /etc/apache2/sites-available/001-SecureWhep.conf
```

```xml
<VirtualHost *:443>

    ProxyRequests Off
    SSLProxyEngine On

	<Proxy *>
		Require ip 192.168
	</Proxy>

    ProxyPass / http://localhost:8889/
    # ProxyPassReverse / http://localhost:8889/

    <Location /proxy/>
        ProxyPassReverse /
        Order deny,allow
        Allow from all
    </Location>

    ServerAdmin webmaster@localhost
    ErrorLog ${APACHE_LOG_DIR}/error.log
    CustomLog ${APACHE_LOG_DIR}/access.log combined

    SSLEngine on

    SSLCertificateFile /etc/apache2/ssl/whep.crt
    SSLCertificateKeyFile /etc/apache2/ssl/whep.key

    <FilesMatch "\.(cgi|shtml|phtml|php)$">
        SSLOptions +StdEnvVars
    </FilesMatch>
    <Directory /usr/lib/cgi-bin>
        SSLOptions +StdEnvVars
    </Directory>

    BrowserMatch "MSIE [2-6]" \
        nokeepalive ssl-unclean-shutdown \
        downgrade-1.0 force-response-1.0
    BrowserMatch "MSIE [17-9]" ssl-unclean-shutdown

</VirtualHost>
```

---

- Oh... wait... before I continue...
```yml
# Enable TLS/HTTPS on the WebRTC server.
webrtcEncryption: no
# Path to the server key.
# This can be generated with:
# openssl genrsa -out server.key 2048
# openssl req -new -x509 -sha256 -key server.key -out server.crt -days 3650
webrtcServerKey: server.key
# Path to the server certificate.
webrtcServerCert: server.crt
```

- I'll try configuring these...
- I ⌘F'd through the README and didn't find anything for this...

```sh
openssl genrsa -out server.key 2048
openssl req -new -x509 -sha256 -key server.key -out server.crt -days 7300
```

- Cool, generated a key and changed from `no` to `yes`
- Now the site is accessible through HTTPS, but

![[Capture 2024-07-12 at 15.07.18.png]]
(accessing the page from HTTP)

![[Capture 2024-07-12 at 15.07.33.png]]
(accessing the page from HTTPS)

- This is because of my self-signed certificate.
- I will try to get a certificate from LetsEncrypt

- Need to install `certbot` through `snapd`
```sh
sudo apt update
sudo apt install snapd
sudo snap install core
sudo snap install --classic certbot
sudo ln -s /snap/bin/certbot /usr/bin/certbot
sudo certbot certonly --standalone
```

- Hm, nevermind, this doesn't directly work—the cert is issued for a specific domain that must be accessible from the internet. This means that I can have a cert issued for something like `rpi.jamesnzl.xyz`, but, then the cert wouldn't work for `rpi.local`—and I cannot issue a cert for an IP address.

![[Capture 2024-07-12 at 15.58.51.png]]

- https://community.letsencrypt.org/t/https-for-local-network-only/135173/2
- https://github.com/NginxProxyManager/nginx-proxy-manager/issues/599#issuecomment-694313126
- https://community.letsencrypt.org/t/certificates-for-hosts-on-private-networks/174
- https://community.letsencrypt.org/t/lets-encrypt-for-internal-only-web-management/158154
- https://superuser.com/questions/1690739/ssl-certificates-for-local-web-applications

- I'll first try finish the Apache forward proxy.

---

```sh
# Enable the site
sudo ln -s /etc/apache2/sites-available/001-SecureWhep.conf /etc/apache2/sites-enabled/001-SecureWhep.conf
```

- Ugh. This is accessible as `https://rpi.local/cm3`, but, has the same unsafe error...
- I suppose they basically do the same thing, and the key is still self-signed...

- Oh! Once you trust it... it seems it at least loads properly through the interface...
- I suppose I could just add something that takes the user to the raw page for them to approve?

- I'll just leave it for the time being—I'd require me to:
	- Change the form defaults
	- Change the WebSocket server
	- Add the extra logic to check if the error is an untrusted certificate

## Sat 13 Jul

- Taking the gantry back in

> [!TODO]
> - KaTeX
> 	- Radians
> - `STATIC_IMAGES`
> - 'Cost function'
> 	- Expand into a sum of radius and angle

- A big catch up meeting with Sam

### Calibration

- Print off a grid/checkerboard
- Use the checkerboard to align

### CV so far

- Taking an image with shiny stuff all the way through to centroids
- Only don't have paste

### Major bits of work that aren't on /learn

- Head design
	- Camera + head nozzle 'superposition'
	- Either swivel or go to a fixed camera at the origin—the human operator doesn't want to have to go to origin every time.

- Repeatability testing

- Everything else is on the website

### Research Direction

- How do you do real-time computer vision, for a human operator?
	- Rotation is a negotiation between the operator and CV

- It's (machine vision, for our project) not an _extra_ function added on, it _is_ the function

- Position is user first
	- If we can describe with a mathematical function, and the user can adjust this on-the-fly, 
- Rotation is machine first
- Optimisation is machine first

Mathemtatical model is jsut those three cost functions
- Evaluate for all points
- Itentify the centroids
- and 

### Other To-Dos

- Draw contour plots

- Inverse the damping -> directivity

> [!TODO]
> Multiplexing a single rotary encoder with a range of inputs
> Directivity controlled by a `Slider`

- Like Altium—adjustable grid sizes

- Three layers
	- Zoomed out to make large jumps
		- Centre box shows curent feed
		- Surrounded with a box on all sides with the key to press to go there
	- Standard with CV pounces, mouse will click on the nearest target regardless of position (keys & mouse snap)
	- Zoomed in for fine adjustments with the mouse

- Ability to display the colour key video stream

- Talk about the background with the Joystick

### Directivity

- The 'damping factor' I previously identified is better thought of as the reciprocal of 

```julia
using Plots
using Colors
using Statistics

# Define the number of sectors and the angle for each sector
num_sectors = 4
θ_start = -π / num_sectors
θ_stop = 2π - π/num_sectors

sector_limits = range(θ_start, stop=θ_stop, length=num_sectors + 1)

# Create the polar plot data
r_max = 1  # Maximum radius
num_r_points = 150  # Number of points along each ray
num_θ_points = 750  # Number of points along each sector arc

# Arrays to hold polar coordinates and colors
r = range(0, stop=r_max, length=num_r_points)
θ = range(θ_start, stop=θ_stop, length=num_θ_points)

# The directivity constant.
directivity = 1e-6
filename = "../interface/client/src/app/learn/static-images/targetPlotWeightedNonLinear1u.png"

# Create a gradient for each sector
gradients = [
    range(RGB(1.0, 0.7, 0), stop=RGB(0.0, 0.7, 1), length=trunc(Int, (1+(1/directivity))*num_r_points)),
    range(RGB(0, 1.0, 0), stop=RGB(1, 0.7, 1), length=trunc(Int, (1+(1/directivity))*num_r_points)),
    range(RGB(1.0, 0.7, 0), stop=RGB(0.0, 0.7, 1), length=trunc(Int, (1+(1/directivity))*num_r_points)),
    range(RGB(0, 1.0, 0), stop=RGB(1, 0.7, 1), length=trunc(Int, (1+(1/directivity))*num_r_points)),
    range(RGB(0, 0, 1), stop=RGB(1, 0, 1), length=trunc(Int, (1+(1/directivity))*num_r_points)),
    range(RGB(1, 0, 1), stop=RGB(0, 0, 0), length=trunc(Int, (1+(1/directivity))*num_r_points)),
    range(RGB(0.0, 0.0, 0.0), stop=RGB(0.3, 0.6, 0.9), length=trunc(Int, (1+(1/directivity))*num_r_points)),
]

# Plot each sector with a different gradient
plot()
for i in 1:num_sectors

    θ_sector = θ[(θ .>= sector_limits[i]) .& (θ .< sector_limits[i + 1])]

    θ_search = mean(θ_sector)
    θ_max_deviation = 2π/(2num_sectors)
    println("Search: $θ_search, Range: $θ_max_deviation")

    θ_deviation = abs.(θ_search .- θ_sector)
    weighting = (θ_deviation / (θ_max_deviation)) .+ (1 / directivity)
    println("Deviation: $θ_deviation \n\n $weighting")

    for j in 1:num_r_points
        index = trunc.(Int, (weighting) * j) .+ 1
        scatter!(θ_sector, [r[j]], markercolor=gradients[i][index], markersize=3, markerstrokewidth=0, markershape=:diamond, leg=false, proj=:polar)
    end

end

# Display the plot
p = plot!(size=(1250, 1250), dpi=240, background_color=:transparent, foreground_color=:gray)
savefig(p, filename)
```

- I'm so proud of the /learn page...

- Tomorrow, I'll add the contour plot, then get started on the report + slides

## Sun 14 Jul

- Minor interface changes
- Editing `/learn` content
- Fixing carousel buttons on mobile

### Seminar Slides

- Starting on the slides

```sh
./elegant-underline elegant '#FF00FF' UbuntuSans-Bold 116 "A pick-ard-plare,"
```

```sh
./elegant-underline elegant '#9E7CE1' UbuntuSans-Bold 116 "frr vapid prolotypivg"
```

## Mon 15 Jul

- Working on slides/report

### Seminar Presentation

- The presentation needs to communicate the intent/theory behind what we're building, not what we've built

- Take a photo of the 301 PCB
- Step through the image capture/masking/centroid identification
- Walk it through

- Sketch through some ideas for the nearest target searching

- Animation + video demo showing the same sequence of events + transition betewen

## Tue 16 Jul

- Seminar slides + demo

## Wed 17 Jul

- Seminar slides

## Thu 18 Jul

- Test boards

## Fri 13 Sep

- I've somewhat accepted that this logbook might be fairly scare from here on out...

- This entry is being written on Saturday 14th, and is definitely not complete.

- Sam and I have switched the SD card over from one Raspberry Pi to another, as the other one was mounted on Sam's head mechanism.
- We've mounted the CM3 onto the new head.
- We've mounted the head mechanism onto the rails.
- The belt on the head mechanism is 👌!!
- We've tested the vacuum nozzle with pump, and she works beautifully.
- Sam has updated `run.sh` for the video streaming into a multi-threaded `run.jl`, which is able to composite a second video stream atop the first, ie composite the feed from the sideways-mounted camera viewing the bottom of the picked component atop the feed from the bottom-mounted camera viewing the board.

- We still haven't sorted SSH out.
- Ever since the beginning of this semester (ie since we've been employed as TAs), we've been on the staff WiFi subnet (`staff-wifi-172...`). This means that we can no longer SSH to the Raspberry Pi, at least unless we establish a wired connection over an Ethernet cable.
- With the current head mechanism though, we cannot access the Ethernet port on the Raspberry Pi anymore.
- Sam has set up an ngrok tunnel to `:22`, but this isn't ideal nor a permanent fix.

- In theory, SSH is only required for development—everything is able to run entirely on the Pi (ie the interface, Julia socket server, video streaming). However, there is still the issue of internet access—without connecting the Pi to the internet, the STUN servers are unreachable and hence the WebRTC connection fails. This is somewhat unideal, as it means our machine cannot currently be operated offline. I was halfway through trying to set up a local STUN server, which would solve this, but didn't get very far—and it really isn't the highest priority.

- Filming for Leavers' Night video

## Sat 14 Sep

- In to grind this out
- My to dos:

> [!TODO]
>  - Fix gantry bug
>  - Expand gantry code to support nozzle/pneumatics
> - Take a new look at new gantry firmware...
> 	- ~~Protobufs in C~~
> 	- Nevermind. We'll scrap the new firmware. I will however think about taking the concepts and applying it to the current gantry FW, and Protobufs in CPP
> - Calibration interface
> 	- Test board with checkerboard/grid pattern, common pads
> 	- See 18 July

- Creating a checkerboard pattern for calibration
![[Capture 2024-09-14 at 11.38.18.png]]
### Calibration Process

> [!IMPORTANT]
> The key idea is that calibration happens in `socket.jl` (ie the controller)—that is the translation/adapter that sits between the interface and the gantry firmware.
> The interface is dumb, and should only need to care about the coordinate space of its `<video>` DOM element.
> As the bed height is potentially variable, as is the height of the board, the controller is the only component that can be expected to understand how to translate the normalised numeric data from the interface into the real-distance units for the gantry.

1. Select which grid scale you are calibrating for
2. Align the corner of a grid square exactly underneath the current camera position
	1. Perhaps have a resizeable grid on the overlay, at least to help ensure that the grid axes are orthogonal to the camera

3. Click on the grid corner immediately to the right
	- The interface sends a `TARGET_DELTAS` message to the controller.
4. The gantry moves to where it thinks that position is
5. Click on where that grid corner actually went, or hit space if it is exactly correct
	- The interface sends a `CALIBRATE_DELTAS` message to the controller, which contains the sum of the two vectors. ie, $\text{corrected} - \text{inital}$. The controller then takes this, and re-computes its scalar factors with this information.
1. The gantry will recalculate, and move to that location

2. Click on the grid corner immediately below
3. The gantry moves to where it thinks that position is
4. Click on where that grid corner actually went, or hit space if it is exactly correct
5. The gantry will recalculate, and move to that location

6. Repeat until satisfied

![[IMG_1539.jpeg]]

- Actually, I will leave the calculation of `travelledD` to the controller. This means that `CALIBRATE_DELTAS` will need to know the `targetD` too.

---

1. Click on a calibration point in a diagonal position
  - The interface sends a `TARGET_DELTAS` message to the controller.
2. The gantry moves to where it thinks that position is
3. Click on where that point actually went, or hit space if it is exactly correct
  - The interface sends a `CALIBRATE_DELTAS` message to the controller, which contains the target & real deltas travelled.
4. The controller then takes this `CALIBRATE_DELTAS` payload, and re-computes its scalar factors with this information.

5. Repeat until satisfied

### System Architecture

- Doing some repository maintenance to finally move the `socket.jl` controller to its own `p4p-83/controller` repository, video streaming to `p4p-83/vision`, etc.

- https://stackoverflow.com/a/77465493 this is cool!

## Sun 15 Sep

### Calibration

- This calibration routine works beautifully!
- This is so good.
- I need to update the algorithm to tell the user to calibrate diagonally, as we really want to maximise the distance if possible. If we are only calibrating in one direction, tiny errors can result in large calibration changes (ie 2 travelled pixels for a 1 pixel input would cause a 2x change!)
- I'll correspondingly make the squares a bit bigger too.
- I also probably don't actually want a checkerboard, but rather some nice distinct fiducials/similar.

### Head

- Creating the new PlatformIO project for the head
- This will be basically the same as the gantry, but just for the head
- The idea is that the gantry and head are independent, and should be driven separately by the controller
- I will implement protobufs on this head, so that I can copy it over to the gantry

- Huh, okay, so Sam's stepper shield works with [Grbl](https://github.com/gnea/grbl)... this looks interesting
- At least it is well documented... I'll give it a read and consider it
- If it is well documented, then it will definitely be faster than writing anything myself

- Flashing grbl
- Just following https://github.com/gnea/grbl/wiki/Compiling-Grbl#if-you-are-using-arduino-ide-2x
- Had to swap the cable because I kept getting
```
avrdude: stk500_cmd(): programmer is out of sync
```

- Right, I think I have grbl flashed.
- Tomorrow morning, I'll crimp the headers onto the stepper and give it a crack.
- I'll need to check the datasheet on mecha4makers to get its operating voltage.
- In theory, I can attach the vacuum pump as one of Grbl's special spindle/etc pins, in which case _there is no_ `p4p-83/head`. It'll all just be G-code from the controller to Grbl.

## Mon 16 Sep

- Working on the test boards, but not very consistently... a few minutes at a time.
- Will do the Grbl now

- I'll do a 555 timer LED oscillator circuit (like the Voltera's hello world board) as the circuit

- I have the stepper spinning with Grbl!

### Tue 17 Sep

- Finishing the test board

The board contains variably-spaced fiducial markings to be used in the machine's calibration routine, and a basic operational amplifier oscillator circuit that flashes an array of LEDs when excited with a nine-volt battery.

![[Pasted image 20240917164426.png]]

![[Pasted image 20240917155210.png]]

![[Pasted image 20240917155202.png]]

- Will continue to work on steppers with Grbl

### Wed 18 Sep

- Kavitha submitted the PCB order this morning
- She accidentally paid for the Leavers' Tickets too... the incomplete Leavers' Tickets...
- Spending a lot of time today to fix that problem
- Also SSCC meeting/chat to Kevin... busy day
- Systems strategising with Lucy till 2.30 am

- Crimping another stepper connector and connecting the head steppers to Grbl
- She moves!
- I didn't get very far though, just a few `G1 Y0.2 F50`s

### Thu 19 Sep

- Reading https://3dtechworks.ca/2020/02/12/grbl-settings-and-calibration/ to properly learn how Grbl works
- I will update `controller.jl`to start writing to the head

### Fri 20 Sep

- Will try to run the gantry with relative coordinates instead of absolute coordinates. This should avoid the issues of bounds checking, where a limit switch is hit such that the current position is written outside of the working range, causing future movement commands to completely break.

### Sat 21 Sep

- Let's just try to get this bloody machine working.
- Adding messages between interface and controller for head operations.
- Also passing machine state back to the frontend and adding a HUD for it.
- Switching the vacuum using `M7` and `M9` coolant controls

https://photos.app.goo.gl/wXHyF1tJWszJSF928
- An up day.

### Sun 22 Sep

- Adding `ROTATE_NOZZLE` message
- Rotation is working!!! Mean!!!!
