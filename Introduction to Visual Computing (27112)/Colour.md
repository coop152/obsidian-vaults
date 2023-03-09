The human eye can see using two kinds of light-sensitive cells:
- **Rods** for low-light vision, no colour
- **Cones** for colour

There are three types of Cone:
- **S** - Sensitive to short wavelength light (vaguely blue)
- **M** - Sensitive to medium wavelength light (vaguely green)
- **L** - Sensitive to long wavelength light (vaguely red)

![](Pasted%20image%2020230309150619.png)
65% of your cones are sensitive to red light, 33% are sensitive to green light, and only 2% are sensitive to blue light (which is outweighed by their higher sensitivity).

## Additive vs Subtractive Colour
Additive colour is the kind that is emitted by a monitor (i.e. mixing light), and Subtractive colour is the kind that is made by paint or ink on paper (i.e. mixing pigments).
![](Pasted%20image%2020230309150848.png)
A green leaf is green because the white light from the sun reflects off the surface of the leaf, which absorbs wavelengths of light other than green. This reflected green light is detected by our eyes. This is subtractive colour.
An image of a green leaf on a screen is green because the LEDs in the screen emitted green light, which is detected by our eyes. This is additive colour.

## Colour Matching
Scenario: I like bright colours, and I want the logo I'm designing to be a bright purple. To this end, I turn up the colour vibrancy settings on my monitor.
With these settings, I design a purple logo that exactly matches the colour of our letterhead (I checked by comparing the colour to a physical letter).
I put this design on a business card and send it off to be printed.
**Why will I be dissatisfied with the results?**
Answer: Because changing the settings of the monitor didn't change the colours you were choosing in the software. If your highly vibrant monitor was showing a pleasant bright purple, it's because of the monitor and not because of your actual colour choice.
You need a **standard** to know what each colour looks like exactly, regardless of a monitor's colour reproduction. One such standard is **CIE 1931**:

## CIE 1931
![](Pasted%20image%2020230309151636.png)
Note that these values don't actually correspond with the true wavelengths that each cone detects; the standard is so old that experimental data did not yet exist when it was made.

In CIE, the amount of red, green and blue for some colour are given as X, Y and Z; these are called the **tristimulus** values. These are normalised like so:
![](Pasted%20image%2020230309151903.png)
Due to this, you can speak in terms of just two of the variables as the other will be trivially calculated from them. We will talk about just x and y.
![](Pasted%20image%2020230309151959.png)
![](Pasted%20image%2020230309152515.png)
This diagram shows the CIE XYZ gamut. The "tongue" shows all of the colours that humans can perceive, while the triangle shows all of the colours that the system can represent.

There are lots of other colour models:
- YCrCb (Luminance, Chroma red, Chroma blue) - encodes colours in terms of their intensity and colour difference. Used widely in broadcasting, as it allowed the transmission of a video signal that is simultaneously monochrome and colour.
- Perceptual spaces such as HSV, IHS, HSB and Lab

The RGB colour space looks like this:
![](Pasted%20image%2020230309153003.png)

## YCrCb
![](Pasted%20image%2020230309153031.png)
Used in broadcast and digitally (i.e. in MPEG).

## Perceptual Spaces
Equal distances on the CIE diagram DO NOT correspond to equal changes in perceived colour; this is important for measurement, and for allowing colours to be described in a way suitable for humans.
(This can be observed in the CIE diagrams; going the length of one grid square can do very little or a lot.)
#### IHS (HSI)
Stands for Hue, Saturation, Intensity.
- Hue - The basic colour. An angle from 0 to 359.
- Saturation - The "depth" of the colour. A lower saturation is more "diluted" with white.
- Intensity - Brightness. Is the weighted average of the RGB values.

This can be represented in two ways:
![](Pasted%20image%2020230309153707.png)
You can convert from RGB to HSI like so:
![](Pasted%20image%2020230309153757.png)
#### Lab (CIELAB)
The colour space used by Photoshop.
- L* - Lightness. 0 = black, 100 = white.
- a* - A scale from green to red. -a = green, +a = red
- b* - A scale from blue to yellow. -b = blue, +b = yellow

![](Pasted%20image%2020230309154013.png)
#### Comparison
![](Pasted%20image%2020230309154040.png)
Note that none of these cover the entire visible spectrum of the human eye.

## Point Processing with HSV
Say you want to extract the yellow balloons in this image:
![](Pasted%20image%2020230309154200.png)
Just like we might split an RGB image into R, G and B channels to find especially red, green or blue sections, we can split an HSV image into H, S and V channels to find sections with certain Hues, Saturations, or Values.
![](Pasted%20image%2020230309154308.png)
To do this in OpenCV, you would do this:
![](Pasted%20image%2020230309154433.png)
We want yellow, so let's check the HSV diagram:
![](Pasted%20image%2020230309154511.png)
![](Pasted%20image%2020230309154533.png)
