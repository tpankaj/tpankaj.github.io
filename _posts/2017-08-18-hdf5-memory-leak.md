---
layout: post
title:  "HDF5 Memory Leak"
date:   2017-08-18 22:00:00 -0600
categories: [ml]
tags: [ml, bdd, hdf5]
---
For the past month or so, my colleague Sauhaarda and I have been trying to solve a strange problem in our training codebase. The longer we train our network, the more RAM it uses, even though there's no in-scope variable that's getting accumulated in our own code. After lots of memory profiling, we figured out that the issue wasn't in our own code, but in the Python library h5py. This is the library that we use to read our data files. So why was it leaking and what was the solution?

## Problem
It turns out that the underlying HDF5 library libhdf5 and the Python bindings h5py don't properly clean up as they accumulate accesses to our data files. I haven't yet tracked down whether this is an actual bug in the library or if it's caused by the way the data files are organized. As a result, as training continues, RAM usage increases more and more until the entire program crashes because it runs out of memory.

## Solutions
I initially went with [pre-processing][bottlenecks-post] to try to solve this problem, which is where that post on pre-processing came from. However, in practice, I was never able to get it to work without having some sort of bug that I couldn't track down. As a result, my colleague came up with a stop-gap measure.

### Pre-processing
While pre-processing did speed up the training as I explained in the post linked above, there was some bug in the code that I couldn't find quickly enough. Somehow, the validation loss was coming out 2 orders of magnitude worse than without pre-processing. Since I couldn't track down the bug, I ended up shelving this method for a time when I don't have a paper deadline coming up.

### Stop-gap Script
For a few days, Sauhaarda and I tried to use the code without pre-processing and just hope that the RAM wouldn't run out, but that quickly became impossible as we started training more and more experiments. Sauhaarda then came up with a stop-gap measure in which the training code runs only for one epoch each time it's called, and a Bash script calls the training program in a loop. This way, the memory usage is reset after each epoch. As long as the RAM doesn't run out before even one epoch can complete, our code runs. This is the method that I'm using to train the experiments [I wrote about yesterday][squeezenet-post].

## Conclusions
Although the memory issue bothers me a lot, I've resigned to using the stop-gap script for now so that research work can continue without being bogged down with the bug. I will eventually fix it after my paper deadline passes and I have some time to spend on the bug. For now, I will go with what my professor told me regarding this problem, "Do what works."

[bottlenecks-post]: {{ site.baseurl }}{% post_url 2017-08-13-gpu-training-bottlenecks %}
[squeezenet-post]: {{ site.baseurl }}{% post_url 2017-08-17-squeezenet-research-status %}
