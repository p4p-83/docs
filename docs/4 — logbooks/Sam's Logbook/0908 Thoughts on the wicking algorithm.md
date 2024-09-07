I feel that I don't properly understand the wicking algorithm and that my implementation is a little haphazard. I got it working and corrected all the glitches that I'm aware of by trial and error. While it seems to be passable and probably good enough to put into the prototype machine, I do want to understand it a bit better so I can refactor it to be more clear, so that I can prove it works, and importantly, so I can properly describe it in the research report.

To this end, I've started trying to view it through a lens quite similar to that of the Gram-Schmitt orthogonalisation procedure — I have, in a sense, three basis functions (counterclockwise-clockwise, right-left, up-down) that describe the possible component movements — and I wish to take all of the individual error vectors and decompose them into movements along these three axes.

Here's a visualisation of that.

![](decomposition%201.png)

```julia
using Plots
default( fontfamily="LinLibertine_Rah", size=(720, 720), label="", background_color=:transparent, foreground_color="#777" )
j = im
deg = 2π/360

p1 = 1+0j
p2 = 1+1j

scatter([p1], xlims=(-1,2), ylims=(-1,2), label="start point", color=:black)
scatter!([p2], label="end point", color=:white)

path1 = [p1 * cis(θ) for θ ∈ 0:1deg:angle(p2)]
plot!([0+0j, p2], color="#ccc", linestyle=:dash)
plot!(path1, color=:"#c22", label="\$\\phi\$ component")

a = angle(p2) - angle(p1)
p3 = p1 * cis(a)
scatter!([p3], label="with \$\\phi\$ corrected", color=:"#c22")

x = real(p2-p3)
path2 = [p3 + u for u ∈ 0:0.01:x]
plot!(path2, color=:"#2c2", label="residual \$x\$ component")

p4 = p3 + x
scatter!([p4], label="with \$\\phi\$ and \$x\$ corrected", color=:"#2c2")

y = imag(p2-p4)
path3 = [p4 + j*v for v ∈ 0:0.01:y]
plot!(path3, color=:"#22c", label="residual \$y\$ component")

p5 = p4 + j*y
scatter!([p5], marker=:x, label="with all components (\$\\phi\$, \$x\$ and \$y\$) corrected", color=:"#22c")

plot!(
	title="Decomposition of error into three basis functions \$(\\phi,x,y)\$"
) |> display
savefig("images/decomposition.png")

println("$(a/1deg)° around, $x right, $y up")
```

Can also do it with a more polar set of basis functions.

![](decomposition-polar.png)

```julia
##
#####################################################
####
#

scatter([p1], xlims=(-1,2), ylims=(-1,2), label="start point", color=:black)
scatter!([p2], label="end point", color=:white)

a = angle(p2) - angle(p1)
p3 = p1 * cis(a)
scatter!([p3], label="with \$\\phi\$ corrected", color=:"#c22")

path1 = [p1 * cis(θ) for θ ∈ 0:1deg:a]
plot!([0+0j, p2], color="#ccc", linestyle=:dash)
plot!(path1, color=:"#c22", label="\$\\theta\$ component")

r = abs(p2) - abs(p1)
# p6 = p3 + r*cis(angle(p3))

# path4 = [p3 + t*cis(angle(p3)) for t ∈ 0:0.1:r]
path4 = range(p3, p2, 50)
plot!(path4, color="#2bb", label="\$r\$ component") |> display

savefig("images/decomposition-polar.png")

```

And even after these visualisations, I still have zero idea how rotation works.