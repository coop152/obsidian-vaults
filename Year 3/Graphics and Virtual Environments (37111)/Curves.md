**Explicit Modelling** - Where geometry is represented by a set of concrete points, which are assembled in some way. Examples include point clouds, polygon meshes (this is the kind covered last year), and NURBS surfaces.
![](Pasted%20image%2020230921151153.png)

**Implicit Modelling** - Where geometry is not represented by a set of concrete points. Examples include level sets and algebraic surfaces.
![](Pasted%20image%2020230921151216.png)

# Smooth Geometry
In many cases, we want properties of our geometry or animation to be smooth. For example, we want our 2D vector art to have smooth edges, we want our 3D geometry to have smooth surfaces, we want our 3D models to smoothly animate, etc.
## Polylines
![](Pasted%20image%2020230921151626.png)
Simply a set of points connected by lines. While simple, they completely lack smoothness.
They are still of great importance, as they are essentially what a GPU takes as input for rendering; while other kinds of lines may be used, when it comes to rendering polylines must be used.

## Spline
![](Pasted%20image%2020230921152126.png)
Spline is a general term for any kind of curve that smoothly connects a set of lines. The points that are used to construct a spline are called **control points**.
There are two distinct categories of splines:
**Interpolation** - Given a set of points, produce a smooth curve that *exactly* passes through every point.
![](Pasted%20image%2020230921152354.png)
**Approximation** - Given a set of points, produce a smooth curve that follows a similar trajectory to the points, but is not required to pass through every point.
![](Pasted%20image%2020230921152609.png)

### Cubic Bezier
![](Pasted%20image%2020230921153030.png)
Cubic Bezier is an approximating curve. The first and last control points will be touched exactly by the curve, while the other two control points define the way the curve bends on its way between the points. 
This curve has some interesting properties:
- The control points form a *convex hull* which the curve will never fall outside of.
- The curve is tangential to the convex hull going out of $P_1$ and going into $P_4$.

These are both useful properties for many reasons.

The Cubic Bezier can be defined by this function:
![](Pasted%20image%2020230921153844.png)
where $P_1$, $P_2$, $P_3$ and $P_4$ are coefficients representing the control points and $t$ is the progress along the curve (in the interval 0 to 1).
The terms in this function are basis functions called **Bernstein Polynomials**:
![](Pasted%20image%2020230921154232.png)
![](Pasted%20image%2020230921155220.png)

Alternatively, you can define it using matrices:
![](Pasted%20image%2020230921154505.png)
![](Pasted%20image%2020230921154532.png)
In this function, G/Geometry is a matrix representing the position of the control points, and B/Spline Basis and T/Power Basis together represent the Bernstein Polynomials seen in the previous representation.

But why use these Bernstein Polynomials? Because of these properties:
- **Non-negativity** - These polynomials output only positive values.
- **Partition of unity** - The sum of all of the Bernstein Polynomials at any given value of $t$ will add up to 1.

These two properties are why the Bezier curve remains inside the convex hull.