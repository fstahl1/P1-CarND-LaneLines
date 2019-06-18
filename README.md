# **Finding Lane Lines on the Road** 


## Writeup template

---

### Results

Lanes in the two provided clips are reliably detected.

![solidWhiteRight_gif](./test_videos_output/solidWhiteRight.gif)

![solidYellowLeft_gif](./test_videos_output/solidYellowLeft.gif)


---


### Pipeline overview 

My pipeline consisted of the following steps:

* Step 1 - Load color image
* Step 2 - Convert image into grayscale image
* Step 3 - Apply blur filter
* Step 4 - Apply canny edge detection
* Step 5 - Select region of interest (roi)
* Step 6 - Find lines using hough transform
* Step 7 - Separate lines into left and right, average and extrapolate them


### Pipeline details

#### Step 1 - Load color image

As a first step an image was read with the `mpimg.imread()` function.

![Original image](./test_images/solidWhiteRight.jpg)


#### Step 2 - Convert image into grayscale image

The origignal image is converted into grayscale with the `cv2.cvtColor(img, cv2.COLOR_RGB2GRAY)` function.

![Grayscale image](./test_images_output/solidWhiteRight_gray.jpg)


#### Step 3 - Apply blur filter

The image is blurred with the `cv2.GaussianBlur(img, (kernel_size, kernel_size), 0)` function to remove noise for a better canny edge performance. 

![Blurred image](./test_images_output/solidWhiteRight_gray_blr.jpg)


#### Step 4 - Apply canny edge detection

For canny edge detection the `cv2.Canny(img, low_threshold, high_threshold)` function was applied. The threshold values were determined following the recommendation of the lecture (low:high threshold ratio of 1:2 or 1:3 in range of tens to hundreds).

![Canny edge detection](./test_images_output/solidWhiteRight_canny.jpg)


#### Step 5 - Select region of interest

The region of interest was determined by the shape of the lanes in the images. The function `cv2.fillPoly(mask, vertices, ignore_mask_color)` sets the color in the region of interest to white, whereafter the mask is used by `cv2.bitwise_and(img, mask)` to keep only the specified region of the image.

![Region of interest](./test_images_output/solidWhiteRight_roi.jpg)
![Region of interest in canny edge image](./test_images_output/solidWhiteRight_canny_roi.jpg)


#### Step 6 - Find lines using hough transform

For finding lines, the probabilistic hough transform function `cv2.HoughLinesP(img, rho, theta, threshold, np.array([]), minLineLength=min_line_len, maxLineGap=max_line_gap)` was used, since it returns the extremes of the detected lines instead of $\theta$ and $\rho$. The relevant parameters were set by set by trial and error. 

![Hough transformed image](./test_images_output/solidWhiteRight_hough.jpg)

#### Step 7 - Separate lines into left and right, average and extrapolate them

In order to draw one single line on the left and one on the right lane, the function `avg_extrapol(lines)` was created. It works as follows:
* Left and right lines are separated by their slope
* Slopes and offsets are averaged respectively
* The extremes of the two resulting lines are returned

![Final image](./test_images_output/solidWhiteRight_final_image.jpg)


### 2. Identify potential shortcomings with your current pipeline

* Only straight lines are detected with the applied hough transform -> curved lanes cannot be detected as curves
* Only intensity is considered using grayscale images -> Problem detecting colored lines under challenging light conditions
* Lines are jittering (not smooth over time)


### 3. Suggest possible improvements to your pipeline

* Use of higher degree polynomials to fit the lanes for curve detection
* A different color space could be used for better performance (e.g. better detection of yellow lines)
* Use of lane history for smoother lanes and avoidance of frames without detected lines
* Parameter tuning could be improved (with a GUI for example)


