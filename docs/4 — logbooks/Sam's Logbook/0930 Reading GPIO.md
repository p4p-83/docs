![](Screenshot%202024-09-30%20at%2022.27.33.png)

So many GPIO libraries don't work on Raspberry Pi 5. PiGPIO.jl is out; it relies on pigpiod which isn't going to support the Pi 5. BaremetalPi.jl doesn't work either. Some of the Python libraries don't work. WiringPi says it works but apt refuses to install it. I give up.

Raspberry Pi provides a pretty bare-bones command line tool called `pinctrl` which is built-in and does not seem to need root perms. I'm just going to do some string processing to make that work.

```julia
getState(pin::Int)::Bool = split(read(`pinctrl get $pin`, String), " | ")[2][1:2] == "hi"
```

Running with no args lets you get an idea of what the format is.

![](Screenshot%202024-09-30%20at%2022.31.22.png)

With `pinctrl help` you can see the definitions of the keys

![](Screenshot%202024-09-30%20at%2022.32.02.png)

And my example usage is reproduced in the first photo.

This has turned out to be unbelievably painful (a good 4-5 hours) just to get to some hacky string `split()` method… You would have thought that RPi would have made this a bit easier, given accessible GPIO is one of their supposed selling features.