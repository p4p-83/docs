Yeah, as usual, I didn't take comprehensive minutes. However, we did discuss

- HUD components
- preferences adjustment in a sidebar on the place feed
- various physical settings interfaces (that we have no current plans to pursue)
- overall state of the project
- research, and how well our project aligns with the stuff in our lit reviews
- how the presentation can be structured (basically a hamburger essay structure but using problem–experiment–results as a stand-in for intro–body–conclusion).

# Research

Information theory isn't so useful as a synthesis tool, but does prove useful when we're doing A/B testing/comparisons and want to explain mathematically why one might be quicker for a user to use. This is useful as it gives us a more rigorous framework than what we'd otherwise have to hand — we don't have the ethics approval (yet) nor volume of test subjects to come up with solid evidence using statistics and focus groups.

Existing literature on shared control works in black and white (game theory employed is win/lose, master/slave) but with continuous input. Ours is a discrete input. There are parallels to be drawn and thus I think that the existing research can be *related* to ours, and we can explain those parallels and that would in itself be a contribution, but the existing research for this reason does not directly/immediately assist us (due to this difference in modus operandi).

Machine vision literature … well, neither of us really discussed much of that in the lit review, and even now, most of what we're doing is mathematical / simple image processing steps and therefore implementation work. The stuff that gets us from camera input to centroids is hardly novel.

However, we have realised that the CV really touches everything in our design — you can't explain why any given component is the way it is without discussing the CV's needs. Perhaps then the most valuable contribution of our project is in charting the way and then profiling CV in this context. Ie, the inputs to the CV, and the outputs from the CV, and how that makes the machine work. While we are on the whole looking into shared control, we really are in a better position to discuss CV's role and requirements within this, and how we have integrated CV with the human input and mechanical constraints.

# Presentation structure

![](Page%201,%20object%205%201.jpg)

Kind of like the fractal structure of a film: you break it up into problem–experiment–results (as per the guidance), and these are like acts. Then you further break it up into little problem—little experiment—little result and then use this as the body of the experiment section, because our whole machine *is* the experiment.

The big problem is the human-facing problem we're trying to address: difficulty placing small SMT components. The experiment is therefore our best guess at how to address this: it's our prototype machine and implementation. Then the results are mostly yet to come, but will basically be a summary of its viability and efficacy and performance.

Within the experiment, we discuss the smaller problems (which could reasonably be said to be the various subsystems), the prototype settled on (experiment) and the results found. We'll have a decent number of results for these, and some of these are decently mathematical.

This structure allows us to focus in on the more researchy aspects and also explain things in a more organised way. As noted in the structure draft above, we'll be able to tick off the three listed project aims that we identified way back in our proposal and explain how what we're doing advances these outcomes.

## The body

One target outcome of the project is to describe the user's interaction with the machine mathematically. Let's use the three cost functions as a pretty accurate proxy for this, at least for now. We have one cost function down, and we have an idea of how the other two will go. This is one "paragraph" of the "body" of our "essay".

Maybe two other paragraphs are in order?

I could talk about the mechanical aspects. Certainly this is a very crucial part. The mathematical interaction works with the centroids output from the CV; the mechanical part sets the CV up for success. We can keep with the "touching the CV" theme and therefore I can discuss the critical design decisions that support the CV's operation / proposed operation.

That leaves us to pick a third topic. (I think at least three are necessary; more would be okay but we may find we lack adequate time to discuss many more than that clearly.) Probably the biggest topic left unaddressed here would be the joystick, or rather, our decision against using one, and therefore our new approach to the human-computer interaction. Perhaps we could indeed discuss this if James doesn't cover it as part of the mathematical models — and if we do cover it, it would make sense to put it before discussing the models, as it might provide some necessary context. (It would be a good time to justify our research into snapping.)

So, maybe those are our three points?

1. Interaction
2. Mathematical models
3. Mechanical considerations

Have I overlooked anything?

## The conclusion

The section on overall results. As discussed in the meeting, outcome #2 (qualitative) is best discussed and summarised at a subsystem level. Qualitative findings are most applicable to a specific component. Put another way, I don't expect to be able to make any overly intelligent qualitative remarks about the final design — "it works" is probably not insightful for a research report. Thus I'd argue that outcome #3 (quantitative) is perfect for the overall summary, as the roles are completely reversed. It'd be overly difficult to quantify a subsystem in isolation, but it's pretty easy to do, say, an overall speed comparison of this whole system vs tweezers.

You could summarise by saying we are specific with the words (by applying them to very defined subsystems) and more general with the numbers (by measuring holistic aspects). This means that the words and numbers will be equally rigorous, and both as useful as possible. But, because the numbers are therefore what we use as an overall description of the project, we'll finish our presentation with the overall results section, and discuss how our next steps for the second half of the year will make progress towards these metrics.