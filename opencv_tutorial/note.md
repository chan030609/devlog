# Introduction to OpenCV

## Pixels

Pixels: the raw __building blocks__ of images
- In OpenCV, there are 256 shades of  
- RGB (Red, Green, Blue) colorspace has a 3-tuple associated with each pixel: (B,G,R).

To extract individual pixel values at coordinates (0,0),
```python
import cv2
image = cv2.imread('path_to_image')
(B,G,R) = image[0,0]
```
By the way, coordinates (0,0) represents the top-left corner.

&nbsp;

## Array slicing and cropping

To extract "regions of interest" (ROIs), array slicing can be used.

```python
roi = image[60:160, 320:420]
```

&nbsp;

## Resizing images

```python
resized = cv2.resize(image, (200, 200))
```
This resizes the original image to 200 x 200 pixel squared, regardless of the aspect ratio. Taking the aspect ratio into account takes a little bit more calculations.

&nbsp;

## Rotating an image

Rotating the image 90 degrees or 180 degrees clockwise/counterclockwise is an easy task, as there is a specific function responsible for it. However, what about angles that are in-between, like 45 degrees?

```python
# first, compute the image center. Then, construct the rotation matrix. Lastly, apply the affine warp.
center = (w//2, h//2) # w and h can be obtained from image.shape

M = cv2.getRotationMatrix2D(center, -45, 1.0)
rotated = cv2.warpAffine(image, M, (w,h))
cv2.imshow("OpenCV Rotation", rotated)
cv2.waitKey(0)
```

&nbsp;

## Smoothing an image

Some image processing pipelines employ smoothing to ensure that noises are removed from the images. `GaussianBlur` function is most widely used:

```python
blurred = cv2.GaussianBlur(image, (11,11), 0)
cv2.imshow("Blurred", blurred)
```

&nbsp;

## Drawing on an image

Drawing operations on images are performed __in-place__, meaning we must make a copy of the original image if we don't want our original image to be altered. Let's start with a rectangle.

```python
output = image.copy()
cv2.rectange(output, (320, 60), (420, 160), (0,0,255), 2)
# parameters: image, starting coordinates, ending coordinates, color, thickness
# Drawing a line works just the same way, with same parameters. Just change the function to cv2.line().
cv2.imshow("Rectangle", output)
cv2.waitKey(0)
```
Now, a solid blue circle.

```python
output = image.copy()
cv2.circle(output, (300, 150), 20, (255,0,0), -1)
# parameters: image, starting coordinates, ending coordinates, color, thickness (solid: -1)
c2.imshow("Circle", output)
cv2.waitKey(0)
```

Lastly, you can overlay a text:

```python
output = image.copy()
cv2.putText(output, "Text Text Text!!!", (10, 25), cv2.FONT_HERSHEY_SIMPLEX, 0.7, (0, 255, 0), 2))
cv2.imshow("Text", output)
cv2.waitKey(0)
```
&nbsp;

## Converting an image to grayscale

```python
image = cv2.imread('path_to_image')
cv2.imshow("Image", image)
cv2.waitKey(0)

gray = cv2.cvtColor(image, cv2.COLOR_BGR2GRAY)
cv2.imshow("Gray", gray)
cv2.waitKey(0)
```

&nbsp;

## Edge Detection

Edge detection is useful for finding boundaries of objects in an image -- for segmentation purposes. Here, we will use a popular Canny algorithm to find edges.

```python
edged = cv2.Canny(gray, 30, 150)
cv2.imshow("Edged", edged)
cv2.waitKey(0)
```
&nbsp;

## Thresholding

Image thresholding is an important intermediary step for image processing pipelines. Thresholding can help us remove lighter or darker regions and countours of images.

```python
thresh = cv2.threshold(gray, 225, 225, cv2.THRESH_BINARY_INV)[1]
cv2.imshow("Tresh", thresh)
cv2.waitKey(0)
```
&nbsp;

## Detecting and drawing contours

```python
# find contours (i.e., outlines) of the foreground objects in the
# thresholded image
cnts = cv2.findContours(thresh.copy(), cv2.RETR_EXTERNAL,
	cv2.CHAIN_APPROX_SIMPLE)
cnts = imutils.grab_contours(cnts)
output = image.copy()

# loop over the contours
for c in cnts:
	# draw each contour on the output image with a 3px thick purple
	# outline, then display the output contours one at a time
	cv2.drawContours(output, [c], -1, (240, 0, 159), 3)
	cv2.imshow("Contours", output)
	cv2.waitKey(0)
```

&nbsp;

## Erosions and Dilations

Erosions and dilations are used for reducing noise in binary images. 

### Erosions

```python
mask = thresh.copy()
mask = cv2.erode(mask, None, iterations=5)
cv2.imshow("Eroded", mask)
cv2.waitKey(0)
```

### Dilations

```python
mask = thresh.copy()
mask = cv2.dilate(mask, None, iterations=5)
cv2.imshow("Dilated", mask)
cv2.waitKey(0)
```