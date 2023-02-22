#### Recap: the camera analogy
If you want to take an image of something with a real life camera, you take these steps:
1. Arrange the subjects of the photo to create the desired composition
2. Position the camera to point at the scene
3. Choose a camera lens based on the angle, zoom, etc...
4. Decide on the final size of the photo
These steps are roughly analogous to 3D viewing:
1. Set the Modelling Transformation
2. Set the Viewing Transformation
3. Set the Projection Transformation
4. Set the Viewport Transformation

This page defines the Projection Transformation.

## Planar geometric projections
How do we map 3D world coordinates to 2D coordinates on a screen? 
![](Pasted%20image%2020230222125601.png)
There are many ways to do this, each with different properties.
![](Pasted%20image%2020230222130149.png)

## Parallel Projections - Orthographic
In a parallel projection, the lines between 3D points and their 2D projection are parallel. 
![](Pasted%20image%2020230222130757.png)
This is an **orthographic** projection. It gives an undistorted view of some side of an object, which makes it useful for seeing the real proportions of an object. However, anything behind the frontmost faces will be completely obscured.
![](Pasted%20image%2020230222131112.png)
#### Making an orthogonal projection
Making an orthogonal projection matrix is extremely simple. Consider the case where the projector is parallel to the z-axis (i.e. the 'camera' is 'looking down' the z-axis). All you have to do is remove the z depth from each point, giving this matrix:
![](Pasted%20image%2020230222131621.png)
You may notice that this matrix (and in fact all projection matrices) are **singular**; that is, they have no inverse. 

#### Making an axonometric projection
There are multiple types of axonometric projection to choose from:
- **Isometric**: The plane that is being projected onto is symmetrical to all three axes (i.e. the projector is at a $45\degree$ angle to all of the axes.)
- **Dimetric**: The plane that is being projected onto is symmetrical to three of the axes (i.e. the project is at a $45\degree$ angle with 2 of the axes.)
- **Trimetric**: The plane that is being projected onto is placed in any location.

![](Pasted%20image%2020230222131836.png)
#### Making an oblique projection
![](Pasted%20image%2020230222133027.png)
(making matrices for axonometric or oblique projections isnt examined.)

## Perspective projection
In a perspective projection, the lines between 3D points and their 2D projections are **not** parallel. Instead of a projector line, there is a centre of projection (or an "eye point"); the lines between 3D points and their 2D projections must meet at this eye point, which is behind the projection plane. In this kind of projection, distant objects appear to converge toward a vanishing point, and lines of an object that are truly parallel may not appear to be.
![](Pasted%20image%2020230222134141.png)
![](Pasted%20image%2020230222134323.png)
There are three kinds of perspective projection:
- **1-point perspective**: The projection plane is parallel to two of the axes (i.e. the 'camera' is looking 'straight at' the scene). This projection will have 1 vanishing point.
- **2-point perspective**: The projection plane is parallel to one of the axes. This projection will have 2 vanishing points.
- **3-point perspective**: The projection plane is parallel to none of the axes. This projection will have 3 vanishing points.

![](Pasted%20image%2020230222134553.png)
#### Making a 1-point perspective projection
You have a set of axes, an eye point (centre of projection), and a projection plane. To project a point onto the projection plane, you need to trace a line from the eye point to the point and find the point $(x_p, y_p, z_p)$ where this line intersects with the projection plane. But how do you accomplish this?
![](Pasted%20image%2020230222134954.png)
Consider this scene when facing down each of the axis. If looking down the Y axis at the XZ plane, you see this:
![](Pasted%20image%2020230222135447.png)
$$\frac{x_p}{x} = \frac{z_p}{z}$$
$$\frac{x_p}{d} = \frac{x}{z}$$
