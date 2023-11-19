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
![](Pasted%20image%2020231119173443.png)
We count which points fall inside the circle. In this example, we sampled 100 points in total of which 70 were inside the circle.