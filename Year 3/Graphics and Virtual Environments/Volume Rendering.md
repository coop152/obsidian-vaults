All of the previous rendering techniques we have covered are concerned with totally solid surfaces, usually made of polygons (with a few exceptions for mathematically defined surfaces such as spheres, toruses, etc.) However, in the real world most objects do not have a perfectly solid surface: In addition to having an actual interior, the surfaces have volume and thickness by themselves. These surfaces are unlikely to have consistent thickness and sharp separation from the surroundings.
"Volume rendering" is not to be confused with "Volumetric rendering", which refers to effects such as fog or dust interacting with light.
In volume rendering, objects do not have explicitly defined surfaces. This might be because we are modelling something that doesn't have a meaningful "surface", such as wind patterns or temperatures in a weather simulation.
On the other hand, the object may still have a well-defined surface but we are also interested in the matter inside, beyond that surface level. It is even useful where we are only concerned with the surface, but we have captured our object in such a way that information about that surface is not easily or obviously presented. An example of both of these is a CAT scan, where a 3D scan of a body is found by taking multiple x-ray "slices" and then stitching the slices back together to make a 3d mesh. This provides no "surface" information, but it does describe a volume which we want to visualise.
# CAT scans
Imagine we want to scan this golf ball, which has a bumpy surface and a rubber inside.
![](Pasted%20image%2020231126131740.png)
If we imagine performing a CAT scan on this golf ball, we would have a series of (in this instance) vertically ascending slices of the golf ball, each slice being a 2D scan of one layer of the ball. We can represent this as a 3D grid of voxels:
![](Pasted%20image%2020231126131942.png)
Each voxel containing a single data point. If we do a scan of the ball we get these values:
![](Pasted%20image%2020231126135214.png)
You can see where the layers of the ball roughly begin, as well as the volume of the inside.
Importantly, we don't have any actual identification for each of these parts. In a real CAT scan, where we have a full continuous spectrum of values for each point in a layer, we have to somehow identify which part is what.
Specifically, in a real CAT scan the value assigned to each point is its **radio density**. This is measured in Hounsfield units, which is the value we must use to represent the scan in 3D space. Hounsfield units can be interpreted like this, in a scan of a human body:
![](Pasted%20image%2020231126140842.png)
That is, air is -1000 units (which is a calibration point), lung tissue is -600, water is 0 (another calibration point), muscle is 40, bone is 350, and skull bone is 1800.
## Transfer Functions
A scan includes no sense of colour or opacity, only radio density. To transfer our raw radio density into something we can display, we employ a **Transfer Function** which maps Hounsfield unit values into display colours.
For simplicity, let's forget Hounsfield units and consider some scanning technique that gives some arbitrary values.
Say we are scanning this hand:
![](Pasted%20image%2020231126141356.png)
The glass will be sampled as having some value, and (assuming for now that the hand is homogeneous in material) the hand will have some other value. We might assign the value 500 to glass:
![](Pasted%20image%2020231126141556.png)
If we say that the hand has value 200, then we would assign the pieces that are fully hand 200 and the pieces that are slightly glass but mostly hand around 250:
![](Pasted%20image%2020231126141714.png)
If we continue until we have a value for every point we have a "scan". We can investigate the distribution to learn something about what is present:
![](Pasted%20image%2020231126141846.png)
You see two spikes vaguely around the glass value and the hand value. The fact that there are two spikes tells us that we probably have two distinct materials; the fact that the spikes aren't very sharp tells us that either there is a "softness" where material gradually "fades out" or that our sampling resolution isn't very good. In this case, our resolution was the reason.
If we use this distribution to assign some colours to our classified materials, we can make somewhat of an image:
![](Pasted%20image%2020231126142108.png)
![](Pasted%20image%2020231126142115.png)
Clearly the low resolution is diminishing the usefulness of this result, but at higher resolutions this would become more detailed and useful.
![](Pasted%20image%2020231126143507.png)
So, we have a clear scan and we need to display it in 3D. We could simply display it as voxels or as spheres like we did for the golf ball:
![](Pasted%20image%2020231126143010.png)
This is okay, but it isn't a great visualisation in most cases.
# Direct Volume Rendering
Introduce a viewpoint and a viewplane. We shoot a ray through a pixel in the viewplane and into the voxel grid.
![](Pasted%20image%2020231126143641.png)
When we enter the volume, we begin **marching** through the grid in regular increments, taking a measurement of the current colour and opacity of the voxel that point falls in. We stop the ray when it leaves the volume, and then take these sampled values to make a single resulting colour. Do this multiple times per pixel and for each pixel in the image (like path tracing) and we can assemble an image.
We also keep a running total of the opacities that we have encountered already. If we "run out" of opacity then we can stop the ray early.
Let's consider an example where the glass is almost entirely transparent and the hand is 100% opaque:
![](Pasted%20image%2020231126144055.png)
The ray marches through the glass, barely picking up any colour or opacity, until it reaches the hand. The hand is completely opaque, so it picks up the colour of the hand and immediately stops. The final colour will mostly be the hand, with a slight glass tint.
Alternatively, let's imagine the glass is not as transparent and the hand is somewhat transparent itself:
![](Pasted%20image%2020231126144233.png)
The ray marches through the glass, picking up incrementally more opacity and glass tint. It reaches the hand and continues marching through, picking up relatively more hand colour, and manages to get through to the other side. It then continues through a bit more glass until the opacity of the ray is exhausted and it stops. The resulting colour will be much more glass tinted than the previous example.

