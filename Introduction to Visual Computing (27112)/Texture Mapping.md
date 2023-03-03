One way of applying surface detail is to apply a texture to a mesh; that is, to wrap some image around the mesh. There are two kinds of texture that can be applied to a mesh:
- Image-based - The texture is a bitmap image of some fixed size
- Procedural - The image is generated on the fly, limiting the kind of textures that can be produced but giving "perfect" detail (i.e. no bitmap artifacts)

An image-based texture is a 2D collection of pixels, just like any other image. In a texture, pixels are called **texels**. In order to map a texture onto a mesh, a coordinate system must be defined to refer to these texels:
![](Pasted%20image%2020230302145819.png)
Generally, the axis are called $u$ and $v$. Texture Mapping is the process of mapping these UV coordinates from the texture onto the mesh.
![](Pasted%20image%2020230302150213.png)
In order to find the colour of some point on the polygon, that point's corresponding UV coordinate must be calculated, then the colour at that texel in the texture is taken and blended with the pixel colour.
![](Pasted%20image%2020230302150435.png)
The colour and coordinate interpolation is done the same way as in previous examples.
There are some difficulties with mapping textures onto a model:
## Texture Seams
![](Pasted%20image%2020230302150656.png)
If a texture is not mapped correctly, or doesn't repeat well, the seams between one occurrence of a texture and the next may have a visible discontinuity. 
## Resolution mismatches
![](Pasted%20image%2020230302150853.png)
When the size of the texture doesn't match up with the on-screen size of the polygon, the texture needs to be scaled or transformed in some way to fit, which can pose many difficulties. There are two cases to consider:
**When the polygon's pixel size is bigger than the texture's**
In this case, we have to 'spread' the same number of texels out over many pixels. We can:
1. Apply no filter

Simply select the texel that the pixel maps to with no modification. This is easy, but the result will look blocky.
![](Pasted%20image%2020230302151443.png)
2. Bilinear Interpolation

Given some pixel coordinate, map it to it's corresponding texel **keeping the fractional part** and then consider distance of this fractional texel from the actual texels that it falls into. Use linear interpolation on both axis to find an averaged colour for the pixel.
![](Pasted%20image%2020230302151958.png)
**When the polygon's pixel size is smaller than the texel**
If consecutive pixels on a polygon map to distant texels due to skipped texels from a low pixel size, you might notice **aliasing**. A widely used solution to this problem is **mipmap filtering**.

#### Mipmapping
Mipmapping is based on the idea that the further an object is from the camera, the less detail is needed in the textures. Therefore, use a set of textures with decreasing detail instead of just one, and swap them based on distance from the object.
![](Pasted%20image%2020230302152718.png)
Without mipmapping, notice the aliasing:
![](Pasted%20image%2020230302152957.png)
With mipmapping:
![](Pasted%20image%2020230302153029.png)

## Light Maps
Textures can be used for more than just surface colour. In some real-time rendering contexts such as games, calculating lighting might be too expensive to do in real-time. What you can do instead is calculate the lighting of the scene beforehand using some global model (e.g. radiosity) and then save these rendered surfaces, lighting included, as textures called **lightmaps**. When rendering in real-time, apply these lightmaps in addition to the surface textures to provide a lit scene with no real-time lighting calculations.

## Bump Maps
If you want to render a rough surface, you might apply a texture of a rough surface to your mesh. This won't give a very convincing result, though; real rough surfaces react differently to light, as their rough surface reflects light differently.
![](Pasted%20image%2020230302153816.png)
To represent these bumps on a surface that change how light is reflected, and therefore to convincingly render rough surfaces, we will make use of another kind of texture called a **bump map**.

Bump maps are not applied to the colour of a surface, like a surface texture or a light map. They are applied to the normals of the surface instead, changing the apparent shape of the surface during rendering which will in turn change how the lighting is rendered.
![](Pasted%20image%2020230302154057.png)

#### Altering the surface normal
Given some surface normal $\hat{N}$, consider the two vectors orthogonal to $\hat{N}$. Let us call these $\hat{N}_U$ and $\hat{N}_V$. 
![](Pasted%20image%2020230302154223.png)
In order to move the vertex normal $\hat{N}$, one might imagine shifting its tip along one of these vectors by some amount. To represent this, we add some coefficients to the orthogonal vectors:
![](Pasted%20image%2020230302154536.png)
With this system, we can transform the normal of this surface by summing these three vectors together, with the amount and direction of the change defined by these coefficients $b_U$ and $b_V$ (and then normalising, which is implicit in the fact that it's a surface normal). These coefficients are given in the bump map.
![](Pasted%20image%2020230302154707.png)
Bump mapping can give results like this, without changing the mesh at all:
![](Pasted%20image%2020230302154818.png)
How are $b_u$ and $b_v$ stored in a bump map? They are not stored directly, but must be derived.
The bump map is like a regular texture, but instead of storing colours for each texel it stores **heights**. Here is an example:
![](Pasted%20image%2020230302155238.png)
However, we do not want heights, we want slopes. Therefore, we can compute $b_u$ and $b_v$ from this bump map by calculating the height difference between neighbouring texels:
![](Pasted%20image%2020230302155402.png)
Here is an example of a model with a bump map and a surface texture applied:
![](Pasted%20image%2020230302155534.png)
This is just a regular torus; there are no spots on the model itself.