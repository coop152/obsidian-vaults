## Part A
1. c 
2. a
3. d
4. b
5. a
6. d

## Part B
1. Modelling is the act of creating a model, while rendering is the act of displaying a model (or models) on a screen.
2. A 3x3 matrix cannot represent 3D translations.
3. "The camera" represents the viewpoint (location, angle, FOV, etc...) from which the rendered image will be taken.
4. Before scaling, the cow must be transformed and rotated such that B coincides with the X axis. It must then be scaled by the desired factors, and then the rotations and transformations done in reverse order. This is the series of events:
	- Transform such that B passes through the origin (matrix M1)
	- Rotate about Y axis such that B lies on the XY plane (matrix M2)
	- Rotate about Z such that B lies on the X axis (matrix M3)
	- Scale about the origin, by factors s, t, u (matrix M4)
	- Apply inverses of the rotations and transformations, in reverse order
	- $\text{Cow}' = M_1^{-1}M_2^{-1}M_3^{-1}M_4M_3M_2M_1\text{Cow}$

5. The near and far clip planes are minimum and maximum distances that objects must be from the camera to be rendered. The near clip plane is the minimum distance, and the far clip plane is the maximum.
6. I would use Phong shading to ensure specular reflections are properly handled, as Gouraud performs poorly with specularity. To apply Phong for one polygon in the mesh you first calculate the surface normal for each vertex. Then you go over the polygon in scanlines; for each scanline, find the edge where drawing begins. Interpolate between the surface normals of that edge's vertices to get the surface normal at that specific point on the edge where the scanline intersects with it. Do the same with the edge where drawing finishes. Now that you have two surface normals for the beginning and end of the drawing area in the scanline, interpolate between these two normals for each pixel, finding the surface normal of that exact pixel and then applying the global illumination model. Do this for every scanline containing the polygon, and then do this for every polygon in the mesh.
7. A normalised vector is a vector that has a magnitude of 1. These are used in computer graphics as part of local illumination, specifically when calculating the angle between a light source and a surface for directional lighting.
8. (not examinable this year)
9. Rotation followed by translation.
10. Textures usually need to be filtered during rendering because the size of the texture and the size of the destination surface do not match exactly. If they did then the texture could be drawn trivially, but most of the time the texture will need to be scaled or otherwise transformed in order to fit. Bilinear interpolation achieves this by calculating the (fractional) coordinate of the desired pixel in the source texture