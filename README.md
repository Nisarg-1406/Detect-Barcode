# Detect-Barcode
To Detect the barcode on the image

## Table of Contents - 
* [About Project](#about-project)
* [Detailed Explanation about Project](#detailed-explanation-about-project)
* [About Me](#about-me)

## About Project
This project aims for the detecting the barcode and scan the barcode and detect the object for the barcode detected. The code is executed in python language in Jupyter Notebook. 

## Detailed Explanation about Project
* Would be reading/loading the image and would be converting it into gray scale image. 
  ```
  image = cv2.imread(args["image"])
  gray = cv2.cvtColor(image, cv2.COLOR_BGR2GRAY)
  ```
  
* Use the OpenCV function Sobel() to calculate the derivatives from an image and Use the OpenCV function Scharr() to calculate a more accurate derivative for a kernel of size 3⋅3. Scharr is also used to detect the second derivatives of an image in horizontal and vertical directions. So here we construct the gradient magnitude representation of the grayscale image in the horizontal and vertical directions with ksize = -1, where ksize is aperture parameter for the Sobel operator.
  ```
  ddepth = cv2.cv.CV_32F if imutils.is_cv2() else cv2.CV_32F
  gradX = cv2.Sobel(gray, ddepth=ddepth, dx=1, dy=0, ksize=-1)
  gradY = cv2.Sobel(gray, ddepth=ddepth, dx=0, dy=1, ksize=-1)
  ```
  
* Then, we subtract the y-gradient of the Scharr operator from the x-gradient of the Scharr operator. By performing this subtraction we are left with regions of the image that have high horizontal gradients and low vertical gradients.
  ```
  gradient = cv2.subtract(gradX, gradY)
  gradient = cv2.convertScaleAbs(gradient)
  ```
  
* We will apply an average blur to the gradient image using a 9 x 9 kernel. This will help smooth out high frequency noise in the gradient representation of the image. We’ll then threshold the blurred image. Any pixel in the gradient image that is not greater than 225 is set to 0 (black). Otherwise, the pixel is set to 255 (white).
  ```
  blurred = cv2.blur(gradient, (9, 9))
  (_, thresh) = cv2.threshold(blurred, 225, 255, cv2.THRESH_BINARY)
  ```

* But there can be a case that there are gaps between the vertical bars of the barcode. In order to close these gaps and make it easier for our algorithm to detect the “blob”-like region of the barcode, we’ll need to perform some basic morphological operations. We’ll start by constructing a rectangular kernel using the `cv2.getStructuringElement`. This kernel has a width that is larger than the height, thus allowing us to close the gaps between vertical stripes of the barcode. We then perform our morphological operation by applying our kernel to our thresholded image, thus attempting to close the the gaps between the bars.
  ```
  kernel = cv2.getStructuringElement(cv2.MORPH_RECT, (21, 7))  ------> Making rectangular kernel with width > height
  closed = cv2.morphologyEx(thresh, cv2.MORPH_CLOSE, kernel)   ------> morphological operation
  ```

* But there might be the case that there are small blobs in the image that are not part of the actual barcode. So, how can we remove blobs - we are doing here is performing 4 iterations of erosions, followed by 4 iterations of dilations. Dilation adds pixels to the boundaries of objects in an image, while erosion removes pixels on object boundaries. An erosion will “erode” the white pixels in the image, thus removing the small blobs, whereas a dilation will “dilate” the remaining white pixels and grow the white regions back out. After our series of erosions and dilations you can see that the small blobs have been successfully removed and we would be only left with the barcode region. 
  ```
  closed = cv2.erode(closed, None, iterations = 4)
  closed = cv2.dilate(closed, None, iterations = 4)
  ```
  
* Contours can be explained simply as a curve joining all the continuous points (along the boundary), having same color or intensity. The contours are a useful tool for shape analysis and object detection and recognition. We use **RETR_EXTERNAL** (Contour Retrieval Mode) because it returns only the extreme outer flags i.e only the outer boundary. Now coming to `cv2.CHAIN_APPROX_SIMPLE`, If we pass `cv2.CHAIN_APPROX_NONE` - all the boundary points are stored, but we only need the endpoints, so it removes all redundant points and compresses the contour, thereby saving memory. Then we use that `imutils.grab_contours(cnts)` that grabs the appropriate tuple value based on whether we are using OpenCV 2.4, 3, or 4. Then with the help of `sorted(cnts, key = cv2.contourArea, reverse = True)[0]` that find the largest contour in the image, which if we have done our image processing steps correctly, should correspond to the barcoded region as we are sorting in reversing order and getting the first value and storing in var c. 
  ```
  cnts = cv2.findContours(closed.copy(), cv2.RETR_EXTERNAL, cv2.CHAIN_APPROX_SIMPLE)
  cnts = imutils.grab_contours(cnts)
  c = sorted(cnts, key = cv2.contourArea, reverse = True)[0]
  ```
  
* To get the rotated bounding rectangle of contour we use `rect = cv2.minAreaRect(c)`. Now to create the box around that rectangle, if we use OpenCV 2.4, then use `cv2.cv.BoxPoints`, else if use OpenCV 3, then use `cv2.boxPoints`, so overall we write it as - `box = cv2.cv.BoxPoints(rect) if imutils.is_cv2() else cv2.boxPoints(rect)`. 
    ```
    rect = cv2.minAreaRect(c)
    box = cv2.cv.BoxPoints(rect) if imutils.is_cv2() else cv2.boxPoints(rect)
    box = np.int0(box)  --------> np.int0 is same as np.int64.
    ```

* To draw the counters - `cv2.drawContours` function is used. It can also be used to draw any shape provided you have its boundary points. Its first argument is source image, second argument is the contours which should be passed as a Python list, third argument is index of contours (useful when drawing individual contour. To draw all contours, pass -1) and remaining arguments are color, thickness etc.
  ```
  cv2.drawContours(image, [box], -1, (0, 255, 0), 3)
  cv2.imshow("Image", image)
  cv2.waitKey(0)
  ```
  
## About me
**IF YOU LIKED MY WORK, PLEASE HIT THE STAR BUTTON, AND IF POSSIBLE DO PLEASE SHARE, SO THAT COMMUNITY CAN GET BENIFIT OUT OF IT BEACUSE I AM EXLPANING EACH AND EVERY LINE OF CODE FOR EACH AND EVERY PROJECT OF MINE.**

I am Solving **Algorithms and Data Structure Problems from more than 210 Days Without any off-Day and have solved more than 400 Questions on various topics**.
You can Visit my Profile of LeetCode here - **https://leetcode.com/Nisarg1406/**

I am good at Algorithms and Data structure and I have good Projects in Machine learning and Deep Learning (Computer Vision). **I am and would be posting the detialed explantion of each and every project working**. I am activily looking for an Internhip in **Software development enginering (SDE) Domain and Machine learning Domain**.

You can contact me on my mail ID - nisarg.mehta18@vit.edu OR nisargmehta2000@gmail.com and even Contact me on LinkedIn - https://www.linkedin.com/in/nisarg-mehta-4a378a185/
