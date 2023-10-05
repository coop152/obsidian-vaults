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