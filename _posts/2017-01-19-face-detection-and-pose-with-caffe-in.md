---
layout: post
title: Face Detection with Caffe in Python
date: '2017-01-19T22:31:00.002-05:00'
author: Andrew Silva
---

I've been working on face detection lately, and Caffe has been tremendously helpful (see my last post for how that installation went...) and provided me with the ability to build on the incredible work available here: https://github.com/kpzhang93/MTCNN_face_detection_alignment

My version of this with Python is adapted from the [Python version of MTCNN][py-mtcnn]. You can find mine on [my GitHub][my-mtcnn].

So what exactly is in that repo? Well for starters there is the model folder, which contains the saved models that the Python files load in and use. Then there is the `demo.py`file, which had function for initializing a model based on the saved parameters, draw functionality to display images and bounding boxes, as well as some helpers to clean up the results that come back from the convolutional neural networks.

There is a `faces` directory with some sample images I used for debugging and testing, which helps to demonstrate not only the success of the models but also their speed as it relates to the size of the image and the number of faces in each image. If you run `demo.py`, you'll be able to tap '0' to move forward through the directory of images and you'll see bounding boxes for every face the model finds.

The `face_detector.py` file basically allows for the creation of a face detector object, so you can create one and continually give it new images from a different file (if you want to use this model as part of a larger system, for example). There's an example of this in the webcam_runner.py file.

The `webcam_runner.py` file will demonstrate this pretty clearly. Using OpenCV to pull live video from the webcam, the program will take each frame, run it through the face detector, and then display with live video (admittedly at a slower framerate) with faces outlined. It also currently does rudimentary pose estimation by using the position of noses in the bounding boxes (though I will hopefully be improving that soon).

There are a few things you'll need to do to get it running on your own machine. First, you need Caffe (see my prior post if you're on Mac, or just follow the Caffe docs otherwise). You will also need numpy and opencv (I'm using 2.4.13, courtesy of homebrew). Inside of the `demo.py` and `face_detector.py` files, search for `caffe_model_path` and change the values to wherever you have the project on your machine. You also need to change the`caffe_root` values to wherever you have Caffe installed.

Once you've got all that sorted, you should be up and running! Things in the files are pretty clear, but if you have any questions then please reach out and I'll try to help out!

[py-mtcnn]: https://github.com/DuinoDu/mtcnn
[my-mtcnn]: https://github.com/andrewsilva9/face_detection_cnn