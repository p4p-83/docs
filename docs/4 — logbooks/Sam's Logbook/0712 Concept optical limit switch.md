Fire an LED at an aluminium tape strip (or some other reflective/retroreflective surface). Point a photodiode at the same strip. The idea is that you'll detect the presence of the strip by looking for a noticeable increase in the LED light hitting the photodiode due to it bouncing off the reflector.

This will only work if we can eliminate the effect of room light. My plan for this is to chop the LED signal so that it becomes ac — just flick it on and off really fast. Then you can filter the output of the photodiode preamplifier and set up a narrow band pass filter that responds only at the frequency of the LED blinking.

At least, that's the janky explanation of it. A more technical and better implementation goes as follows.

You feed the LED a square wave. This is the carrier wave. The modulation is done by the tinfoil that's going in and out of the optical path. Therefore the photodiode receives (among other signals) a amplitude-modulated signal, where the amplitude corresponds with limit switch position.

The unique part of this arrangement is that you have a detector, but the signal processing circuitry can also know the exact broadcast. That is, you can compare the signal at the photodiode to the raw broadcast exactly — same frequency, same phase.

I therefore will use a direct conversion receiver. This will basically be a IQ demodulator, but minus the quadrature branch, given that we're phase locked and therefore only have the in-phase part of the signal by definition. This detector is fairly simple, and its implementation should be even simpler given that we're working with a square wave.

Basically, I'll multiply the square carrier wave signal as broadcast together with the incoming signal off the photodiode amplifier. This can be done pretty simply by just inverting the incoming signal whenever the carrier is low (or high, doesn't matter which polarity) and leaving it upright otherwise. Then I should be able to integrate over a period of time (read: shove it through an RC filter) and this will give a pretty accurate representation of the size of the carrier signal within the noisy spectrum coming off the photodiode amplifier. The theory behind this is just the theory of the FFT (and also the IQ receiver, oddly enough).

You just feed that output into a comparator. You'd set the threshold with a trimpot so that you can adjust for correct limit operation. Probably you'd also do some degree of beam shaping with a 3D printed housing or something to further increase selectivity / repeatability.

Terrible description, but that should be enough to stop me forgetting the idea. In a nutshell, it's radio engineering technique to almost perfectly isolate the desirable signal from all others (way better than I could do with any RC filter that I could construct).

---

This is far from a new idea. I've just had a look on Wikipedia, and this type of device has a name: it's a [lock-in amplifier](https://en.wikipedia.org/wiki/Lock-in_amplifier). Probably I could have saved myself a lot of writing just to say that I'll build a lock-in amplifier.

Here's a handy diagram that pretty well covers off what I'm doing here. (Only change is that the light source will be an LED and the chopper will therefore be a MOSFET.) <https://en.wikipedia.org/wiki/Lock-in_amplifier#/media/File:Lock-in_amplifier_experimental_setup.svg>

---

Probably I'll make this with opamps, comparators, and maybe a 555 for my carrier generation. I could perhaps take the embedded route too, but there are already far too many digital things in this project. Let's bring some electronics along.

---

Yes, I could also try to make a switch of sorts. In theory a mechanical solution would be simpler. However, I'm yet to think up a design that's sufficiently simple that feels like it would also be reliable. (Switch contacts corrode over time and I have no idea how this will affect them.)

---

In theory, a lock-in detection method like this might be on the table as a substitute for chroma keying — but this would require a reasonably high frame-rate video feed to get even close to the required bandwidth.