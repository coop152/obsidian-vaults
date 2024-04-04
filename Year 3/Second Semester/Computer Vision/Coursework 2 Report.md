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

I used a threshold of **0.05** and a k of **0.05** when finding keypoints in these images. I used this threshold because it gave a similar number of points to the built-in ORB detector on the original image, and (skipping ahead to the matching part) it had a reasonably low number of bad matches.

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
The ORB detector gets good results in those images, as well as the two noisy images. It gets middling results with the blurred image, and similarly unusable results to my implementation in the others.
The ORB detector/matcher does a significantly better job at finding matches than my Harris implementation.
# Appendix (Code listing)
```python
import cv2
from cv2.typing import MatLike
import numpy as np
import scipy.ndimage as spi
import scipy as sp

def HarrisPointsDetector(img: MatLike, k: float, threshold: float) -> 'list[cv2.KeyPoint]':
    # convert to grayscale, and blur a little to reduce noise
    img = cv2.cvtColor(img, cv2.COLOR_BGR2GRAY)
    # img = cv2.medianBlur(img, 5)
    # img = cv2.GaussianBlur(img, (5, 5), 0.5, borderType=cv2.BORDER_REFLECT)
    # calculate image derivatives for M
    Ix  = cv2.Sobel(img, cv2.CV_64F, 1, 0, ksize=3)
    Iy = cv2.Sobel(img, cv2.CV_64F, 0, 1, ksize=3)
    Ixy = np.multiply(Ix, Iy)
    Ix2 = np.multiply(Ix, Ix)
    Iy2 = np.multiply(Iy, Iy)

    # apply gaussian window, with reflective padding
    Ixy = cv2.GaussianBlur(Ixy, (5, 5), 0.5, borderType=cv2.BORDER_REFLECT)
    Ix2 = cv2.GaussianBlur(Ix2, (5, 5), 0.5, borderType=cv2.BORDER_REFLECT)
    Iy2 = cv2.GaussianBlur(Iy2, (5, 5), 0.5, borderType=cv2.BORDER_REFLECT)

    # calculate the determinant and trace
    # M = [Ix2 Ixy]
    #     [Ixy Iy2]
    # (using np functions to satisfy the typechecker)
    det = np.multiply(Ix2, Iy2) - np.multiply(Ixy, Ixy)
    trace = np.add(Ix2, Iy2)

    # calculate the corner response
    R = np.subtract(det, np.multiply(k, np.multiply(trace, trace)))

    # non-maxima reduction (in a 7x7 neighborhood)
    maxed = spi.maximum_filter(R, size=7)
    R[R != maxed] = 0

    # apply threshold to get corners
    corners = np.empty_like(R)
    corners.fill(0)
    corners[R > threshold * R.max()] = 255

    # calculate gradient direction
    dir = np.arctan2(Iy, Ix)

    # create KeyPoint objects
    kps = []
    for y in range(corners.shape[0]):
        for x in range(corners.shape[1]):
            if corners[y, x] == 255:
                kps.append(cv2.KeyPoint(x, y, 5, np.rad2deg(dir[y, x])))

    return kps

def SSDFeatureMatcher(desc1: MatLike, desc2: MatLike) -> 'list[cv2.DMatch]':
    matches = []
    # find SSD between descriptors for each image
    distances = sp.spatial.distance.cdist(desc1, desc2, 'euclidean')
    # for each descriptor in the first image...
    for i in range (len(distances)):
        m = cv2.DMatch()
        m.queryIdx = i
        # ...match to the descriptor in the second image that is closest to it
        m.trainIdx = np.argmin(distances[i])
        m.distance = distances[i][m.trainIdx]
        matches.append(m)
    return matches

def RatioFeatureMatcher(desc1: MatLike, desc2: MatLike) -> 'list[cv2.DMatch]':
    matches = []
    # find SSDs between descriptors for each image
    distances = sp.spatial.distance.cdist(desc1, desc2, 'euclidean')
    # for each descriptor in the first image...
    for i in range (len(distances)):
        m = cv2.DMatch()
        m.queryIdx = i
        # ...find the two closest descriptors in the second image
        closest, runner_up = np.argpartition(distances[i], 2)[:2]
        # if the ratio of the distances is less than 0.8, consider it a match
        if distances[i][closest] < 0.8 * distances[i][runner_up]:
            m.trainIdx = closest
            m.distance = distances[i][closest]
            matches.append(m)
        # otherwise, discard it
    return matches


# Use my implementation of Harris to match features, then save to `filename`
# provide either SSDFeatureMatcher or RatioFeatureMatcher as an argument
def matchFeaturesHarris(img1, img2, filename: str, featureMatcher):
    # Find corners in both images
    kps1 = HarrisPointsDetector(img1, 0.05, 0.05)
    kps2 = HarrisPointsDetector(img2, 0.05, 0.05)

    # Create ORB descriptors
    fd = cv2.ORB.create()
    kps1, descs1 = fd.compute(img1, kps1)
    kps2, descs2 = fd.compute(img2, kps2)

    # Match descriptors.
    matches = featureMatcher(descs1, descs2)

    # Draw matches
    imgMatches = img1.copy()
    imgMatches = cv2.drawMatches(img1, kps1, img2, kps2, matches, imgMatches, flags=2)

    cv2.imwrite(filename, imgMatches)

# Use ORB to match features in two images, then save to `filename`
def matchFeaturesORB(img1, img2, filename: str):
    # Initiate feature detector: ORB
    fd = cv2.ORB.create(nfeatures=200) # Limit number of features found to 200

    # find the keypoints and descriptors with ORB
    kps1 = fd.detect(img1, None)
    kps2 = fd.detect(img2, None)

    kps1, descs1 = fd.compute(img1, kps1)
    kps2, descs2 = fd.compute(img2, kps2)

    # create BFMatcher object
    bf = cv2.BFMatcher(cv2.NORM_HAMMING, crossCheck=True)

    # Match descriptors.
    matches = bf.match(descs1, descs2)

    # Draw matches
    imgMatches = img1.copy()
    imgMatches = cv2.drawMatches(img1, kps1, img2, kps2, matches, imgMatches, flags=2)

    cv2.imwrite(filename, imgMatches)


def main():
    # Find features in every image and save to imageFeatures/ directory
    # comment out after making these images to save time
    image_paths = ["bernie180.jpg",
        "bernieBenefitBeautySalon.jpeg",
        "BernieFriends.png",
        "bernieMoreblurred.jpg",
        "bernieNoisy2.png",
        "berniePixelated2.png",
        "bernieSanders.jpg",
        "bernieShoolLunch.jpeg",
        "brighterBernie.jpg",
        "brighterBernieClip.jpg",
        "darkerBernie.jpg",
        "darkerBernieClip.jpg"]

    for path in image_paths:
        img = cv2.imread(f"images/{path}")
        if img is None:
            print('Failed to open', path)
            return
        kps = HarrisPointsDetector(img, 0.05, 0.05)
        img = cv2.drawKeypoints(img, kps, img)
        cv2.imwrite(f"imageFeatures/{path}", img)

    # Load two images and find matches between them
    input_file = 'images/bernieSanders.jpg'
    img = cv2.imread(input_file)
    
    # Check file exists
    if img is None:
        print('Failed to open', input_file)
        return

    input_file = 'images/bernie180.jpg'
    imgObj = cv2.imread(input_file)
    
    # Check file exists
    if imgObj is None:
        print('Failed to open', input_file)
        return

    # Measure feature point counts for various thresholds (for the graph in the report)
    # comment out after finding the counts to save time
    for i in range(0, 81):
        kps = HarrisPointsDetector(img, 0.05, i * 0.01)
        print(f"Threshold: {i * 0.01}, Feature count: {len(kps)}")

    # Do feature matching
    matchFeaturesHarris(img, imgObj, "SSDMatches.jpg", SSDFeatureMatcher)
    matchFeaturesHarris(img, imgObj, "RatioMatches.jpg", RatioFeatureMatcher)
    matchFeaturesORB(img, imgObj, "ORBMatches.jpg")

if __name__ == '__main__':
    main()

```