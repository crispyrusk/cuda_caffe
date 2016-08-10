#cuda_caffe 

The repo contains a few Dockerfiles to build the Caffe deep learning framework (https://github.com/BVLC/caffe) using NVIDIA's docker tool, nvidia-docker (https://github.com/NVIDIA/nvidia-docker). Nvidia-docker is convenient wrapper over docker (https://www.docker.com/) that simplifies containerizing GPU applications, like deep-learning frameworks. See the "Why NVIDIA Docker" page (https://github.com/NVIDIA/nvidia-docker/wiki/Why%20NVIDIA%20Docker) for the motivation of this tool.

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

Test that nvidia-

# list of base images
https://hub.docker.com/r/nvidia/cuda/


# cuda_caffe
Dockerfile to setup cuda-cudnn-5.0 on docker



