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


## Camera Exposure
How can a camera take photos both outside on a sunny day but also in a dimly lit room? The Camera would need to vary the amount of light that is arriving at the camera sensor depending on the brightness of the scene.
The most straight forward way to do this is to vary the camera's aperture:
![](Pasted%20image%2020230419133227.png)
You can also vary the camera's shutter time:
![](Pasted%20image%2020230419133334.png)
