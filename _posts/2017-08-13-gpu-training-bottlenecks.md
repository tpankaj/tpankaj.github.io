---
layout: post
title:  "GPU Training Bottlenecks"
date:   2017-08-13 14:00:00 -0600
categories: [ml]
tags: [ml, pytorch, python, bdd]
---
Since I started at my research lab, model training has taken about 12 hours per epoch, and models need to be trained for at least 10 epochs. Over the past couple weeks, I started to analyze the code to understand why it was taking so long even though we have powerful GPUs for training. After profiling the code line by line, it turned out that the bottleneck wasn't the actual computation, but simply loading in the training data and processing it on the fly. In this post, I will discuss a couple ways I used to eliminate this bottleneck and push up the GPU utilization to nearly 100%.

## Pre-processing Data
The biggest portion of the data loading bottleneck isn't the I/O, but the processing of the raw data into the form that's inputted to the network. For example, in my lab, we have what we call "runs" of human driving data which we need to break up into time steps for the network. Even if this processing isn't very complicated, it's still done on the CPU while the GPU waits to be fed data, thus slowing down the entire training speed.

I solved this issue by separating the processing and the training. By processing all of the data beforehand into the form the network accepts as input, I eliminated most of the steps required to go from data sitting on the hard disk to training on the GPU. Now, the code only has to load the pre-processed data into memory and queue it up to feed to the GPU. This is significantly faster and shifts the bottleneck from processing the data to loading the data from disk. I needed another technique to eliminate that bottleneck.

## Multiprocessing
Once I was able to eliminate the data processing bottleneck, I used multiprocessing to eliminate the data loading bottleneck.

To speed up data loading, I first looked into multithreading, where I would launch several threads and each one would simultaneously read data from disk and put it in a queue. However, I ran into trouble with this approach because of a Python lock called the Global Interpreter Lock (GIL). From what I understand, the GIL prohibits C extensions of Python from accessing the Python interpreter from more than one thread at once, to prevent and race conditions due to code that isn't thread-safe. This of course ended any hope for multithreading since it would naturally end with the GIL becoming the bottleneck.

It turned out that PyTorch has a built-in data loading class called `torch.utils.data.DataLoader`. This class simply required me to extend `torch.utils.data.Dataset` with an overloaded function to enable random access to an element in my data. Armed with this, PyTorch could handle launching multiple *processes*, rather than threads, that each had their own Python interpreter. This is what finally eliminated all of the data bottlenecks and left the GPU as the primary bottleneck in the system.
