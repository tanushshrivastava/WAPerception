import cv2
import numpy as np

# Load the image
input_image = cv2.imread("red.png")

# Convert color space from BGR to RGB
rgb_image = cv2.cvtColor(input_image, cv2.COLOR_BGR2RGB)

# Convert to HSV color space
hsv_image = cv2.cvtColor(rgb_image, cv2.COLOR_RGB2HSV)

# Thresholding to isolate red/orange regions
lower_red = np.array([0, 135, 135])
upper_red = np.array([15, 255, 255])
thresh_low = cv2.inRange(hsv_image, lower_red, upper_red)

lower_orange = np.array([159, 135, 135])
upper_orange = np.array([179, 255, 255])
thresh_high = cv2.inRange(hsv_image, lower_orange, upper_orange)

binary_image = cv2.bitwise_or(thresh_low, thresh_high)

# Morphological operations to clean up the image
kernel = np.ones((5, 5), np.uint8)
opened_image = cv2.morphologyEx(binary_image, cv2.MORPH_OPEN, kernel)

# Median blur for further noise reduction
blurred_image = cv2.medianBlur(opened_image, 5)

# Edge detection using Canny
edges_image = cv2.Canny(blurred_image, 80, 160)

# Find contours in the edged image
contours, _ = cv2.findContours(edges_image, cv2.RETR_TREE, cv2.CHAIN_APPROX_SIMPLE)

# Initialize empty lists to store contours and convex hulls
approx_contours = []
all_convex_hulls = []

# Approximate and draw contours
for contour in contours:
    approx = cv2.approxPolyDP(contour, 10, closed=True)
    approx_contours.append(approx)

# Compute convex hulls
for approx_contour in approx_contours:
    all_convex_hulls.append(cv2.convexHull(approx_contour))

# Filter convex hulls by size
convex_hulls_3to10 = [ch for ch in all_convex_hulls if 3 <= len(ch) <= 10]

# Initialize lists for cones and bounding rectangles
detected_cones = []
bounding_rectangles = []

# Iterate through convex hulls and filter noise
for convex_hull in convex_hulls_3to10:
    area = cv2.contourArea(convex_hull)
    if 50 < area < 3250:
        detected_cones.append(convex_hull)
        rect = cv2.boundingRect(convex_hull)
        bounding_rectangles.append(rect)

# Calculate the middle x-coordinate of the detected cones
middle_x = sum([rect[0] + rect[2]//2 for rect in bounding_rectangles]) // len(bounding_rectangles)

# Separate cones into left and right based on their x-coordinates
left_cones = [rect for rect in bounding_rectangles if rect[0] + rect[2] // 2 < middle_x]
right_cones = [rect for rect in bounding_rectangles if rect[0] + rect[2] // 2 >= middle_x]

# Find the closest and farthest cones on the left side
closest_left_cone = min(left_cones, key=lambda rect: abs(rect[0] + rect[2] // 2 - middle_x))
farthest_left_cone = max(left_cones, key=lambda rect: abs(rect[0] + rect[2] // 2 - middle_x))

# Find the closest and farthest cones on the right side
closest_right_cone = min(right_cones, key=lambda rect: abs(rect[0] + rect[2] // 2 - middle_x))
farthest_right_cone = max(right_cones, key=lambda rect: abs(rect[0] + rect[2] // 2 - middle_x))

# Draw lines through the closest and farthest cones on the left side
path_img = input_image.copy()
height, width, _ = path_img.shape

# Calculate the slope for the left side
slope_left = (farthest_left_cone[1] + farthest_left_cone[3] // 2 - closest_left_cone[1] - closest_left_cone[3] // 2) / (
        farthest_left_cone[0] + farthest_left_cone[2] // 2 - closest_left_cone[0] - closest_left_cone[2] // 2)

# Calculate the y-coordinates for the top and bottom borders
y_top = int(-closest_left_cone[0] * slope_left + closest_left_cone[1] + closest_left_cone[3] // 2)
y_bottom = int((width - closest_left_cone[0]) * slope_left + closest_left_cone[1] + closest_left_cone[3] // 2)

# Draw the line on the left side
cv2.line(path_img, (0, y_top), (width, y_bottom), (0, 0, 255), 3)

# Calculate the slope for the right side
slope_right = (farthest_right_cone[1] + farthest_right_cone[3] // 2 - closest_right_cone[1] - closest_right_cone[
    3] // 2) / (farthest_right_cone[0] + farthest_right_cone[2] // 2 - closest_right_cone[0] - closest_right_cone[
    2] // 2)

# Calculate the y-coordinates for the top and bottom borders
y_top_right = int(-closest_right_cone[0] * slope_right + closest_right_cone[1] + closest_right_cone[3] // 2)
y_bottom_right = int((width - closest_right_cone[0]) * slope_right + closest_right_cone[1] + closest_right_cone[
    3] // 2)

# Draw the line on the right side
cv2.line(path_img, (0, y_top_right), (width, y_bottom_right), (0, 0, 255), 3)

# Save the resulting image
cv2.imwrite("answer.png", path_img)
