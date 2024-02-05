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
