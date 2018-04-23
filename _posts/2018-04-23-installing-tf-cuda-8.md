---
layout: post
title: Installing TensorFlow for CUDA 8
date: '2018-04-23T00:00:00.000-00:00'
author: Andrew Silva
---

TensorFlow is a fast and useful deep learning library, but the documentation is a bit lacking for older versions of CUDA or CuDNN. This is complicated if you want to use something like Keras, which now depends rather heavily on TensorFlow. Fortunately, the process is just as easy, and you can get off the ground in no time. If you're on CUDA 8, you simply need to refer to the older binary of TensorFlow, specifically TF 1.4. You can find the install documentation [here](https://www.tensorflow.org/versions/r1.4/install/), but the short version is to get into whichever environment you are planning on using (virtualenvs highly recommended), and run the install command for your particular environment. See the link above for the list, but if you're using Python 2.7 on Ubuntu then you'd run:
```
$ pip install --upgrade https://storage.googleapis.com/tensorflow/linux/gpu/tensorflow_gpu-1.4.0rc1-cp27-none-linux_x86_64.whl 
```
Fortunately, it's still that simple. If you want to bring Keras with it, that's even easier:
```
$ pip install keras
```
And you're ready to go!

Open up a new terminal and run:
```
$ python
Python 2.7.12 (default, Nov 20 2017, 18:23:56) 
[GCC 5.4.0 20160609] on linux2
Type "help", "copyright", "credits" or "license" for more information.
>>> import tensorflow as tf
>>> import keras
Using TensorFlow backend.
```

As long as nothing blows up, you're good to go with GPU-enabled TensorFlow on CUDA 8! (and Keras, for a bit of an easier time)