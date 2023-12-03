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
But what if we want to know if a single point intersects with just one of these objects? We need to iterate the entire list and check every object.
We can do better than this, which is what Spatial Enumeration/Partitioning is about.

# Regular methods
These regular methods of spatial enumeration are so called because they divide space into regular chunks. This makes them simpler, but they aren't necessarily optimal.
## Gridcell
We actually covered one of these techniques already, in a different form:
![](Pasted%20image%2020231203134716.png)
When shooting a ray into a voxel structure, we have all of the object information stored in a simple 3D array that can be instantly indexed into given some coordinates. There is no searching required just to find what our ray has intersected with.
So let's generalise this to non-voxel objects. We take a 3D scene with some objects in it, and we introduce a grid:
![](Pasted%20image%2020231203134922.png)
We have a 3D array like before, but instead of the item stored being the voxel's info like before we have a pointer to the object that resides in that grid cell.
![](Pasted%20image%2020231203135033.png)
We would still need a linked list for storing every object; the objects that we refer to need to reside somewhere (and operations that work on every object in the scene would be awfully inefficient working on the gridcell structure, too). We then need to iterate over every space in the grid cell and check which object lies in it:
![](Pasted%20image%2020231203135306.png)
This won't be a computationally easy task, but when it is done we can **instantly** lookup what object resides in a cell at any time. Complexity-wise, finding an object with this method is in **constant time (O(1))**. The trade-off is that this structure is **very** space consuming; in a large scene, and with a small grid size, the size of the array will grow polynomially.
We *could* improve this by making the grid cells bigger, but this has it's own obvious problems:
![](Pasted%20image%2020231203135636.png)
In addition to this, think what happens when the state of the scene changes. What if an object is deleted, moved or added? Is there a way to efficiently update the gridcell structure to match?

## Octree
You might have noticed that a lot of space in the Gridcell's array is wasted on empty spaces or on subsequent identical spaces. For example, the large empty areas between objects or the large contiguous areas inside objects:
![](Pasted%20image%2020231203135033.png)
What if instead of having a set grid of equally sized squares, we only split up our grid as necessary to hold the specific objects in our scene? This structure is called an **Octree**:
![](Pasted%20image%2020231203140031.png)
And it's also hard to visualise in 3D, so back to the 2D scene.
![](Pasted%20image%2020231203140107.png)
We start with a single cell, and we set a maximum threshold for the number of objects in a square. For this example, let's set a threshold of 2. Right now we have 7 objects in this square, so we must split it in half along both directions:
![](Pasted%20image%2020231203140220.png)
Our first square has 1 object in it, so it's already done. So does our second and third. Our fourth still has 4, which is too many, so we split this square again:
![](Pasted%20image%2020231203140328.png)
And now every square has at most 2 objects in it. If we do this recursively then we can make an octree for any such scene. This process gave us a hierarchical structure:
![](Pasted%20image%2020231203140433.png)
Because this was in 2D, we actually get a **Quadtree** and not an **Octree**. Each non-leaf node contains 4 pointers to it's children, while leaf nodes contain pointers to the objects contained within them.
This data structure is distinct in that it adapts to the scene. Areas with many objects will be "subdivided" many times while sparse areas can be represented with fewer divisions. This saves memory, but the hierarchical structure means that accessing a space is not quite as fast as a Gridcell structure.
Generally this is a good compromise; the savings in memory are massive for a relatively smaller cost in speed.

# Irregular methods
These methods are different to the regular methods in that space is not divided into regular, equally sized chunks. This makes them more complicated, but it can improve their efficiency.
These methods take advantage of some properties that are true of most 3D scenes.
![](Pasted%20image%2020231203142521.png)
Scenes usually include large objects that completely occlude much of the scene behind them. These might be walls, large pieces of furniture, etc. We would like if we didn't have to do any calculations at all for these objects we can't see.
In addition, observe how objects in this busy scene assemble small "collections". The plant pot and the plant, the cabinet and everything on it, the desk and the monitor, etc. Some of these things make smaller collections by themselves, such as the coffee machine on the cabinet being many smaller parts. The thing that puts objects into these logical "collections" is the relatively large distance between them and other objects versus the objects in the collection. This effect can be called **Spatial Cohesion**: the fact that there generally isn't an even spread of objects in a scene, but various "clumps" of objects close together with large spaces in between.
Octrees take advantage of spatial cohesion, but in most scenes the gaps will not be evenly spread, limiting the effectiveness. A similar approach that takes better advantage of spatial cohesion is **Hierarchical Bounding Volumes**.
## Hierarchical Bounding Volumes (HBVs)
To construct an Octree, we start with a single big cell around everything in the scene and gradually subdivide it until we meet our desired object density for each cell. HBVs work somewhat in reverse: we start with a bounding box around a single object and expand this shape until we encompass the whole scene.