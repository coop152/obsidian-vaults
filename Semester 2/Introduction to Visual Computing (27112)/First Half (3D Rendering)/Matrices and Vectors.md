## Translation
$x' = x + t_x$
$y' = y + t_y$
$z' = z + t_z$
Move an object in space. Add a constant to the coordinates.
## Scaling
$x' = s_xx$
$y' = s_yy$
$z' = s_zz$
Change the size of an object. Multiply the position by a constant.
## Rotation
Rotation is an operation that takes place in 2 planes.
#### Deriving the 2D formulae
In 2D, rotating anticlockwise by $\theta$:
![](Pasted%20image%2020230207123344.png)
Let the prior values of the coordinates be
$x = r\cos{\phi}$
$y = r\sin{\phi}$
Then the transformed coordinates are
$x' = r\cos(\theta + \phi)$
$x' = r\sin(\theta + \phi)$
Substitution gives:
$x' = x\cos{\theta} - y\sin{\theta}$
$y' = x\sin{\theta} + y\cos{\theta}$
If the rotation is about a point $(q_x, q_y)$, then
1. Translate by $(-q_x, -q_y)$ to bring the centre of rotation to the origin
2. Rotate by $\theta$
3. Do the inverse of the translation (that is, $(q_x, q_y)$)
#### 3D Rotation
Rotating a point in 2D is the same as rotating a point in 3D in the XY plane/about the Z axis. The Z axis stays the same. Therefore, 3D rotation about the Z axis has the formulae:
$x' = x\cos{\theta} - y\sin{\theta}$
$y' = x\sin{\theta} + y\cos{\theta}$
$z' = z$
In general, any rotation about some axis does not change the coordinate for that axis.
#### Rotating about a vector
The axis being rotated about doesn't have to be one of the axis X, Y or Z. More to come about that later.

# Matrices as Transformations
Represent coordinates as a column vector.
$$
(x, y, z) \Rightarrow
\begin{bmatrix}
x \\ y \\ z
\end{bmatrix}
$$
Now, transformations can be represented as matrices and applied by post-multiplication.
## Scaling
$$
\begin{bmatrix}
x' \\ y' \\ z'
\end{bmatrix}
=
\begin{bmatrix}
s_x & 0 & 0 \\
0 & s_y & 0 \\
0 & 0 & s_z
\end{bmatrix}
\cdot
\begin{bmatrix}
x \\ y \\ z
\end{bmatrix}
$$
## Rotation
Around the Z axis:
$$
\begin{bmatrix}
x' \\ y' \\ z'
\end{bmatrix}
=
\begin{bmatrix}
\cos{\theta} & -\sin{\theta} & 0 \\
\sin{\theta} & \cos{\theta} & 0 \\
0 & 0 & 1
\end{bmatrix}
\cdot
\begin{bmatrix}
x \\ y \\ z
\end{bmatrix}
$$
## Translation?
Not possible with this 3x3 matrix format. The solution is [Transformations & Homogeneous Coordinates](Transformations%20&%20Homogeneous%20Coordinates.md).
