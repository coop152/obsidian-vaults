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
