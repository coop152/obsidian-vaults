# Raytracing
A ray is line with an origin and a direction in 3D space.
![](Pasted%20image%2020231110151421.png)
![](Pasted%20image%2020231110151428.png)
A ray can be represented simply as two column vectors.
Assuming the ray's direction vector is normalised, you can perform a multitude of useful linear algebra calculations about the behaviour of the ray in a 3D space.
For example, if you want to find a point along the ray at some distance $t$, you can use this equation:
![](Pasted%20image%2020231110151818.png)
Which is called the explicit equation of the ray.

![](Pasted%20image%2020231110152857.png)
Using raytracing to render an image can generate some incredibly realistic images. Many of the impressive effects in this image just come standard with using raytracing:
- The realistic reflections
- The transparency
- The glass and liquid refractions
- The specular highlights
- The soft shadows
- The depth of field (!)

## An algorithm
Imagine a simple 3D scene with a camera, a light, and some objects with varied materials. Let's try rendering this scene by accurately simulating a light ray from the lightbulb.
![](Pasted%20image%2020231110153329.png)
The ray of light is emitted towards the semi-transparent green ball. When the ray connects with the surface it is reflected, transmitted and absorbed in some proportion according to the BSDF of the ball. Some light reflects into the camera, and is shifted to a green shade. Some transmits through the ball to the other side, bouncing again into the diffuse blue floor where it is scattered evenly. One of those rays, with the last of it's energy, bounces into the blue cube and is absorbed.
This happens again and again, for every ray that the lightbulb emits (which is an innumerable amount).
![](Pasted%20image%2020231110153716.png)
There are some obvious problems here aside from just the number of rays; the fact that our rays are splitting apart and becoming more rays is giving us a recursive tree-like structure. We only considered one of the rays that scattered from the floor, but its much more likely that the scattering created a multitude more rays:
![](Pasted%20image%2020231110153925.png)
All of these new rays might spawn yet more rays when they collide with other surfaces, leading to an insane amount of calculation. And all of this to potentially **not even land a single ray into the camera**.

Before we discuss how this can be resolved, lets learn another technique. We clear out the scene and introduce the **view plane**:
![](Pasted%20image%2020231110154205.png)
This is a plane perpendicular to the camera, with a grid that represents the pixels in the final image we wish to render.
We shoot a single ray **from the camera** and into the scene, through one of the pixels in this view plane. This is called **raycasting**:
![](Pasted%20image%2020231110154338.png)
We can use the intersection of this ray with the objects in the scene to generate an image that represents certain properties of the scene, such as perspective, distance and occlusion.
Raycasting can be used for many things, including things not related to graphics. Imagine seeing the scene from the point of view of the camera, through the view plane:
![](Pasted%20image%2020231110154941.png)
Perhaps we want to allow the user to select an object. Cast a ray through the pixel the user clicks, and select the first object that intersects the ray:
![](Pasted%20image%2020231110155045.png)
Hitscan weapons in video games (assuming they are perfectly accurate) work in much the same way.
And, back to graphics, if you want to find the colour of a pixel then you start by casting a ray.
![](Pasted%20image%2020231110155244.png)
With this new technique, we are ready to describe an algorithm for rendering a scene using rays that is actually feasible.
## Whitted's Algorithm
While every ray that reaches the viewpoint definitely originated from a light source, not every ray that originates from a light source will reach the viewpoint. This is the most important observation that Turner Whitted made, and which motivates this algorithm.
Whitted's algorithm functions by casting rays from the viewpoint, through the view plane and into the scene and noting interactions with surfaces and light sources as it bounces around the scene. When the ray expires these interactions can be "reversed", giving a colour for the pixel.

Lets go through an example. We shoot a ray through the pixel we wish to render:
![](Pasted%20image%2020231110160451.png)
The ray moves forward until it comes in contact with an object, in this case the semi-transparent green ball. At this point we generate a new kind of ray called a **shadow ray**, or a **shadow feeler ray**. These rays are fired toward every light source in the scene - in this case there is only the one.
![](Pasted%20image%2020231110160548.png)
Our shadow ray reached the light source without any interactions with other objects, so we know there is direct light upon the sphere from the direction of the light. We note this interaction down and carry on.
![](Pasted%20image%2020231110160921.png)
The light refracts through the sphere and reaches the other side. At this point another shadow ray is generated, which reaches the light source again.
![](Pasted%20image%2020231110161030.png)
We continue following the ray until it hits the box. Again we cast a shadow ray, but this time it collides with the cylinder: The box is in shadow at that point.
We carry on doing this until some stopping point. This may be because the ray runs out of energy, because it leaves the scene, or because we set a max bounce count which it has exceeded.
We do this for every single pixel until we have filled the view plane in:
![](Pasted%20image%2020231110161315.png)
And we have a completed render.
You may have noticed that rays were never scattered; how does this rendering algorithm handle diffuse reflections? The answer is that it does not. Whitted's algorithm disregards any scattering properties of materials and thus renders all objects as perfectly specular (and very shiny).
![](Pasted%20image%2020231110161706.png)

# Radiosity
Radiosity is another rendering technique which, unlike raycasting, can handle diffuse reflections. 
While raytracing treats light as a concrete object (rays), radiosity instead treats light as a quantity on a surface which is exchanged directly between surfaces.
![](Pasted%20image%2020231111133927.png)
While raytracing can cope very well with transparent and specular objects, it is infeasible to render diffuse reflections due to the massive explosion in the number of light rays required for scattering. In contrast Radiosity excels at soft shadows, colour bleed and subtle lighting but significantly complicates the rendering of transparent and specular materials.

