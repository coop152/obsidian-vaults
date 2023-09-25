## Edges
An "edge" is defined as an extended, significant local change in image intensity. We don't want to detect sharp changes between individual pixels; that will capture noise. Instead, we want to capture local changes in intensity over many pixels. To help with this, we can calculate the gradient vectors of an image:
![](Pasted%20image%2020230328115240.png)
![](Pasted%20image%2020230328115331.png)
![](Pasted%20image%2020230328115458.png)
## Roberts Cross Edge Detector
![](Pasted%20image%2020230328115735.png)
This is the simplest edge detector kernel. It has awkward edge positioning, where any detected edges fall in the join between the four pixels. It is also noise sensitive, meaning that if a pixel is corrupted then the edge strengths are equally corrupted.
These two kernels find edges in the two diagonal directions, meaning they give different results. Combining them will give all of the edges in the image. For example:
![](Pasted%20image%2020230328115950.png)
![](Pasted%20image%2020230328115958.png)
![](Pasted%20image%2020230328120006.png)
![](Pasted%20image%2020230328120115.png)
As you can see, this is not very effective.
## Prewitt Operator
![](Pasted%20image%2020230328120156.png)
The Prewitt operator estimates the location of edges at the centre pixel, which is easier to use than Roberts' result. It is more robust against noise, due to it's consideration of more neighbours for each pixel. Again, each kernel gives a partial result which must be combined to get all of the edges. For example:
![](Pasted%20image%2020230328120421.png)
![](Pasted%20image%2020230328120429.png)
![](Pasted%20image%2020230328120443.png)
Note that Roberts and Prewitt aren't actually that different in effectiveness:
![](Pasted%20image%2020230328120521.png)
Prewitt has a little less noise, but more importantly it has **thicker lines on more prominent edges**.
For a more exaggerated example, consider this test image:
![](Pasted%20image%2020230328120637.png)
When these edge detection algorithms are run on this test image, the results are like this:
![](Pasted%20image%2020230328120745.png)
Notice that Prewitt is, in fact, noticeably less noisy. It also has the thicker edges.
But why does Prewitt have less noise? Consider one of the kernels:
![](Pasted%20image%2020230328120927.png)
You can see the row vector as an **averaging** operation; it takes 3 subsequent rows and adds the values together, then (due to normalisation) divides the result by 3. **Averaging reduces noise**. The column vector can be seen as the vector that actually detects edges.
## Sobel Operator
![](Pasted%20image%2020230328121242.png)
Sobel is similar to Prewitt, though it gives more weight to the pixels that are in line with the current pixel. The results look like this:
![](Pasted%20image%2020230328121343.png)
![](Pasted%20image%2020230328121358.png)
![](Pasted%20image%2020230328121409.png)
Very similar results to Prewitt. This is to be expected, as when you break it down:
![](Pasted%20image%2020230328121452.png)
It is essentially the same operator but with a differently weighted average. The results are almost indistinguishable:
![](Pasted%20image%2020230328121526.png)
## Canny Edge Detector
John F. Canny, in 1986, decided that an edge detector:
- Should accurately find as many edges in the image as possible
- Should not falsely detect edges
- Should accurately localise detected edge points on the centre of the edge (No odd placement like Roberts)
- Should only mark any given edge one time in an image (No double-thick lines like Prewitt or Sobel)

> [!Reminder]
> This is the 2D Gaussian function:
> ![](Pasted%20image%2020230328121932.png)
> We can also change $\sigma$ to blur different amounts in the x and y directions:
> ![](Pasted%20image%2020230328122018.png)
> 

