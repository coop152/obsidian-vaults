Polygons (specifically, triangles) are the most commonly used method of modelling and rendering 3D objects (but not the only method). 

## Polygons
A many-sided shape. Represented simply with an ordered set of vertices.
OpenGL requires **convex polygons**, that is, polygons where all angles are less than 180 degrees.
![](Pasted%20image%2020230214113336.png)
If a polygon that isn't concave needs to be rendered, it can be **tessellated** to split it into multiple polygons that are.
![](Pasted%20image%2020230214113428.png)
#### Properties of the polygon
- The **surface normal** is the vector perpendicular to the plane of the polygon. We can use this normal to give a polygon a distinguishable 'front' and 'back'.
- And we can also use this to describe its **orientation** in the 3D space.
- The orientation of a polygon is an essential property; it's used in lighting calculations, collisions, culling...
The **cross product** is relevant to the surface normal - it can be used to find a vector perpendicular to two other vectors. This could be applied to a surface.
![](Pasted%20image%2020230214113923.png)
#### Representing scenes with polygons
Can be done multiple ways:
1. Mash together the entire scene into one big group of polygons and render it all at once. This is called a **polygon soup**.
	- This is a huge waste of storage space. Most models contain surfaces and not individual polygons, and the vertices on a surface could be shared instead of being duplicated per polygon
	- The semantics of what belongs to what is completely lost. Does that polygon belong to the table, the floor, the projector?
	- For these reasons, interacting with the model is difficult
	- The example cow model (which i cannot be bothered copying) is 18,000 vertices.
![](Pasted%20image%2020230214114202.png)
2. Provide a hierarchical model, where each polygon belongs to some **mesh** where vertices can be shared.
## Meshes
#### Triangle Strips
If you have 3 vertices, you have one triangle. Add one more, and now you have 2 triangles. Given a list of vertices $V_0, V_1, V_2, \dots, V_n$, each triangle is given by a sequence of three vertices, e.g.
$V_0,V_1,V_2$, then $V_1,V_2,V_3$, then $V_2,V_3,V_4$.
![](Pasted%20image%2020230214115336.png)
Triangle strips are very widely used due to their high efficiency; $N$ linked triangles can be defined with just $N+2$ vertices - very good compared to the $3N$ vertices required by a polygon soup.
#### Triangle Fan
Similar to triangle strips, but instead of taking three consecutive polygons it takes two consecutive polygons along with another vertex common to every polygon. e.g. $V_0, V_1, V_2$ then $V_0, V_2, V_3$ then $V_0, V_3, V_4$.
This creates a fan-like shape.
![](Pasted%20image%2020230214115739.png)
This method has the same level of efficiency as triangle strips, allowing you to define $N$ linked triangles using just $N+2$ vertices.
#### Quadrilateral Strips
Like triangle strips, but using quadrilaterals. e.g. $V_0, V_1, V_2, V_3$ then $V_2, V_3, V_4, V_5$ then $V_4, V_5, V_6, V_7$
![](Pasted%20image%2020230214120105.png)
To represent $N$ quadrilaterals with this method requires $2N + 2$ vertices, compared to the $4N$ required for storing individual quads.
#### Quadrilateral meshes
A collection of quads that share all sides apart from those on the outside. Looks like a warped grid. This method is often used in terrain modelling, and when approximating curved surfaces.
![](Pasted%20image%2020230214120512.png)
To represent $N \times M$ quads in a quad mesh, you need $(N+1) \cdot (M+1)$ vertices. With individual polygons, you would require $4*M*N$ separate vertices.
Usually triangles are preferred while rendering, so a quad mesh may be tessellated.
![](Pasted%20image%2020230214120832.png)

