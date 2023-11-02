![](Pasted%20image%2020231102120225.png)
This is the Rendering Equation, which is a mathematical abstraction of how light interacts with materials in a 3D space. It's probably the most important equation in modern 3D graphics.
In this section, we will break this equation down to it's constituent parts, and demonstrate how to use it.
The equation is actually based on similar mathematical representations of heat transfer. This is the basic idea:
You have a 3D environment with some objects in it.
![](Pasted%20image%2020231102120616.png)
Each object radiates some amount of heat, some of which will hit the other objects and impart some energy. These other objects will be doing the same. This will continue until the objects all reach some heat equilibrium, wherein their temperature will stop changing.
This concept intuitively transfers well to simulating visible light. Add a viewpoint to the scene:
![](Pasted%20image%2020231102120852.png)
What we want to sample is the steady equilibrium light values from these surfaces. This is what the rendering equation models.

Notice that this is **THE** graphical equation, not **A** graphical equation. This equation can describe essentially all graphical scenes, and trying to calculate what a scene looks like has come to be known as "solving the graphical equation".

# From Scratch: 
Consider this scene:
![](Pasted%20image%2020231102121453.png)
We have a viewpoint (represented by a camera), a few objects, and a light source.
As for the objects, we have:
- A shiny ball
- A reflective monkey
- A diffuse fractal broccoli
- A transparent cube

So what colour should each of these things be to the camera? Well, clearly the light should be pure white because the light that the camera sees is coming directly from the bulb. The light is an **emitter**, and we want to take this into account in our formula.
What about the matte diffuse floor? The apparent colour of the floor is a function of the light bouncing off of it and the reflectivity of the floor's material. We can't see the floor because it is emitting light, but because it is reflecting it. The colour of this surface will not just be affected by direct reflections from light sources, either; it will be affected by light that is bouncing off of other objects in the room:
![](Pasted%20image%2020231102121854.png)
In this case the final colour is clearly dominated by the bright direct light of the lightbulb, but in other cases the colour can be affected more strongly by the surrounding surfaces.
So, let's apply these intuitions to the rendering equation. First let's explain what the equation takes:
![](Pasted%20image%2020231102122040.png)
This part represents **spectral radiance**. $\mathbf{x}$ is the point that the spectral radiance originates from, and $\omega_o$ is the direction it is moving. The $o$ in $\omega_o$ is for "out", as in it is the light moving "out" of the scene.
![](Pasted%20image%2020231102122455.png)
Spectral radiance also takes into account the wavelength of light $\lambda$ and the time $t$, but these terms make the equation more complicated and can be reasonably ignored in a simple explanation, so let's remove them:
![](Pasted%20image%2020231102122744.png)
So $L_o$ is an expression that represents the total light the observer receives from a point $\mathbf{x}$ along direction $\omega_o$. In other words, it's what we want to know: the colour of some point in the image from the observer's point of view.
$L_e(\mathbf{x}, \omega_o)$ represents light **emitted directly** from point $\mathbf{x}$ in direction $\omega_o$. This part will be the overwhelming decider of colour if point $\textbf{x}$ is on a light emitting surface such as a light bulb.
$$\int_\Omega$$
This integral says that, for the hemisphere $\Omega$, we will add up the contributions of every incoming vector $\omega_i$.
![](Pasted%20image%2020231102124716.png)
The $i$ in $\omega_i$ stands for "incident".
$L_i(\mathbf{x},\omega_i)$ represents the incident light on $\mathbf{x}$ from direction $\omega_i$. In fact we will be adding up infinite $\omega_i$'s, this being inside an integral.
$f_r(\mathbf{x}, \omega_i, \omega_o)$ is the **Bidirectional Reflectance Distribution Function**, or **BRDF**. This part gets a whole video to itself, but in summary it modifies the incident light on $\mathbf{x}$ coming from $\omega_i$ in a way that corresponds to the material of $\mathbf{x}$. Essentially, it represents the way that the surface's material influences light reflections.
$(-\omega_i \cdot \mathbf{n})$ is the dot product of the inverse of the incident direction and the normal of the surface at $\mathbf{x}$.
![](Pasted%20image%2020231102125510.png)
This represents how the effect of some incoming light source will be reduced by a sharp angle with the surface. If the light is coming in directly against the normal then it will be at it's brightest:
![](Pasted%20image%2020231102125712.png)
But if it is just grazing the surface at a sharp near-$90\degree$ angle, the intensity will be lowered:
![](Pasted%20image%2020231102125734.png)
In more concrete mathematical terms this term is just Lambert's Cosine Law. When the angle with the normal is $0\degree$ then $\cos\theta = 1$, and we have maximum effect. When the angle with the normal is near $90\degree$ then $\cos\theta$ is near $0$, and we have near minimum effect.
![](Pasted%20image%2020231102130208.png)
Remember that $\omega_i$ is just one incoming vector of the infinite number that we are summing up in this integral.
So, in summary:
![](Pasted%20image%2020231102130328.png)
To calculate the light that the viewer receives from some point $\textbf{x}$, we add up the direct emitted light from that point (if any) and the total light that was reflected off of it.
Calculating $L_o(\textbf{x}, \omega_o)$ for all of a scene is often called "solving the rendering equation".

