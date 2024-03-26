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
1. Assemble the data into a matrix $D$. We have 40 variables (from 20 2-variable position pairs) so there are 40 columns, and we have 6 samples so there are 6 rows. (Note that you can flip the rows and columns if you like; transposing a matrix is trivial)
2. Compute the covariance matrix $C$ as described earlier. In this case it will be $40 \times 40$.
3. Find the eigenvalues and eigenvectors of $C$ by solving $|C-\lambda I| = 0$. We will get 40 eigenvalues/eigenvectors, with the matrix $V$ containing the eigenvectors being $40\times 40$ (40 eigenvectors of length 40).
4. Choose the $K$ largest eigenvalues, but for ASM instead of removing them entirely we change how much influence they have using the **Shape Parameter** vector, $b$. This is a ($40\times 1$) vector 