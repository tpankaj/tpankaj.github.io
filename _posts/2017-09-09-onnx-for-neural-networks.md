---
layout: post
title:  "ONNX for Neural Networks"
date:   2017-09-09 22:00:00 -0600
categories: [ml]
tags: [ml, onnx]
---
Just in the last few days, I've been seeing a lot about a new open source format for neural network models called [Open Neural Network Exchange][onnx], or ONNX. I haven't yet gotten a chance to try it out myself, but it looks very promising.

ONNX appears to be a way to save neural network models from multiple deep learning frameworks in a universal format that is cross-compatible. If this turns out to really work, then it would be a major advancement, as currently models made in one deep learning framework are very hard to translate to another.

I can think of several cases in the projects I've already worked on where having a format like ONNX would have been immensely helpful. The way I'd use it, it would allow me to take advantage of the pros of each deep learning framework I work in, while avoiding the cons by loading up my weights file in a different framework when I encounter a framework-specific issue.

I hope that ONNX ends up being integrated into every major deep learning framework. Their GitHub page claims that Caffe2, PyTorch, and Cognitive Toolkit will all support ONNX. However, in order for it to take off, I'd expect that TensorFlow/Keras support would be absolutely crucial. This will be an interesting project to watch. When I have some time to try out ONNX, I may test
to see if I can transfer some simple networks between PyTorch and Caffe2.

[onnx]: https://github.com/onnx/onnx
