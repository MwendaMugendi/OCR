# OCR For Field Weight Scale Detection
The application used a determinist approach. No AI/ML is involved in the modeling process. 
The image is optionally filtered and then transformed into a monochrome representation with the digits as foreground using some form of thresholding. This image is segmented to find the digits, and each digit is recognized individually.

By default, darker parts of the image comprise the foreground and light parts, the background (i.e., black digits on a white canvas).

## Segmentation
Starting at the left margin, a column containing some foreground pixels is searched, marking the start of the first digit. After that, a column containing only background pixels is searched to find the horizontal stretch of the digit. This process is repeated to find the specified number of digits or until no more digits are found.

The vertical segmentation works similarly, but gaps in digits are allowed because, in some digits, the middle segment is unset.

The result is a sequence of, in general, differently sized rectangles containing one digit each.

This segmentation technique works for a single row of digits only.

Since the rectangles are aligned with the image given to OCR, skewed digits can result in more than one digit inside one rectangle, thus breaking image segmentation and, consequently, digit recognition. Quite often, a decimal point is located so close to a digit that a slight digit skew results in a segmentation error where the decimal point is contained in the same rectangle as a digit. Often this results in correct recognition of the digits, but the decimal point is ignored. Seven-segment displays often use skewed images. Thus, this is a common problem.

## Character Recognition
Every digit found by the segmentation is classified as follows: A vertical scan is started in the center top pixel of the digit to find the three horizontal segments. Any foreground pixel in the upper third is counted as part of the top segment, those in the second third as part of the middle, and those in the last third as part of the bottom segment.

Two horizontal scanlines starting on the left margin of the digit are used to examine the vertical segments. The first starts a quarter of the digit's height from the top, the other from a quarter of the digit's height from the bottom—foreground pixels in the left resp. The right half represents the left resp—right segments.

The recognized segments are then used to identify the displayed digit using a table lookup.

Since the above algorithm cannot recognize the digit one, a digit with a width of less than one-quarter of its height is recognized as a one.

To recognize a decimal point, e.g., of a digital scale, the size of each digit (not recognized as one already) is compared with the maximum digit width and height. If a digit is significantly smaller than that, it is assumed to be a decimal point. The decimal point or thousands of separators count towards the number of digits to recognize.

A method similar to recognizing the digit 'one' is used to recognize a minus sign. If a digit is less high than 1/3 of its width, it is considered a minus sign.

## Practical Use of the OCR application
The OCR application is intended for use with consistent input images. Thus a fixed setup, with a camera pointing at a number display based on seven-segment digit displays, together with consistent lighting, provides the best recognition results. Such a consistent setup allows for finding preprocessing image settings that compensate for both systematic problems (e.g., non-uniform brightness) and variations in the input data. Variations in the input data are caused by random noise and changes in the displayed number, that is, in the amount of light reflected due to changes in set and unset segments.

## Requirements
* opencv
* numpy
* matplotlib

## Setup
```
git clone https://github.com/MwendaMugendi/OCR.git
```

## Acknowledge (Inspiration)
[OCR](https://www.unix-ag.uni-kl.de/~auerswal/ssocr/)