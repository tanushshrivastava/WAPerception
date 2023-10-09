**Cone Detection**


**Files**
answer.png: The output image containing lines drawn through the cones.
cone_detection.py: Python script for cone detection.

**Methodology**
Color Space Conversion: The input image is converted from BGR to RGB and then to HSV color space.

Thresholding: The HSV image is thresholded to isolate red and orange regions.

Morphological Operations: Morphological operations are applied to clean up the binary image.

Edge Detection: Canny edge detection is used to highlight edges in the image.

Contour Detection: Contours are found in the edged image.

Approximation and Convex Hulls: Contours are approximated and convex hulls are computed.

Filtering by Size: Convex hulls with inappropriate sizes are filtered out.

Calculating Midpoint: The midpoint of all detected cones is calculated.

Separating Cones: Cones are separated into left and right based on their x-coordinates.

Finding Closest and Farthest Cones: The closest and farthest cones on both sides are identified.

Drawing Lines: Lines are drawn through the cones.

Saving Output: The resulting image with drawn lines is saved as answer.png.

What Was Tried
Different color spaces and thresholding ranges were experimented with to isolate red and orange cones. Additionally, various morphological operations and edge detection parameters were tested for optimal results. 
Also, object detection for cones itself was tried w/training data untill realized it wasnt allowed.

Libraries Used
OpenCV: Used for image processing tasks such as color space conversion, thresholding, edge detection, and contour finding.
NumPy: Used for numerical operations and array handling.
