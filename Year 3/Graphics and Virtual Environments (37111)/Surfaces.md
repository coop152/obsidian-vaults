Unlike curves, there are 4 kinds of surface:
![](Pasted%20image%2020230929134030.png)
# Explicit Surfaces
Surfaces defined in terms of a collection of concrete units, such as points in a point cloud or vertices in a mesh. Meshes are the more common type.
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
