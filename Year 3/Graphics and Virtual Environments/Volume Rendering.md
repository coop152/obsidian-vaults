All of the previous rendering techniques we have covered are concerned with totally solid surfaces, usually made of polygons (with a few exceptions for mathematically defined surfaces such as spheres, toruses, etc.) However, in the real world most objects do not have a perfectly solid surface: In addition to having an actual interior, the surfaces have volume and thickness by themselves. These surfaces are unlikely to have consistent thickness and sharp separation from the surroundings.
"Volume rendering" is not to be confused with "Volumetric rendering", which refers to effects such as fog or dust interacting with light.
In volume rendering, objects do not have explicitly defined surfaces. This might be because we are modelling something that doesn't have a meaningful "surface", such as wind patterns or temperatures in a weather simulation.
On the other hand, the object may still have a well-defined surface but we are also interested in the matter inside, beyond that surface level. It is even useful where we are only concerned with the surface, but we have captured our object in such a way that information about that surface is not easily or obviously presented. An example of both of these is a CAT scan, where a 3D scan of a body is found by taking multiple x-ray "slices" and then stitching the slices back together to make a 3d mesh. This provides no "surface" information, but it does describe a volume which we want to visualise.
## CAT scanning
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
The scan includes no sense of colour or opacity, only radio density. To transfer our raw radio density into something we can display, we employ a **Transfer Function** which maps Hounsfield unit values into display colours.
Before we investigate transfer functions for Hounsfield units, let's consider the more general case of how we might classify values for any input.
Say we are scanning this hand:
![](Pasted%20image%2020231126141356.png)
The glass will be sampled as having some value, and (assuming for now that the hand is homogeneous in material) the hand will have some other value. We might assign the value 500 to glass:
![](Pasted%20image%2020231126141556.png)
If we say that the hand has value 200, then we would assign the pieces that are fully hand 200 and the pieces that are slightly glass but mostly hand around 250:
![](Pasted%20image%2020231126141714.png)
If we continue until we have a value for every point we have a "scan". We can investigate the distribution to learn something about what is present:
![](Pasted%20image%2020231126141846.png)
You see two spikes vaguely around the glass value and the hand value. The fact that there are two spikes tells us that we probably have two distinct materials; the fact that the spikes aren't very sharp tells us that either there is a "softness" where material "fades out" or that our sampling resolution isn't very good. In this case, our resolution was the reason.
If we use this distribution to assign some colours to our classified materials, we can make somewhat of an image:
![](Pasted%20image%2020231126142108.png)
![](Pasted%20image%2020231126142115.png)
Clearly the low resolution is diminishing the usefulness of this result, but at higher resolutions this would become more detailed and useful.