But can we simply just solve this equation to render a scene? Unfortunately, it isn't that simple. Even having simplified it by removing $\lambda$ and $t$, we still have an integral in there that we don't actually have a way to solve. We cannot just perform infinite calculations. We need some special technique to calculate a result to this integral, in the same way that we need a special technique (calculus) to solve integrals over simple polynomials.

The rendering equation is one of many equations in the "Fredholm Equations of the Second Kind". Mathematicians have found analytical solutions to several of these equations, but unfortunately the rendering equation is not one of those. Part of the reason why is that it's infinitely recursive; The $L_i(\textbf{x}, \omega_i)$ inside the integral is a nested rendering equation that must also be solved. It's worse than just being a potentially deep recursion:
![](Pasted%20image%2020231102131702.png)
For example, finding the reflected light on $\textbf{x}$ requires finding the reflected light on the plinth, which requires finding the reflected light on the ball, which in fact **requires finding the reflected light on $\mathbf{x}$**, creating a loop that never ends. Clearly we need to cut corners somewhere here.

# Light and Materials
There are three ways that light can interact with a material:
![](Pasted%20image%2020231102134233.png)
1. It can reflect off, which can give a shiny, mirror-like appearance with high specularity or a smooth, diffuse appearance with low specularity.
2. It can transmit through, which can make the object appear transparent or refract light like a prism, depending on the specifics of the material.
3. It can be absorbed.

All three of these interactions can be mixed together. That is, you could for example have a material that absorbs half of the light that hits it, and reflects the remaining half with high specularity.
# Reflections
If light hits a surface and reflects back off in a single direction (or a very tight range of close directions, at least) then we perceive it as a shiny mirrored surface.
![](Pasted%20image%2020231102135036.png)
This is called a **mirror** reflection.
Because the angle of incidence and angle of reflection are (roughly) the same, the light coming off of the surface are identical (or very similar) in shape and colour to the light that hit it, resulting in a recreation of the incoming image.

Surfaces with this mirror appearance tend to be metallic, as the same mobile electrons that allow a material to conduct electricity also tend to repel electromagnetic radiation such as light.

On the other hand, a reflection is **diffuse** if the light scatters off in lots of different directions when it's reflected.
![](Pasted%20image%2020231102135521.png)
In materials like this, the light is penetrating the surface a small amount before being reflected back out, resulting in a more varied exit angle. Because the reflected light is scattered so much, diffuse surfaces do not appear smooth or mirror-like and instead appear flatly coloured, for example white paper.

There are also **specular** reflections, which are similar to mirror reflections but with a variance in the reflection angle:
![](Pasted%20image%2020231102135805.png)
Specular surfaces appear smooth and shiny, but not mirror-like. If the spread is low enough then there may be a slight mirror image, but it will be very faint compared to a fully mirrored surface.

