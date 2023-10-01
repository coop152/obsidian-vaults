Unlike curves, there are 4 kinds of surface:
![](Pasted%20image%2020230929134030.png)
# Explicit Surfaces
Surfaces defined in terms of a collection of concrete units, such as points in a point cloud or vertices in a mesh. Meshes are the more common type.
## Point Set
The points in a point set are unconnected and have no control over anything. They are just a free set of points:
![](Pasted%20image%2020230929162327.png)
This is often the result of doing a 3D scan of a real life object. Generally you don't actually want a point set, and need to convert it to another kind of surface somehow.
There are, however, some uses for them that make them useful in modelling.
![](Pasted%20image%2020230929162435.png)
# Tensor Product Splines
Analogous to splines when talking about curves. A tensor product spline is a surface defined in terms of a curve function, which produces a smooth surface.
We know how to define Bezier curves - how might we extend this from a line to a solid surface?
Consider how a Bezier curve is constructed, by interpolating smoothly between the control points according to $t$. Now imagine the control points themselves moving along Bezier curves, according to another variable called $v$. For each value of $v$ you would have a single Bezier curve, similarly to how you have a single point for each value of $t$ in a regular Bezier curve. If you do this continuously for every single value of $v$, the individual Bezier curves will assemble into a smooth surface.
![](Pasted%20image%2020230929134647.png)
This surface, made of cubic Bezier curves, requires 16 control points.
Here is the formula for a Bezier curve, as seen previously:
![](Pasted%20image%2020230929140040.png)
This surface has a very similar formula, with the new parameter added:
![](Pasted%20image%2020230929140126.png)
Formerly $P1$, $P2$, etc. represented the location of each control point and were constant values. Now, the control points move according to the value of $v$, so they have become functions on $v$.
This surface (specifically with a degree of 3) is called a **Bicubic Bezier surface**.
![](Pasted%20image%2020230929140345.png)
## Parametric Surfaces
A more general type of surface, which includes Tensor Product Splines. Any kind of curve that is defined in terms of parameters fits into this category.
![](Pasted%20image%2020230929143218.png)
From now on, parametric surfaces will be used as an interchangeable way to refer to tensor product splines such as the Bicubic Bezier Surface.
A point on a parametric surface is defined by the function $P(u,v)$. This was seen previously in the bicubic Bezier surface example.
Like a curve, you can extract some useful differential properties from this function.
### Differential Properties
![](Pasted%20image%2020230929144243.png)
Because there are now two parameters, we find partial differentials instead.
We can find the velocity/tangent at point P along $u$ (called T on the diagram) by finding the derivative with respect to $u$.
We can find the velocity/tangent at point P along $v$ (called B, or "bi-normal" on the diagram) by finding the derivative with respect to $v$.
We can find the normal at point P by finding the second derivative.

### Matrix Representation
A parametric curve can also be represented as a matrix. Here is a general form of a parametric curve:
![](Pasted%20image%2020230929145813.png)
This can be given in matrix form like this, much like a curve:
![](Pasted%20image%2020230929145916.png)
For comparison:
![](Pasted%20image%2020230929150035.png)


## Displacement Maps
Sometimes you don't want a perfectly smooth surface - after all, most natural surfaces are rough. We can do this using a displacement map:
![](Pasted%20image%2020230929154058.png)
A displacement map is a texture/function/something that you apply to a surface. The brightness of the map at a given point shows how far the surface should displace in the direction of the normal. Pure black will remain at the actual surface location, while pure white will stick out massively in the normal direction from the surface. Grey will stick out less.
Note that this is done to an explicit mesh; therefore it will usually be necessary to tessellate the surface before applying the displacement to get fine detail.

## Subdivision Surfaces
Unlike subdividing a curve, subdividing a surface begins with an explicit polygon mesh. Subdividing a surface involves splitting the polygons of the surface apart; there are many methods of doing this, but generally they make the mesh smoother.
One method is "corner-cutting". Start with this mesh:
![](Pasted%20image%2020230929155024.png)
We cut the sharp edges, giving us more edges:
![](Pasted%20image%2020230929155107.png)
Eventually we approach the "limit", which is perfectly smooth:
![](Pasted%20image%2020230929155124.png)
With this method, also called **Chaikin's Algorithm**, the resulting mesh (or the limit that it approaches) is actually a **quadratic B-Spline curve**. The original mesh's vertices become the control points to the curve.
![](Pasted%20image%2020230929155305.png)
In 3D:
![](Pasted%20image%2020230929155456.png)
Here is what happens when you do it on a cube:
![](Pasted%20image%2020230929155904.png)
(That is a perfect sphere, not just a vaguely sphere-like shape.)
In fact, you can get any shape out of a cube.
# Implicit Surfaces
An implicit surface is a mesh defined by an equation. For example:
![](Pasted%20image%2020230929160057.png)
Implicit surfaces are very efficient, as you can make a perfectly smooth shape with no need for a large amount of polygons. They are also very efficient for boolean operations such as intersection, as there is only a single equation to handle.

However, the obvious limitation is that you can only create shapes which you can express as an equation. If you want to create the shape shown above but with a bump on the surface, you cannot.

Notably, you can do very easy bounds checks:
![](Pasted%20image%2020230929160613.png)

# Procedural Surfaces
A **surface of revolution** defines a 3d surface by first defining a 2d shape, and then rotating it around some axis to create a 3d surface. For example:
![](Pasted%20image%2020230929160753.png)
A surface of revolution can be represented as this formula:
![](Pasted%20image%2020230929160825.png)
Where $s(u,v)$ is the function defining the surface, $R(v)$ is the rotation matrix and $q(u)$ is the vector being rotated around.

Another kind of procedural surface is the **Generalised cylinder** (aka **General Swept Surface**), which takes a 2D profile and a 3D curve and produces the shape of the 2D profile sweeping across the 3d curve. For example:
![](Pasted%20image%2020230929161305.png)
A surface of revolution is actually a special case of a generalised cylinder, where the trajectory is a circle.
![](Pasted%20image%2020230929161503.png)

# Differential Parameters of Surfaces
## Frenet Frame
The Frenet Frame of a surface is a right-hand system formed by the first, second and third derivatives of a surface.
![](Pasted%20image%2020230929161825.png)
T (tangent/velocity) is the first derivative.
N (normal) is the second derivative.
B (binormal) is the third derivative, and is equal to the cross product of the other two.

The tangent is usable, but there is a problem with the normal: At points of inflection (when the curvature changes sign) the normal becomes zero and then flips sides.
![](Pasted%20image%2020230929162211.png)
