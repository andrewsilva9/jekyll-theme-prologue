---
layout: post
title: Installing Caffe on macOS
date: '2017-01-18T21:58:00.000-05:00'
author: Andrew Silva
tags: 
modified_time: '2017-01-19T22:32:26.195-05:00'
blogger_id: tag:blogger.com,1999:blog-8622339093465720931.post-1756440546390141269
blogger_orig_url: http://www.andrew-silva.com/2017/01/installing-caffe-on-sierra-with-homebrew.html
---

I recently had to install Caffe on a new MacBook Pro, and it was something of an ordeal. I decided to put a short guide up here in the hope that it will make life easier for anyone else trying to get up and running with Caffe but being stuck with some outdated docs.

So to get started, the docs are a good reference. You can find them here http://caffe.berkeleyvision.org/install_osx.html and they will require you have homebrew and CUDA. I went ahead and used Cuda 8 (as opposed to the recommended 7). Unfortunately, MacBooks don't have NVIDIA GPUs to take advantage of, but luckily Caffe can be run on CPUs alone.

Once you've got the dependencies from the caffe docs, go over to [their git repo][caffe-git] and clone it somewhere you'd like to keep it.

Once you have that, the Caffe docs are helpful once again for getting the makefile setup ( http://caffe.berkeleyvision.org/installation.html#compilation ). Using homebrew instead of Anaconda, you have to set the `python_include` and `python_lib` variables to the homebrew paths. For me, this means:

``` PYTHON_INCLUDE := /usr/local/lib/python2.7/site-packages/numpy/core/include/ /usr/local/Cellar/python/2.7.12_2/Frameworks/Python.framework/Versions/2.7/include/python2.
PYTHON_LIB := /usr/local/Cellar/python/2.7.12_2/Frameworks/Python.framework/Versions/2.7/lib/  
```
Next up, find the `level_db := 0` and uncomment it, and do the same for `cpu_only`.

Almost there! Open up a Terminal window and use homebrew to uninstall / reinstall openblas (I had to do this because the version Sierra already had was not working properly). Now finally, open up the Makefile.config and set:
`BLAS := open`

There, the Makefile.config changes are finished!

Now, when you run:
```$ make all
$ make test
$ make runtest
```
You might run into a problem that looks similar to this: `/build_release/caffe/tools caffe no such file or directory:` To fix this, I had to create a txt file with the following contents:
```caffe
compute_image_mean
convert_imageset
device_query
dump_network
extract_features
finetune_net
net_speed_benchmark
test_net
train_net
upgrade_net_proto_binary
upgrade_net_proto_text
```
Then, in a new Terminal window: `$ for req in $(cat all_execs.txt); do ln -s $req.bin $req; done`

And you should be able to re-make everything and have it work! It's a pain, but having Caffe for local development is worth it!

[caffe-git]: https://github.com/BVLC/caffe