James was thinking about ways to represent the directivity in the heads-up display on the machine. I've been looking at ways to visualising the mathematics for the presentation. Here's what I've come up with.

# Better than piecewise

I personally am not the biggest fan of the existing piecewise functions used for the positional cost function. Being piecewise, they aren't so easy to manipulate analytically, they take a few extra computational steps, and they also aren't very smooth. I reckon it'd be easier to work with a fully analytical function, and that this would look better and behave better at the same time.

I spent the better part of today testing out different functions that would provide this behaviour.

- I tried a $\cos$ function for the polar pattern. I can scale the lobe either by raising to a power ($\cos^n(\theta)$) or by multiplying the angle ($\cos(D\theta)$). I prefer multiplying the angle, as this mimics the directivity behaviour best.
- Unfortunately, plain old $\cos$ has too many lobes. I eventually realised that the hyperbolic version, $\cosh$, fixes this.
- It's still possible to bleed into neighbouring sectors, and so I needed another term to "clip" all of the out-of-sector parts. The obvious option was a Heaviside unit step function with an absolute value, but I wanted something a bit more "organic". The Wikipedia page for the unit step function showed a few approximations using logistic curves and so forth, which could be done with $\tanh$ or an exponential. (This was where I got the hyperbolic trig function idea from that gave rise to the $\cosh$ above.) I preferred the exponential method, as the $\tanh$ ranged from -1 to 1, and dealing with this would have introduced a magic number factor of 2.

Therefore, I came up with the following.

- Let $w(\theta) = \cosh\left(D\left(\theta-\theta_s\right)\right)$ be the new weighting function.
- Let $u(\theta) = \frac{1}{1+e^{I\left(\left(\theta-\theta_{s}\right)^{2}-\theta_{d}^{2}\right)}}$ be the into-sector clipping / squeezing function.

These use the following terms.

- $D$ is the directivity, describing the extent to which the search pattern has been smeared in a direction. This has the effect of drawing an isotropic search ($D=0$) into an ellipsoidal search (large $D$) and eventually into a line (infinite $D$).
- $I$ is the "isolation", or the degree to which the search pattern is confined to the sector. Where adjusting $D$ squished a circle into an ellipse, adjusting $I$ eventually causes a complete wedge shape.
- $\theta_s$ is the search direction. Given we're working at a keyboard with four direction keys, $\theta_s$ will be 0 rad for the `D` key, π/2 rad for the `W` key, π rad for the `A` key, etc.
- $\theta_d$ is half of the nominal included angle of the search sector. This is π/4 radians, or 45°, for our case with four cardinal search directions. Essentially, this mean that pressing a key is going to look in a given direction but allowing ±45° either side of centre. Where $N$ is the number of cardinal directions (number of keys), $\theta_d = \frac{2\pi}{2N}$.

This gives rise to the following cost function $c(\theta, r)$. We'll use this function to calculate the 'cost' of a given point. (The next jump target will be the lowest-cost point — this has already been documented by James.)

$$
 c(\theta, r) = r \cdot w(\theta) \cdot u(\theta) 
$$

I also need a function to calculate the radius that would result in a particular cost value as a function of the angle, $r(\theta, c)$, which will allow me to plot a locus of constant cost in the visualisation I'm putting together. This can be found by rearranging the equation for cost.

$$
 r(\theta, c) = c / w(\theta) / u(\theta) 
$$

At this point, I've got a cost function and a locus function that behave similarly in principle to James's piecewise ones, but without the piecewise-ness that I wanted to avoid. Let's plot them.

# Visualisation

As with everything, this took a bit of experimentation, but this is what I came up with. It shows how adjusting the directivity influences the candidate jump point.

![](directivity.gif)

As usual, this was done with a spot of julia code.

```julia
using Plots, Random

N = 4
θd = (2π)/2N

w(θ, θs, D) = cosh(D*(θ-θs))
v(θ, θs, I) = 1+exp(I*((θ-θs)^2 - θd^2))

r(θ, c; θs=0, D=0, I=50) = c / w(θ, θs, D) / v(θ, θs, I)
c(θ, r; θs=0, D=0, I=50) = r * w(θ, θs, D) * v(θ, θs, I)


function makeplot(D)

	Random.seed!(18) # 15 looks okay too
	points = rand(2, 10) .* [2π, 10^2] .- [π/4, 0]
	points[2,:] .^= 0.5
	scatter(points[1,:], points[2,:], proj=:polar, marker=:circle, label="all points")

	for (i, dir) = enumerate(["D", "W", "A", "S"])

		θs = ([0, π/2, π, 3π/2])[i]
		color = ([:red, :orange, :green, :blue])[i]

		costs = c.(points[1,:], points[2,:], θs=θs, D=D)
		mincost = min(costs...)
		
		θtest = range(-π/4-0.1, 7π/4+0.1, 200)
		rtest = r.(θtest, mincost, θs=θs, D=D)
		
		plot!(θtest, rtest, proj=:polar, label="$dir search", color=color)
		
		point = points[:, costs .== mincost]
		scatter!([point[1]], [point[2]], proj=:polar, marker=:circle, label="$dir point", color=color)

	end

	plot!(ylims=[0, 10.5], title="Search results for \$D=$D\$", legend_position=:topright)

end


anim = @animate for D ∈ range(0, 10, 101)
	makeplot(D)
end
gif(anim, "directivity.gif", fps = 12)
```

That's probably enough for now. I'll need to figure out exactly what's going in the slides and a few more details before refining this any further.