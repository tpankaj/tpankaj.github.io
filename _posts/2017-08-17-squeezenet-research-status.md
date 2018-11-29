---
layout: post
title:  "SqueezeNet Research Status"
date:   2017-08-17 22:00:00 -0600
categories: [ml]
tags: [ml, bdd, squeezenet, z2color, lstm, gru, alexnet]
---
As you may know from my first blog post, my research work is on autonomous driving with SqueezeNet. Despite the post a few days ago about speeding up the model training, I haven't had much success applying the pre-processing and multiprocessing approached to this problem. It turns out that the HDF5 data storage format that we're using at my lab to store our training data doesn't easily support concurrent access from multiple processes. For now, since I need to get some models trained, I'm training them using our old training code that takes 12 hours per epoch.

## Experiments
I'm currently running 5 experiments: 1 baseline, 2 to explore the ability of SqueezeNet to learn to drive and 2 on modified versions of SqueezeNet that have memory.

### Z2Color
Z2Color is the baseline model that Karl, my professor, has been researching for over a year. The network has done all of our autonomous driving, but Karl hadn't been able to find a better network when he first hired me. Now, of course, I'm working on creating many networks that are better.

There are two main problem with this network. The first is that the maximum receptive field size (the effective vision size of the network) is smaller than the image, so it requires fully connected layers at the end of the network that are not spacially invariant. The other big issue is that the network has no memory, so if the car gets stuck in a position where the camera can't help it get out, it has no memory of where it was before.

### SqueezeNet
SqueezeNet is my primary means of fixing the first problem with Z2Color. The receptive field size is bigger than the input camera image size, so it's able to see everything at once even though there's no fully connected layers. This saves on training computation and allows me to use that computation in other parts of the network.

### AlexNet
AlexNet is what inspired the creators of SqueezeNet. They wanted to have AlexNet-level performance on image classification with a much more efficient network. Of course, I'm not doing image classification, so I'm training both SqueezeNet and AlexNet to drive so that I can compare them against each other.

### SqueezeNet with LSTM
LSTMs, a form of neural network memory, should help SqueezeNet remember what its past actions were when it's trying to make a decision for future actions. If this experiment ends up performing very well, this will solve problem &#35;2 with Z2Color.

### SqueezeNet with GRU
GRUs are a newer, simpler form of neural network memory that have been shown to perform as well as LSTMs. Thus, I figured I should try them as well, since I'm inexperienced with LSTMs and GRUs and don't know which one will work better here.

## Conclusion
Overall, I have several exciting experiments running that will greatly improve our understanding of SqueezeNet. I will post an update on these experiments when I start seeing some results. Unfortunately, the training is very slow so it may take a week or so.
