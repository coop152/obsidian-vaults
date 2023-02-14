We only have flat 2D displays. Any 3D object shown on the screen has to be **projected** from 3D to 2D. In real life, we project 3D to 2D all the time - think how cameras can create a 2D image from a 3D world.

## The camera analogy
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

## 3D Viewing Pipeline
![](Pasted%20image%2020230214132343.png)

## The default view
![](Pasted%20image%2020230214132642.png)
Simply removes all Z-depth, reducing the scene to 2 dimensions. The z=0 plane is mapped to the display screen, and whatever geometry there is on it gets rasterised, with the z-buffer applied.

## The duality of modelling and viewing
Instead of changing the camera's viewpoint, we can obtain the same location and orientation of the subjects by transforming them instead. This is important, because **there is no camera in CG**. There is only a set of objects that are to be rendered, and a series of transformations to be applied beforehand.
However, the idea of a camera is familiar and convenient, so we pretend that we have a camera anyway. Graphics APIs achieve this by presenting a "camera", but in fact applying equivalent transformations to the objects themselves.
It bears repeating:
**There is no camera.**

## Finding the camera's coordinate system
Given the camera's location $E$ and it's centre of view $C$, can you calculate the coordinate system $\hat{U},\hat{F},\hat{S}$?
1. Find the vector from the centre of view to the camera: $F = E - C$
2. Normalise $F$ to get $\hat{F}$.
3. What now?
More information is required; suppose we are also given a vector $V$ which gives the upward direction of the camera. We can't just normalise this and use it as $\hat{U}$, as there is no guarantee that its orthogonal to $\hat{F}$, and if it weren't then the coordinate system would be invalid. Therefore, the full method is this:
1. Find the vector from the centre of view to the camera: $F = E - C$
2. Normalise $F$ to get $\hat{F}$.
3. Normalise $V$ to get $\hat{V}$.
4. Take the dot product of $\hat{F}$ and $\hat{V}$ and normalise it, which gives a vector $\hat{S}$ perpendicular to both: $\hat{S} = \text{normalise}(\hat{F} \times \hat{V})$
5. Do it again with $\hat{F}$ and $\hat{S}$, to get $\hat{U}$: $\hat{U} = \text{normalise}(\hat{F} \times \hat{S})$
6. Discard $\hat{V}$; $\hat{U}$ will be the up vector.
7. Complete; we have found the camera's coordinate system where $\hat{F}$ is anti parallel to where the camera is facing, $\hat{U}$ points upwards (in parallel with the columns of the image) and $\hat{S}$ points sideways (in parallel with the rows of the image). This system makes no assumptions about the properties of $V$ and is guaranteed to be a valid orthogonal coordinate system.

## Deriving the view transformation 
To move the camera to this location, we would find the transformation $T_c$ that maps the XYZ axes to $\hat{S}\hat{U}\hat{F}$ (i.e. translates the origin to E and rotates such that XYZ = $\hat{S}\hat{U}\hat{F}$). However, there is no camera. 