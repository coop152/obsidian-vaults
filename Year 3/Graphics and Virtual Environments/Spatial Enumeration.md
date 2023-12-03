Spatial Enumeration (or Spatial Partitioning) techniques are used to organise space in ways that accelerate the commonly used computer graphics calculations can be done more quickly.
These calculations are not the low-level matrix/vector calculations - GPU hardware already has those incredibly optimised. The calculations optimised by spatial enumeration are higher level operations on higher level data structures.
# Database analogy
Imagine we're building a database to hold information about animals. We start off by just putting the names of the animals into the database in no particular order.
![](Pasted%20image%2020231203131607.png)
If we just want a list of animals then this is fine. We just step through the records one by one and take them all, which is about as efficient as it could be.
But say we want them in alphabetical order quite often - then it isn't so good. Every time we want them sorted we need to extract them all from the database and sort them manually. If we do that regularly