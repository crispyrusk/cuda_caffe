#cuda_caffe 

The repo contains a few Dockerfiles to build the Caffe deep learning framework (https://github.com/BVLC/caffe) using NVIDIA's docker tool, nvidia-docker (https://github.com/NVIDIA/nvidia-docker). Nvidia-docker is convenient wrapper over docker (https://www.docker.com/) that simplifies containerizing GPU applications, like deep-learning frameworks. See the "Why NVIDIA Docker" page (https://github.com/NVIDIA/nvidia-docker/wiki/Why%20NVIDIA%20Docker) for the motivation of this tool. The list of base images provided by NVIDIA is documented in https://hub.docker.com/r/nvidia/cuda/


The Dockerfile help setup the dependencies required for Caffe with various options CUDA and cuDNN. It uses Anaconda's distribution of python as default python for Caffe.

The repo provides Dockerfile that can be deployed on a local machine or AWS ec2 GPU instance.

# Installation of NVIDIA-Docker

Install both Docker and NVIDIA-Docker.

## Docker Installation

The detailed instructions for docker installation is available at https://docs.docker.com/engine/installation/linux/ubuntulinux/

Installation from the get-docker script:
```
$ curl -sSL https://get.docker.com/ | sh
$ sudo usermod -aG docker <user> # add <user> to docker group, where <user> is your username
# it is recommeded to login and logout for changes to effect.
```

## NVIDIA-Docker Installation

Make sure you have the NVIDIA drivers installed on your machine:
```
$ nvidia-smi # this should display right driver version
```

Install nvidia-modprobe:
```
$ sudo apt-get install nvidia-modprobe
```

Install nvidia-docker from source:
```
$ mkdir /path/to/install
$ cd /path/to/install
$ git clone https://github.com/NVIDIA/nvidia-docker.git
$ cd nvidia-docker # cd to the source repo
 
# build
$ make
$ suo make install 
```

Start the nvidia-docker plugin. The plugin runs in the background to the containers under the host GPU drivers and files.
```
$ sudo -b nohup nvidia-docker-plugin # start nvidia-docker plugin as daemon
```

Test if nvidia-docker works:
```
$ nvidia-docker run --rm nvidia/cuda nvidia-smi
# you should nvidia-smi output similar to what you will see on your host; same driver details
```

# Using the cuda_caffe repo

Clone the repo:
```
$ mkdir /path/to/clone
$ cd /path/to/clone
$ git clone https://github.com/crispyrusk/cuda_caffe.git
$ cd cuda_caffe
```

Build a docker image with your chosen version of CUDA and cuDNN. As an example we will use the Dockerfile in the cuda7_5_cuddn5 folder, which has CUDA 7.5 and cuDNN 5.0. The same procedure works for other Dockerfiles contained in other folders.
```
$ cd cuda7_5_cuddn5
$ docker build -t <image>:<tag> . # this builds a docker image from the Dockerfile in the current folder
```

## Building Caffe within the cuda_caffe docker container

Run the docker container with built image and build caffe:
```
$ nvidia-docker run --rm -ti <image>:<tag> /bin/bash

# now you should inside the docker container running your image, <image>:<tag>
# within the container

$ cd $HOME/caffe
$ make -j8 # build caffe
$ make test -j8 # build tests
$ export LD_LIBRARY_PATH=/home/cudacaffe/miniconda2/lib:$LD_LIBRARY_PATH # this is the ugly part to make things work propertly is anaconda python
$ make runtest -j8 # all the test should run without errors
```

## Building NVIDIA-Docker images on AWS ec2 instance

AWS provides a couple of GPU instance types, g2.2xlarge and g2.8xlarge. The NVIDIA-Docker provides instructions on how to deploy NVIDIA-Docker based containers on these instances: https://github.com/NVIDIA/nvidia-docker/wiki/Deploy-on-Amazon-EC2

The following instructions works and is tested on the g2.2xlarge instances running Ubuntu 14

Prepare the instance to expose the GPU:
```
# install additional packages. The base AWS instance is thin and has many packages missing
$ sudo apt-get install -y linux-image-extra-`uname -r` linux-headers-`uname -r` linux-image-`uname -r 

# install compilers
$ `sudo apt-get install --no-install-recommends -y gcc make libc-dev`

# Install NVIDIA drivers 361.42
wget -P ~/sw http://us.download.nvidia.com/XFree86/Linux-x86_64/361.42/NVIDIA-Linux-x86_64-361.42.run
sudo sh ~/sw/NVIDIA-Linux-x86_64-361.42.run --silent

# Install NVIDIA modprobe
sudo apt-get install nvidia-modprobe
```

Some issues have been reported with AWS GPU instances with the latest drivers. In such cases, it is recommeded to install drivers that are stable with CUDA 7.0. Alternatively, you can also use a prebuilt AMI with a stable driver and CUDA installation, such as [ami-763a311e](https://console.aws.amazon.com/ec2/v2/home?region=us-east-1#LaunchInstanceWizard:ami=ami-763a311e) (https://github.com/BVLC/caffe/wiki/Caffe-on-EC2-Ubuntu-14.04-Cuda-7). 

Once you have a stable driver setting on the ec2 instance, you can follow the Docker and NVIDIA-Docker installation from above to install docker, and then clone the cuda_caffe repo.

## Docker options for running Jupyter-Notebook within the container

Jupyter-Notebook is a convenient browser-based development tool for quick prototyping for data applications (http://jupyter.org/). Jupyter-Notebook is packaged with the anaconda python packaged in the docker image built using cuda_caffe. 





