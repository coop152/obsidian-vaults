# Grouping
The goal in image segmentation is to gather pixels in an image such that features that belong together are grouped together.
![](Pasted%20image%2020240213131518.png)
This yields a representation which simply and compactly describes key parts of an image or video. This is desirable in image processing as an intermediate step, as working on these simplified forms is much easier.
Groupings are useful for things such as:
- Splitting an image into regions (e.g. road vs offroad)
- Grouping frames of a video into shots (i.e. split the video when the camera cuts)
- Determine the location of moving objects against the backdrop in a video feed (e.g. outlining a person on a security camera feed)
- Background removal (e.g. remove the background from an image of a person or object)

## Gestalt psychology
Visual perception relies heavily on grouping, and there are some ways that the human brain groups objects that go beyond simple shape or colour continuity. For example:
![](Pasted%20image%2020240213133525.png)
Shape A appears to contain a triangle, even though it is just three spheres. Shape B appears like a tube twisted around another tube, but is simply two symmetrical shapes. Shape C appears as a series of cones sticking out of a 3D sphere, even though there is only cones. Shape D has the appearance of a zig-zagging tube coming in and out of view, even though it is just a series of unrelated shapes.

There are a variety of ways that we group objects together in order to recognise objects.
The **Gestalt Factors** describe these:
![](Pasted%20image%2020240213133919.png)
- **Proximity** - We tend to group objects that are close.
- **Similarity** - We tend to group objects that are similar in some way (e.g. colour, shape)
- **Common Fate** - We tend to group objects that show the same behaviour (e.g. moving in the same direction)
- **Common Region** - We tend to group objects that are contained in the same place.
- **Parallelism, Symmetry, Continuity, Closure** - etc. for other recognisable properties

This is relevant to computer vision; if we want to detect objects in the same way that the human visual system does then we should take note. But how to map these factors to algorithms?