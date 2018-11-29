---
layout: post
title:  "Giving CUDA 9 Another Shot"
date:   2017-08-15 22:00:00 -0600
categories: [ml]
tags: [ml, cuda, pytorch]
---
A few days ago I [posted][cuda-9-post] about my test of CUDA 9 and CuDNN 7. As I said at the end of that post, more experimentation was required before making conclusions. Today, I worked on running better benchmarking experiments. This time, I trained 10 epochs of a simple network using the CIFAR10 dataset. I ran the experiment 10 times on CUDA 8/CuDNN 6 and 10 times on CUDA 9/CuDNN 7 and took the best of the 10 for each of the cases. I believe this experiment is better at measuring the performance because the training was more GPU intensive than the MNIST test, so the training time should be more heavily affected by improvements in performance of the GPU.

## Results
I trained the network with CIFAR10 for 10 epochs on an AWS p2.xlarge instance using an Nvidia Tesla K80 GPU. On the instance with CUDA 8/CuDNN 6, it took 110 seconds. On the instance with CUDA 9/CuDNN 7, it took 103 seconds. CUDA 9 was approximately 6% faster.

While this is only one data point, it does appear to show that CUDA 9 can be faster than CUDA 8 when training GPU-intensive models.

[cuda-9-post]: {{ site.baseurl }}{% post_url 2017-08-12-cuda-9-and-cudnn-7-with-pytorch %}
