## Region processing functions
Region processing functions are functions that involve more than one source pixel, for example:
$$I'(x, y) = |I(x, y) - I(x+1, y)|$$
This specific function subtracts the intensity of the next pixel from the current one, implementing a simple form of **edge detection** for vertical edges:
Take, for example, this input:
![](Pasted%20image%2020230324113025.png)
Which produces this output:
![](Pasted%20image%2020230324113042.png)
You can also implement horizontal edge detection with this similar, yet different formula:
$$I'(x, y) = |I(x, y) - I(x, y+1)|$$
How might we combine these to get all of the edges in an image?
You could OR the images together:
$$I'(x, y) = I_H(x, y) \lor I_V(x, y)$$
This would involve thresholding first, to facilitate the binary logic operations.
You could add the images together, then threshold the result:
$$I'(x, y) = |I(x, y) - I(x, y+1)| + |I(x, y) - I(x+1, y)|$$
This is called the **city-block distance**. The results follow this graph:
![](Pasted%20image%2020230324113844.png)
Or you could use Pythagoras:
$$I'(x, y) = \sqrt{(I(x, y) - I(x, y+1))^2 + (I(x, y) - I(x+1, y))^2}$$
This is called the **Euclidean distance**. The results follow this graph:
![](Pasted%20image%2020230324114119.png)
Using this method and thresholding at 100, we get this result:
![](Pasted%20image%2020230324114344.png)
Increasing the threshold would remove weaker edges from the image, and vice versa.

## Convolutions
This is the definition of convolution:
$$I'(x, y) = W \otimes I(x, y) = \sum^a_{s=-a}\sum^b_{t=-b}W(s, t)I(x - s, y - t)$$
($\otimes$ is the symbol of convolution.)
$W$ is a matrix of values called the **filter kernel**, also known as the **filter template** or **filter mask**.
![](Pasted%20image%2020230324115317.png)
The two summations cycle through the elements of $W$, with $w_4$ in the previous diagram being $W(0, 0)$.
$$\sum^a_{s=-a}\sum^b_{t=-b}W(s, t)$$
Each of these weights is multiplied by a value in the image, then the results added up. The result of this multiply-then-sum is placed in the result image at the same place as the centre of the kernel.
![](Pasted%20image%2020230324115735.png)
This is an operation on a single pixel, but it takes all of it's neighbours into account. To apply the convolution to the entire image, it would need to be applied to each pixel individually. So, for this image, the next step would be to calculate this convolution:
![](Pasted%20image%2020230324115951.png)
But why is the intensity of the pixel retrieved like this?
$$I(x - s, y - t)$$
According to this, the pixel to be multiplied by $w_0$ is actually this one:
![](Pasted%20image%2020230324120139.png)
This is because Convolutions have the kernel flipped diagonally. If you wanted the matrix to line up as you expect, you would use a **Correlation** instead.
## Correlations
Correlations are like Convolutions, but the kernel is in the order that you would expect (i.e. top left weight corresponds to the pixel to the top and left of the centre pixel).
Most image processing software will actually do a correlation when asked to do a convolution. For example, OpenCV's `filter2D()` function.
So, while it technically isn't the right definition, when we say "convolution" in image processing we generally mean this operation:
![](Pasted%20image%2020230324120527.png)
That is, we actually mean a correlation. 

## In-place?
In point processing, we can calculate a new pixel value and then write it "in-place", back into the source image. That is, we can do:
$$I(x, y) = I(x, y) - 28$$
Rather than:
$$I'(x, y) = I(x, y) - 28$$
But we cannot do the same thing with convolution; you need a new image for the result.

## Off the edges?
You probably already noticed a problem with the previous diagrams:
![](Pasted%20image%2020230324121548.png)
What to do for the pixels around the edge? The kernel is going off the edge of the image. You could:
- Assume all of the outside "pixels" are 0
- Assume all of the outside "pixels" are 255 (or whatever the max intensity is)
- Wrap around to the other side of the image (using modulo or similar)
- "Extend" the image out by repeating the outermost rows/columns (just add/subtract 1 from the out-of-bounds image coordinates)

