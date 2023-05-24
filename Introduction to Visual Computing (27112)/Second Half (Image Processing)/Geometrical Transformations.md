(Note: Some of these would require interpolation for most uses will be covered later)
## Scaling
![](Pasted%20image%2020230317144821.png)
## Transformation
![](Pasted%20image%2020230317145002.png)
or use homogeneous coordinates.
[Transformations & Homogeneous Coordinates](Transformations%20&%20Homogeneous%20Coordinates.md)
## Reflection (Flipping)
![](Pasted%20image%2020230317145134.png)
![](Pasted%20image%2020230317145142.png)
## Shear
(Since this is a less common one, here is what it looks like:)
![](Pasted%20image%2020230317145258.png)
It's like mapping the image from a rectangle to a parallelogram by shifting each row of pixels some fixed amount.
![](Pasted%20image%2020230317145227.png)
This is another transformation where the image is usually translated to the origin first, then translated back.
## Rotation
![](Pasted%20image%2020230317145413.png)
Again, this one is about the origin, so you most likely want it to be transformed to the origin first and then back afterwards.
## Affine Transformations
For each of these transformations, you can see that each value in the transformation matrix is responsible for a certain set of the transformations:
![](Pasted%20image%2020230317145552.png)
## Camera Lens Distortion
![](Pasted%20image%2020230317145622.png)
![](Pasted%20image%2020230317145659.png)
These transformations involve an effect that gets stronger as points on the image get further away from a central point; this is called **Radial distortion**. Assuming that the origin of the image is the centre, these equations can be used to model this:
![](Pasted%20image%2020230317145837.png)
Another similar kind of distortion is **tangential distortion**, which can be modelled with these equations:
![](Pasted%20image%2020230317150032.png)
In OpenCV, there exists a function `undistort()` that can be used to reverse these kinds of distortion. You must give values for $k_1, k_2, p_1, p_2$ and $k_3$.
This correction is commonly used for wide-angled camera lenses, such as fisheye lenses or the wide-angle lenses you might find in a phone camera. Here is an extreme example of barrel distortion in a fisheye lens:
![](Pasted%20image%2020230317150425.png)
If we wanted to solve this distortion with OpenCV, we would need to find the right parameters. OpenCV provides a method to find these, in the form of an image of a checkerboard; When given many images of this checkerboard from the camera that needs to be corrected, OpenCV can automatically infer the correct parameters by analysing these images. As the optical properties of the camera do not change based on the subject of the image, these parameters will also work for other images from the same camera, allowing us to correct the other images.
Before correction, the checkerboard looks like this:
![](Pasted%20image%2020230317150714.png)
And after, it looks like this:
![](Pasted%20image%2020230317150822.png)
Here is the fisheye lens image after being corrected:
![](Pasted%20image%2020230317150917.png)
(This image still looks really bad due to the intense chromatic aberration around the edges from the cheap lens used.)

## Interpolation
So we have an image and we want to scale by a factor of two. For some of the pixels this is trivial; multiply the coordinates by two and put the colour of that pixel at those coordinates in the result.
![](Pasted%20image%2020230317151151.png)
But what do we do about these missing pixels? We need to interpolate.
#### Nearest Neighbour
Simply pick the closest pixel on the source image and use that.
![](Pasted%20image%2020230317151322.png)
Produces sharp, jagged results. May be desirable in a few cases, but not usually - we want a smooth image. There are many other algorithms that can achieve this, with varying properties:
![](Pasted%20image%2020230317151511.png)
This **does not** just apply to scaling; any kind of transformation where you might sample non-integer coordinates from the source image need some kind of interpolation.
The initially described method of scaling where you loop through the source pixels and produce result pixels from it is called **Forward Mapping**, and it is not suitable for interpolation any more complicated than nearest-neighbour.
The inverse of this method, **Reverse Mapping**, involves looping through the result pixels and then calculating the corresponding pixels in the source image. This will often give non-integer pixel coordinates in the source image, which is what facilitates the more complicated interpolation methods.
![](Pasted%20image%2020230317152048.png)