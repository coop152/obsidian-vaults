# Grouping
The goal in image segmentation is to gather pixels in an image such that features that belong together are grouped together.
![](Pasted%20image%2020240213131518.png)
This yields a representation which simply and compactly describes key parts of an image or video. This is desirable in image processing as an intermediate step, as working on these simplified forms is much easier.
Groupings are useful for things such as:
- Splitting an image into regions (e.g. road vs offroad)
- Grouping frames of a video into shots (i.e. split the video when the camera cuts)
- Determine the location of moving objects against the backdrop in a video feed (e.g. outlining a person on a security camera feed)
- Background removal (e.g. remove the background from an image of a person or object)

## Motivating psychology (Gestalt)
Human visual perception relies heavily on grouping, and there are some ways that the human brain groups objects that go beyond simple shape or colour continuity. To illustrate this:
![](Pasted%20image%2020240213133525.png)
Shape A appears to contain a triangle, even though it is just three spheres. Shape B appears like a tube twisted around another tube, but is simply two symmetrical shapes. Shape C appears as a series of cones sticking out of a 3D sphere, even though there is only cones. Shape D has the appearance of a zig-zagging tube coming in and out of view, even though it is just a series of unrelated shapes. How do we recognise these objects?

There are a variety of ways that we group shapes together in order to recognise objects.
The **Gestalt Factors** describe these:
![](Pasted%20image%2020240213133919.png)
- **Proximity** - We tend to group objects that are close.
- **Similarity** - We tend to group objects that are similar in some way (e.g. colour, shape)
- **Common Fate** - We tend to group objects that show the same behaviour (e.g. moving in the same direction)
- **Common Region** - We tend to group objects that are contained in the same place.
- **Parallelism, Symmetry, Continuity, Closure** - etc. for other recognisable properties

This is relevant to computer vision; if we want to detect objects in the same way that the human visual system does then we should take note. But how to map these factors to algorithms?

# Segmentation
As an exemplar of how we would like the computer to segment our images, we can start by manually segmenting an image:
![](Pasted%20image%2020240213135258.png)
These desired outcomes are often called the "correct answer" or "ground truth".
Doing this demonstrates how segmentation is a hard problem to define. Asking multiple people to segment an image in this way will give wildly different solutions:
![](Pasted%20image%2020240213135606.png)
The Berkeley segmentation dataset, where these images came from, resolves this somewhat by taking a weighted average of multiple user's segmentations. Therefore, frequent areas of segmentation (e.g. the woman's outline) will have a higher weight than less common areas (e.g. the eyes).
## Superpixels
In this method we don't even try to compute a "correct" segmentation. Instead, we aim for over-segmentation wherein every region is very likely to be uniform.
![](Pasted%20image%2020240213140417.png)
These results don't tend to be useful by themselves, but they can aid subsequent steps in the image processing pipeline.
![](Pasted%20image%2020240213140703.png)

## Clustering
Clustering is a method commonly used in machine learning to split a dataset into chunks. This can be applied quite directly to images in order to perform segmentation. Here is a very simplified example:
![](Pasted%20image%2020240213141334.png)
We have an image, represented in terms of it's intensity. We can plot an intensity histogram, and in this artificial image there are three very obvious groups. We could easily assign a label to these ranges of intensities in order to get a segmentation. In this example image it would be a very good result, with each object being completely continuous, uninterrupted and unique in colour.
Of course, real images have noise:
![](Pasted%20image%2020240213141919.png)
How do we determine the three groups now? This is where clustering comes in.
![](Pasted%20image%2020240213142409.png)
We need to choose three centre points for the three groups, and then assign each pixel to one of these groups according to it's proximity to the centres. The best choices for cluster centres are those that minimises the SSD (Sum of Square Distances) between the points and their cluster's centre ($c_i$):
![](Pasted%20image%2020240213142823.png)
But how do we find these centres? If we knew group membership already then we could easily find the optimal centre, but we don't. In fact, we want to find the centres **exactly because** we don't have the group membership for our pixels.
![](Pasted%20image%2020240213143103.png)
### k-Means Clustering
This is an algorithm for finding cluster centres, which solves the previously shown problem.
The basic idea of the algorithm is to randomly initialise $k$ cluster centres, and 