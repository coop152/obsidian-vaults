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
![](Pasted%20image%2020230524110409.png)
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

## Deriving the view transformation (and justifying the duality by way of example)
There exists a transformation $T_c$ that maps the $XYZ$ axes to $\hat{S}\hat{U}\hat{F}$ (i.e. translates the origin to $E$ and rotates such that $XYZ$ = $\hat{S}\hat{U}\hat{F}$). This transformation, if applied to a camera at the origin facing down the Z-axis (i.e. the default view), would change the viewpoint to that of the camera at $E$, which is what we want to do; our aim is to render from the perspective of the camera.
However, remember that there is no actual camera to move; we cannot change the view from the default. Instead, imagine we moved the camera $E$ and the entire scene along with it, such that the camera's view is now the same as the default view, but the objects have all moved in such a way that they have maintained their relative position to the camera. This will produce the exact same scene, but we have moved the objects instead of the camera, which we *are* allowed to do.
To do this we will apply the inverse of $T_c$ (called $T_c^{-1}$) to all of the objects in the scene, which  will translate the $\hat{S}\hat{U}\hat{F}$ camera system to the default $XYZ$ camera system.
To do this, we follow these steps:
1. Translate by $-E$: "Move the camera to the origin"
![](Pasted%20image%2020230214142850.png)
2. Rotate such that the axes of the camera $\hat{S}\hat{U}\hat{F}$ are coincident with the world aces $XYZ$, where $\hat{F}$ is aligned with $-Z$: "rotate the camera to match the default view"
![](Pasted%20image%2020230214143044.png)
3. Find the composite translation: $T_c^{-1} = T_2 \cdot T_1$
4. Apply this transformation to the objects in the scene.

## Summary
There is a duality of modelling and viewing; it says that we can achieve the same view as transforming the camera by $T$, if we instead transform the subject by $T^{-1}$.
The default view (which you can think of as the default "camera location") is at the origin looking down the z-axis.
Therefore, if we want to transform the view by some $T_c$, we instead transform the objects in the scene by $T_c^{-1}$.
None of this matters to the programmer in three.js! `camera.lookAt(x, y, z)` computes this all for you, maintaining the convenient and easy-to-understand illusion of a camera.