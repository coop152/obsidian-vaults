(Lots of obvious stuff about image representation goes here)
The origin of an image is at the top-left:
![](Pasted%20image%2020230309144457.png)
But when representing an image as a matrix, remember that the row comes first:
![](Pasted%20image%2020230309144606.png)

A point processing function takes an image and applies some function to each pixel individually, with no influence on or from other pixels.
![](Pasted%20image%2020230309144815.png)
To negate an image, subtract the intensity (mono or colour) from the max value.
To increase/decrease brightness, add some constant to the intensity (and clamp to the acceptable range).