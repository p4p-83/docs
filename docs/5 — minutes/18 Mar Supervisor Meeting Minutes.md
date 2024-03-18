### Supervisor Meeting

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
---
- James to come in and start working on gantry & robotic arm
- James to begin creating test PCB to allow for benchmarking performance
---
- Sam to write down the various scenarios, pros and cons of each, and we explore one of them before the submission date
- Sam to begin producing prototype of mechanical head assembly
- Nitish recommends to use off-the-shelf components where possible
---
- To look into getting a lens protector for the camera
- Need to get the 27W Raspberry Pi 5 power supplies
- Sam to provide a full list of other mechanical/pneumatic parts to buy
---
- Risk assessment essentially completed, James and Sam just to finish the risk assessment matrix and submit to Canvas

#### Minutes

- James has completed an exploratory investigation into the Raspberry Pi & high quality camera
- Can stream output to laptop, mount Linux `rootfs` SD card on laptop, etc.
- SSH into the Pi, including on the University network
- Look into getting a lens protector
---
- James and Sam need the Raspberry Pi 27W (5V, 5A) USB PD power supplies ([$22 from PB Tech](https://www.pbtech.co.nz/product/SEVRBP0511/Raspberry-Pi-Official-27W-Power-Supply-White-AU-Pl))
---
- Dropped joystick ideas in favour of keyboard due to time management/other implementation considerations like zero point, project scope
- Gone more down a video game direction for user input/interface/visual feedback
- eg field of view feedback
---
- Sam recently working on the pickup head, with a small mockup
---
- In terms of literature, James intending to focus into:
	- Image processing algorithms; keying
	- Integration of machine vision with human input; shared control
	- General fly-by-wire controls
- Nitish suggested looking into AI techniques for decluttering
- Purely for the literature review
- A reference clean PCB image from the PDF
- Overlaid onto a location, and matching the camera data onto the PDF image
---
- Also algorithms required for the 6DOF control
- The robot itself is actually 4DOF, but look into the relevant algorithms/coordinate transforms
---
- Sam has been thinking about lots of ideas for the vacuum head
- Hoeken linkage
- Need to start laser cutting a prototype and looking at feasibility
- Operate in a horizontal plane rather than vertically
---
- Sam discussing force sensing considerations/design
- To provide Nitish with a list of parts to buy—vacuum pump, and other needs
---
- Laser targeting of the nozzle/head
---
- Sam to write down the various scenarios, pros and cons of each, and we explore one of them before the submission date
---
- Discussion about benchmarking the performance of the two mechanisms
- Frosted glass to act as an optical filter, with a bottom-mounted camera that is looking up to see where the head is (when it is lowered to touch the glass)
- Some electrical solution with a test PCB that implements continuity tests
- Varying pad sizes, etc., to benchmark repeatability & precision
- We want some automated benchmark method
---
- Sam asking about the University's preferred suppliers for mechanical parts
- Not a hard and fast rule
---
- James to look into ROS (Robot Operating System)
- Gold standard in robotics/research for a while now
---
- Risk assessment essentially completed, James and Sam just to finish the risk assessment matrix and submit to Canvas
---
- Discussion about final project requirements re: connected computer
- Eventually have the two Raspberry Pis running everything standalone, for now use our computers for development
---
- Discussion about network switch/router to access Pi/VNC
- James has managed to get SSH access to the Pi over WiFi though
---
- More of Sam's head discussion
- Geneva mechanism, crank slider
- Two brass tubes
- Nitish recommends to use off-the-shelf components where possible
- Profile the wear of the tubes
- Buy a few of the pumps
