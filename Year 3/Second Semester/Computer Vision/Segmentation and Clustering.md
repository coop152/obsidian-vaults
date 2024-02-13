# Grouping
The goal in image segmentation is to gather pixels in an image such that features that belong together are grouped together.
![](Pasted%20image%2020240213131518.png)
This yields a representation which simply and compactly describes key parts of an image or video. This is desirable in image processing as an intermediate step, as working on these simplified forms is much easier.
Groupings are useful for things such as:
- Splitting an image into regions (e.g. road vs offroad)
- Grouping frames of a video into shots (i.e. split the video when the camera cuts)
- Determine the location of moving objects against the backdrop in a video feed (e.g. outlining a person on a security camera feed)
- Background removal (e.g. remove the background from an image of a person or object)

## Importance of grouping
