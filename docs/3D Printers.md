#bf5af2/university 

# 3D Printers

Another available option for our [[Gantry System]] is to adapt an existing 3D printer, which has a number of benefits/points of attraction for us:

1. Requires more in-context development than the [[LitePlacer]] or [[LumenPnP]].
2. If built atop an open-source platform, we can produce a DIY instruction kit.
3. Has motor drive/control already implemented.
4. Will likely have sufficient precision/accuracy to meet our specifications.

Looking into the available [filament 3D printers](https://www.jaycar.co.nz/3d-printing/filament-3d-printing/filament-3d-printers/c/10AE) from Jaycar, we will compare each product:

- [[Voxelab Aquila X2]] (NZD$335.00, discontinued clearance stock)
- [[Creality Ender-3 V2 Neo]] (NZD$399.00, discontinued clearance stock)
- [[Creality Ender-3 V3 SE]] (NZD$419.00, new product)
- [[Creality Ender-3 S1 Pro]] (NZD$899.00)
- [[Creality Ender-3 S1 Plus]] (NZD$959.00)

Unfortunately, none of these are maintained open-source—the Creality Ender-3 supposedly has an open-source [GitHub repository](https://github.com/Creality3DPrinting/Ender-3), but this has not been updated since 2019 and does not specify the specific Ender-3 version/variant. The [[Voxelab Aquila X2]] appears to have a repository at [`Voxelab-64/Aquila_X2`](https://github.com/Voxelab-64/Aquila_X2) with its source code, but there does not appear to be any documentation available. 

There do appear to be actively maintained third-party repositories however, such as [`mriscoc/Ender3V2S1`](https://github.com/mriscoc/Ender3V2S1) for the [[Creality Ender-3 S1 Pro]] and [[Creality Ender-3 S1 Plus]] printers.

These repositories all appear to feature the term '[[Marlin]]'—which seems to be an open-source firmware/driver for 'most of the world's 3D printers'—this will require more investigation in due course.
