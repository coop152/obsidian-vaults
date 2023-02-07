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