When raytracing a scene, we are concerned with the relationship between rays, the surfaces they collide with, and the light sources in the scene. The result is an image with all of the "detail": In this example raytracing does a good job rendering the glassy texture on the plaque and wine glasses, the reflectivity of the doors on the cabinet, and the specular sheen on the plant pot.
![](Pasted%20image%2020231111134618.png)
When using radiosity to render a scene, we are concerned with **patches** of surfaces and how they are influenced by both the light sources in the scene and the surrounding patches.
![](Pasted%20image%2020231111134824.png)
This method does better at rendering matte surfaces, which in most cases are much more prevalent than the kind of surfaces that raytracing excels at.

To show how accurate radiosity can be, here is a classic example called the Cornel Box:
![](Pasted%20image%2020231111135156.png)
On the left is a real photograph of a physical Cornel Box, with everything from lighting to positioning carefully calibrated. On the right is a computer render of the same scene. The idea is to use the real photograph as a reference to compare a render against. If we subtract one image from the other, we can get a representation of the difference between our real photo and our render:
![](Pasted%20image%2020231111135428.png)
These are actual historical images from Cornel's original experiment, so they are quite low quality and the render itself has more errors than a modern one would. Here is a modern recreation in Blender (keeping in mind that Blender doesn't actually use Radiosity anymore):
![](Pasted%20image%2020231111135642.png)
First of all you'll notice the more saturated colours; this is mostly an artefact of the Cornel images being old. The left box also isn't reflective because this is a Radiosity render, which cannot handle reflections like the rendering algorithm used in the Cornel image does.

You can see the properties of Radiosity in the image; the colour of the walls has bled onto the pure white cubes, giving them slightly red and green sides and tinting the shadows. This is called **colour bleed**.
The boxes are casting realistic soft shadows. There are also subtle shadows where faces meet at hard angles, and especially in the corners.

Another important difference between raytracing and radiosity is shown here: The light source is represented as actual geometry in the scene, unlike raytracing where every light source is an infinitesimally small point.
With raytracing, we have a **Point Light**:
![](Pasted%20image%2020231111140602.png)
With radiosity, we have an **Area Light**:
![](Pasted%20image%2020231111140620.png)
The light source in the radiosity example is not a special object like in raytracing; it is simply a polygon that has been defined as emitting light.

## Energy transfer
Radiosity as a general method originates from the field of heat transfer. At its most basic level it's based on the idea of conservation of energy, or energy equilibrium. There is some energy in a scene, and the components exchange energy until they reach some steady state. One must also consider the energy coming into and leaving the scene.
![](Pasted%20image%2020231111140833.png)
The term **thermodynamic radiosity**, or **radiance** is defined as the *flux* leaving a surface at point $x$. The counterpart is **irradiance**, which is the *flux* arriving at that point. *Flux* here is just a term meaning a flow of particles. In our case, the particles are protons.
Now clearly we cannot just calculate the energy exchanged between single surfaces, because on a surface of any realistic size the energy exchanged will not be constant. For example, take a look at the back wall of the Cornel Box:
![](Pasted%20image%2020231111141446.png)
Despite being a single surface with a single colour, the actual perceived colour of the surface varies greatly.
Any meaningful model based on Radiosity will need to deal with this fact; if we just take every surface in the scene as a patch then each surface can only be one exact colour. We do this by splitting surfaces apart into many small patches.
![](Pasted%20image%2020231111141722.png)

## An example
Let's take the surface $A_X$ and consider it's relationship to the other surfaces in the scene (a single one shown here as $A_{X'}$):
![](Pasted%20image%2020231111141947.png)
This relationship is expressed via this formula:
![](Pasted%20image%2020231111142035.png)
Noting that $\delta A$ in these terms represents a small section of $A_x$:
$B(x)\delta A$ is the total energy leaving the small section of $A$ around the point $x$.
$E(x)\delta A$ is the total energy emitted by the section.
$\rho (x) \delta A$ (that's Greek letter rho) is the reflectivity of the section, which multiplies:
$$\int_S B(x') \cdot F(x,x') \delta A'$$
which is the sum of all inbound energy on the section.
Breaking this integral down further, we note that $S$ simply represents all surfaces in the scene.
$B(x')$ is a recursion, representing the total energy leaving that surface and being received by this one.
$F(x,x')$ is the "form factor" of $x$ to $x'$. This evaluates to 1 if the two points are completely visible to each other, and 0 if they aren't.

Since this is an integration, we would like to take it to it's limit and get a continuous result - this would give us perfectly smooth shadows. Unfortunately, this is another Fredholm Equation of the Second Kind, and there is no analytical solution. It has the same infinite recursion problem as the rendering equation. So, can we make this method computationally feasible?

First of all, we drop the integral and give up on a perfectly continuous result. We will be calculating radiosity on actual small patches of the surface. Changing the equation to take this into account, we get:
![](Pasted%20image%2020231111144243.png)
So we subdivide our surfaces into tiny patches which can only have a single colour. We have to make these patches small if we want a smooth looking result:
![](Pasted%20image%2020231111144156.png)
Even this example would be noticeably blocky unless it was rendered at a very low resolution.
Let's return to our new equation:
![](Pasted%20image%2020231111144243.png)
Solving this is now technically possible, by solving a number of simultaneous equations that scales with the number of patches. This is still insanely computationally intensive, considering how small the patches must be and therefore how many there must be.