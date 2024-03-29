There are three main kinds of animation:
- Artist-driven: Where the animation is created manually. Involves steps such as Rigging, Skinning and Posing.
- Data-driven: Where the animation is created using recorded data. Motion capture is the main example.
- Procedural: Where the animation is generated automatically by the computer. An example is fabric simulation.

There are some crucial concepts in animation:
- Squash and stretch - How an object deforms based on the pressure exerted on it, or it's own exertion
- Timing - The apparent weight of objects is portrayed by the speed and acceleration of their movements. Heavily related to interpolation.

Motion falls into two different categories, which need to be handled differently:
- Production - Motion in an offline production such as a movie, where there is lots of time to generate animation.
- Interactive - Motion in an immediate interactive form such as a game or a simulator, where there is little time and information to generate animation.

When you animate, you don't go frame by frame and create each pose one after the other. You first create **keyframes**, strong poses with lots of time in-between them, and then create **tweens** which bridge the gaps between these keyframes:
![](Pasted%20image%2020231005120427.png)

# Procedural Animation
Procedural animation, like procedural surfaces or curves, is animation that is expressed as a function of a number of parameters. For example, if you wanted to animate a clock you could manually set the position of the clock hands for every single second, for the entire time the clock is visible. More reasonably, you could define the position of the clock hands according to a function which takes the current time as a parameter, making the animation completely automatic.
This also includes simulations, such as foliage, objects with physics, cloth physics, etc.
# Skeleton Animation
Animating a mesh by binding it to a skeleton and animating the skeleton.
First, you create an articulated skeleton that represents the bones and joints of the mesh.
![](Pasted%20image%2020231012110525.png)
This skeleton is a tree-like structure that represents which body parts are connected to each other, the relative positions and scales, and the overall scale of the body.

The simplest way to animate this skeleton is by rotating the bones to achieve the desired pose, which is called **forward kinematics**. With this method, the animator gives a set of angles as input, and the computer determines the position of the bones from these angles.
![](Pasted%20image%2020231012110844.png)
In this image, the "end effector" is $p$, which is the result of the forward kinematics. The input is in the form of the two angles $\theta_1$ and $\theta_2$, which are continuous functions that the animator defines (see represented on the right as graphs.)

The inverse of this is (appropriately) called **inverse kinematics**. With this method, the animator gives a desired endpoint (still called the end effector) and the computer instead calculates the required angles for the bones to reach this endpoint.
![](Pasted%20image%2020231012111131.png)
Inverse kinematics is a hard problem to solve: For any given point there are many possible ways to achieve the desired location for the end effector, and there may be a large number of joints to solve for.
In addition, there may be no solution at all. For example, if the desired end effector is too far away for the bones to reach even when fully outstretched, or if there is a minimum angle constraint and the angle between a bone and the end effector is too great.
![](Pasted%20image%2020231012111619.png)
The benefit of inverse kinematics is that it allows the animator a great deal of control over the position of body parts which forward kinematics doesn't - an animator can give an absolute position for some body part that does not change with the rest of the body, for example if a character is standing up on a bus and their body is shaking around, but their hand is held still on a handle.
Inverse kinematics can also give unnatural results if not used correctly, where a body part is too still compared to the rest of the body or the surroundings. In the bus example, imagine if the bus was very bumpy but the person's hand was perfectly anchored on the handle with no slipping or moving at all.

# Motion Capture
Instead of manually animating a rig, you can take a data-driven approach where the pose of a character is extracted from real footage of actors. Generally the actors will be required to wear a large number of markers to allow easy, automated extraction of the position of many points on their body.
Motion capture can achieve very realistic and nuanced animations, but it requires a whole set-up to record the footage.
![](Pasted%20image%2020231012112353.png)
Motion capture records the positions of many 3D markers, but these are not useful for animating a rig; we need to convert these marker positions into character controls through a process called **retargeting**.
# Skinning
So we can animate the skeleton, but how to move the mesh based on the movement of the skeleton/rig?
Skinning is the process of linking a mesh to the bones in a skeleton such that the mesh animates along with the skeleton.
![](Pasted%20image%2020231012112811.png)
The most popular technique is Skeleton Subspace Deformation (SSD), but other methods of skinning exist.

We define which vertices on the model should move with which bone by assigning each vertex a weight for each bone. If a vertex has max weight for a single bone then it will exactly follow the transformations of that bone. If a vertex has zero weight for a bone then it will not be effected at all by the transformations of that bone. Typically, a vertex will be affected by multiple bones, which provides a more fluid animation.
![](Pasted%20image%2020231012112925.png)
In this example coloured patches represent sections of the mesh that belong to a single bone, and black patches represent sections that belong to multiple bones:
![](Pasted%20image%2020231012113242.png)
You can see that the parts belonging to multiple bones are around the joints, which is where you would naturally expect smooth bends to happen.
Formally speaking, we define these weights as a function $W$:
![](Pasted%20image%2020231012113406.png)
where $i$ is the vertex and $j$ is the bone. So, $W_{ij}$ is the weight of vertex $i$ for bone $j$.
When $W_{ij} = 1$, vertex $i$ is **rigidly attached** to bone $j$ and will follow it's transformations exactly.
When $W_{ij} = 0$, vertex $i$ is unattached to bone $j$ and is not influenced by it's transformations.
Generally speaking, the weight is non-negative (Between 0 and 1), and for any given vertex the sum of the weights across all bones must equal 1.
$$\sum_{j \in \text{Bones}}W_{ij} = 1 \text{ for all $i$}$$

