# Part IV Project

Part IV Project #83 — [James Bao](https://www.linkedin.com/in/jamesnzl/) and [Sam Skinner](https://www.linkedin.com/in/sam-skinner-752347224/), supervised by [Dr. Nitish Patel](https://profiles.auckland.ac.nz/nd-patel).

[Department of Electrical, Computer, and Software Engineering](https://www.auckland.ac.nz/en/engineering/about-the-faculty/electrical-computer-and-software-engineering.html), [The University of Auckland](https://www.auckland.ac.nz/en.html).

## A pick-and-place for rapid prototyping

Modern circuit board designs incorporate surface-mounted electronic components for their smaller sizes and better manufacturability on production lines with industrial pick-and-place machines. Unfortunately for a design engineer tasked with prototyping a board, these machines are too slow to programme and require too much overhead to be used when only a handful of boards are needed. The engineer's only real alternative is to prototype by hand, usually with a pair of tweezers, and either a soldering iron or a stencil with a reflow oven to place the components. All of these options are painstaking, time consuming, and prone to causing ergonomic strain. 

Our project aims to investigate the usage of a lightweight gantry and vacuum pickup system (which is already an option to engineers) but with the addition of light machine vision to assist with the tedious task of component alignment. Unlike all other existing computerised systems, the user will drive the machine in real time. Such a device would be of considerable benefit to a design engineer's productivity and health, and especially for those that may have limited dexterity or impaired vision. 

To this end, the project will involve the assembly of a mechanical pick-and-place machine with fly-by-wire motion control as an initial benchmark; the conception of a mathematical and algorithmic method of interpreting the operator's input; the implementation of a cooperative machine vision algorithm to provide positioning assistance that is responsive to this input; and the integration of these together to create a pick-and-place machine under shared control. 

The end goal is to demonstrate a machine vision algorithm that is sympathetic to the intentions of a human, materialised as part of a pick-and-place machine where control over the placement head is intuitively and effectively shared. 

### Research Outcomes

We intend to research the improvements to accessibility, productivity, and health that could come as a result of light computer assistance. Presently, many machines exist that are either entirely human­controlled or entirely computer numerical-controlled, and relatively few designs deliberately blend the two approaches. 

The unique challenges associated with machine tool control in this manner will require us to iterate upon and blend research from neighbouring fields. This will lead to a better understanding of machine vision algorithms in the context of shared control methods. 

In completing this research, we hope to; 

1. Demonstrate (through formulation and testing) a theoretical model applicable to such machines and applications.

2. Qualitatively profile the benefits, drawbacks, and constraints of machine tools (a pick-and-place) under such a control scheme. This understanding will be developed through trial and error through the process of building the machine and implementing the control scheme and user interface.

3. Quantify the performance of such a shared control method insofar as the human-centric (and thus sometimes subjective) nature of the project allows. Such measurements should include factors such as cost and productivity (assembly time per board), and would be compared across varying levels of computer assistance.

## Project Documentation

> [!important]
> Visit the [GitHub Pages deployment](https://docs.jamesnzl.xyz/p4p) to view this project's documentation.
