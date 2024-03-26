The model-based feature detection shown in the previous part is a **global representation**. It detects a face by searching for the entirety of a face in the given image. While these kinds of detectors can work well under good conditions, they have some limitations:
- They often struggle with occlusions
- They often struggle with so-called "intra-category variations". That is, they struggle with valid outlier inputs, for example a human face with an abnormally shaped nose, missing an eye, etc..

For example, this kind of image would cause an ASM a lot of trouble:
![](Pasted%20image%2020240326130559.png)

These issues can be better handled by detectors that use **local features**.

# Features
In Computer vision, a "feature" of an image is a (usually small) portion of an image which is meaningfully distinct to its local surroundings, and therefore easy to detect. These features are locations of sudden change, for example the border between two colours on the surface of an object, or the edge of an object on a contrasting background.
Features are very useful for detecting things in images. They are high in information content, invariant to changes in the view point or in illumination, and they are less computationally intense than some high-detail models.
![](Pasted%20image%2020240326140705.png)
You can implement many things using local features:
- Visual SLAM (Simultaneous Localization and Mapping) - Map out a 3D representation of an area using just a camera feed
- Image Matching - Determine if two images represent the same place or object, very useful for search engines, photo album viewers, etc.
- Image Alignment - Connect two images together by associating the common features in them. Could be something advanced like mapping Mars by connecting photos from the Mars Rover, or something as simple as connecting multiple photos from a phone camera into a panorama.
- Motion tracking - Stabilise video by detecting how features move and smoothing it out
- Robot navigation - Detect where a robot's appendages are, to inform its next actions
- ...and others

# Image Stitching
![](Pasted%20image%2020240326141153.png)
We have two images that we know are from the same place. We want to join these images together into one contiguous image, with the least visual discontinuity possible. We can do this using feature detection.
1. Detect feature points in both images
2. Find pairs of feature points that match
![](Pasted%20image%2020240326141323.png)
3. Transform the image such that these pairs lie in the same place.
![](Pasted%20image%2020240326141355.png)
(You would need extra steps to correct the colour if you wanted it to actually look good.)

The general approach on a technical level is:
1. Find a set of distinctive **key/interest points**
2. Define a region around each key point
3. Extract and normalize the region's content
4. Compute a local descriptor from the normalized region
5. Match the local descriptors in both images

Based on our description of the image stitching process, we can infer some requirements for our detector and our descriptors:
- The detector must detect the same points independently in every image (i.e. it must be repeatable)
![](Pasted%20image%2020240326141753.png)
- The descriptor must be able to match each point with a corresponding point in another image (i.e. it must be distinctive and reliable)
![](Pasted%20image%2020240326141937.png)
# Feature detectors
More generally, a local feature detector must satisfy these requirements for the features it locates:
- Feature extraction must be repeatable, i.e.:
	- invariant to translation or rotation or scale changes
	- robust to affine transformations
	- robust to lighting variation, noise, blur, quantization
- There must be enough features to cover the subject, so that matching can still proceed when features are occluded.
- The chosen features must be distinctive enough, and contain enough "interesting" structure, that they will be found in a large range of conditions.
- It must be fast enough to run in close-to-realtime.

Examples of feature detectors available for use are:
- **Harris**
- **Laplacian, Difference of Gaussians**
- Harris-/Hessian-Laplace
- MSER
- FAST
- BRIEF
- ORB
- etc...

Harris, Laplacian and DoG are the ones which will be covered in this course.

# Harris detector
![](Pasted%20image%2020240326144628.png)
The Harris detector is a feature detector that aims to find **corners**.
Why corners? We have plenty of methods for finding *edges*, but edges by themselves do not provide good local features; it looks the same all the way along the edge. However, when two edges meet at a corner we do get a distinct local feature:
![](Pasted%20image%2020240326144452.png)
