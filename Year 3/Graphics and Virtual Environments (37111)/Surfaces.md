Unlike curves, there are 4 kinds of surface:
![](Pasted%20image%2020230929134030.png)
# Explicit Surfaces
Surfaces defined in terms of a collection of concrete units, such as points in a point cloud or vertices in a mesh. Meshes are the more common type.
# Tensor Product Splines
Analogous to splines when talking about curves. A tensor product spline is a surface defined in terms of a curve function, which produces a smooth surface.
We know how to define Bezier curves - how might we extend this from a line to a solid surface?
Consider how a Bezier curve is constructed, by interpolating smoothly between the control points according to $t$. Now imagine the control points themselves moving along another Bezier curve, according to another variable called $v$. For each value of $v$ you would have a single Bezier curve, similarly to how you have a single point for each value of $t$ in a regular Bezier curve. If you do this continuously for every single value of $v$, the individual Bezier curves will assemble into a smooth surface.
![](Pasted%20image%2020230929134647.png)

This surface, made of cubic Bezier curves, requires 16 control points.