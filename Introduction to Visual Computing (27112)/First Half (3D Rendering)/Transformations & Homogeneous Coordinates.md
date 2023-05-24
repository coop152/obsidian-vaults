In order to have a consistent set of matrix representations for all kinds of linear transformations, we add an extra coordinate $w$ to the usual 3D coordinates $(x, y, z)$. This new form of $(x, y, z, w)$ is called **Homogeneous Coordinates**.
But what *is* $w$? What does it represent? In truth, it is a 4th spacial dimension.
Usually, $w=1$ so we don't need to bother with it. However when it isn't we need to "normalise" the resulting coordinates. This is covered later in perspective.
## Translation (now made possible)
$$
\begin{bmatrix}
x' \\ y' \\ z' \\ 1
\end{bmatrix}
=
\begin{bmatrix}
1 & 0 & 0 & t_x \\
0 & 1 & 0 & t_y \\
0 & 0 & 1 & t_z \\
0 & 0 & 0 & 1 \\
\end{bmatrix}
\cdot
\begin{bmatrix}
x \\ y \\ z \\ 1
\end{bmatrix}
$$
## Scaling
$$
\begin{bmatrix}
x' \\ y' \\ z' \\ 1
\end{bmatrix}
=
\begin{bmatrix}
s_x & 0 & 0 & 0 \\
0 & s_y & 0 & 0 \\
0 & 0 & s_z & 0 \\
0 & 0 & 0 & 1
\end{bmatrix}
\cdot
\begin{bmatrix}
x \\ y \\ z \\ 1
\end{bmatrix}
$$
## Rotation
Around the Z axis:
$$
\begin{bmatrix}
x' \\ y' \\ z' \\ 1
\end{bmatrix}
=
\begin{bmatrix}
\cos{\theta} & -\sin{\theta} & 0 & 0 \\
\sin{\theta} & \cos{\theta} & 0 & 0 \\
0 & 0 & 1 & 0 \\
0 & 0 & 0 & 1
\end{bmatrix}
\cdot
\begin{bmatrix}
x \\ y \\ z \\ 1
\end{bmatrix}
$$
Notice that $w$ was completely redundant in all of these.

## Composite Transformations
If one were to apply two transformations in sequence, you would have
$P'' = T_2 \cdot T_1 \cdot P$
If you wanted to represent the combination of these two transformations as one matrix, you can simply multiply the matrices together:
$T_C = T_2 \cdot T_1$
$P'' = T_C \cdot P$
Remember that matrix multiplication is not commutative!
This is vital to graphics rendering, where multiple matrices will be applied repeatedly to points in sequence without changing. For example, a model may have a modelling matrix applied, followed by a camera matrix, followed by a projection matrix. 

## Inverse Matrices
For a given matrix $M$, its inverse is the matrix such that $M \cdot M^{-1} = I$. That is, it is the matrix that, when multiplied with $M$, results in the identity matrix. In the context of transformations an inverse is the opposite of a transformation; the transformation that undoes $M$.

Not all matrices have an inverse - this makes sense when thought of in context of transformations. Consider the transformation that "flattens" a 3D object by setting all $y$ coordinates to 0; how could this have an inverse? The information was lost in the transformation.

## Arbitrary Rotation
How to rotate about an arbitrary axis by $\theta$? This can be achieved using composite transformations.
1. Apply translation such that the arbitrary axis goes through the origin. ($M_1$)
2. Rotate the arbitrary axis such that it lies in the XY plane. ($M_2$)
3. Rotate again such that the arbitrary axis is the same as the X axis. ($M_3$)
4. Perform the desired rotation about the X-axis by $\theta$. ($M_4$)
5. Undo each transformation in reverse order (not including the final rotation of $\theta$).
The final operation is $P' = M_1^{-1}M_2^{-1}M_3^{-1}M_4M_3M_2M_1P$.

## Transformations in OpenGL
OpenGL maintains two transformation matrices internally:
- The `modelview` matrix transforms geometry, and specifies the camera position
- The `projection` matrix, which controls how the 3D environment is projected onto the 2D screen through the camera.
This happens in the vertex shader. If you don't write a vertex shader, it's done automatically.
$P_{\text{drawn}} = \text{ProjectionMatrix} \cdot \text{ModelViewMatrix} \cdot P_{\text{specified}}$ 

## Vectors with Homogeneous Coordinates
The same as regular vectors, but with an extra $w$ coordinate that is completely ignored.
#### Addition
![](Pasted%20image%2020230207135938.png)
#### Subtraction
![](Pasted%20image%2020230207135951.png)
#### Multiplication by a Vector
![](Pasted%20image%2020230207140052.png)
#### Magnitude
![](Pasted%20image%2020230207140115.png)
#### Normalisation (Unit Vectors)
![](Pasted%20image%2020230207140258.png)
#### Vector Multiplication
There are two ways to "multiply" vectors:
- Dot product
	- Also called Inner product
	- Gives a scalar result
- Cross product
	- Also called Outer product
	- Gives a vector result
Both of these operations are essential in 3D graphics.
**Dot Product**:
![](Pasted%20image%2020230207140546.png)
If the vectors are normalised, the dot product is the cosine of the angle between the two vectors.
**Cross Product**:
![](Pasted%20image%2020230207140752.png)
For any two vectors, their cross product is a vector perpendicular to both of them. This forms a right handed system.

You can remember the method by imagining a line going through both vectors for the line you're currently calculating. For example, say you're finding the result for the first row; you cover up $x_1$ and $x_2$, leaving the bottom two rows (ignore $w$). With these two leftover rows, you multiply the diagonals, like finding the discriminator of a matrix. First value of first vector times last value of second vector, minus the bottom value of the first vector times the top value of the second vector. In this example, $y_1 \cdot z_2 - z_1 \cdot y_2$. Note that you don't pick the highest and lowest, you pick the next and the one after; the "first" value in the first vector is the one below the line that you are blocking out, and the "second" is the one below that, wrapping around if you reach the end.