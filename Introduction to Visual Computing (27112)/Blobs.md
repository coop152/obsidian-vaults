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
This representation shows each region as a different shade of grey. The number of shades (or, the number of labels) is a count of the tadpoles.
Note that the background was also labelled as a distinct region; it is just shown as black. In this instance the background was labelled 0 and each object was labelled from 1 to N. 