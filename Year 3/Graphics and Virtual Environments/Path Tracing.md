Path Tracing is another rendering algorithm that addresses many of the problems with Whitted-style raytracing and Radiosity models.
## Motivating shadow example
Raytracing produces shadows that look like this:
![](Pasted%20image%2020231119163712.png)
The edges of the shadow are perfectly sharp, which is not very realistic in most situations.
Radiosity gives nicer results:
![](Pasted%20image%2020231119163804.png)
The shadow is realistically soft. The totally dark section of the shadow is called the **umbra**, and the section in-between the umbra and the un-shadowed surface is called the **penumbra**. Raytraced shadows have no penumbra.
![](Pasted%20image%2020231119163919.png)
The reason for raytracing's poor shadow performance is obvious; the light source is a single point, so either a point on a surface is visible to the light source or it isn't. There is no middle ground for a penumbra to form:
![](Pasted%20image%2020231119164045.png)
In contrast, radiosity produces realistic soft shadows because it models light sources with area, as they are in real life. For a point in the umbra, there is no path from anywhere on the light source to the point. For a point in the penumbra, there are some paths from the light source to the point but some are obstructed, giving a light level in-between shadowed and lit.
![](Pasted%20image%2020231119164348.png)
Looking at this diagram, you might imagine a way to get soft shadows with a reverse-raytracing model. What if instead of point lights we used area lights, like radiosity, and then when we fire shadow feelers we fire many of them instead of just one? This way, we can get (an estimation of) how exposed the point is to the light source.
![](Pasted%20image%2020231119164804.png)
In this example we shoot three feelers, two of which reach the light. Therefore, we say that this point is two thirds lit. Clearly only using three rays is not a good estimate, but this is easily solved by shooting more rays (at the expense of performance).
## The Monte-Carlo technique
The preceding method for getting soft shadows with raytracing is an example of a **monte-carlo technique**, where we endeavour to solve something that is computationally intractable using a **statistical** approach rather than an **analytical** one. In other words, we cannot calculate the exact exposure of a point to some light source in a reasonable amount of time: Instead, we use an estimation of the solution and stop when we deem the result acceptable.

As an example of a Monte-Carlo technique, think how you might find the area of a circle.
For finding the area of a circle there is a simple analytical solution: Get the radius and compute $\pi r ^2$.
Alternatively, we could **brute-force** the problem. If we take the circle at some resolution, count the number of pixels in the circle, and then multiply the number of pixels by the known area of one pixel, we can find the area of the circle. This solution is not a good one; it takes a lot of counting and the result isn't even exact due to the limited resolution.
The better solution would be using Monte-Carlo. We draw a bounding box around the circle, and then randomly sample points in that bounding box:
![](Pasted%20image%2020231119174607.png)
We count which points fall inside the circle. In this example, we sampled 100 points in total of which 70 were inside the circle. We can take the ratio between total points and inside points as an approximation of the ratio between the area of the bounding box and the circle:
$$\frac{inside}{total} \approx \frac{circle}{box}$$
$$\frac{70}{100} \approx \frac{circle}{2\times 2}$$
$$circle = 4 \times \frac{70}{100}$$
So our final estimate is 2.8. Considering the actual area is $\pi$, that is a pretty poor estimate. If we increase our number of samples to 500 we get 3.07, and with 1000 samples we get 3.14 which is accurate to 2dp. Using Monte-Carlo, we've gotten an accurate approximation in only 1000 samples, vs maybe a million with brute force.
Clearly this method is useless for a circle, but it becomes useful for shapes where we cannot simply calculate the area using a formula. For example:
![](Pasted%20image%2020231119180755.png)
# Path Tracing
Path tracing is similar to Whitted-style raytracing, with a few significant differences. Let's take an example scene, and cast a ray into it:
![](Pasted%20image%2020231119181111.png)
This looks identical to raytracing at first glance, but that is due to the specific ray that was chosen.
In raytracing, a ray is cast and a hierarchy is formed based on every way the ray splits as it is cast. It may be transmitted through a transparent object, reflected off of a wall, etc.
In path tracing, there is no hierarchy and each ray is only concerned with a single path. When a ray contacts a surface it picks a random location to move in, and continues doing this until the ray leaves the scene, bounces too many times, etc. Instead of just one ray being cast per pixel in the view plane, many are cast into the same pixel so that lots of random paths can be explored and composited together.
Why do it this way? By randomly selecting the direction of the ray at each reflection point, we are **uniformly sampling the hemisphere around the reflection points**:
![](Pasted%20image%2020231119181736.png)
## An example of path tracing
Let's cast a ray into the scene through some point in some pixel of the view plane.
![](Pasted%20image%2020231119181934.png)
In traditional raytracing, we would have split these rays into multiple rays at each step due to the transparent material. Instead, we just pick a random direction at each point and keep going until our ray leaves the scene. The colour that we derived from this ray is represented by the pink dot in the bottom left. Note that the box represents the colours sampled within a single pixel.
Lets shoot some more rays:
![](Pasted%20image%2020231119182223.png)
Eventually we will have enough colour samples in this pixel to find a suitable average, which will determine the final colour of the pixel.

You can see that this process requires many, many rays to be fired, the number of which is often called **Samples Per Pixel (SPP)**. A low SPP will result in a noisy, low quality image. Hundreds or maybe thousands of samples will be required for clean images, depending on the scene.