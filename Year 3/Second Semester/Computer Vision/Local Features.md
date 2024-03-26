The model-based feature detection shown in the previous part is a **global representation**. It detects a face by searching for the entirety of a face in the given image. While these kinds of detectors can work well under good conditions, they have some limitations:
- They often struggle with occlusions
- They often struggle with so-called "intra-category variations". That is, they struggle with valid outlier inputs, for example a human face with an abnormally shaped nose, missing an eye, etc..

These issues can be better handled by detectors that use **local representations**.