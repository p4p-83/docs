#bf5af2/university 

# Custom Vacuum Head

So a little pricier than I thought (and this is a US vendor… ouch) but you can buy these to pull a vacuum if you already have a compressed air supply
https://www.mcmaster.com/products/vacuums/fixed-flow-air-powered-vacuum-pumps-7/

You can switch a compressed air line with these
https://www.mcmaster.com/products/compressed-air-control-valves/

And I made it sound way more difficult than it would be but essentially you mount the head on some linear rail (which constrains its motion to the z axis) and use something like this to lift it whenever you need to lift it
https://www.mcmaster.com/products/air-cylinders/actuation-type~single-acting/linear-air-cylinder-type~round-body/

Using a piston allows you to quickly fire the component upwards to max stroke. Then gravity pulls it down. To limit the maximum downwards force (ie offset the weight) you either:
Use a spring to provide a static upwards force
Use a pulley and counterweight to provide a more accurate counterbalance
Install a regulator valve as a parallel piston feed line. Essentially you would switch between full pressure (piston goes hard up) and low pressure (but not off: the piston therefore still provides some upwards force and still acts to lift the head up a bit) and that eliminates the need for a spring etc

I'll leave off the idea of compressed air head actions for now because 1) Nitish thinks it should wait, 2) it requires compressed air, and 3) it's fairly pricey, but it has its advantages. 1) You run a single feed line to a piston on the head, so all the heavy stuff is off the machine. No need to push a stepper motor around. 2) It is fast. Way faster than trying to spin a screw up and down. Rise/fall times would be less than a second. 3) The biggest advantage is that it's constant force. That's where pneumatics shine. A stepper with a screw controls displacement. The pneumatics directly control seating force without any control loop
