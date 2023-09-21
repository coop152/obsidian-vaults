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
#### 8-bit Colour
In the past, computer video hardware (and displays) could only display 256 colours, meaning various formats were made that only used 8 bits per pixel. Unlike GIF, which uses a palette, these work like standard images but simply have less pixels per colour. A common configuration used now is:
- 3 bits for Red
- 3 bits for Green
- 2 bits for Blue

Formats like this are still popular on embedded systems, where low colour depth displays are still available.
#### Multiple Image Sizes
Some formats can store multiple images of different sizes. A common one is the windows icon file (.ico), which has many different sizes ranging from tiny titlebar icons to large desktop shortcuts.
![](Pasted%20image%2020230419135535.png)
This allows the operating system to pick an appropriate image for the situation; a tiny 16x16 icon on a titlebar is likely to be drawn differently to a large 128x128 desktop icon for the sake of usability (i.e. the small one is likely to be drawn pixel-by-pixel with no anti-aliasing, while the large one is likely to be smooth and anti-aliased).
#### Camera Settings
Many image formats have space for camera information, which can be useful in some applications. For example, knowing the focal length and aperture of the camera can be helpful in image processing.
![](Pasted%20image%2020230419135830.png)
#### Location
They may also store location. This can be only GPS coordinates (latitude and longitude), but it may include more precise measurements such as the camera/phone's gyroscope readings (which tell you the facing of the device).
![](Pasted%20image%2020230419140033.png)
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

## Compression
#### Run Length Encoding (RLE)
Encode long sequences of repeated values as a value plus a number of repetitions. Extremely basic and not likely to work well with realistic or noisy images. Note that some formats may offer the capability to switch between RLE compressed and uncompressed on the fly, allowing the overhead to be avoided in sections of the image that are poorly handled by RLE.
![](Pasted%20image%2020230419140259.png)
This method was used in the PCX image format, and was also used as **part of** the JPEG compression algorithm. It is lossless.
#### JPEG (Discrete Cosine Transform/DCT)
![](Pasted%20image%2020230419140702.png)
The DCT represents the frequencies in a block of an image; the lower frequencies are at the top left of the DCT, with higher frequencies at the bottom right. JPEG works on the assumption that the higher frequency detail is less likely to be important and can therefore be traded off for a reduction in size.
![](Pasted%20image%2020230419140859.png)
When the DCT has been calculated for a block, we continue to the next step of JPEG compression:
![](Pasted%20image%2020230419140954.png)
![](Pasted%20image%2020230419141103.png)
With this, we have compressed an entire 8x8 block of the image down to 7 pairs of numbers.
To decompress the image:
![](Pasted%20image%2020230419141231.png)
![](Pasted%20image%2020230419141259.png)
![](Pasted%20image%2020230419141328.png)
With all of this, we can have these results:
![](Pasted%20image%2020230419141357.png)
![](Pasted%20image%2020230419141529.png)
As is clearly apparent from these examples, JPEG compression is **lossy**. The compressed image can **not** be used to exactly reconstruct the input image. 
Note that this DCT method is just one part of the JPEG compression algorithm, and other methods are used.