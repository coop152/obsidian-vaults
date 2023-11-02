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
This integral says that, for the hemisphere $\Omega$, we will add up the influence of every incoming light 