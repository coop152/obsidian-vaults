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
![](Pasted%20image%2020230317135409.png)
The histogram is simply shifted right by 50. You can see that we have gained no information in the dark areas, simply boosted the minimum intensity. In fact, we have lost detail in the bright areas as those colours have all been homogenised to the same max intensity.
**Contrast Adjustment**:
![](Pasted%20image%2020230317135546.png)
![](Pasted%20image%2020230317135618.png)
This adjustment causes gaps in the histogram.

## Clipping
Question: How do you handle 255 + 1 in the context of colours?
Since colours are unsigned bytes (most commonly), then 255 + 1 = 0 due to an overflow. But does this make sense for a colour? What effect would this have if you, for example, added 50 intensity to an image to increase its brightness? Any pixels that exceeded the max brightness would wrap around and become dark, which is not the desired behaviour.
Instead, we **clip** the intensity of colours when performing transformations; that is, if the transformed value exceeds the max possible value, we set its value to the max possible value instead of allowing it to overflow, and vice versa for the minimum value.
![](Pasted%20image%2020230317140208.png)
