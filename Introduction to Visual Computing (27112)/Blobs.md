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

Here is an example run:
![](Pasted%20image%2020230419121820.png)
![](Pasted%20image%2020230419121847.png)
Note that the second pass is more important than you might initially think! Lots of edge pixels don't get included in the obvious group like you might have assumed.
## Boundary Detection
If you want the outline/boundary of the blob, you can use this procedure:
![](Pasted%20image%2020230419122352.png)
![](Pasted%20image%2020230419122444.png)
![](Pasted%20image%2020230419122508.png)
![](Pasted%20image%2020230419122536.png)
## Blob Description
Some useful descriptive information can be derived from blobs.
#### Random physics revision of Moments
![](Pasted%20image%2020230419122651.png)
![](Pasted%20image%2020230419122713.png)
![](Pasted%20image%2020230419122758.png)
![](Pasted%20image%2020230419122901.png)
![](Pasted%20image%2020230419122934.png)
![](Pasted%20image%2020230419123032.png)
You can think of it like Moments, or just like weighted averages. Sum together every x co-ord with the number of pixels in that column, and take the average. Same with y, but rows instead of columns.
![](Pasted%20image%2020230419123234.png)
![](Pasted%20image%2020230419123400.png)
![](Pasted%20image%2020230419123453.png)
## Chain Codes
Imagine a compass like this, where each direction is assigned a number:
![](Pasted%20image%2020230419123545.png)
Now place this compass on the boundary of a blob, and follow the boundary, noting the number that corresponds to the direction you went with every step:
![](Pasted%20image%2020230419123649.png)
A chain code is a numerical representation of a blob by the shape of its boundary.
These codes are **cyclic**; you can start at any point on the boundary and you will get the same code, albeit shifted across.
They are **not rotationally invariant**. If the shape is rotated by 90 degrees, a different code will be produced.

Chain codes are a compact representation of a blob: If you were to represent the previously shown blob by giving the $(x, y)$ coordinates of each boundary pixel, it would take 14 coordinates. Assuming each number is a byte, a coordinate will take 2 bytes and the whole blob would take 28 bytes to represent. If a coordinate couldn't fit in a single byte (i.e. if the image was more than 255 pixels wide in either direction) then it would be even larger.
Since there are only 8 possible values for each digit in the chain code, each digit can be represented by 3 bits. Therefore, this chain code only takes 42 bits or 5.25 bytes.
#### Differential chain codes
A regular chain code, as stated before, is not rotationally invariant. If this is a desired property, then you can use differential chain codes instead:
![](Pasted%20image%2020230419124559.png)
![](Pasted%20image%2020230419124658.png)
Clearly, this method is a pain to do manually. Instead, think in terms of the differential nature of the method:
![](Pasted%20image%2020230419124855.png)
As mentioned before, these differential chain codes are **rotationally invariant**, and they are also **cyclic**.
#### Perimeter from a chain code
(Note this is for a regular chain code, not a differential one.)
![](Pasted%20image%2020230419125521.png)
Basically,
```python
alg perimeter_of_chain_code(code):
	let sum = 0
	for direction in code:
		if direction in [0, 2, 4, 6]:
			sum += 1
		else:
			sum += sqrt(2)
	return sum
```
#### Area from a chain code
![](Pasted%20image%2020230419125827.png)