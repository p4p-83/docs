#bf5af2/university 

# Part IV Project

James Bao and Sam Skinner

> [!note]
> View the $\LaTeX$-rendered copy of this pitch at [[pitch.pdf]].

## Elevator Pitch

> [!summary] Elevator Pitch
> Existing pick-and-place machines are wholly impractical for rapid prototyping. This project aims to implement a computer vision-augmented, zero-setup pick-and-place designed *for* prototyping. The goal of such a machine is to empower hardware engineers to prototype efficiently with a wider range of electronic components than they may otherwise be capable of without machine assistance.

### The research question

> [!question]
> *How could computer vision be employed to augment the design of a zero-setup pick-and-place machine tailored for rapid prototyping?*

## The Problem

Existing pick-and-place machines are cumbersome and entirely impractical for rapid prototyping. Pick-and-place machines are designed for high-volume manufacturing—*once programmed*, existing machines are designed to churn out populated printed circuit boards, placing components by the [[Ultra-High-Speed Modular ZTAR YSM40R Overview|hundreds of thousands per hour]] with extreme efficiency.

Within the context of rapid prototyping, this operating profile is untenable. An engineer enveloped within a prototyping stage of development desires a machine with zero setup that can be used immediately—such a context demands low-setup overhead and processes optimised for low volumes, perhaps a handful of boards for any singular revision. This deems the [[Setup time reduction for electronics assembly|multi-hour setup and programming time]] required for a typical industrial pick-and-place largely infeasible; an effectual non-consideration for rapid prototyping.

Similarly, industrial pick-and-place machines are designed around an ecosystem of tape and reels. These are fantastically versatile when assembling at-scale, but engineers scarcely prototype with 5000 component-count reels; instead preferring cut-tape and loose-in-bag packaging options with the small quantities required. These loose components are wholly unsupported by industrial pick-and-place machines in the worst case, or severely inhibit performance and balloon operating costs in the best case.

For many engineers, this creates an impasse—many engineers struggle to work with the increasingly-smaller packages in which modern electronic devices are available. Some find even 'standard' `0603` components challenging, let alone small `0402` packages or miniscule `0201` devices. This discussion would not be complete without a similar acknowledgement of fine-pitch integrated circuits, or packages with no iron-solderable pads—ball grid array (BGA) or quad-flat no-lead (QFN) devices often cause limitless headaches for inexperienced engineers, or those with degraded fine-motor skills.

The *inaccessibility* of these components renders many electronic components non-prototype-able for many engineers, who are left with only those part numbers also available in larger, easier-to-handle packages. This is a thoroughly imperfect solution, as many modern integrated circuits are simply not produced in more 'accessible' packages, or parasitic, layout, or package-variation issues eventuate in ineffective prototypes that *cannot* accurately model real production results.

*What if we could design a manually-operated pick-and-place machine with zero setup, specifically tailored for rapid prototyping?* Of specific research interest is the potential application of computer vision to augment such a machine, in such a manner to elevate the user experience. Critically, however, any 'intelligent' features should *not* incite any additional setup cost.

## The Outcome

Whilst the full implementation of such a machine would be the ideal outcome of our Part IV project, we must be cautious to temper expectations and to be pragmatic about practicalities. The intention is thus to tackle this project in a series of 'stages', with each sequential stage representing some increase in complexity and implementation difficulty whilst prioritising key research focusses above all else—ultimately, this is a *research* project.

Therefore, this project is to comprise of the following component parts, each represented by a focussing question:
1. How can we circumvent visual occlusions caused by the nozzle, such that the user retains a clear view of the board at all times?
2. How can we intelligently locate ('snap') a component to its respective pads, regardless of the presence of applied solder paste?
3. Is it feasible to apply solder paste directly with the machine? If so, does this produce *better* system performance than pre-applied paste?
4. How can we identify the optimal location at which to pick up a component?
5. Is there a need or benefit to an increase in the degrees of freedom of the head?