Here is a 5x5 Gaussian kernel:
![](Pasted%20image%2020230328122142.png)
We take an image with lots of noise that may disrupt an edge detector, and we blur it using this kernel:
![](Pasted%20image%2020230328122304.png)
Now, if we apply Sobel:
![](Pasted%20image%2020230328122330.png)
The Gaussian blur has reduced the noise in the image before the edge detection. With this, we can explain the Canny edge detector's algorithm:
1. Create a Gaussian smoothing kernel $G(x, y)$
2. Convolve the image with it to get a **smoothed image**, $I_S(x, y) = G(x, y) \otimes I(x, y)$
3. Apply an edge detector to the smoothed image (could be Roberts, Prewitt, Sobel...) and calculate edge normals for each pixel (that is, the **gradient magnitude** and **direction**). These edge normals are $\theta(x, y)$.
4. Classify the edge normals in $\theta(x, y)$ into one of these four categories depending on their direction:
	- Horizontal edge ($0/180\textdegree$)
	- Vertical edge ($90/-90\textdegree$)
	- $45\textdegree$ edge ($135/-45\textdegree$)
	- $-45\textdegree$ edge ($45/-135\textdegree$)
	- ![](Pasted%20image%2020230328123238.png)
	- Now each pixel in $\theta(x, y)$ only contains one of four values giving its direction.
5. Now we perform **non-maxima suppression** (aka edge thinning). Create an image $M_\text{thin}(x, y)$ that will contain the thinned edges.
6. For each pixel in $M(x, y)$:
	1. Find its direction in $\theta(x, y)$
	2. Find the direction of its 8 neighbours
	3. If two or more neighbours have the same direction and a higher magnitude then $M_\text{thin}(x, y) = 0$
	4. Otherwise, $M_\text{thin}(x, y) = M(x, y)$
7. Choose **two thresholds**, $T_L$ and $T_H$.
	- Magnitudes below $T_L$ are not edges.
	- Magnitudes between $T_L$ and $T_H$ are **weak** edges.
	- Magnitudes above $T_H$ are **strong** edges.
8. Create two binary images using these thresholds:
	- $M_S(x, y) = [M_\text{thin}(x, y) \geq T_H]$
	- $M_W(x, y) = [M_\text{thin}(x, y) \geq T_L]$
9. Because $T_H$ is higher than $T_L$, $M_W(x, y)$ contains pixels that are also in $M_S(x, y)$. Remove these duplicates: $M_W(x, y) = M_W(x, y) - M_S(x, y)$
10. We assume that all of the set pixels in $M_S$ are valid edge pixels. However, there will likely be gaps in the edges of $M_S$, so...
11. For each set pixel in $M_S$, if any of its neighbours in $M_W$ are set then set those same pixels in $M_S$ (in other words, if a strong edge has a weak neighbour, promote the weak edge to a strong edge)

In summary:
1. Smooth the input image with a Gaussian blur
2. Compute the gradient magnitudes and directions (edge normals)
3. Apply non-maxima suppression to the magnitudes 
4. Use double-thresholding and connectivity analysis to detect and link edges

Canny suggested that $T_H$ should be two or three times $T_L$.
This edge detection method is available in OpenCV as `Canny(imIn, ImOut, ThL, ThH)`.
The results look like this:
![](Pasted%20image%2020230328125339.png)
Sobel is still noticeably noisy, and the edges are quite thick. In contrast Canny has thin, continuous lines and less noise. Here is another example, with a human retina:
![](Pasted%20image%2020230328125500.png)
## Laplace
![](Pasted%20image%2020230328125836.png)
This edge detection kernel performs the **second derivative** on the gradients in the image. The point on the second derivative where the value crosses zero while going from positive to negative (the **zero-crossing point**) is the location of the edge.
The previous edge detection methods have required us to apply two kernels, one for each direction. However, Laplace can find edges using **a single kernel**. However, it does highlight noise. This can be helped using a Gaussian blur, as with Canny, but the results are (predictably) not as good:
![](Pasted%20image%2020230328130250.png)
This is called **Laplacian of Gaussian (LoG)**.
This leads into the next algorithm:
## Marr-Hildreth Algorithm
1. Blur the image with a Gaussian blur
2. Convolve with a Laplacian kernel (these two steps are LoG with signed arithmetic)
3. Mark zero-crossing pixels as edge pixels
	- That is, pixel **e** is an edge pixel if there is a differing sign on **any** of the pairs b/h, d/f, a/i, c/h
	- ![](Pasted%20image%2020230328130805.png)

## Difference of Gaussians (DoG)
This is performed by applying two Gaussian blurs to an image with different sigmas, then subtracting one from the other.
![](Pasted%20image%2020230328131055.png)
