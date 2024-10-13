> [!NOTE] I originally drafted this as an appendix for my final report, but eventually decided it didn't belong there and cut it. However, it might be helpful here, so I'm reproducing this copy.

While this logbook contains much of the software and electronics work that I did for this project and is organised chronologically, it is hardly the easiest thing to follow. Here’s a brief guide to how our research looked while we were in the thick of it.

# Mechanical design

The mechanical design of the machine frame and enclosure is not something I have hard documentation for. The blue frame we used was originally a Makeblock-branded pen plotter, one that I heavily reconfigured. The woodwork was all done based on existing machine dimensions and figured out at the saw, rather than in CAD. (Only the head mechanism was properly designed in CAD, and this not until much later.)

# OpenCV, ImageSegmentation, and custom CV code

While I always held out hope for a simple, custom solution to our CV problem, we expected for a long time that OpenCV would be required or perhaps the quickest option. In the middle of the year I tried to give it a go, but didn’t get very far — I wasn’t so keen on learning Python just to use OpenCV.

I subsequently tried out some of the existing Julia packages, but found them both too slow for real-time use and a bit limited in functionality. They didn’t directly solve my problem.

I wrote some Julia code that tried to directly solve our problem, and it worked. I rewrote it in C, and it was fast enough. I left it there — the C implementation was more than good enough.

# Nozzle cone (*z*-axis limit switch)

The non-preprogrammed nature of our machine means that we don’t know how tall a component is before it is picked. This would make it pretty easy to crash the nozzle (with varying degrees of severity, depending on the height of the component and the strength of the motors) without some form of limit switch or other way of detecting component height.

We settled on a limit switch that did triple duty: if you put a hollow cone-shaped thing around the nozzle with inbuilt switch contacts, you can detect the cone’s upward motion as an indication that you’ve made contact with a component (and thus stop the nozzle’s descent); you can use the cone as a nice, even backdrop for the second camera feed that makes compositing and masking more predictable; and you can also design the cone with limited travel so that if you do accidentally crash the nozzle (whether by bad code or otherwise) there is something solid that will bear the full load of the steppers and physically protect the more delicate nozzle within.

At least, that was the plan. It took a while to get it right, and this ranged from the initial hard *z*-axis limit idea, through and optical method (when I was afraid that contacts would be too likely to corrode), then through a magnetic method (when optics was hard, but I soon realised magnetics were harder), and finally resulted in the $5 Mitre10 copper washer method in use at the end of the project (on the grounds that with a high enough drive voltage and switching current, it should be possible to break through mild levels of oxidation). The final result here still isn’t ideal — gold-plated contacts would inspire more confidence long-term — but it is as good as we were able to do and sufficient to make the claims that I have made in this report.

# Head mechanism design

This is fairly well documented, relatively speaking, in that it has already appeared in this report (so should be familiar) and all entries in my logbook describing it tend to refer to it by this same name.

# Assorted Julia code

I did a bunch of tests that weren’t CV related just to gauge the difficulty of various tasks using Julia, partly in the hopes of making visualisations for both immediate understanding and eventual presentation.

# Rotation and wicking code

It’s here that I prove that centroids are a viable CV output by assuming that they form the input to rotation and wicking code, and then coming up with algorithms that achieve the correct results. I’ll admit it’s not a direct test, but again, it provided sufficient evidence for me to define CV-compatibility in this report with the communicated level of confidence.

# Where we left off

We managed to get most of the components integrated together and vaguely behaving, but the one big thing left out was the CV. There was no true CV assistance in the final machine. There are really two reasons for this. Firstly, we ran out of time to properly investigate paste detection and implement the required lighting setup to get masking right. Secondly, the user interface lacked the required logic to support selection of a reference lead, and the controller code lacked the required provisions to coordinate this selection with the front end and did not already have the ability to track the reference lead as the nozzle rotated. The front end also did not have a concept of reference lead when it came to positioning, nor an easy visual way to input rotation, nor the states and prompts to take you through the whole process with optional manual overrides. None of these tasks in and of themselves are that difficult to implement on paper, but collectively they represented a significant workload that we really didn’t have the time for — there were higher priorities more directly related to the research. As far as our real research was concerned, it was enough to simply know that CV is possible (despite the inevitable integration headaches, and even if I might be personally disappointed that we were never able to demonstrate it in action).