Playstation controller for user interaction? Actually maybe not a console controller specifically, but something heavy with a good joystick (*x*-*y* movement), encoder (rotation), and a bunch of really nice mechanical keys.

I’m thinking something like the Davinci Resolve interface boards. Or something as seamless as using Final Cut Pro when you have the keybindings down-pat. The interface needs to promote a state of flow.

The head itself needs to be movable by the user, but tedious stuff like z-axis motion should be handled with a simple keypress. Not even directly: there should be a “pick the nearest component up” button to grab something, and there should be a “place at crosshair” button to release it. Better yet, we have a “place at shown snap location” button.

Snapping is critical. Perhaps we can treat that like the shift key on a keyboard. Press shift and you get one capital letter. Press snap and you get one perfectly aligned placement at the nearest set of pads. The best bit is caps lock: if you need a lot of capital letters, caps lock has your back. Snap lock would too.

But the idea of simplification remains. A PCB is a flat board. There is no information in z. The up-down axis only exists so that we can move the nozzle from A to B without scraping all the components off in the path. On these grounds, the z-axis should really only be a solenoid. Something snappy.

Say we have a light-weight nozzle elastically connected to the plunger using a spring (and maybe that spring is the air hose). This allows us to snap up and down, and the spring provides a fairly constant downwards force when the nozzle is pushed into the board. This means the nozzle will conform with height of different components naturally — and it will also seat the components reliably and push them into the paste just enough to stay put.

To me, the whole idea is that the device is as simple as possible so that there is an absolute minimum of stuff in between you and your prototyping.

That's the mechanical control perspective, but the software still deserves some thought. If we've treated the physical control aspects like an analog oscilloscope, with all the key actions having a physical button, and with the state of the machine clearly visible from the physical state of its front pane, we've done well. Even better though is to use the available digital aspects to add a little gloss on top. Can we make it so that the movements you make when placing components are repeatable across multiple boards? Is it possible to use a node-based (block diagram) approach?

Here’s what I’m thinking. Engineers love block diagrams. They’re visual, simplistic, and easy to comprehend. They’re also really versatile.

Scratch is a really intuitive programming language which works well as a teaching aid. It’s very easy to get started with, and that’s its attraction. It’s therefore unimportant in its use-case that it is time consuming to write a big program in.

Block diagrams also show up in software for creative arts, except the blocks are typically called ‘nodes.’ The most distinctive example is Blackmagic Design’s professional video software. DaVinci Resolve employs nodes to visually represent recolouring effects, and their compositing software Fusion employs a similar method.

People just tend to find interconnecting blocks logical and intuitive and can build things from them experimentally. LEGO isn’t popular without reason.

This relates to pick-and-place machines in an interesting way. What if we could use nodes to program a pick and place? What if you could *teach* the pick and place how to populate your board?

The simplest case of memory in such a machine is when you have several identical boards to place. Ideally you place one board and then the machine repeats your steps to give you several more identical boards. This would be an extremely favourable cost-benefit ratio: there’s no setup time, the first board is fast because you have assistance, and the subsequent boards are virtually free.

That’s not too difficult — just memorise the coordinates. What about panels? It’s the same board each time, just displaced in *x* and *y*. Perhaps you just program an offset on to each of the above steps.

That’s about where the extensibility stops with these simple solutions. But perhaps we borrow the node-based approach. **What if the pick and place generated a block diagram of your steps as you worked live?** What if it could have edit→undo? What if you could flick out of the live placement mode and over to a computer screen, manipulate the nodes, add a for loop, and watch as the remaining subassemblies populated themselves? This would represent the ultimate in modularity.

Let’s say you wanted to place a capacitor grid. You place one cap, then you add a ‘repeat in *x*’ node, just like you might in an MCAD package. You set the bounds visually using the physical board, make a few test placements to get a feel for it, and then run the rest programatically. You then run that ‘repeat in *x*’ through a ‘repeat in *y*’, and hey presto, your grid populates itself.

This is clearly distinct from any method using Gerbers, because you have to generate the pick and place instructions from ECAD to do anything with Gerbers. You then have to home the head and possibly do a dry-run to make sure everything is right. Gerbers are good for big runs where you can’t afford to have a human inspect everything for correctness and where accuracy is everything. That’s not what this machine does. This machine snaps components to pads organically. Tolerances aren’t exact. It has a few simple machine vision routines and it exploits them to turn a whim into a component on the board. It makes sense to speed the process up by scripting in the same organic manner as the machine is operated.

I like the idea of **nodes because they provide a modular and dead simple solution to a really difficult and open-ended problem.** They also check the box of extensibility: let’s say someone in the future wants the ability to work with Gerbers. How best to integrate such a feature with out device? Write it as a back-end for a ‘place Gerber’ node and let the user drag it into their routine just the same as elsewhere.

A self-generating node diagram could truely be the killer feature. After the first board, you save the node diagram to your hard drive. Making the next board is going to be a trivial operation, and flexible too. It won’t matter if you did the first board ten minutes ago or ten weeks ago: just open the node diagram and get placing.

Save-load could even in future be even better than gerbers: if you revise the board, a pad might move. With machine vision, the moved pad will raise a snapping error and pause the placement operation. This means you can run an earlier board’s node diagram until the machine complains, change a few nodes interactively, then run the rest and call it a day. There are speed enhancements wherever you look!