Generally, surfaces will not be purely mirrored, diffuse or specular - they will be a combination of the three. For example, here is a surface that has both diffuse and specular reflections:
![](Pasted%20image%2020231102140129.png)
And of course in the real world, there is no such thing as a perfectly flat surface. Even a polished mirror that produces a great reflection is in reality not perfectly smooth. The previous mixed reflection surface probably appears more like this in reality:
![](Pasted%20image%2020231102140322.png)
This applies to all kinds of reflections; you are not going to get a perfect mirror, and you are also not going to get a perfectly diffuse material, where the rays are equally distributed in all directions. If we want to model realistic surfaces, then we need a model that takes into account these imperfections, and this is what BRDF is trying to do.

# Colour
We perceive colour using the three kinds of light receptors in our eyes. Each of these three **cones** perceives a range of wavelengths, and the combination of these three cones allows us to perceive colour.
![](Pasted%20image%2020231102140800.png)
(Note that we have way more red receptors than any others; this will be mentioned later.)
The range of electromagnetic radiation that these cones allow us to perceive is called the visible light spectrum. There is nothing special about this range of wavelengths; other animals have different cones that allow them to see a different range of colours. The visible light spectrum is only important to us because we're concerned with how humans see.
![](Pasted%20image%2020231102141231.png)
This graph plots the wavelength of each colour against the response of each cone when exposed to it. For example, when exposed to a saturated blue with a wavelength of about 460nm, the S cone has a strong response which the M and L cones have similarly weak responses.
Note that these cones are not named B, G and R, but instead are called S, M and L for Short, Medium and Long. This refers to the wavelength of the light they are most responsive to - Short for short wavelengths and Long for long wavelengths.
Notice that these curves are not evenly distributed across the visible light spectrum - M and L are quite close together while S is quite far away. In practice, this makes humans better at distinguishing differences between greens and reds.
This has important ramifications for computer graphics, in that when you experience a pure white, the distribution of colours will not actually be an even distribution of frequencies due to our sensitivity to certain frequencies over others.

For most topics in this course these subtleties do not matter, but it is important to keep in mind at all times. We often talk about primary colour as if it's some fundamental property of light; that mixing an "equal amount" of red, green and blue will produce white, and that any other colour is just some mix of these primary colours. In reality this is just a convenient shorthand. Colours across the visible light spectrum all exist independently of the primary colours. For example, consider this aqua-marine colour:
![](Pasted%20image%2020231102142400.png)
In terms of colour we might think of this as a mix of blue and green, but it is important to remember that this is no fundamental property of the colour. It is entirely possible for this colour to exist without any use of the primary colours blue and green. You can achieve the same responses from the cones with a mix of different wavelengths, but that is not the only way.
Also note that this spectrum doesn't contain every colour; where is purple? Purple actually cannot exist as a monochromatic colour; it is always a mixture of many different wavelengths.

# Bidirectional Reflectance Distribution Function
![](Pasted%20image%2020231102143105.png)
The BRDF (in a simplified form) describes the ratio between radiance and irradiance for a surface. Radiance and irradiance are terms from radiometry with specific meanings:
**Radiance** is the **radiant flux** emitted, reflected, transmitted or received by a given surface, **per unit solid angle per unit projected area**.
**Radiant flux** is a measure of energy over time. Essentially, it's how much energy is imparted in some amount of time.
**Per unit solid angle, per unit projected area** is what really complicates the calculation (and the definition). What this means is that instead of measuring our quantity (radiant flux) over some flat surface (in which case it would be **per unit distance squared** or similar), we are measuring over the projected surface of a (hemi)sphere:
![](Pasted%20image%2020231102143850.png)
This can seem quite complicated, so let's start from the basics. We have a hemisphere $\Omega$ around our point $\mathbf{x}$, in a regular 3D coordinate system.
![](Pasted%20image%2020231102143940.png)
We have our incident ray and reflected ray, both of which are parameterized in terms of their **altitude** $\theta$ and the **azimuth** $\phi$.
![](Pasted%20image%2020231102144025.png)
The BRDF then does some integration over some delta quantities (i dont understand what they are). When you take this to the limit, you get *something* in the direction of the reflected ray which represents *something*. I think.
![](Pasted%20image%2020231102150202.png)
This is where $\lambda$ can be re-introduced; the result of this calculation will be different depending on the wavelength of colour:
![](Pasted%20image%2020231102150248.png)
This gives how 