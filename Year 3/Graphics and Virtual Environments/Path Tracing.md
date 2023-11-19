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
