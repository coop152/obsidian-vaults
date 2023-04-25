## Structuring Elements
Structuring Elements are shaped sets of pixels, for example:
![](Pasted%20image%2020230425140006.png)
Similarly to a convolution, these pass over an image one pixel at a time and apply a transformation.
## Erosion
Consider this binary blob:
![](Pasted%20image%2020230425140116.png)
Erosion follows these rules:
- If the center of the structuring element (SE) is over an object pixel and any of the other parts are over a background pixel, the pixel under the centre becomes a background pixel
- The output **must be drawn into a new image**. The output of one pixel does not depend on the output of another, only on the input image.

On the green object, we reach our first object pixel, and it features background pixels. Therefore, it becomes part of the background:
![](Pasted%20image%2020230425140415.png)
Same for the next one:
![](Pasted%20image%2020230425140430.png)
Until everything has been covered:
![](Pasted%20image%2020230425140558.png)
Without the illustrative grey colouring:
![](Pasted%20image%2020230425141318.png)
(Note: This image seems wrong. The top object pixel has a background neighbour, so it shouldn't be included.)
As a result of this erosion using a 3x3 SE, all of the borders of this object have been removed.
Here is an example on a real image. First we threshold:
![](Pasted%20image%2020230425140802.png)
And then we erode the image with a 5x5 SE:
![](Pasted%20image%2020230425140821.png)
The result is that the object will shrink in either direction. Many of the noisy particles have been removed, but some of the background noise inside the objects has also been expanded. It isn't noticeable in these high-resolution images, but the shapes have also shrunk by a few pixels around the boundaries.
## Dilation
Dilation is essentially the opposite of Erosion.
- If the center of the structuring element (SE) is over a background pixel and any of the other parts are over an object pixel, the pixel under the centre becomes an object pixel.
- The output **must be drawn into a new image**. The output of one pixel does not depend on the output of another, only on the input image.

While Erosion causes objects to "shrink", Dilation causes them to "grow".
On the green object, it looks like this:
![](Pasted%20image%2020230425141218.png)
![](Pasted%20image%2020230425141235.png)
![](Pasted%20image%2020230425141226.png)
![](Pasted%20image%2020230425141303.png)
As a result of this dilation using a 3x3 SE, a border has been added around this object.
On a real image, it looks like this:
![](Pasted%20image%2020230425141443.png)
It has the inverse effect of Erosion, where the small noisy bits of object grow in size but the noisy bits of background inside the objects shrink away. The objects have grown by a few pixels around the borders.

## Opening and Closing
So we have Erosion which can remove noisy object pixels, and Dilation which can fill in holes. Unfortunately they both cause the object size to change, which is often undesirable (e.g. if we want an image mask, or to measure size).
To solve this, there are two more operations which are composites of Erosion and Dilation.
#### Opening
This operation will remove 'sticky out bits' and object noise while enlarging holes, like Erosion. However, it will not change the size of objects. It is achieved by first eroding then dilating:
$$\text{open(I)} = \text{dilate}(\text{erode}(I))$$


#### Closing
This operation will fill in holes and enlarge 'sticky out bits' like Dilation. However, it will not change the size of objects. It is achieved by first dilating then eroding:
$$\text{close(I)} = \text{erode}(\text{dilate}(I))$$
