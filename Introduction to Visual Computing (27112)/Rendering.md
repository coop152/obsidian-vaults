
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
When a light source emits a ray inside a room, it bounces around and illuminates more than just the first place it touched. This changes the ambient brightness and colour of the room. This can be defined by these rules:
- If the monochrome intensity of ambient light is $I_a$
- Amount of ambient light diffusely reflected from a surface is $I_{\text{ambient}} = k_aI_a$.
- $k_a$ is the ambient reflection coefficient of the surface. $0 \leq k_a \leq 1$.

So our first model for reflections off a surface is this:
$I$ = ambient light
$$I = k_aI_a$$
![](Pasted%20image%2020230223163548.png)
This fills things in, but all depth is completely lost. Here is how the ambient reflection coefficient affects the lighting:
![](Pasted%20image%2020230223163653.png)
#### Directional Lighting
![](Pasted%20image%2020230223163855.png)
In order to provide a greater sense of depth and shape, we need to factor in the influence of angle and distance. Intuitively, you can see that objects at a sharper angle to a light source will be less illuminated. How to model this?
![](Pasted%20image%2020230223164106.png)
Take these variables regarding the position of an object relative to a light source. We can use Lambert's Law:
$I_p$ = intensity of light source
$I_e$ = effective intensity received
$$ I_e = I_p\cos{\theta}$$
#### Diffuse reflectivity
We describe the diffuse reflectivity of a surface by assigning it a value $k_d$.
This number is the **diffuse reflection coefficient**, $0 \leq k_d \leq 1$.
By Lambert's Law, the amount of diffusely reflected light is:
$I_{\text{diffuse}} = I_pk_d\cos{\theta}$
or in terms of vectors:
$I_{\text{diffuse}} = I_pk_d(\hat{N} \cdot \hat{L})$

So our second version of our local illumination model is:
$I$ = ambient + diffuse light
$$I = k_aI_a + I_pk_d(\hat{N} \cdot \hat{L})$$
![](Pasted%20image%2020230223165011.png)
![](Pasted%20image%2020230223165056.png)
This adds a sense of depth that the purely ambient model lacked. Here is how the diffuse reflection coefficient affects the lighting:
![](Pasted%20image%2020230223165230.png)
#### Light source distance