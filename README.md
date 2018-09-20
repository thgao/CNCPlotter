# CNCPlotter
This project is a stroke-based printer with image processing. The printer was built with an Arduino, with movements controlled by stepper motors, and could take any input image. The device was designed to be able to move the 3 dimensions, with a 'canvas' being able to be layed on the x-y plane and a pen (to draw out the image) held parrallel to the y-z plane. These two planes were able to move, controlled by the stepper motor, to allow for all two dimensional movement. In addition the pen was able to move up and down by using a servo, this allowing for lines to be discontinuous. 

Here is the plotter in action:

![alt text](https://github.com/thgao/CNCPlotter/blob/master/src/Image%20Processing%20Output%20Images/goosedrawing.gif)



# Image Processing 
This project used OpenCV for it's image processing. First, the image is resized, using **imgResize** so that the pixel dimensions line up the movement range of the stepper motors. The inputted image is then smoothed out with **imgBlurred** to reduce noise.

Once resized, the image is processed two other times using **imgGreyscale** and **ImgBlackWhite**. The coloured, greyscale, and monochrome processed images are then all processed again with **imgOutlined** to countour them and finally a weighted average is taken to get the maximum outline detail.

For example, here is a sample image:

![alt text](https://raw.githubusercontent.com/thgao/CNCPlotter/master/src/Image%20Processing%20Output%20Images/goose.png)<br>

![alt text](https://raw.githubusercontent.com/thgao/CNCPlotter/master/src/Image%20Processing%20Output%20Images/img_resize.png)
![alt text](https://raw.githubusercontent.com/thgao/CNCPlotter/master/src/Image%20Processing%20Output%20Images/img_bw.png)
![alt text](https://raw.githubusercontent.com/thgao/CNCPlotter/master/src/Image%20Processing%20Output%20Images/img_outline.png)
- image after resizing and blurring
- image after black and white processing
- image after all three processed images are combined as a weighted average

with it's output on paper:

![alt text](https://raw.githubusercontent.com/thgao/CNCPlotter/master/src/Image%20Processing%20Output%20Images/CNC%20Goose%20Drawing.jpg)



# Pathway Detection
The final outlined image is converted to coordinates by detecting black pixels as an ordered pair. This process is done chronologically and generates a list of coordinates. Developing this printer required a pathway detection functionality to be created so that the image is drawn out in a continuous line, unlike you everyday dot printer.

The approach was to iterate through all the pairs and find adjacent coordinates where in existence. The only case were there would not be an adjacent pixel is when the line stops becoming continuous. 

This was implemented by creating two primary functions, checkAroundCurrentPoint() and checkIfBlack(). All the coordinates were looped through and called checkAroundCurrentPoint() to recusively search for adjacent pixels; with the helper function checkIfBlack() to validate the adjacent locations to pixels. The list is reordered in the desired to be sequentially drawn and sent off to the Arduino.

# Communication to Arduino
All of the image processing and pathway detection is done in Python. To send this data to the Arduino, the coordinates are passed serially. There were several difficulties along the way ensuring the data was being passed and read in fully before proceeding to the next instruction, so the serial communication is implemented with an "@GetNext" string as a flag. Once the Python code detects this flag, it uses **ser.write()** to send the data to the Arduino. The Arduino reads in the serial data using **Serial.available()**.

