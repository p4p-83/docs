Assume things start off/idle/no holding torque/un-homed until I specify otherwise.

Have some sort of method of setting to 90° vertical at start up. User holds this position as the power / holding torque comes on.

Then have the user align a known mark on the PCB directly underneath the nozzle. Perhaps they just push the gantry around to roughly the centre of its travel and then bump the head into position over the reference mark.

Head lifts up to show downward-facing camera feed.

User clicks on the known mark. This becomes the datum.

Camera feed switches to component camera. User clicks nozzle. This is recognised as the same datum, just the other camera's perspective of it, and the internal optical calibrations are updated.

The camera feeds are now shown overlaid, and there is (well, should be) zero offset between the datums on each.

At this point we can be sure that the nozzle downward position is correct and that the upward position corresponds to this.

We leave the nozzle in the upward position and home the gantry. Ideally we'd track the extent by which it moved and then put it back after homing, but that's not going to be possible during our tenure with this project.

Once the gantry is homed, we are in a comparable state to what we assume we start in presently, and the user can now take control using the front-end and do an optional distance calibration if they so desire.

**How**

I envisage that this will be done by the controller at the backend. This means I can do it and prototype it myself without needing James — good, given this week is systems week. Maybe James will disagree, but I think the the controller is the best place for some of these early steps anyway.

There will therefore be a text-based prompt that guides through each of the steps in real-time and user input will be required to advance to the next step. I'll try to provide decent written descriptions of the nature of each step so that the only vaguely-familiar user can know what to do at what time.

— well, maybe it's mostly backend. I have to handle the clicks on the reference marks somehow.

— maybe I just let the front end come up with default instructions, and spoof the logic at the backend temporarily to do this special behaviour. The front-end won't look any different in the meantime, but the user could just be told to follow the text-based prompts until instructed otherwise.

**Thoughts**

This is probably the best option as it means that we can have the user do simple sense-checking and fault-checking as they go through the steps. This way we don't immediately crash if the startup state is slightly unexpected. It's also good to help familiarise the user with the machine, and also makes a lot of sense given our architecture — this machine works in tandem with the user, so a one-minute setup process where the user starts the subsystems in place fits this ethos nicely.