> [!NOTE]
> See [0908 Centre-finding](0908%20Centre-finding.md) instead.

Follows on from [0908 Thoughts on the wicking algorithm](0908%20Thoughts%20on%20the%20wicking%20algorithm.md), because clearly I actually just need to do the maths.

A [rotation in linear algebra](https://en.wikipedia.org/wiki/Rotation_matrix) is defined with the following matrix.

$$
R={\begin{bmatrix}\cos \theta &-\sin \theta \\\sin \theta &\cos \theta \end{bmatrix}}
$$

It can be done in complex numbers manually, using code.

```julia
using Plots; default( fontfamily="LinLibertine_Rah", size=(720, 720), label="", background_color=:transparent, foreground_color="#777", dpi=300 )

deg = 2π/360
j = im

# a complex number cannot affect a scale change if its magnitude is 1
isRotation(factor::ComplexF64) = abs(factor) ≈ 1.0

a = cis(45deg)
a |> isRotation

d = real(a)
e = imag(a)

function rotate(point, angle)

	x = real(point)
	y = imag(point)

	c = cos(angle)
	s = sin(angle)

	println("c = $c")
	println("s = $s")
	println("    $(√(c^2+s^2))")
	println("")

	u = x*c + y*s
	v = x*s - y*c

	return u + j*v

end

points = [u + j*v for u ∈ -3:3 for v ∈ -3:3]
scatter(points, xlims=(-5,5), ylims=(-5,5))

rotatedPoints = rotate.(points, 10deg)
scatter!(rotatedPoints)

savefig("rotation-test.png")
```

We can prove that a rotation is a rotation because $\cos^2\varphi + \sin^2\varphi = 1$.

I'm trying to relate these rotations to the line-of-best fit method that I was investigating earlier. If I have appropriate lines of best fit through the new points, can I reverse calculate the rotation? Or, better yet, can I define some function that looks more like a Taylor series that achieves both rotation and translation at once?

![](Image%207.jpeg)

A point $(x,y)$ becomes the point $(u,v)$ under rotation using the following mapping. (You can also see this as the line/axis $x$ becoming $u$ under the rotation, and the same for $y$ becoming $v$.)

$$
 u = x \cos \varphi + y \sin \varphi 
$$

$$
 v = x \sin \varphi - y \cos \varphi 
$$

I can make the working a bit easier by defining the shorthands $c = \cos \varphi$ and $s = \sin \varphi$. Note that by trig, $c^2 + s^2 = 1$.

$$
 u = cx+ sy 
$$$$ v = sx - cy 






































$$

I've done this step (replacing the trig functions with placeholder constants) because I don't actually know, or want to have to know, the angular error $\varphi$. I will have to find these terms empirically. What is important is that because we started with the trig terms, we know that so long as we don't break the identity $c^2 + s^2 = 1$, we will still be doing pure rotation.

Let's rearrange a bit to complete the picture.

![](Image%208.jpeg)

As a checkpoint, here's a summary of what I've found so far.

$$
\begin{align}
u &= sy + cx \\
v &= sx - cy \\
x &= sv + cu \\
y &= su - cv
\end{align}
$$

> [!QUESTION] Why no inverses?
> I'm not super convinced by this — I would have expected the equation for $y$ to do something different to the equation for $v$ and that it would just look like I switched the variable names. I kind of expected one to undo the behaviour of the other. Why is this not the case? (Or is it the case?)

Anyway, I might as well continue. I'd quite like to work out how the slopes of the lines can be factored in.

$$
\begin{align}
u=m_{1}x + b_{1} \\
v=m_{2}x + b_{2}
\end{align}
$$

We'll say the new coordinate system passes through the origin for now, so $b_1 = b_2 = 0$.

$$
\begin{align}
u = m_{1}x \\
v=m_{2}x
\end{align}
$$

$$
\begin{align}
m_{1} &= \frac{u}{x} \\
&= \frac{sy+cx}{x} \\
&= c + s \frac{y}{x}
\end{align}
$$

Whatever the hell that means. Same for $m_2$:

$$
\begin{align}
m_{2} &= \frac{v}{x} \\
&= \frac{sx-cy}{x} \\
&= s - c \frac{y}{x}
\end{align}
$$

I have genuinely no idea what to do with this. Let's keep doing substitutions.

$$
\begin{align}
m_{1} &= c + s \frac{y}{x} \\
&= c + s \frac{su-cv}{sv+cu} \\
&= c + \frac{s^2u}{sv+cu} - \frac{csv}{sv+cu} \\
m_{1}sv + m_{1}cu &= csv + csu + s^2u + csv \\
0 &= s^2u - m_{1}sv + 2csv - m_{1}cu + csu \\
&= s^2 + sv(2c-m_{1}) - cu(s-m_{1}) \\
&= s^2 + v(2cs-m_{1}s) - u(cs-cm_{1}) \\
0 &= su - m_{1}v + 2cv - \frac{m_{1}cu}{s} + cu \\

\end{align}
$$

Nah, screw this, I'm just going to try and throw two equations together to see what happens.

$$
\begin{align}
m_{1} + m_{2} &= c + s \frac{y}{x} + s - c \frac{y}{x} \\
&= \frac{y}{x} (s-c) + c + s \\
\end{align}
$$

$$
\begin{align}
\frac{m_{1}}{m_{2}} &= \frac{c+s \frac{y}{x}}{s - c \frac{y}{x}} \\

\end{align}
$$

Screw this.