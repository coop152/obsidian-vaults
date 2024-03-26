# Principal Component Analysis (PCA)
![](Pasted%20image%2020240326105143.png)
Suppose you have two sets of variables, for example scores for 24 student's Maths and English tests. These are plotted with the Maths results on the x-axis and English results on the y-axis.
Using this data we can:
- Draw a line of best fit
- Find the mean point
- Draw a region (ellipse) that contains most of the data

![](Pasted%20image%2020240326105728.png)
When annotated like this, you can clearly notice a property of this data; the largest variance is along the line of best fit ($v_1$), while the smallest variation is in the line perpendicular to it ($v_2$).
![](Pasted%20image%2020240326110301.png)
Say that the variance in $v_2$ is small enough that we can safely deem it negligible. We could use this to reduce our data to a single data point.
- Project every point onto $v_1$ (see blue point)
- Store the data as just $v_1$ and a list of distances along it, each distance representing a single point projected onto $v_1$
- To (approximately) reproduce the data, we just take $v_1$ and add points at the given distances along it.

With this, we can represent each point using a single value. The smaller the variance in $v_2$, the better this technique is. This method is called **Dimensionality Reduction**.
This method isn't very useful for a simple 2D example as shown, but it can be applied to higher dimensional data. For example, you could use it to visualise 4 variable data in 3D or even 2D.
So how would you do this in higher dimensions? We need to:
- Find a line of best fit for any dimension of data
- Find the distance of each point from this line

The **PCA algorithm** can do this.
## The PCA Algorithm
1. Assemble the data into a matrix, where each row represents one sample and each column represents a variable (sample count $\times$ variable count, the previous example would be 24 $\times$ 2)
2. Compute the **Covariance Matrix**, $C$ (instructions for this later)
3. Find the **Eigenvalues** ($\lambda_i$) and **Eigenvectors** ($v_i$) of $C$. Each eigenvector represents a line through the data, either the line of best fit or a line perpendicular to it, and the corresponding eigenvalue represents the variance in that direction. The total variance is $T = \sum^{N}_{i=1}\lambda_i$.
4. Take eigenvalues, starting with the largest, until their sum accounts for at least $p\%$ of the total variance $T$ (where $p$ is a parameter to the algorithm). Any eigenvalues we don't choose can be discarded, along with the corresponding eigenvectors.

These leftover eigenvectors/eigenvalues represent the new variables which we will encode the data with, which will likely be lesser in number. This is how we reduce the number of dimensions.
### Computing the covariance matrix
Take this example data, where we have 2 variables and 3 data points.
![](Pasted%20image%2020240326112304.png)
The covariance matrix will have size 2x2 (var count $\times$ var count).
![](Pasted%20image%2020240326112425.png)
The covariance matrix is as the name suggests; it is a matrix of all the covariances between the variables. We can immediately observe that **covariance matrices are symmetrical**:
![](Pasted%20image%2020240326112514.png)
And that the diagonal will be the regular variances of the variables, due to the definition of covariance.
But what actually is covariance? It is defined like this:
![](Pasted%20image%2020240326112617.png)
(Where $\mathbb{E}$ is the expected value, in this context the mean)
So let's find the covariance matrix for our data:
![](Pasted%20image%2020240326112732.png)
![](Pasted%20image%2020240326112723.png)
![](Pasted%20image%2020240326112739.png)
You can do this in OpenCV using a simple call to `calcCovarMatrix()`:
![](Pasted%20image%2020240326112903.png)

### Eigenvalues and Eigenvectors
For a square matrix $A$, an eigenvalue $\lambda$ and eigenvector $v$ is a pair of values that satisfy this equation:
$$Av=\lambda v$$
In terms of transformations, an eigenvector is a line that is **invariant** under the transformation represented by $A$, and the eigenvalue of that eigenvector is the amount by which the line is scaled. 
For example, if a transformation/matrix has an eigenvector $[1, 2]$ with eigenvalue $2$, it means that all points along the vector $[1, 2]$ through the origin will map back onto that vector, with their distance multiplied by 2.

To find eigenvalues, we solve
$$|A-\lambda I| = 0$$
Where $I$ is the identity matrix and $|~|$ is the determinant.

You can do this in python using numpy:
```python
from numpy import linalg as la
vals, vecs = la.eig(C)
```

### An example
Take the data from the beginning, with the 24 students and their Maths/English exam scores. We calculate the covariance matrix, then the eigenvalues and eigenvectors:
![](Pasted%20image%2020240326114135.png)
![](Pasted%20image%2020240326114311.png)
![](Pasted%20image%2020240326114329.png)
# Active Shape Models
Active Shape Models are a kind of shape matching model that allows for **non-rigid** matching. Non-rigid meaning it isn't limited to simply scaling, rotating, translating, etc. the reference shape; parts of the shape can be moved (somewhat) independently to allow for more flexibility.
an ASM is able to generate a multitude of different valid shapes based on those supplied in the training dataset.

