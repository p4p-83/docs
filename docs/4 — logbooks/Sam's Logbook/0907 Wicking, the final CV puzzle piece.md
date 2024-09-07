My success with the rotational stuff earlier today went to my head, and I decided that the wicking step surely couldn't be that hard, so I had a go at it. (I also have a photonics assignment that I'm trying to productively avoid.)

My implementation does not work the way I thought it would, but if this morning's experience is anything to go by then that's probably why it actually does work.

Basically, we assume that the previous two steps got all of the pads aligned to a pretty good degree — this means that we can make pairs of pads and leads, 1:1, and analyse the quality of fit of each pair. Mathematically, I just calculate the mean positional error and mean angular error and then make a correction by that amount.

## Graphical explanation

We take a haphazardly simulated input (this is supposed to be the output of the rotation stage).

![](before-wicking.png)

We do some vector math to calculate the individual errors (little white arrows — you might need to view this page in dark mode) and then the mean errors. The mean errors are a little difficult to represent visually, but I've tried to show the average positional error in blue and the average rotational error with the grey dashed line (which deviates from the $y$ axis by the error angle).

![](analysed-wicking%201.png)

We then just apply opposite positional and rotational offsets to the pads to correct the error. (Well, that's what we do in simulation. On the machine we make this correction by jogging the head.)

![](after-wicking.png)