---
layout: post
title:  "Docker for Deep Learning"
date:   2017-08-16 22:00:00 -0600
categories: [ml]
tags: [ml, docker, pytorch, keras]
---
Recently, I've started using [Docker][docker] to manage the development environment required for deep learning. Docker allows me to decide exactly which versions of which software are installed, and how. It's very useful to be able to script the development environment and easily switch between different environments. For example, in my lab, the codebase is currently in Python 2, but we're about to change it to Python 3. This change would normally require changing over the host machine's environment to Python 3, but using Docker, I can just update the Dockerfile to say I want Python 3. Allow me to explain by example.

## Using a Docker Image
Suppose we have a machine with Ubuntu 16.04, but the code we're trying to run needs the software package versions of an Ubuntu 14.04 distribution. Rather than installing Ubuntu 14.04 just to run our code, we can it inside a Docker container that has Ubuntu 14.04 packages. Let's say our code can be run with the command `my_code`. Then to run it inside an Ubuntu 14.04 Docker container, we can run it like this:

{% highlight shell %}
docker pull ubuntu:14.04
docker run -it ubuntu:14.04 my_code
{% endhighlight %}

The first line will pull the latest Ubuntu 14.04 image from [Docker Hub][docker-hub], an online repository of Dockerfiles that have already been built into a Docker image. The second line will launch a Docker container that is running the Ubuntu 14.04 image, and execute our code. When our code executes inside a Docker container, it still has access to the host machine's CPUs and RAM as it normally would outside the container. However, the filesystem that our code sees is the Docker image filesystem, not our host machine filesystem.

But what if we want to access files in our host machine's filesystem? We can run the following snippet instead:

{% highlight shell %}
docker run -v /path/to/my/data:/foo -w /foo -it ubuntu:14.04 my_code
{% endhighlight %}

The `-v` argument binds the host machine's `/path/to/my/data` to `foo` in the Docker image. The `-w` argument sets the present working directory of the Docker container to `/foo`.

## Making a Docker Image
Now, suppose that we aren't happy with the Ubuntu 14.04 Docker image that's provided on Docker Hub and you want to make your own modified version that adds the libjpeg-dev package, for example. How do we do that? First, we define our own file named Dockerfile:

{% highlight shell %}
mkdir ubuntu-custom
cd ubuntu-custom
touch Dockerfile
{% endhighlight %}

Then, we open our Dockerfile in an editor and write this:

{% highlight Docker %}
FROM ubuntu:14.04

RUN apt-get update && apt-get install -y --no-install-recommends \
libjpeg-dev && \
rm -rf /var/lib/apt/lists/*
{% endhighlight %}

This tells Docker that we want to inherit from the Ubuntu 14.04 image and then install libjpeg-dev. We clean up the Apt package list after installing libjpeg-dev so that we aren't packaging in unnecessary cached files into our new Docker image.

Finally, to build a new image from our Dockerfile, we run the following:

{% highlight shell %}
docker build -t ubuntu-custom .
{% endhighlight %}

Now, we can execute our code in our new image with:

{% highlight shell %}
docker run -it ubuntu-custom my_code
{% endhighlight %}

## Using Docker with GPU Training
One caveat of Docker is that it cannot easily access GPUs belonging to the host machine. To make it easier, Nvidia has created [nvidia-docker][nvidia-docker], a modified version of Docker that does support GPUs. The usage of it is identical to Docker, but it provides GPU support.

## My Docker Hub Images
For my own convenience, I've uploaded my own Docker images for deep learning to Docker Hub. <code>tpankaj/dl-pytorch</code> is built for PyTorch users and <code>tpankaj/dl-keras</code> is built for Keras (TensorFlow backend) users. Both use the latest stable version of their respective libraries. Here is an example of how to use them:

{% highlight shell %}
nvidia-docker pull tpankaj/dl-keras
nvidia-docker run -it tpankaj/dl-keras python Train.py
{% endhighlight %}

## Conclusion
I just started using Docker this summer, but it's already sped up the setup process for training on a new machine, because I only need to install CUDA and Docker, and then pull my image from Docker Hub. I've also slowly begun to convince people in my lab to use it instead of dealing with installing everything on each training machine themselves.

[docker]: https://www.docker.com/
[docker-hub]: https://hub.docker.com/
[nvidia-docker]: https://github.com/NVIDIA/nvidia-docker
