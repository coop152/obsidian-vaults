What data do we want to store in an image file?
- Image data (pixel values)
- Colour palette (for images which do not have a fixed palette)
- Multiple image versions (e.g. icons)
- Transparency
- Camera data (model, focal length, shutter speed, etc.)
- Location (latitude, longitude)

## Image Data
So far we have discussed two kinds of image data formats:
- Unsigned byte (8 bit, 0-255) for greyscale images
- 24-bit colour (three 8-bit channels, RGB) for colour images

What other formats might be desirable?
#### Portable Bit Map (.pbm)
When we threshold an image, we only get two colours (black and white). If we are storing/transmitting an image like this, we don't want to use the two previously mentioned formats; it would be wasteful, as each pixel can be represented by a single bit.
In the Portable Bit Map format, each pixel is represented by **a single bit**, meaning eight pixels are packed into a byte.
![](Pasted%20image%2020230419132836.png)
![](Pasted%20image%2020230419132915.png)
#### Deep Colour
For certain photography reasons, it can be desirable to have a greater range of colours available in an image. For the reasons behind this, see the Camera Exposure section. These formats use so-called **deep colour**, aka 48-bit colour, which has 16 bits per channel. Examples include TIFF, PNG, and many proprietary RAW formats.
#### Graphics Interchange Format (GIF)
This format is mostly used today for its animation support, but in the past it was used commonly for static images too, due to its small size.
It only has 8 bits per pixel, no matter if it is greyscale or colour.
I can contain any colours from the 24-bit colour space, but an image can only contain a maximum of 256 colours, which are stored in a **palette**.
![](Pasted%20image%2020230419135043.png)
This palette is stored within the image file.

## Camera Exposure
How can a camera take photos both outside on a sunny day but also in a dimly lit room? The Camera would need to vary the amount of light that is arriving at the camera sensor depending on the brightness of the scene.
The most straight forward way to do this is to vary the camera's aperture:
![](Pasted%20image%2020230419133227.png)
You can also vary the camera's shutter time:
![](Pasted%20image%2020230419133334.png)
![](Pasted%20image%2020230419133605.png)
![](Pasted%20image%2020230419133726.png)
![](Pasted%20image%2020230419133747.png)
![](Pasted%20image%2020230419133920.png)
![](Pasted%20image%2020230419133955.png)
#### High Dynamic Range (HDR)
![](Pasted%20image%2020230419134158.png)
![](Pasted%20image%2020230419134346.png)
![](Pasted%20image%2020230419134447.png)
#### HDR Sensors
![](Pasted%20image%2020230419134525.png)
![](Pasted%20image%2020230419134559.png)
