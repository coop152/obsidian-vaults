In an image, edges are areas of large colour discontinuity. They usually represent useful properties about objects in the image, as they arise from:
- Depth discontinuity
- Surface colour discontinuity
- Illumination discontinuity

An edge is only an edge if the change in colour/intensity is *rapid*; the change must take place over a short distance.
If we represent an image as an intensity function $I(x)$, then a portion representing an edge might look like this:
![](Pasted%20image%2020240205152400.png)
We want to detect the location where the function is changing the fastest. If we're concerned with rate of change of a function, then differentiating is a good idea:
![](Pasted%20image%2020240205152516.png)
And now we have a nice spike right on our edge. We can differentiate again to get this:
![](Pasted%20image%2020240205152706.png)
Which gives the "direction" of the edge: Intensity went up when going left-to-right and down when going right-to-left.
Here is an example with two edges:
![](Pasted%20image%2020240205152850.png)
# Edge detection w/ Convolutions
We can create a convolution kernel that is analogous to differentiation, allowing us to find edges in an image with convolution. Here is a kernel that performs this task (in 1 dimension for simplicity):
![](Pasted%20image%2020240205153108.png)
We can also create a convolution kernel for the second derivative:
![](Pasted%20image%2020240205153413.png)
Unfortunately, these are not actually very useful for finding edges in real images because of **noise**.
![](Pasted%20image%2020240205153458.png)
Differentiation is very strongly affected by noise and small structures in an image. If we want to use it to find edges in our image, we need to get rid of this noise first.
To solve this, we use a **smoothing filter**. In this example, $f$ is a gaussian smoothing kernel:
![](Pasted%20image%2020240205153746.png)
By applying a smoothing function we can eliminate the noise and successfully use differentiation to find the edge.
Here is the same process with discrete values:
![](Pasted%20image%2020240205154033.png)
Also, recall that convolution is associative. If we have a kernel $d$ that can differentiate an image, and we're performing two convolutions $I *f*d$ to find the edges, then instead of convolving twice we can pre-apply $d$ to $f$ and get a single combined kernel. This kernel can be applied to any unsmoothed image directly to find edges.
![](Pasted%20image%2020240205154312.png)
# Popular edge detection kernels
## Prewitt
This is pretty much the edge detection kernel given previously, but in 2D. Because there's two directions, there are actually two Prewitt kernels, one for each direction.
![](Pasted%20image%2020240205154818.png)
Essentially, Prewitt differentiates the image in one direction to find discontinuities. It also smooths the image out in the other direction.
It is sometimes called "the Prewitt operator".
## Sobel
Sobel is similar to Prewitt, but with weighting towards the middle. It also has two kernels, one for each direction.
![](Pasted%20image%2020240205155320.png)
Sobel is the most widely used "simple" edge detector.

# Image Gradient
For both Prewitt and Sobel the output is two images, one for each kernel. These images are called the **derivative images**, or the **gradient images**. 
![](Pasted%20image%2020240205160047.png)
(Where $I'_x$ is the output of the x-direction kernel, and $I'_y$ the output of the y-direction kernel.)
By putting these together we get the **image's gradient**:
![](Pasted%20image%2020240205160410.png)
(it's $\delta x$ instead of $dx$ cause it's a partial derivative. Don't worry about it.)
$||\nabla I||$ is the **edge magnitude**, and it represents how "hard" or "strong" the edge is. Softer edges will have a lesser gradient, and vice versa.
$\theta$ is the **edge orientation**, and it gives the orientation of the edge (obviously). In the form of an angle.

Here is an example of an image's gradient:
![](Pasted%20image%2020240205161122.png)
Here is the results of finding the magnitude and orientation:
![](Pasted%20image%2020240205161210.png)
# Decomposable Kernels
Some kernels (specifically, Prewitt and Sobel) are structured in such a way that they can be **decomposed** into two smaller kernels. For example, here is the vertical edge Sobel kernel:
![](Pasted%20image%2020240205161757.png)
Applying these two smaller kernels is faster than applying the big kernel. In this example, calculating the result of applying the big kernel to one pixel takes 9 multiplications, but applying the two smaller kernels only takes 6. In the general case, an $n \times n$ kernel will take $n^2$ multiplications, while the decomposed kernels ($1 \times n$ and $n \times 1$) will take $2n$ multiplications.

# Why Gaussian smoothing?
We know that an image needs to be smoothed before edge detection, but why use Gaussian smoothing? The obvious first answer is that it looks better than an average filter, but there are more reasons.
Gaussian is **decomposable**:
![](Pasted%20image%2020240205162749.png)
This is a good thing by itself, but it's extra important for a smoothing filter because they tend to get quite large.
It is also symmetric, which is good because (WATCH NEXT LECTURE WHERE AN ANSWER WAS PROMISED)