## Normalisation
What would be the result of applying this filter mask to an image where each pixel is 255?
![](Pasted%20image%2020230324122541.png)
Answer: $255 \times 9$, which is too big to put in the output image.
How to solve this? We can either normalise the mask, or normalise the input.
Normalising the mask looks like this:
![](Pasted%20image%2020230324123133.png)
Where the sum of the weights must add up to 1. This can be done simply by dividing each element by 9.
## Composite Filters
Convolution is associative:
$$A \otimes (B \otimes C) = (A\otimes B) \otimes C$$
(A and B are kernels and C is an image)
So, if we want to apply two filters to an image we can do it two ways:
- Apply kernel A to kernel B to get a 5x5 kernel. When applied to an image, this kernel will perform both of the filters at once.
- Apply both kernels to the image one after the other.
![](Pasted%20image%2020230324123724.png)
## Separable Kernels
![](Pasted%20image%2020230324124150.png)
It's true that applying these two small matrices will be the same as applying the bigger one, but is there a benefit to this? Yes:
![](Pasted%20image%2020230324125336.png)
For a 7x7 kernel, this would change to $49MN$ vs $14MN$, and the efficiency gains would only increase as the size goes up.
## Some kernels
**Smoothing**:
![](Pasted%20image%2020230324130200.png)
Simply average out the colours of an area. Softens the image.
![](Pasted%20image%2020230324130308.png)
Bigger filter maps will blur more.
**Sharpening**:
![](Pasted%20image%2020230324130404.png)
Emphasises the discontinuities of the image, but also amplifies noise.
![](Pasted%20image%2020230324130541.png)
**Edge detection (Laplace)**:
![](Pasted%20image%2020230324130633.png)
The kernel is similar to the sharpening filter, but instead of just emphasising the discontinuities it shows only the discontinuities.
![](Pasted%20image%2020230324130823.png)
## Noise
Noise is a deviation of a value from its expected value. There are a few kinds of noise which demand different methods of handling and arise for different reasons:
- Random changes
	- $I \rightarrow I + n$
	- where n is positive or negative
	- randomly distributed, with a mean of zero
	- $n$ is usually significantly smaller than $\max(x)$
- Salt and pepper noise
	- $I \rightarrow \{ max, min\}$, totally random
	- Not a very common kind of noise
- Imaging artefacts
	- Streaks of light
	- Bloom

![](Pasted%20image%2020230324132441.png)
When it comes to the source of noise, there are two schools of thought:
- Anything within the imaging system that is causing an unintended change
	- Electrical interference
	- Optical aberration
- Anything (at all) that causes a change
	- Atmospheric disturbance

Generally, the first is the preferred definition.

Here are some examples of real-world noise:
![](Pasted%20image%2020230324132704.png)
These are images taken by a phone camera, with good lighting on the left and bad lighting on the right. Both of these images were taken in colour; the signal had to be boosted so much that not only is it full of noise, it's devoid of colour.
And another example:
![](Pasted%20image%2020230324132813.png)
This is video tape interference, caused by a misalignment of the magnetic read or write head of either the recording device or the playing device.
And another:
![](Pasted%20image%2020230324132954.png)
This is a digital video, suffering from the effects of corrupted blocks in the MPEG encoding.
And another (two):
![](Pasted%20image%2020230324133125.png)
![](Pasted%20image%2020230324133153.png)
These are examples of atmospheric interference introducing noise. The first image was taken in heavy fog conditions, obscuring everything. The satellite image was taken while it was overcast, causing clouds to block the land that is being photographed.
And another:
![](Pasted%20image%2020230324133351.png)
Some CCDs are vulnerable to this effect when photographing very bright light sources, where the light bleeds into other pixels in the frame.

## Noise Reduction
So how do we remove the more typical kinds of noise?
- Smoothing
	- $\sum{x + n} = \sum{x} + \sum{n} \approx \sum{x}$
	- Since noise is random and has a mean of zero
- Local smoothing
	- Convolution (with which kernel?)
	- Removes detail
	- Introduces ringing
	- The amplitude of the noise will be reduced by template length
- Temporal Smoothing
	- Average together multiple images of the same subject
	- Called "stacking" in astronomy, commonly used

