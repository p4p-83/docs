#bf5af2/university 

# Head Position Control

https://youtu.be/Q76dMggUH1M?si=5FxmPIebdU4_tlly

I'll explain my idea properly tomorrow but the gist of it was that we should probably hone the scope and research question in on one specific concept so that the project is manageable. Personally I think that the 'snap to pad' feature is the killer feature and it's the one thing that sets this apart from any other device. The existing pneumatic-needle-on-a-gantry ones don't do it, and the industrial ones are unwieldy. This one is useful precisely because it snaps to pads and it's really easy to get it to do that.

And that right there can be taken as the scope. 1) pad snapping, and 2) seamless user interface. That also means there is a good balance between software (CV for the snapping) and hardware (user interface… with haptics?). It opens up some very specific research questions. Admittedly they are not very hard, concrete, nor data-driven, but it is something to write about. Something along the lines of applying computer vision assistance to improve user input, not replace it, and effective ways of seamlessly balancing the two. Had a brief look for papers and there's some stuff in the field but not that much. It seems like a valid research question and not a question that's been answered yet in the form we'd be trying to do it.

[https://doi.org/10.3182/20020721-6-ES-1901.00891](https://doi.org/10.3182/20020721-6-ES-1901.00891 "https://doi.org/10.3182/20020721-6-ES-1901.00891") 
[https://link.springer.com/chapter/10.1007/3-540-45118-8_6](https://link.springer.com/chapter/10.1007/3-540-45118-8_6 "https://link.springer.com/chapter/10.1007/3-540-45118-8_6")

An example of the 'user interface stuff' would be questions like, 'how do we move the component.' And one concept I have is that you could have a controller with a joystick to move the component in x and y, and an encoder to rotate the component. If so, you could have a motorised encoder (think Smart Knob with no screen) and a motorised joystick, and the snap action is just the computer trying to move the controls for you

So essentially whenever you get near a snap point, the controls feel like they're trying to 'fall into' the snap point. As if there's some physical detent or some force pulling the component to the pads. You can override the snap by pushing against it, and that also signals to the computer that maybe you want a different snap point, which triggers it to move.

Yes you could have a normal input device to move the component and a second cursor on screen showing the snap point, and a button to snap to the snap point; and yes, if the UX is not a research focus that's the better idea (to avoid scope creep). But if we choose to make UX a focal point, I think we could make a really cool design with something to write about. "An algorithm allowing the user to nudge the snap point" is not something I've seen in the CV literature.

Oh and also I think it'd just be fun to mess around with a machine with haptic snapping. It'd also be fun to work on. More custom electronics (something the project currently lacks a bit) and something interesting to write the code for.

> [!todo] 
> If relevant, create reference pages using the [[! Referencing Template]] for above papers
