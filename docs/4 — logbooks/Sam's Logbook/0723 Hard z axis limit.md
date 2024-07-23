Developing a z axis limit switch is proving difficult.

- It's not clear what feature on the nozzle I would use to interrupt an optical style limit switch.
- I could try for a limit switch that looks at the amount of (green) light being reflected from a small spot, but I don't know where I'd fit the lens and other optics that would be required to make that spot.
- Today I began looking into a magnetic / inductive style limit. Basically I'd put a coil of wire around the spring on the nozzle. In theory, the inductance of the coil would vary as the spring is compressed and the bit of metal that it's wrapped around moved away, but I'm not sure how significant this effect would be relative to fluctuations that may happen in the environment.

What if I flipped the problem on its head? What if the limit switch doesn't need to be part of the existing nozzle assembly, but could still be concentric? This idea stems from a parallel need: I still need to create some kind of backlight.

Perhaps then I create a cone with a small hole in the end. The hole allows only the very tip of the nozzle to pass through. The end of the cone sits maybe 1 mm back from the end of the nozzle. The nozzle's travel is therefore quite heavily restricted, but this probably doesn't make too much difference.

Why a cone? It allows me to fire the backlight LEDs at it from the sides, yet still see the backlight reflected (at least partly) forwards. This means that all of the LEDs can very easily be put behind the picked component, reducing spill by a degree without creating optical nightmares. It also has the advantage of allowing the central section to be hollow, and the nozzle geometry is such that a cone would fit around really pretty nicely.

I then use the cone itself as the moving part of the limit switch. Depending on how I support the cone, I can then make and break electrical contact with it. (I could still use more involved methods if desired, but just getting some gold foil and using it to make some switch contacts seems pretty easy.)

And the motion of the cone? I'd support it in such a way as to provide it with a limited amount of travel. Thus, after the nozzle contacts the component, there is ~1 mm travel until the component starts contacting the shroud. Then, ~1 mm later, the shroud makes switch contact. Perhaps ~1 mm after that, if the electronics have failed to register that the limit has been reached, the cone bottoms out. The nozzle itself has ~6 mm travel, so it won't sustain any damage. Instead, the full load of the z axis will be supported by the cone. Ideally it wouldn't come to this, but if it did there would be a (small) chance that the cone would protect the nozzle, and some other part of the system would give.

There shouldn't be any problem with the cone pushing the component off, as it nominally sits ~1 mm back. If the component is touching the cone, it's because something is pushing it into the cone, and therefore the component isn't going anywhere.