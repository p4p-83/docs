In case I need it later, here's the dodgy code I used to create the overlay data file from a set of test frames. (It's not my finest work, but it does the trick and I don't expect to need it again. I'm mostly just recording it because without it, the format of my existing data files will be absolutely opaque.)

```
using Images, Serialization

files = readdir()

imageNames = []

for f in files
	num = try
		parse(Int, split(f, ".")[1])
	catch
		continue
	end
	println(num)
	push!(imageNames, f)
end

images = []

for f in imageNames
	push!(images, load(f))
end

yuvImages = []

rawImages = []
composite = []

# i = images[1]
for i in images
	i2 = YCbCr.(i)
	i3 = UInt8.(round.(channelview(i2)))
	y = i3[1, :, :]'
	# y[y .≈ 16.0] .= 0.0
	u = i3[2, 1:2:end, 1:2:end]'
	v = i3[3, 1:2:end, 1:2:end]'

	c = .![px ≈ 16.0 for px in y]
	push!(composite, [c[:]; c[1:2:end, 1:2:end][:]; c[1:2:end, 1:2:end][:]])
	
	push!(yuvImages, [y, u, v])
	push!(rawImages, [y[:]; u[:]; v[:]])

end

serialize("suspiciousfile", [composite, rawImages])
```

Assumes you're working in a directory with the frames saved as `000001.png` and so forth.

I've opted to do as much preprocessing as possible in advance here, just to get this over the line more easily. In future a lot of the "preprocessing" will have to be done live (as the data will also be live).