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
Take this example pseudocode for clipping:
![](Pasted%20image%2020230317140546.png)

## Input-output Mapping
A brightness adjustment maps the intensity values in an image linearly, like this:
![](Pasted%20image%2020230317140658.png)
And a contrast adjustment maps the intensity values polynomially, like this:
![](Pasted%20image%2020230317140725.png)
You can represent these transformations, and many others, as mappings from some input range to some output range:
![](Pasted%20image%2020230317140823.png)
You can represent these mappings in code as mathematical formulae, or with a lookup table.
![](Pasted%20image%2020230317141104.png)

Here is the "solarise" filter, which maps some pixels of the input to their inverse if they are over a certain threshold:
![](Pasted%20image%2020230317141237.png)
The mapping looks like this:
![](Pasted%20image%2020230317141328.png)
Here is another filter called "Min-max linear stretch", which essentially takes some interval of the input image and "stretches" it to fill the entire colour space, clipping any colours that fall outside:
![](Pasted%20image%2020230317141509.png)
The mapping looks like this:
![](Pasted%20image%2020230317141623.png)
## Thresholding Part 2 
The mapping for threshold looks like this:
![](Pasted%20image%2020230317141721.png)
Question: how do you decide on a threshold value? Here is the histogram of a greyscale image:
![](Pasted%20image%2020230317141820.png)
You can see a noticeable peak; can this help us to pick a threshold?
Because usually the point of thresholding is to separate objects of interest, we may decide (as humans) that we will place the threshold somewhere around 170, to separate this peak from the plateau before it. How might a computer make this decision?
#### Percentile calculation of T
If we know that an object should occupy a certain percentage of the pixels in an image, we can automatically select T.
![](Pasted%20image%2020230317142223.png)
![](Pasted%20image%2020230317142318.png)
![](Pasted%20image%2020230317142411.png)
Note that this algorithm **was not affected by the lighting of the image**.
#### Histogram mean calculation of T
For each colour/intensity, we find the average intensity above and below it. We then take the average of those two averages to find a midpoint. The intensity which is closest to its calculated midpoint is chosen as T.
![](Pasted%20image%2020230317142609.png)
#### Otsu's method
Otsu's method is similar to the histogram method, though the maths looks significantly more complicated. Just look at this pseudocode instead:
![](Pasted%20image%2020230317142829.png)

These methods of finding a threshold value can only do so much; thresholding is inherently limited by the fact that the colours/intensities of the background and objects are bound to overlap in some conditions.
![](Pasted%20image%2020230317143004.png)
When an overlap like this exists, there is no "correct" solution; you can only get the least bad solution. For example:
![](Pasted%20image%2020230317143046.png)
