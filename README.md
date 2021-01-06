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
  
  TO BE CONTINUE --------:)
  
