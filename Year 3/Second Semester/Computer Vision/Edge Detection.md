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
So we can get the peak by checking the second derivative for a zero-crossing.
Here is an example with two edges:
![](Pasted%20image%2020240205152850.png)
# Edge detection w/ Convolutions
We can create a convolution kernel that is analogous to differentiation, allowing us to find edges in an image with convolution. Here is a kernel that performs this task (in 1 dimension for simplicity):
![](Pasted%20image%2020240205153108.png)
We can also create a convolution kernel for the second derivative:
![](Pasted%20image%2020240205153413.png)
Unfortunately, these are not actually very useful for finding edges in real images because of **noise**.
![](Pasted%20image%2020240205153458.png)
Differentiation is very strongly affected by small scale structure in an image, be it noise or actual small objects. If we want to use it to find edges in our image, we need to get rid of this noise first.
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
## Canny
See ahead at [Canny Operator](#Canny%20Operator).
# Image Gradient
For both Prewitt and Sobel the output is two images, one for each kernel. These images are called the **derivative images**, or the **gradient images**. 
![](Pasted%20image%2020240205160047.png)
(Where $I'_x$ is the output of the x-direction kernel, and $I'_y$ the output of the y-direction kernel.)
By putting these together we get the **image's gradient**:
![](Pasted%20image%2020240205160410.png)
(it's $\delta x$ instead of $dx$ cause it's a partial derivative. Don't worry about it.)
$||\nabla I||$ is the **gradient magnitude**, and it represents how "hard" or "strong" the edge is. Softer edges will have a lesser gradient, and vice versa.
$\theta$ is the **gradient orientation**, and it gives the orientation of the gradient (obviously). In the form of an angle.

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
![](Pasted%20image%2020240206124615.png)
It is also symmetrical, which is very good because it doesn't influence the apparent orientation of edges. If it wasn't symmetrical, it would mess with the gradient orientation.
The way it blurs images anti-aliases them, which is another benefit for edge detection. Some filters (e.g. median blur) don't always get rid of hard edges, which would cause undesirable spikes in the histogram and thus false edges.
## Edge Scale
Gaussian smoothing also allows more control over how our image is smoothed.
![](Pasted%20image%2020240206122948.png)
When we smooth the image with Gaussian smoothing, there is a parameter $\sigma$ that we must set. This controls the spread of the distribution, and will affect the resulting edge detection.
Smaller $\sigma$ will result in less spread, and thus the kernel will have a higher weighting toward the middle pixel. Inversely, larger $\sigma$ will give a lower weighting toward the middle pixel.
For illustrative purposes, here is a 1D gaussian:
![](Pasted%20image%2020240206123252.png)
Compared to a 2D gaussian, which is what the smoothing kernel samples it's weights from:
![](Pasted%20image%2020240206123419.png)
If $\sigma$ is higher then the curve will be "sharper", and if it is lower then the curve will be "flatter". Looking at this 2D curve from above, and then writing down the height in each cell, essentially gets you a gaussian kernel. Here is an example of a larger one:
![](Pasted%20image%2020240206123531.png)
(Though it needs to be normalised before use.)
Here is an example of how different $\sigma$ values influence the smoothing output:
![](Pasted%20image%2020240206124740.png)
These smoothed images will provide very different results when put through edge detection: The less smoothed one will include more edges, perhaps some of them false or undesired, while the more smoothed one will include less edges, perhaps missing some smaller ones.
The ability to choose the scale allows you to adjust your edge detection's "sensitivity" to fit your use case. Very noisy images (as in, literal image noise or just very busy backgrounds) may need a higher $\sigma$ to extract any useful information, but in a controlled environment with clean images, you may use a low $\sigma$ to capture smaller edges.

## Analytical derivative
As mentioned before, we can combine the smoothing and the derivative filter into one kernel in order to save computation.
![](Pasted%20image%2020240206125549.png)
Another advantage of the Gaussian distribution is that we have an *analytical solution* to finding it's derivative. So, we don't even need to apply the derivative kernel; We can just find the analytical derivative of our chosen Gaussian distribution.
![](Pasted%20image%2020240206125812.png)
This new kernel has a name:
# Canny Operator
Not to be confused with the Canny **edge detector**, the Canny operator is the filter that results from the derivative of the Gaussian distribution.
Remember the earlier section where we created an all-in-one smoothing and "edge-detecting" kernel by differentiating our smoothing kernel?
![](Pasted%20image%2020240206130459.png)
When doing this with Gaussian smoothing, you get the Canny operator.
Note that we are only differentiating with regards to one variable, but we are using the 2D Gaussian distribution. Therefore, we actually have two Canny operators, one for each direction:
![](Pasted%20image%2020240206130745.png)
These detect edges in both directions.