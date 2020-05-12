# Yolov3--Custom-Detection

# Introduction

This directory has been forked from PyTorch YOLOv3 software developed by Ultralytics LLC. This project was done under the course cse-40536-60536-spring for University of Notre Dame. The course syllabus is- http://adamczajka.pl/files/Computer-Vision-2-syllabus.pdf. Amazon Robotics was the industrial partner for the course and they supported it.
The goal was to identify (using only single monocular optical camera) a wide variety of known and unknown objects when densely packed inside plastic totes. These objects could present themselves in a variety of orientations while inside the tote and additionally have various levels of occlusions presented to the camera due to other objects resting on top.

<p align="center"> <img src="image1.JPG" width="500" height="400"/> </p>

From this tote, I had to detect 10 objects/ classes using only 90 images in my training set. This code customization works for detectio<p align="center"> <img src="image1.JPG" width="500" height="400"/> </p>n of small objects using really few training image set, though it would definitely perform better with a larger training set.

# Requirements

Python 3.7 or later with all `pip install -U -r requirements.txt` packages including `torch >= 1.5`.

# Train
**1. Data Labeling** 
I used Labelme tool to label all my images and then exported it to darknet format. The data needs to be stored with images and labels in separate parallel folders, and one label file per image (if no objects in image, no label file is required). The label file specifications are:

One row per object
Each row is class x_center y_center width height format.
Box coordinates must be in normalized xywh format (from 0 - 1). If your boxes are in pixels, divide x_center and width by image width, and y_center and height by image height.
Class numbers are zero-indexed (start from 0).
Each image's label file must be locatable by simply replacing /images/*.jpg with /labels/*.txt in its pathname. An example image and label pair would be:
`
../data/images/IMG_5201.jpg  # image
../data/labels/IMG_5201.txt  # label
`
An example label file with 5 persons (all class 0):
<p align="center"> <img src="label.JPG" width="500" height="400"/> </p>

**2. Create train and test *.txt files.**
Here we create data/train.txt and data/val.txt which contains the path to all the training images and the validation images respectively. Each row contains a path to an image, and remember one label must also exist in a corresponding /labels folder for each image containing objects.
<p align="center"> <img src="image2.JPG" width="500" height="400"/> </p>

**3. Create new *.names file** 
Here we list the class names in our dataset. I made the labels for my 10 objects. Classes are zero indexed, so ping pong is class 0, cookie tin is class 1, etc.
<p align="center"> <img src="image3.JPG" width="500" height="400"/> </p>

**4. Create new *.data file**
This file has the class count, paths to train and validation datasets and with the path to your *.names file.
<p align="center"> <img src="image4.JPG" width="500" height="400"/> </p>

**5. Update yolov3.cfg**
By default each YOLO layer has 255 outputs: 85 values per anchor [4 box coordinates + 1 object confidence + 80 class confidences], times 3 anchors. Update the settings to filters=[5 + n] * 3 and classes=n, where n is your class count. This modification should be made in all 3 YOLO layers.

**6. Train** 
Run 
`python3 train.py --cfg yolov3.cfg --data data/yolo.data`
to train using your custom *.data and *.cfg. I used the darknet53.conv.74 weights to train my model, you can download the sams from the download_yolov3_weights.sh file. You can instead train from scratch with --weights '', or from any other weights or backbone of your choice, as long as it corresponds to your *.cfg.

# Visualization

Run from utils 
`import utils; 
utils.plot_results()` 
to see your training losses and performance metrics vs epoch. If you don't see acceptable performance, try hyperparameter tuning and re-training. Multiple results.txt files are overlaid automatically to compare performance. After completing the training, a results.png file gets saved if you use the above training command, you can visualize your results using that.
<p align="center"> <img src="image5.JPG" width="500" height="400"/> </p>

# Detection

Run inference with your trained model by copying all the test images to the test_images folder in your data folder and running
`python3 detect.py --weights weights/best.pt --source data/test_images`

<p align="center"> <img src="image6.JPG" width="500" height="400"/> </p>