The dataset for the example ASM we investigate looks like this:
![](Pasted%20image%2020240326115236.png)
This example is very simple, with the shapes having few points and with few samples. Ideally you would have much more than 6 to better capture variations in shape.
Specifically, this dataset has 20 data points per sample, with each point having an x and y - so each sample has 40 variables. For better results, you would prefer to have more points, but this makes creating the dataset take longer.

We then perform PCA on the entire dataset, with each sample being a data point with 40 variables. So, step by step:
1. Assemble the data into a matrix $D$. We have 40 variables (from 20 2-variable position pairs) so there are 40 *rows*, and we have 6 samples so there are 6 *columns*. (Note that you can flip the rows and columns if you like; transposing a matrix is trivial, and this reversed ordering is helpful later)
2. Compute the covariance matrix $C$ as described earlier. In this case it will be $40 \times 40$.
3. Find the eigenvalues and eigenvectors of $C$ by solving $|C-\lambda I| = 0$. We will get 40 eigenvalues/eigenvectors, with the matrix $V$ containing the eigenvectors being $40\times 40$ (40 eigenvectors of length 40).
4. Order the eigenvalues and choose the $K$ largest, though for ASM we don't entirely discard the less significant ones. More on that in a moment.

Now that we have the dimension-reduced eigenvectors and eigenvalues, we calculate a **mean shape** from the initial data matrix $D$. That is, we simply average out the values of each variable from every image, giving us this face shape:
![](Pasted%20image%2020240326121135.png)
The feature point positions here are the average positions of those points in the dataset.
## Generative Shape Models
Now we have a mean face shape and a set of eigenvectors/eigenvalues. The vectors represent ways in which faces in the dataset vary from the average face, with the $K$ largest ones we chose being the ones with the most effect.
We introduce a Shape Parameter vector $b$. This is a $40 \times 1$ (variable count $\times$ 1) vector that we can use to generate face shapes. To generate these faces, we use this formula:
$$x = \bar{x} + Vb$$
Where $\bar{x}$ is the mean shape and $V$ is the matrix containing the eigenvectors as columns. Each value in $b$ multiplies an eigenvector, which is added to the mean shape to deform it, producing $x$.

This shape parameter vector is also how we can eliminate the low-impact eigenvectors/eigenvalues from the final step of PCA. By simply using a weight of zero for that eigenvector in $b$, it will have no effect.
Here are examples of the faces generated with this model, when the 6 most impactful eigenvectors are added in varying amounts:
![](Pasted%20image%2020240326122323.png)
Each variable represents some kind of difference in face shape or image composition. You could interpret the first three as:
- $b_1$ changing the vertical position
- $b_2$ making the face skinnier or wider
- $b_3$ angling the face down or up (though that's a bit of a stretch)
- $b_4$ angling the face right or left

PCA automatically finds these **modes of variation** that we are making use of. In this example we only changed one value at a time, but we can change them all to any value we want to generate a multitude of faces.

## Shape Fitting
Now we have a shape model, and we can change the shape parameters to generate valid shapes (well, valid according to the dataset, and only if we use reasonably parameters.) Now how do we map this generative model to a face shape detector?

The point of an Active Shape Model is to enable a shape to be found in a previously unseen image. This is achieved by an iterative localised search in the image. We:
- Place the shape into the image
- Search in the neighbourhood of the current feature points for better locations
- Fit the model to the new suggested shape
- Repeat until the model converges on a final shape

So we start by placing the shape into the image blindly:
![](Pasted%20image%2020240326123451.png)
Note that simply changing the shape parameters $b$ is not good enough to find a good match. We also need to allow for scaling ($s$), rotation ($\theta$), and translation ($r$) of the shape. These are called the **pose parameters**.
The total set of parameters we will be tuning is $(s, \theta, r, b)$.

You can see that the mean shape is not at all aligned with the image. Only a couple of points on the right of the image are anywhere near their desired locations. How are we going to find better locations to push these feature points towards?
First, we perform edge detection on our image to get an image gradient. We can then calculate the normals to the model curve at each point.
![](Pasted%20image%2020240326124223.png)
We search along these normals and find the strongest nearby edge in the image (which we note may not be the edge we really want). Doing this for each point gives a set of suggested points $x'$. 
Now, we can't just set these new points as the positions of the feature points because they probably don't describe a valid face shape. Instead we tweak the parameters to bring the current points $x$ closer to $x'$.
We start by tweaking $(s, \theta, r)$, finding the best rigid transformation to fit $x$ to $x'$. This transformation of the model is called $x''$.
We then use our model backwards, via this rearranged formula:
$$b = V^{-1}(x''-\bar{x})$$
Which provides a new set of shape parameters $b$ for a new shape, closer to the desired points.
We repeat this process, iterating until we converge on some shape.
## Active Appearance Models
Having completed Shape Fitting, we have a shape that (hopefully) conforms well to the face in the given image. There are various things we could do with this, and one of them is to construct an Active Appearance Model.
If we split our face model into triangles and sample the given image, we can construct a 2D polygon mesh of our face model and then texture map the face from the image onto it. We can then manually modify the shape parameters (or just move the points) and the face texture will transform along with it, enabling us to change the expression of the face in an image.