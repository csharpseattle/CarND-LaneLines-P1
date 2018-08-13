# **Finding Lane Lines on the Road**

submitted by Chris Sharp

---

**Finding Lane Lines on the Road**

The goals / steps of this project are the following:
* Make a pipeline that finds lane lines on the road
* Reflect on your work in a written report


[//]: # (Image References)

[image1]: ./examples/challenge-bridge.png "Lane lines thrown off by change of pavement color"

---

### Reflection

The goal of the project was to detect and annotate lane lines on a road.  The pipeline I wrote to carry out this task took a color image, converted it to grayscale, performed a histogram equalization, gaussian blur, and used canny edge detection.  A region of interest was then masked out of the canny edges and a hough transform was used to detect the lines in this region of interest.  The lines from the hough transform were then averaged and extrapolated to extend to the height of the region of interest and drawn back over the original image.

The complexity of the pipeline posed a challenge where changing parameters in one step of the pipeline affected steps further down the line.  To facilitate the finding of the correct parameters of gaussian kernel size, canny threshold values, hough threshold, mininum line length values, etc, I used openCV trackbars to show the effects to a frame as I changed values.

The pipeline described above is in the process_image() function.  However, most of the work is done in the draw_lines() function.  This function takes the output from the hough transform and averages and extrapolates the lines before rendering them onto the original image.  The steps to accomplish this are:
* Split the hough lines into left and right lane lines.  A simple split right downt the center of the image worked fine here.
* On each side I used openCV's fitLine() method to perform a least-squares distance to fit all points to a single line.  From this I calculate the slope and y-intercept.
* If the slope was within the tolerance of the average slope from the previous 10 frames I included the line in the running average.  If not the line was discarded.
* The average slope and y-intercept was then used to render the line on the original image.


### 2. Shortcomings

The pipeline worked well overall but certain situations could throw off the line detection.  This is most easily seen in the optional challenge video when the car passes thorough the shadow of a tree and the pavement color changes while driving across the bridge.

![image1]

Curves were another problematic situation.  Since the rendered lines were straight extending the lane lines to the furthest point in the curve would result in lines that didn't quite match the lines in the video.

### 3. Possible Improvements

Further testing and tweaking of the parameters may result in better detection in the case of shadows and changes in pavement color.  Further investigation into histogram equalization could also be a solution here.

To address the problem with curved lanes one might take the approach of multiple regions of interest.  Lane lines could be segmented not only into left and right but also into close, middle, and far distances.
