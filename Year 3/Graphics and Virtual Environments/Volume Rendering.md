All of the previous rendering techniques we have covered are concerned with totally solid surfaces, usually made of polygons (with a few exceptions for mathematically defined surfaces such as spheres, toruses, etc.) However, in the real world most objects do not have a perfectly solid surface: In addition to having an actual interior, the surfaces have volume and thickness by themselves. These surfaces are unlikely to have consistent thickness and sharp separation from the surroundings.
"Volume rendering" is not to be confused with "Volumetric rendering", which refers to effects such as fog or dust interacting with light.
In volume rendering, objects do not have explicitly defined surfaces. This might be because we are modelling something that doesn't have a meaningful "surface", such as wind patterns or temperatures in a weather simulation.
On the other hand, the object may still have a well-defined surface but we are also interested in the matter inside, beyond that surface level. It is even useful where we are only concerned with the surface, but we have captured our object in such a way that information about that surface is not easily or obviously presented. An example of both of these is a CAT scan, where a 3D scan of a body is found by taking multiple x-ray "slices" and then stitching the slices back together to make a 3d mesh. This provides no "surface" information, but it does describe a volume which we want to visualise.
## Example CAT scan
Imagine we want to scan this golf ball, which has a bumpy surface and a rubber inside.
![](Pasted%20image%2020231126131740.png)
If we imagine performing a CAT scan on this golf ball, we would have a series of (in this instance) vertically ascending slices of the golf ball, each slice being a 2D scan of one layer of the ball. We can represent this as a 3D grid of voxels:
![](Pasted%20image%2020231126131942.png)
Each voxel containing a single data point. If we do a scan of the ball we get these values:
![](Pasted%20image%2020231126135214.png)
You can see where the layers of the ball roughly begin, as well as the volume of the inside.
Importantly, we don't have any actual identification for each of these parts. In a real CAT scan, where we get a full continuum of values for each layer, we have to somehow identify which part is what, manually or otherwise.
# Direct Volume Rendering