## Rendering a polygon
Converting a representation of a 3D mesh into an image output is a multi-step process.
#### Scan conversion
The process of converting a line into a grid of pixels. This is done using **Bresenham's algorithm**:
1. Find the line in terms of gradient and intercept (i.e. $y = mx+c$)
2. Represent an increase of one pixel as an increase of $x$ by 1
3. Therefore, $y_{n + 1} = y_n + m$
4. Round $y_{n+1}$ to the nearest pixel (just for drawing, the decimal place remains for the next iteration)
According to the gradient of the line, $x$ and $y$ may need to be swapped.
How to apply this to a polygon? Well, scan conversion is applied at a point after the viewing pipeline, meaning that the polygon to be rendered has already been given coordinates in **screen space** - that is, for vertex coordinates $(x, y, z)$, $x, y$ correspond to position on the screen and $z$ is distance from the camera (or depth into the screen).
In theory $z$ can simply be ignored, giving you a set of vertices that can be drawn as lines using Bresenham's algorithm. These lines can then be filled in. However, drawing the lines then filling is naive and not very efficient.
![](Pasted%20image%2020230214122326.png)
An alternative is the "sweep-line algorithm" where the gradient of each line is calculated and used to find the point on each scanline in which the polygon begins and ends. In between these two points is the section on that scanline where the polygon should be filled.
![](Pasted%20image%2020230214122726.png)

## Hidden surface removal
From a particular viewpoint, what parts of the world can we see and which can we not? Objects may be obscured from view by other polygons in the same mesh, or by other objects in the same scene. For example, consider this scene with a series of objects being viewed through a square hole:
![](Pasted%20image%2020230214123455.png)
The objects off to the sides should not be visible; they should be occluded. In addition, the edges on the backs of the objects can also be seen.
The desired result is this:
![](Pasted%20image%2020230214123641.png)
Now that only the correct surfaces are being shown, shading can be applied:
![](Pasted%20image%2020230214123820.png)
There are two fundamentally different approaches to removing hidden surfaces:
- Work in **3D world space**. Calculate geometrically (in 3D) which surfaces are obscured, and then draw the result.
- Work in **2D display space**. During scan conversion, whenever some pixel P is generated we determine if some other object also maps to P but is nearer to the camera. This is the standard approach.
#### The Z-buffer (aka the depth-buffer)
We introduce a new data structure called the Z-buffer. For every pixel, there is a corresponding entry in the Z-buffer that keeps track of the depth of each pixel.
![](Pasted%20image%2020230214124042.png)
To perform hidden surface removal using the Z-buffer, use this algorithm:
1. Initialise every pixel to a desired background colour.
2. Initialise every Z-buffer entry to the biggest possible number (`MAXDEPTH`)
3. For each pixel P generated during scan-conversion:
	- If the Z coordinate of P is less than `ZBuffer[P]` then P is closer than whatever was at that pixel before; Compute the colour of P, store it in the pixel memory, and update the Z-buffer with this new lowest value of Z.
	- Otherwise, something else has already been mapped to P that's closer. Don't change anything
This method generally works well, but if two surfaces are mapped to a very similar depth then a problem called **Z-fighting** can arise. Due to rounding errors or other errors that would cause slightly off depth calculations, the two surfaces may inconsistently switch between being in front and behind, leaving ugly artifacts where the surfaces appear to weave into each other. This is exacerbated when the image is moving, as the artifacts will rapidly flicker.
![](Pasted%20image%2020230214130255.png)
## Modelling with multiple objects
Using a structured hierarchical system for modelling, the rendering process can be optimised and simplified.
![](Pasted%20image%2020230214130513.png)
A model is made of object(s), which is made of one or many surfaces, which is made of one or many polygons, which is made of many edges/vertices.
A mesh can be represented by a data structure containing three lists: a list of vertices, a list of edges, and a list of faces.
![](Pasted%20image%2020230214130736.png)
#### File formats
How to encode a large mesh containing thousands of vertices, edges and faces into a file? Consider the .obj file format (not examinable, just an example):
- A list of vertices
	- `v x y z`
- A list of vertex normals
	- `vn x y z`
- A list of vertex texture coordinates
	- `vt s t`
- A list of faces
	- `f v1/vt1/vn1 v2/vt2/vn2 v3/vt3/vn3`
- A list of groups
	- `g f1 f2 f3`