Consider these bones in a resting position (also known as **bind position**):
![](Pasted%20image%2020231012113828.png)
We know that the weight of $P_0$ is split half-and-half between these two bones.
We want to find the position of $P_0$ after some transformation of the bones. 
![](Pasted%20image%2020231012113943.png)
(In less ugly notation, $P'_0 = 0.5P'_1 + 0.5P'_2$).
We calculate the position of the point $P'_1$ by applying the same transformation that was applied to $T_1$ to $P_0$. The same is done for $T_2$ to get $P'_2$. We now multiply these two positions by the weights of the corresponding bones and sum them up to get the final position $P'_0$.

Vertices' positions are given in world space, while transformations applied to bones (and thus to the vertices) will be taking place locally. To remedy this we must convert the vertices' positions to local space using like this:
![](Pasted%20image%2020231012114615.png)
$p_i$ is the global position of the vertex in bind position, $B_j$ is the matrix representing the coordinate system of bone $j$ relative to world space (where applying the inverse converts the world space vertex coords to local bone space coords), and $T_j$ converts back from local bone space to world space.
Basically, $T_jB_j^{-1}$ gives the relative change between the bone in bind and in current pose, and applying it to the point will transform it accordingly.

How do we get the vertex weights? Usually, by painting them on by hand. There are some automatic methods, but they aren't good enough to supersede doing it manually.

# Physically-Based Animation
Animating objects by assigning them physical properties, e.g. weight, density, and performing a physics simulation. Examples include rigid and soft body simulations, wind simulations, and cloth simulations.
## Mass Spring Models
Simulate a surface that folds like cloth, which is hold together using internal springs. Ideal for cloth animations.
![](Pasted%20image%2020231012122746.png)
Here is a simple implementation of internal springs, shown in 2D simulating a strand of hair:
![](Pasted%20image%2020231013111846.png)
The objective here is to keep the distance between particles constant, so we use **Hooke's Law** for calculating the force exerted by a spring. The variables involved are:
- $P_i, P_j$ - The points connected by the spring. The calculated force acts on $P_j$ and pulls it towards $P_i$.
- $L_0$ - The length of the spring when at rest.
- $||P_x||$ - The magnitude of a vector (I don't know why it's double lines)
- $K$ - A scalar value representing the stiffness of the spring. A stiffer spring will pull back to rest length with greater force.

$F(P_i, P_j)$ calculates the force exerted on $P_j$ by the spring between it and $P_i$, but we can easily find the force exerted on $P_i$ by the same spring by simply inverting the result. Remember Newton's third law; for each action (force) there is an equal and opposite reaction (force).

You can essentially implement this same idea for strings as hair simulation.

With this method, we get three types of forces interacting on the model (be it string, hair or cloth):
- **Structural forces**, which enforce strict invariant properties on the system. For example, you may require certain particles to have constant distance from each other or to not move (e.g. anchoring one side of a flag to a pole), or you may enforce a constant distance between all particles if the cloth material you are simulating is non-elastic. These should ideally be **hard constraints**, and not forces.
- **Internal deformation forces**, which are the internal forces described prior that cause a string to bend, a piece of cloth to flatten, etc.
- **External forces**, such as gravity, friction, wind, etc.
## Particle Systems
Simulate a large set of small particles. Ideal for fluid simulations (e.g. water, smoke, flames, sparks). We will cover Point Dynamics.
![](Pasted%20image%2020231012121759.png)
For each particle we need to store some properties representing their current state. This can include:
- Position
- Velocity
- Age
- Colour

Emitters are objects that create particles, for example a sprinkler or an exhaust pipe.
Forcefields affect the particles, for example a wind or gravity forcefield will change the acceleration/velocity.
Finally, using the properties of the particles and taking into account external forces, we make the particles move by implementing the laws of mechanics. We do this using Ordinary Differential Equations (ODEs). 
In the simplest case, particles are completely independent of each other. They often have lifetimes, beyond which they are removed.
By using enough particles (and enough randomness), we can achieve some nice effects; while we are treating them as points while calculating the dynamics, we can render them however we like.
Here is something you might recreate using a particle system:
![](Pasted%20image%2020231012122538.png)

## Ordinary Differential Equations (ODEs)
We have many tools to solve differential equations and thus implement the laws of mechanics in our simulations. Take, for example, this equation representing the Newtonian mechanics for point mass:
![](Pasted%20image%2020231012123013.png)
We can rewrite this as a differential equation, as both force and acceleration are vectors. Express acceleration in terms of the position vector:
![](Pasted%20image%2020231012123055.png)
Split this into two ODEs:
![](Pasted%20image%2020231013105050.png)
Then combine into a single state vector $X$ to get a 1st order ODE, and take the derivative of both sides:
![](Pasted%20image%2020231013105151.png)
We can calculate $X$ (that is, $x$ and $v$) using this result.

### Estimation stuff

## Forces
