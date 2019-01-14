---
layout: post
title:  "CUDA 9 and CuDNN 7 with PyTorch"
date:   2017-08-12 17:32:00 -0600
categories: [ml]
tags: [ml, cuda, pytorch, python,]
---
I've been trying to find ways to speed up training of my autonomous driving networks, since the current training time is about 12 hours per epoch. One of my most recent efforts has been trying to upgrade to the newly released CUDA 9.0 RC and CuDNN 7 packages from Nvidia. While these are optimized for Nvidia's new Volta architecture, they claim it speeds up operations on Pascal GPUs like the 1080 Tis my lab has as well.

To get CUDA 9 and CuDNN 7 working with PyTorch, the deep learning framework all of my group's research code is written in, I had to clone [Pull Request #2263][pytorch-2263] from the PyTorch GitHub, which is written by an Nvidia engineer to add CUDA 9 and CuDNN 7 support to PyTorch. However, it turned out there were some other issues with this. To get everything to work, here are the steps I had to follow:

1. Download and install [CUDA 9][cuda-9]
2. Download and install [CuDNN 7][cudnn-7]
3. Download and install [NCCL][nccl]
4. Download and install [Anaconda][anaconda] for Python 3.6
5. Run the following workaround for NCCL:
   {% highlight shell %}
   mkdir ~/nccl
   mkdir ~/nccl/include
   mkdir ~/nccl/lib
   ln -s /usr/include/nccl.h ~/nccl/include/
   ln -s /usr/lib/libnccl.so ~/nccl/lib/
   export CMAKE_PREFIX_PATH="~/nccl":$CMAKE_PREFIX_PATH
   export LIBRARY_PATH="~/nccl/lib/":$LIBRARY_PATH
   {% endhighlight %}
6. Clone the CUDA 9 branch for PyTorch:
   {% highlight shell %}
   git clone https://github.com/csarofeen/pytorch
   cd pytorch
   git checkout cuda9
   {% endhighlight %}
7. Compile PyTorch
   {% highlight shell %}
   export CMAKE_PREFIX_PATH="$(dirname $(which conda))/../"
   conda install numpy pyyaml mkl setuptools cmake gcc cffi
   conda install -c soumith magma-cuda80
   python setup.py install
   {% endhighlight %}

Once I finally got it working, I ran a speed test by running the [PyTorch MNIST example][pytorch-mnist] on an AWS p2.xlarge instance with an Nvidia Tesla K80 using all of the default settings in the example code. Unfortunately, the speed test didn't show CUDA 9 speeding up training in this case. On an instance with CUDA 8 and CuDNN 6, the MNIST example took 88 seconds to train 10 epochs. On an instance where I did the above steps to get CUDA 9 and CuDNN 7 working, it took 89 seconds. More experimentation is required to see if extra performance can be squeezed out of CUDA 9 and CuDNN 7.

[pytorch-2263]: https://github.com/pytorch/pytorch/pull/2263
[cuda-9]: https://developer.nvidia.com/cuda-toolkit/whatsnew
[cudnn-7]: https://developer.nvidia.com/cudnn
[nccl]: https://developer.nvidia.com/nccl
[anaconda]: https://www.continuum.io/downloads
[pytorch-mnist]: https://github.com/pytorch/examples/blob/master/mnist/main.py
