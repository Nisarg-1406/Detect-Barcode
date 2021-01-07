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
  
TO BE CONTINUE .... :)