In summary:
1. Apply a transfer function to convert the sampled values into colours and opacities.
2. Raycast from some viewpoint to get a 3D render of the volume.

This gives us images that look like this:
![](Pasted%20image%2020231126144522.png)
The images look much like x-rays, with no discernible depth apart from the little clues given by the shapes themselves (e.g. the curves of the teapot). Indeed, when raycasting to get our render we essentially performed an x-ray on the volume. This is called **Additive Reprojection**, and it gives quite acceptable results. If you generate an animated turn-around or similar then the results can convey quite a good sense of depth.
We can do better, though. We would rather have a result like this:
![](Pasted%20image%2020231126144906.png)
The most noticeable difference here is the specular highlights on the surfaces. We don't even have a concept of surfaces in our volume, though; how would we have specular highlights?
We can improvise a surface normal for each point in our volume using the colours. For each point, take the difference from each surrounding point in the three dimensions and use these differences to find a vector:
![](Pasted%20image%2020231126150641.png)
By using this as a surface normal, we can add a light source and find specular highlights using a local illumination model. This assumes that the transition between colours will be gradual and smooth, but in a realistic setting this should be the case.
This is completely unfounded in reality (most of the things being lit here aren't even exposed to light) but more importantly it assists our depth perception and allows us to more easily visually parse the 3D scan.
This gives results like this:
![](Pasted%20image%2020231126151311.png)
# Indirect Volume Rendering
There is a problem with direct volume rendering in that is it **viewpoint dependent**; if we want to see the scan from a different angle, we need to completely redo the entire raycasting process, which is computationally intense. The reason it is computationally intense to recalculate is because graphics cards are not designed to render volumes: they are however extremely efficient at rendering textured polygon meshes. If we wish to have a 3D scan that can be inspected with a moving viewpoint with high speeds, we need to somehow convert it into a traditional mesh that the GPU can easily re-render. There are many ways to do this, and these methods fall under **Indirect Volume Rendering**.
## ISO Surfaces
An **ISO line** is a line that represents some constant value. You see this often in 2D visualisations of 3D objects. For example, here is a 3D "mountain", where the height is represented by contour lines and colouring (and actual 3D height, of course):
![](Pasted%20image%2020231126152305.png)
The contour lines are projected onto the terrain at some constant height (for example, 100 m apart). You can see this distance in this side view:
![](Pasted%20image%2020231126152354.png)
But when shown from above, as it would be in a 2D map, the contour lines look like this:
![](Pasted%20image%2020231126152439.png)
In combination with the colouration, the contour lines convey depth in a way that doesn't require actual 3D depth. The meaning is intuitive; if you imagine the contour line between the blue and green sections as a coastline, then following the contour line is analogous to "walking around the coastline", and it is clear that you would be at the same height all the way around. This is an **ISO line that represents constant height**.
These also show up in weather forecasts, representing barometric pressure:
![](Pasted%20image%2020231126153530.png)
So, along any of these lines we have equal barometric pressure. These are called ISO bars.

Finding an ISO line for a 2D set of values is easy:
![](Pasted%20image%2020231126153950.png)
We draw a line between pairs of points which pass between our defined constant. We can classify points based on this boundary:
![](Pasted%20image%2020231126154936.png)
We might say that blue pixels are rock and pink pixels are air, which allows us to represent them accordingly.
Depending on the thing we are sampling we might use a smooth line instead:
![](Pasted%20image%2020231126155046.png)
But that poses it's own challenges (which we won't cover now).
In essence, this is what we're trying to do with **ISO surfaces**. We want to perform a process like this in 3D, but with surfaces separating different classifiable things instead of lines. We then use these ISO surfaces to convert the point grid into a mesh.
This is not an easy process in 3D. Creating these surfaces is known as **marching cubes**.
![](Pasted%20image%2020231126155434.png)
(Watch the video for a video visualisation.)
The output of marching cubes is a regular mesh, which can be rendered extremely quickly by the graphics card.
## Proxy Surfaces
Another approach is to use **proxy geometry**. The idea is to use each layer of the scan as a texture, and pile them up into a stack of transparent screens. When the layers come together they will make a coherent image.
![](Pasted%20image%2020231126160405.png)
In terms of polygons, it takes very few to draw this proxy. Each scan only takes a few polygons, and there are only as many layers as there are scans. Rendering a texture on these layers is, of course, extremely fast on a GPU. But how do we create these textures?
Recall how UV mapping takes place for 2D textures:
![](Pasted%20image%2020231126160733.png)
We interpolate between the points on the surface and then find the colour at the calculated UV coordinate by interpolating over the texture. Now imagine doing this with a 3D texture, where that 3D texture is the voxel grid of scans:
![](Pasted%20image%2020231126161042.png)
Modern GPUs usually support hardware 3D textures (known as volumetric textures) which makes this just as trivial as regular UV mapping. So the solution is to convert our voxel grid into a 3D texture and then simply apply this texture to our proxy surfaces.

We can tell that simply having a static stack of surfaces will not be good enough: If viewed from the side, we would see the gaps.
![](Pasted%20image%2020231126161338.png)
To fix this, we need the surfaces to be parallel to the viewplane at all times.
![](Pasted%20image%2020231126161424.png)
This is an easy and fast calculation, so while this does technically make this method viewpoint dependent it is an insignificant amount of work.
The amount of slices is very important. Not enough slices and the image simply doesn't come together:
![](Pasted%20image%2020231126161528.png)
As we add more layers, it becomes clearer and clearer:
![](Pasted%20image%2020231126161554.png)
And when we reach a suitable amount the image becomes clear.
![](Pasted%20image%2020231126161627.png)