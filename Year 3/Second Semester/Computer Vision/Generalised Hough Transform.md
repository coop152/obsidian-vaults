# Hough Lines
An algorithm that takes the output of edge detection (e.g. from Sobel or Prewitt) and finds lines in the image. For full recap see [these second year notes](Line%20Detection.md).
# Hough Circles
By performing an algorithm very similar to Hough lines but with the equation for a circle, we can locate circles in an image.
## Simple fixed radius case
We have a set of points that we believe to be points along a circle, as well as a radius that we believe the circle to be.
![](Pasted%20image%2020240213122657.png)
We want to find $(a, b)$, the centre of the circle. The equation of a circle is
$$(x-a)^2 + (y-b)^2 = r^2$$
Like with Hough lines, we will re-arrange this equation in order to get our points in **parameter space**. We can negate the insides of the brackets thanks to the square:
$$(a-x)^2 + (b-y)^2 = r^2$$
And this gives us the equation for a circle, but $a$ and $b$ are the parameters. We also insert our known radius, for this simplified case.

The process then works out like Hough lines; Instead of each point being a line in parameter space, each point is a circle. The circles are composed of every point such that a circle of radius $r$ centred on that point goes through $(a, b)$. 
Visual example:
![](Pasted%20image%2020240213123336.png)
This only shows three such circles, but imagine every possible circle that goes through this point; the centres would form another circle that is centred on $(a, b)$.

We do this for every point, just like Hough lines, and the point where the circles all intersect is the point where the centre of our detected circle lies.
![](Pasted%20image%2020240213123600.png)
![](Pasted%20image%2020240213123613.png)
Like with Hough lines we calculate this using an accumulator array, though we draw circles instead of lines.
## General unknown radius case
If we don't know the radius ahead of time, then we have three parameters, and therefore three variables in parameter space. This problem is simple enough; instead of finding the intersection of 2D circles in the 2D parameter space, we will be finding the intersection of 3D cones (because the resulting shapes will be circles with linearly increasing radius) in 3D parameter space.
Concretely, we will need a 3D accumulator array:
![](Pasted%20image%2020240213124156.png)
In each layer we will do the same as previously, with each layer using a different value for $r$. This results in a 3D volume of results, which you can search through to find the detected circle centres and radii.
![](Pasted%20image%2020240213124337.png)

# Generalised Hough Transform
