#**Finding Lane Lines on the Road**

## Project Writeup

---

**Finding Lane Lines on the Road**

The goals / steps of this project are the following:
* Make a pipeline that finds lane lines on the road
* Reflect on your work in a written report


[//]: # (Image References)

[original]: ./test_images/solidWhiteCurve.jpg
[annotated]: ./line-segments-example.jpg "Annotated Segments"
[extrapolated]: ./test_images/solidWhiteCurve_processed.jpg

---

### Reflection

###1. Pipeline description:

My lane-detection pipeline takes in an RGB image of the road and produces an RGB
image with the lane lines annotated.

![Road with lane-lines annotated][annotated]

 The following are the image-processing
steps that take place within the pipeline:

####a. Grayscale conversion:
We want to work on a single plane of intensities so that we can
perform edge-detection easily. So we convert the image from the
RGB plane to grayscale.

####b. Gaussian blurring:
Edge-detection works better if the noise in the image is cut
down. Gaussian blurring smoothens the difference in intensities
between neighbouring pixels, and thus helps in edge-detection.

####c. Canny Edge Detection:
In order to detect lane-lines, we rely on the differences in
intensities between the lane pixels and the road pixels.
Canny Edge detection is an algorithm that detects the variance
in pixel intensities and helps single out edges in the image.

####d. Region masking:
We're interested in the lane-lines only, and not the rest of
the image. We define a polygonal area that is overlayed on the
edge-detected image, thus producing an image that contains
highlighted edges only in our area of interest.

####e. Hough Lines detection:
We take the Hough transform for the image produced in the last
step to detect the position and orientation of lane-lines. This
transform requires tuning certain parameters like:
- `rho`: distance of line from center
- `theta`: angle of perpendicular from center to the line
- `threshold`: minimum number of lines that must meet at a point
- `max_line_gap`: Maximum gap in pixels between two segments
	after which they are considered part of different lines
- `min_line_length`: Minimum length in pixels for detected lines

After tuning these parameters to achieve desired results, the
values I arrived at were (using images in the test folder):
- `rho`: 1
- `theta`: pi/180 radians
- `threshold`: 10
- `max_line_gap`: 200
- `min_line_length`: 80

####f. Superimposition:
The image from the previous step contains only lane-lines
highlighted in red. This image is then superimposed on the
original image to get an annotated version of the lane-lines.

###2. Drawing lines after Hough Transform:

In order to draw a single line on the left and right lanes, I modified the
`draw_lines()` function by writing a helper to segragate lines into the left
and the right lanes, by calculating slopes.

The next step was to extrapolate
the lines on each of the lanes into two lines that spanned the top and
bottom of the road. This was accomplished by averaging out the positions of
the lines to produce two lines - each representing the left and right lanes.
Then, using the line equation

	y = mx + c

the x-coordinates for the top and bottom were calculated.

![Road with lane-lanes extrapolated][extrapolated]
###2. Identify potential shortcomings with your current pipeline

The pipeline does not account for shadows on the road. So stray shadows that
lie within the region of interest will show false-positives.

The pipeline's extrapolation also won't work correctly for images of varying
sizes since the top and bottom of the road are hard-wired in code.


###3. Suggest possible improvements to your pipeline

A possible improvement would be to perform color-selection as part of the
pipeline - the lane lines are majorly white and yellow, so filtering out
the rest of the colors will reduce noise, for example, shadows.
