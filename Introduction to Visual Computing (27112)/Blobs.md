**Generic Definition**: A group of pixels which share some property, and are connected.
For example, a group of touching pixels in a thresholded image. The property is that their intensities are all over a certain bound.
## Connectivity
There are multiple ways that you might define "connected":
![](Pasted%20image%2020230419115725.png)
These are not the only ways, and they do have some issues:
![](Pasted%20image%2020230419115804.png)
## Connected Component Analysis (Region Labelling)
The aim of Connected Component Analysis is to identify groups of contiguous pixels, and to derive separate blobs from these groups which can be labelled.
For example, if we want to count the tadpoles in an image:
First we reduce the image to one channel to threshold it:
![](Pasted%20image%2020230419120059.png)
Then we threshold the image. With this thresholded image, we use one of the definitions of connectivity to group the pixels together:
![](Pasted%20image%2020230419120150.png)
The number of separate regions that were labelled is a count of the tadpoles. (Note that the shades of grey are not unique and do not represent distinct regions.)
The background was also labelled as a distinct region; it is just shown as black. In this instance the background was labelled 0 and each object was labelled from 1 to N. 
There are some problems with this result, though:
- The tail of the top-middle tadpole was split into multiple regions by the thresholding
- In the top right, two tadpoles are detected as one region
- The tadpole on the middle right has eyes

These can be addressed using **Dilation** and **Erosion** (and **Opening and Closing**).

#### The algorithm
1. **First pass** (From left to right, top to bottom)
	1. If no neighbours have a label, the current pixel receives a new label
	2. If one or more neighbours have a label, the current pixel receives that same label
	3. If two or more neighbours have different labels, the current pixel receives one of the labels and the equivalence of the two labels is recorded
2. **Second pass** (From left to right, top to bottom)
	1. Re-label all equivalent labels to be equal