Light is a kind of electromagnetic wave residing between 380nm and 760nm in wavelength. Light is characterised by it's spectrum, which is the amount of energy that light of each wavelength has.
![](Pasted%20image%2020231005105640.png)
The perceived light is a composite of the illumination of the light itself multiplied by the reflectance of the surface(s) it bounced off of to reach your eye.
![](Pasted%20image%2020231005110051.png)
Our eyes percieve colour using three types of cone, which can each measure one wavelength in these ranges:
![](Pasted%20image%2020231005110659.png)

The primary (additive) colours are red, green and blue, vaguely matching the cones.

Light intensity/value is measured in $cd/m^2$ (candela per meter squared) and represents how bright the colour is. It is the area under the light wave on the diagram:
![](Pasted%20image%2020231005111349.png)
The hue of the colour is determined by the wavelength of the mean of the light wave, that is where it is centrally positioned on the graph.
![](Pasted%20image%2020231005111449.png)
The Saturation/Chroma is the amount of light in the wave that isn't the "main" colour; a large amount of non-central energy will wash out the colour and lead to a less saturated perception. On the graph, this corresponds to the deviation of the curve from the mean.
![](Pasted%20image%2020231005111636.png)
All of these three factors are related to each other, and they cannot just be changed independently.
![](Pasted%20image%2020231005111721.png)

# Colour Spaces
Human vision is linear and three-dimensional, so you can suitably convert any colour space to one that humans can perceive. For this reason there are many colour spaces, but the most popular now is **CIE XYZ**.

The colour values for CIE XYZ were found with this practical method:
![](Pasted%20image%2020231005112035.png)
A subject monitors a split screen, one side being illuminated by the colour they want to measure and the other size being illuminated by three primary lights of R, G and B. The values of the RGB lights are adjusted until the subject decides the difference in colour between the test light and the combined primary lights is negligible. Because light is linear, you can interpolate between the values found using this method to get a continuous colour space. These are the values that were recorded:
- 435.8 (blue)
- 546.1 (green)
- 700 (red)