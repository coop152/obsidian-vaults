# Rigid Body Simulation
A rigid body simulation is somewhat like the particle simulation's covered previously, but on objects with actual shape and geometry as opposed to single points.
![](Pasted%20image%2020231020112636.png)
Like a particle, a rigid body has a position $(x, y, z)$, but it also has an *orientation* expressed in it's rotation along each of the 3 axis. We say that a particle has **3 degrees of freedom**, while a rigid body has **6 degrees of freedom**. These extra degrees of freedom make rigid body simulation significantly more complicated.

The main challenge is how the rigid body interacts with the environment compared to a particle.
When dropping a particle on a surface, the interaction is simple in that it can stop, reflect off, etc. In contrast, dropping a rigid body on a surface can have many very different outcomes that are dependent on many variables. For example, if we drop a rigid body on the edge of a surface, it is expected to tilt and come to rest at an angle:
![](Pasted%20image%2020231020113140.png)
Determining the amount of rotation requires calculating the torque of the object (i.e. the rotational force) in addition to the regular force.
Generally a technique called **impulse-based collisions** is used for rigid body simulations. With this technique, instead of calculating forces and allowing them to influence the acceleration of an object, thus changing its speed and trajectory, the simulation directly affects the velocity of the body. This is a technique that could also be used in mass spring models.

![](Pasted%20image%2020231020113748.png)
Consider the case where the object falls into the middle of the surface, and *should* settle to a stable position. It is a common mistake in some simulations that, because of miniscule differences in points along the bottom of the body, there will be an extremely slight difference in force exerted and the object will move when it should be static. This can cause objects to vibrate; we fix this by defining a "**rest in contact**" state where objects that satisfy some condition are declared as static and not simulated at all (until an external force releases it from this state).
This leads to a further problem called "stacking", which is the state achieved when multiple rigid bodies are at rest on each other, for example:
![](Pasted%20image%2020231020114305.png)
This can be a tricky state to handle because when one object is affected by some force and leaves the rest state, it is likely that the other objects it is touching should be affected too. 
## Articulated Rigid Body Simulation
Similar to joints in a rig, we want to simulate some rigid bodies that are bound together by some articulated joints. For example:
![](Pasted%20image%2020231020114516.png)
We can see that this object has **7 degrees of freedom**: this is because each of the separate rigid bodies has 6 DOF, but the joint constraint prevents them both from rotating in 2 directions (-2 DOF) and also prevents one of them from moving away from the other (-3 DOF). The 7 degrees of freedom are:
- Overall position $(x, y, z)$
- Overall rotation (in three directions)
- Angle of the joint between the two rigid bodies, in one direction.

These articulated rigid bodies can be used for simulating rigged meshes as ragdolls, but they are also useful for any kind of object with rigid connected components. For example, a car has a main rigid body with wheels attached with a single degree of freedom (rotating to go backwards or forwards).
## Deformable Body Simulation
