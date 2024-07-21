Pretty crude attempt at a photon budget, but it's informative nonetheless.

I discovered yesterday that the AD630 chip from [0713 Off-the-shelf version of yesterday's idea](0713%20Off-the-shelf%20version%20of%20yesterday's%20idea.md) is about $70 and that the Hamamatsu chips are rather difficult to procure. The only one I could find on element14 had digital output — no good. So, this is to see if I could easily implement it myself.

I've simplified the design (at least temporarily) to include an [0603 LED](https://www.digikey.com/en/products/detail/liteon/LTST-C191KRKT/386837) (store stock [DIO000109](https://share.engineering.auckland.ac.nz/ece/ts/_layouts/listform.aspx?PageType=4&ListId={61BA02A2-09AE-43D9-928A-8404626AA3B3}&ID=688&ContentTypeID=0x0100F15804224016034EA9B87F5470A936F5)) separated from a [photodiode](http://share.engineering.auckland.ac.nz/ece/ts/Student%20Documents/SD%20019-101-411.pdf) (store stock also, [OTH000002](https://share.engineering.auckland.ac.nz/ece/ts/_layouts/listform.aspx?PageType=4&ListId={61BA02A2-09AE-43D9-928A-8404626AA3B3}&ID=680&ContentTypeID=0x0100F15804224016034EA9B87F5470A936F5)) by a 50 mm air gap. No other optical components. The idea is that both dies are quite small, and therefore the direct path of the light will only be about half a mm in diameter — place this right, and it'll be easy to interrupt with a flange on the placement nozzle or the like. (This is the engineering potato version of the setup just to gauge the difficulty of the electronics.)

Now, ideally, I'll skip over the electronics pretty much entirely. My goal is to feed a the output of a transimpedance amplifier directly into a microcontroller. I'll do lock-in detection digitally. This of course requires sufficient dynamic range. I'll use daylight as the worst case bias, and check how much bigger that would be than the received signal.

![](Page%201,%20object%205-1%201.jpg)

Daylight will put 262.5 µW on the photodiode, and the LED will put 3.195 µW on it. Thus daylight will be 6.4 bits brighter. For a 12-bit ADC (true resolution maybe 9 bits) that would give us 3-6 bits spare to measure the level of the LED. That should be enough.

Note that I could actually low-pass filter the signal to remove the dc, which will get rid of that part of the signal. Note also that 1050 W/m² is a figure you'd probably use for a solar panel, which is pointed at the sun and in direct, outdoor sunlight. Based on the machine geometry, direct sunlight will be very rare, and it'll be dropped in intensity by windows and whatnot in its path anyway. Therefore, this dc bias is kind of the extreme case.

Noise will pose a bigger challenge, and the next step would be to take these figures and work out what kind of noise I can expect from the sunlight (which will remain even after filtration) and perhaps from the amplifiers. However, this will be using a lock-in detector, so as long as I haven't clipped the signal, I should be able to deal with the noise to a fair degree.

Conclusion: I can feed this into an ADC and do the lock-in detection with DSP methods with at least 3 bits (but probably several more) of desired signal. (This is better than the Hamamatsu chip, which gives 1 bit of info.)

---

At 0.4 A/W on that photodiode, 3.195 W of incident LED power would give me a 1.278 µA received signal. (The sunlight would cause maybe 100 µA or so.)