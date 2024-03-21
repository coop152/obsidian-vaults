# Explicit Methods and Stability
(See the video for a good example of an explicit parabolic PDE solve, this time with a time dimension)

Like with ODEs, explicit methods are quick and easy to implement. They are also well-suited to PDEs involving a time component (which generally have initial conditions).
They also parallelise easily, thanks to the low level of interaction between values of the solution at different nodes.
Also like with ODEs, they require small timesteps to achieve accurate solutions, and **they suffer similar stability problems**. For example, in the parabolic PDE explored in the video, $\Delta t \leq \frac{\Delta x^2}{2k}$ is required for a stable solution.

# Implicit Methods
An implicit solution based on finite difference approximations can also be developed for PDEs, like with ODEs.

We perform the same steps as usual, but we approximate one timestep ahead at (i, j+1). This gives us these approximations:
![](Pasted%20image%2020240321143752.png)
![](Pasted%20image%2020240321143727.png)
Applying these to the PDE from the video example gives us this equation:
![](Pasted%20image%2020240321143825.png)
And rearranging in the same way as with ODEs gets us this update scheme, with knowns on the right and unknowns on the left:
![](Pasted%20image%2020240321143923.png)
We go through and apply this to every node as before. Example for the first node:
![](Pasted%20image%2020240321144007.png)
Do this for each node at time j=0, then collect them into one matrix problem:
![](Pasted%20image%2020240321144046.png)
This is a tri-diagonal system so we solve it efficiently using the Thomas algorithm. That gives us our solutions for time j + 1, which means we can move on to the next timestep. We do this until our desired time is reached.
This implicit method is **unconditionally stable**, and is more accurate, allowing us to take larger timesteps.

## Crank-Nicholson Method
A limitation of the basic implicit scheme demonstrated above is that it is only first-order accurate in time, thanks to this first order approximation:
![](Pasted%20image%2020240321144835.png)
The **Crank-Nicholson** method uses a central difference approach to approximate the derivative at the mid-point of the interval:
![](Pasted%20image%2020240321144932.png)
![](Pasted%20image%2020240321144947.png)
The spatial derivative at this temporal midpoint can be evaluated as the mean of the approximations