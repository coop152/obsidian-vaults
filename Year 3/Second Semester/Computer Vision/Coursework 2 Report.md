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
The darkened image (with clipping) is an example of a matching where my implementation performs well:
**My Harris implementation, SSD matching:**
![](SSDMatches.jpg)
Mostly good matches, with some outliers.
**My Harris implementation, Ratio matching:**
![](RatioMatches.jpg)
Only good matches, as far as I can tell.
**OpenCV ORB detection and matching:**
![](ORBMatches.jpg)
Much less points, but all of them are good and they are much more spread out; my implementation clumps the key points heavily on the gloves.

An example of my implementation performing poorly is the noisy image (bernieNoisy2.png):
**My Harris implementation, SSD matching:**
![](SSDMatches%201.jpg)
Little to no good matches. Might as well be random.
**My Harris implementation, Ratio matching:**
![](RatioMatches%201.jpg)
Basically every match is eliminated, but the ones that remain are still bad. Has a single good match (the pink line on the gloves.)
**OpenCV ORB detection and matching:**
![](ORBMatches%201.jpg)
Not perfect, but it finds a good amount of valid matches.

My implementation gets good results for the rotated, darkened (clipped) and brightened (clipped) variants. The results for everything else are unusable.
The ORB detector gets good results in those images, as well as the two noisy images. It gets middling results with the blurred image, and equally unusable results in the others.
The ORB detector/matcher does a significantly better job at finding matches than my Harris implementation.