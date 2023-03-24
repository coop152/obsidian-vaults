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
But we cannot do the same thing with convolution.