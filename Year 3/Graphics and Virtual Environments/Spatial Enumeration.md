Spatial Enumeration (or Spatial Partitioning) techniques are used to organise space in ways that accelerate the commonly used computer graphics calculations can be done more quickly.
These calculations are not the low-level matrix/vector calculations - GPU hardware already has those incredibly optimised. The calculations optimised by spatial enumeration are higher level operations on higher level data structures.
# Database analogy
Imagine we're building a database to hold information about animals. We start off by just putting the names of the animals into the database in no particular order.
![](Pasted%20image%2020231203131607.png)
If we just want a list of animals then this is fine. We just step through the records one by one and take them all, which is about as efficient as it could be.
But say we want them in alphabetical order quite often - then it isn't so good. Every time we want them sorted we need to extract them all from the database and sort them manually. If we do that regularly it's a massive waste of time. So we build an index, which allows us to view the database entries in alphabetical order easily, given we do some pre-calculation:
![](Pasted%20image%2020231203132729.png)
By doing this we've done a trade-off between speed and memory usage; this index will likely multiply the size of the database by some amount (even if small) but in return we get potentially massive speedup.
We can add more indexes if we're interested in more properties:
![](Pasted%20image%2020231203132930.png)
As mentioned before, this also comes at the consequence of some pre-calculation. If we remove something from this database, we can't simply remove it like we could before. We need to follow these pointers back to the indexes and remove the entries there:
![](Pasted%20image%2020231203133058.png)
And similar (potentially more intensive) housekeeping is required when adding an item.

Let's think what this represents in a (2D) graphical scene. 
![](Pasted%20image%2020231203133334.png)
We have some objects, but what properties of these objects do we want quickly accessible as our "indexes"? There are two main ones:
- Is an object intersecting a certain point?
- Does a ray go through an object?

![](Pasted%20image%2020231203133531.png)
These operations, and many more, can be sped up massively by considering how we organise our objects in a 3D space. Our default representation might have been a linked list: In the most general case, we might be constantly moving our objects around and constantly iterating over all of them, which a linked list is great at.
![](Pasted%20image%2020231203133709.png)
But what if we want to know if a single point intersects with just one of these objects