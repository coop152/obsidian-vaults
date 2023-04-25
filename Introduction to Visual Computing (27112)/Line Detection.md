![](Pasted%20image%2020230425132114.png)So we can detect edges in an image using the filters covered previously, but how can we actually use these edges in computations? We need an algorithm to convert an edge map to a set of lines.
There are some complications that arise when trying to do this; while edge detection generally looks quite good to the human eye, with it automatically filling in the gaps, there *are* holes in the lines that disrupt them from being actual contiguous lines. For example:
![](Pasted%20image%2020230425132233.png)
An algorithm that solves this issue and allows the extraction of lines from an edge map is **Hough**.
## Hough Lines
![](Pasted%20image%2020230425132408.png)
You can see in this image that there are three points which are clearly part of a line, and one that isn't. One approach we might think of is to apply a **minimum number of points in each line**; in this image, the only valid line goes through three points.
#### Hough Parameter Space
![](Pasted%20image%2020230425132549.png)
(For now, we will discuss the problem using Cartesian coordinates.)
If we had the equation of a straight line ($y = mx + c$) and we wanted to plot it, we would chose a range of values for $x$ and plot them on a graph.
We can do the same in **parameter space** using the straight line equation $c=-xm+y$, where we vary $m$ to calculate $c$.
![](Pasted%20image%2020230425132856.png)
![](Pasted%20image%2020230425132938.png)
This is the Hough Parameter Space. (Note: this is a graph of m to c, NOT x to y).
Notice that three of the lines cross at $m = -1, c= 0$, leading to the equation $y = -1x +0$.
This is the equation of the line that goes through three of our points.
![](Pasted%20image%2020230425133254.png)
![](Pasted%20image%2020230425133340.png)
How to perform this procedure programmatically?
We can create a 2D array and use the columns for the values of $m$ and the rows for the values of $c$. 
![](Pasted%20image%2020230425133608.png)
This matrix represents the parameter space. Whenever a line is drawn in the parameter space, fill in the corresponding space in the matrix.
![](Pasted%20image%2020230425133830.png)
![](Pasted%20image%2020230425133842.png)
![](Pasted%20image%2020230425133853.png)
![](Pasted%20image%2020230425134021.png)
Essentially, you're drawing a low resolution bitmap of the graph with a very, very low opacity brush.
When you've drawn every line, we can choose the entries with the highest values. What we can do is decide on a minimum value; any line that goes through more than this number of points will be included in the result as a line. This is the **Hough Threshold**. Previously, when we decided that 3 was the minimum number of points to be considered a line, we set a Hough Threshold of 3.
In this case, we pick the only cell in the matrix greater or equal to 3 and we read off the coordinates of that cell: $c=0, m=-1$. This is the resulting line.
#### Vertical Lines & Polar Coordinates
You may have noticed a problem with the previous method; how do you detect a line in the image if it's vertical? With Cartesian coordinates, a vertical line has a gradient of $m = \infty$. Clearly we cannot have a matrix with $\infty$ size in one direction.
Instead, we can use Polar coordinates:
![](Pasted%20image%2020230425134655.png)
So as before, we use a matrix to represent the parameter space. This time, the parameters are $\theta$ and $r$, instead of $c$ and $m$.
![](Pasted%20image%2020230425134826.png)
![](Pasted%20image%2020230425134908.png)
![](Pasted%20image%2020230425134925.png)
![](Pasted%20image%2020230425134933.png)	
From this, we can see that the highest count occurs at $\theta = 45^\circ, r=0$.
Due to the way we've represented lines, the resulting line is $0$ away from the origin and perpendicular to the line $\theta = 45^{\circ}$ (thus has angle $-45^{\circ}$). Therefore, the resulting line is $y = -x$ (skipping the working cause representing a line in polar coordinates is a pain).
#### OpenCV
OpenCV features a Hough function:
![](Pasted%20image%2020230425135722.png)