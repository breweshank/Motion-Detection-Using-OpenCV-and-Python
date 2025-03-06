# Motion Detection Using OpenCV and Python

## Overview
This project implements a simple motion detection system using OpenCV in Python. The script captures video from a webcam, processes the frames to detect movement, and highlights moving objects with bounding rectangles.

## Features
- Captures live video from a webcam
- Converts frames to grayscale and applies Gaussian blur for noise reduction
- Computes the absolute difference between frames to detect motion
- Uses contour detection to identify moving objects
- Draws bounding boxes around detected movement
- Displays real-time motion detection feed
- Stops execution when the user presses 'q'

## Prerequisites
Ensure you have Python installed along with the necessary dependencies:

```bash
pip install opencv-python imutils
```

## How It Works
1. The script initializes the webcam and captures frames.
2. It converts each frame to grayscale and applies Gaussian blur to reduce noise.
3. The first frame is stored as a reference.
4. The absolute difference between the current frame and the first frame is computed.
5. A thresholding method is applied to highlight changes in the scene.
6. Contours are detected, and if the detected area is greater than a set threshold, a bounding box is drawn.
7. The feed updates continuously until the user presses 'q' to exit.

## Code Breakdown
```python
import imutils
import cv2
import time
```
- Imports required libraries for image processing and time handling.

```python
cam = cv2.VideoCapture(0)
time.sleep(1)
firstFrame = None
area = 500
```
- Initializes the webcam and waits for 1 second to allow it to adjust.
- Defines `firstFrame` to store the reference frame.
- Sets a motion detection threshold (`area` = 500 pixels).

```python
while True:
    _, img = cam.read()
    text = "Normal"
    img = imutils.resize(img, width=500)
    grayImg = cv2.cvtColor(img, cv2.COLOR_BGR2GRAY)
    gaussianImg = cv2.GaussianBlur(grayImg, (21, 21), 0)
```
- Captures a frame from the webcam.
- Resizes the frame for faster processing.
- Converts the frame to grayscale and applies Gaussian blur to smoothen it.

```python
    if firstFrame is None:
        firstFrame = gaussianImg
        continue
```
- If `firstFrame` is not initialized, set it as the first frame for motion comparison.

```python
    imgDiff = cv2.absdiff(firstFrame, gaussianImg)
    threshImg = cv2.threshold(imgDiff, 25, 255, cv2.THRESH_BINARY)[1]
    threshImg = cv2.dilate(threshImg, None, iterations=2)
```
- Computes the difference between the current frame and the reference frame.
- Applies thresholding and dilation to highlight the moving regions.

```python
    cnts = cv2.findContours(threshImg.copy(), cv2.RETR_EXTERNAL, cv2.CHAIN_APPROX_SIMPLE)
    cnts = imutils.grab_contours(cnts)
```
- Detects contours from the thresholded image.

```python
    for c in cnts:
        if cv2.contourArea(c) < area:
            continue
        (x, y, w, h) = cv2.boundingRect(c)
        cv2.rectangle(img, (x, y), (x + w, y + h), (0, 255, 0), 2)
        text = "Moving object"
        print(text)
```
- Iterates through the detected contours and draws bounding boxes if the contour area is larger than 500 pixels.
- Updates the status to "Moving object".

```python
    cv2.putText(img, text, (10, 20), cv2.FONT_HERSHEY_SIMPLEX, 0.5, (0, 0, 255), 2)
    cv2.imshow("camera feed", img)
```
- Displays the detected motion status on the video feed.

```python
    key = cv2.waitKey(1) & 0xFF
    if key == ord("q"):
        break
```
- Allows the user to exit the program by pressing 'q'.

```python
cam.release()
cv2.destroyAllWindows()
```
- Releases the webcam and closes all OpenCV windows.

## Usage
Run the script using:
```bash
python motion_detection.py
```
Press **'q'** to exit the program.

## Future Enhancements
- Implement background updating for better motion tracking.
- Save detected motion as images or videos.
- Send alerts when motion is detected.
- Improve accuracy using deep learning models.

## License
This project is open-source and can be modified as needed.

