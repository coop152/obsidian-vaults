# Interest point Detection
Here are some results of finding keypoints in every provided image:
**bernieSanders.jpg:**
![](bernieSanders.jpg)
The gloves are a hotspot for keypoints, as well as the corners of the chairs and the frame of Bernie's glasses.
**darkerBernie.jpg (no clipping):**
![](darkerBernie.jpg)
Without clipping, most features are found in the edges of the "voids". Not a good result.
**darkerBernieClip.jpg (with clipping):**
![](darkerBernieClip.jpg)
With clipping, however, the results are much more like the original image.
**bernie180.jpg:**
![](bernie180.jpg)
The keypoints in the rotated variant are very similar to the original.
**bernieNoisy2.png:**
![](bernieNoisy2.png)
The noise completely ruins the results. The salt-and-pepper noise variant has a similar problem, while the blurred variant has too few keypoints.
**BernieFriends.png:**
![](BernieFriends.png)
The keypoints on the gloves could potentially match with the original image, but most of them don't. The same goes for the other heavily edited images.

I used a threshold of **0.05** and a k of **0.05** when finding keypoints in these images. I used this threshold because it gave a similar number of points to the built-in ORB detector, and (skipping ahead to the matching part) it had a reasonably low number of bad matches.

Measuring on the original image (bernieSanders.jpg), the number of features located scales as such with the threshold:
![](Features%20detected%20at%20certain%20thresholds.png)
# Feature Matching
The 180 degree rotated image is an example of a matching where my implementation performs well:
**My Harris implementation, SSD matching:**
