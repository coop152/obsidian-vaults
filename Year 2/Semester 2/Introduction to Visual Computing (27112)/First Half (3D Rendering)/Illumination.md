## Illumination
![](Pasted%20image%2020230223160035.png)
In a local illumination model, the lights casts directly onto individual objects and colours them. Global illumination also calculates the indirect lighting of objects caused by light continuing to bounce after contacting a surface.
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

So the first model for reflections off a surface is this:
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

So the second version of our local illumination model is:
$I$ = ambient + diffuse light
$$I = k_aI_a + k_dI_p(\hat{N} \cdot \hat{L})$$
![](Pasted%20image%2020230223165011.png)
![](Pasted%20image%2020230223165056.png)
This adds a sense of depth that the purely ambient model lacked. Here is how the diffuse reflection coefficient affects the lighting:
![](Pasted%20image%2020230223165230.png)
#### Light source distance
You can also intuitively tell that objects further from a light source should be less lit; light intensity falls off over a distance. This is given by the inverse square law:
After travelling $d$, original intensity $I_p$ becomes $I_e$
$$I_e = \frac{I_p}{4\pi d^2}$$
Note that in certain situations $d^2$ changes very rapidly, so instead we use this approximation:
$$I_e = \frac{I_p}{k_c + k_ld + k_qd^2}$$
We then tune the three coefficients $k_c, k_l, k_q$ for the best results.

So the third version of our local illumination model is:
$I$ = ambient + distance(diffuse)
$$I = k_aI_a + \frac{I_p}{d'}k_d(\hat{N} \cdot \hat{L})$$
where $d' = k_c + k_ld + k_qd^2$
#### Specular reflections
Our objects are now well shaded and display depth, but they all look dull and matte. Some objects are smooth, and display specularity. We would like to simulate partial specularity, as most objects are some mix of diffuse and specular. Consider the previously given definition of specular reflections:
![](Pasted%20image%2020230223171056.png)
![](Pasted%20image%2020230223171158.png)
Consider these cases:
![](Pasted%20image%2020230223171440.png)
The observer is looking directly down the vector of max specular reflection, giving them 100% of the specular reflection.
![](Pasted%20image%2020230223171536.png)
If the observer moves away from the vector of max specular reflection, naturally the amount of observed specular reflections decreases. Therefore, when $\phi$ increases, $I_{\text{specular}}$ decreases.
So the variation in the observed specular reflection is a function on $\phi$. What is this function?
###### Phong shading
A popular approximation of this function is $F(\phi) \approx \cos^n{\phi}$, but this approximation has no basis in reality and just happens to look quite good while being computationally tractable. The true definition of $F$ is rather complex.
However, if we stay with this approximation we end up with this formula:
$I_\text{specular} = I_p\cos^n\phi$
Or, using vectors:
$I_\text{specular} = I_p(\hat{R} \cdot \hat{V})^n$
This produces results like this:
![](Pasted%20image%2020230223172428.png)
Increasing the power has the effect of "narrowing" the light.

#### Incident angle and wavelength
So we've found some function that represent's $\phi$'s influence on the specular reflections, but we still need to find functions for $\theta$ and $\lambda$.
![](Pasted%20image%2020230223173044.png)
To show why this isn't a simple thing to do, observe this diagram showing the reflectivity of real polished copper:
![](Pasted%20image%2020230223173123.png)
And here is a diagram of the reflectance of aluminium:
![](Pasted%20image%2020230223173231.png)
For our purposes, we will be assuming that the angle of incidence and the wavelength of the incident light affect the illumination of the surface independently. This isn't actually true, but it would make things much harder so let's pretend.
Consider this diagram:
![](Pasted%20image%2020230223173519.png)
As a refresher:
$\hat{L}$ = The (inverse of the) vector giving the direction of the incident light
$\hat{N}$ = The surface normal of the surface being lit
$\hat{R}$ = The vector giving the direction of maximum specular reflection
$\phi$ = The angle between the surface normal and the incident light and/or $\hat{R}$
And the new symbols are:
$\hat{T}$ = The vector giving the direction of the transmitted light
$\theta$ = The angle between (the inverse of) the surface normal and the transmitted light.
With these defined, we can use **Fresnel's equation**:
![](Pasted%20image%2020230223174050.png)
where:
- $F$ is the fraction of light reflected
- $\sin\theta = \frac{\sin\phi}{\mu}$ 
- $\mu$ is the refractive index of the material in air (which depends on $\lambda$)

(You don't have to remember this, obviously.)
Now clearly this is a lot of computation, so another approximation can be enacted:
![](Pasted%20image%2020230223174336.png)
This degrades the visual appearance, but provides a great improvement in performance.

So the fourth version of our local illumination model is:
$I$ = ambient + distance(diffuse + specular)
$$I = k_aI_a + \frac{I_p}{d'}[k_d(\hat{N} \cdot \hat{L}) + k_s(\hat{R} \cdot \hat{V})^n]$$
where $d' = k_c + k_ld + k_qd^2$

This model produces results like this:
![](Pasted%20image%2020230223174800.png)
![](Pasted%20image%2020230223174843.png)
Here is how changing the coefficients for specular and diffuse illumination looks:
![](Pasted%20image%2020230223174858.png)
#### Incorporating colour
We have only considered monochrome intensity so far. How would we express colour?
Its actually quite easy - we express coloured light as a triple of RGB intensities $I_{pR}, I_{pG}, I_{pB}$. Therefore, we express surface colour using 6 coefficients $k_{aR}, k_{aG}, k_{aB}$ and $k_{dR}, k_{dG}, k_{dB}$.

So the fifth colour version of our local illumination model is:
$I$ = ambient + distance(diffuse + specular)
$$I_R = k_{aR}I_{aR} + \frac{I_{pR}}{d'}[k_{dR}(\hat{N} \cdot \hat{L}) + k_s(\hat{R} \cdot \hat{V})^n]$$
$$I_G = k_{aG}I_{aG} + \frac{I_{pG}}{d'}[k_{dG}(\hat{N} \cdot \hat{L}) + k_s(\hat{R} \cdot \hat{V})^n]$$
$$I_B = k_{aB}I_{aB} + \frac{I_{pB}}{d'}[k_{dB}(\hat{N} \cdot \hat{L}) + k_s(\hat{R} \cdot \hat{V})^n]$$
where $d' = k_c + k_ld + k_qd^2$

#### Multiple Lights
Finally, what if we have multiple lights? Easy again - Compute the illumination separately for each light and sum. For M lights:
$$I = \text{ambient} + \sum^M_{i = 1}(\text{diffuse}_i + \text{specular}_i)$$
Here is how that looks:
![](Pasted%20image%2020230223175917.png)
And some various examples of the finished model:
![](Pasted%20image%2020230223175942.png)