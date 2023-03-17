(Lots of obvious stuff about image representation goes here)
The origin of an image is at the top-left:
![](Pasted%20image%2020230309144457.png)
But when representing an image as a matrix, remember that the row comes first:
![](Pasted%20image%2020230309144606.png)

A point processing function takes an image and applies some function to each pixel individually, with no influence on or from other pixels.
![](Pasted%20image%2020230309144815.png)
To negate an image, subtract the intensity (mono or colour) from the max value.
To increase/decrease brightness, add some constant to the intensity (and clamp to the acceptable range).
You can implement a threshold like this:
![](Pasted%20image%2020230309145424.png)

## Image Histograms
An image histogram is what you would think from the name; it is a histogram of the colours that occur in an image, that allows you to visualise the distribution of colours in an image.
![](Pasted%20image%2020230317133847.png)
To get the histogram of an image, you count the occurrences of each colour/intensity.
For example:
![](Pasted%20image%2020230317134039.png)
![](Pasted%20image%2020230317134105.png)
![](Pasted%20image%2020230317134129.png)
This histogram displays some gaps; this is because **this image has been "upscaled" from 64 shades of gray to 256**. The upscaling algorithm hasn't attempted to smooth the colours, and has instead multiplied them all by some fixed amount.

Let's apply some pointwise transformations and observe how the histogram changes:
**Brightness Adjustment**:
![](Pasted%20image%2020230317134524.png)
