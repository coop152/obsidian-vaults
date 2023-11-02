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
