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
