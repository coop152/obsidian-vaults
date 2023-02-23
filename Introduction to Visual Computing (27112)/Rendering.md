## Illumination
![](Pasted%20image%2020230223160035.png)
In a local illumination model, the lights casts directly onto individual objects and colours them. Global illumination also calculates the indirecting lighting of objects caused by light continuing to bounce after contacting a surface.
In this unit, we will develop a simpler local illumination model. COMP37111 next year covers global models.

In illumination, we approximate the interaction of light and matter; the standard local model is a simple approximation. Adding per-material algorithms (called shaders) often gives better results, and a global model is always better than a local model.

#### Implementation steps
- Light intensity (no colour)
- Ambient illumination
- Diffuse reflection
- Positional light sources
- Specular reflection
- Coloured lights and surfaces

## Light/surface interaction
![](Pasted%20image%2020230223160547.png)
**Diffuse** reflections is the combination of light absorption and uniform re-radiation.
- Some wavelengths are absorbed by the object, and some are reflected.
- A green object appears green because it absorbs all colours but green.

Specular reflection is reflection at the air/surface interface
- At a first look, the colour of a specular reflection is the same as the light source.
- (This isn't actually true but that's for later)

![](Pasted%20image%2020230223160914.png)

#### Diffuse reflections
![](Pasted%20image%2020230223161033.png)
This kind of reflection results in a dull/matte surface appearance.

#### Specular reflections
![](Pasted%20image%2020230223161157.png)
A perfect specular reflection doesn't change the colour of the incoming light, resulting in a perfect reflection.
![](Pasted%20image%2020230223161234.png)
Meanwhile, an imperfect specular reflection will change the incoming light somewhat. Depending on the level of specularity, this may give an object a weak mirror like appearance (for example, a metal spoon or a CD), or a smooth, shiny appearance (for example, a smooth vase or tiling).
#### Diffuse/specular surfaces
![](Pasted%20image%2020230223161445.png)
Most surfaces actually exhibit a mixture of diffuse and specular reflections. More on this in COMP37111.

## Developing a local illumination model
#### Ambient Illumination
![](Pasted%20image%2020230223161626.png)
When a light source emits a ray inside a room, 