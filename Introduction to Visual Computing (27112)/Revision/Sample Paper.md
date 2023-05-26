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
6. 