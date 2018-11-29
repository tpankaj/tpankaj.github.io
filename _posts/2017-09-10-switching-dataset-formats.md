---
layout: post
title:  "Switching Dataset Formats"
date:   2017-09-10 22:00:00 -0600
categories: [ml]
tags: [ml, bdd, hdf5]
---
During the last week at Karl's autonomous RC car lab, we made significant progress in fixing the [slow training speed][bottlenecks-post] and [memory leaks][memleaks-post]. Essentially, there were two main problems. One problem was that the internal structure of the dataset within our HDF5 files was much too complicated. The second was that our autonomous driving dataset is simply too large and complex to do any substantial on-the-fly data processing during training.

## Improving HDF5 Layout
To address the first problem, Karl created a new, somewhat simplified layout inside the HDF5 files containing the dataset, which made random access significantly more efficient. Previously, random access within the dataset required multiple dictionary lookups, which are significantly slower than indexing into an array. All of this was flattened out into a single, large, multi-dimensional array that contained all of the data indexed with integers.

## Pre-processing
The second issue had only one answer: pre-processing. We finally created pre-processing code that worked. This required a pre-processing pipeline made up of multiple stages. I won't go through all of them here, but I'll discuss the crucial part.

We made a complete pass through the dataset to make it fully ready for training. In the old system, we had a couple hundred HDF5 files called "runs." Each run is a set of data collected in an uninterrupted timeline. However, the entire timeline wasn't necessarily good for training. When the car was picked up or not moving, data was still recorded in the run, but it would not have been useful to train on this. Instead, we had a system of converting runs into "segments" on the fly. Each segment is a set of data collected in an uninterrupted timeline, and consisting entirely of usable data. In this new system, when we passed through the data during pre-processing, we broke up each run HDF5 file into several segment HDF5 files, each containing a continuous stream of trainable data. Any data that wasn't trainable was discarded. We ended up with clean, compact files that each contained only continuous, usable data.

[bottlenecks-post]: {{ site.baseurl }}{% post_url 2017-08-13-gpu-training-bottlenecks %}
[memleaks-post]: {{ site.baseurl }}{% post_url 2017-08-18-hdf5-memory-leak %}
