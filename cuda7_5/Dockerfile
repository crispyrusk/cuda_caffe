# Start with cuDNN base image 
FROM nvidia/cuda:7.5-devel 
MAINTAINER Kiran <skyrus@gmail.com> 
# Install usual dependencies 
RUN apt-get update && apt-get install -y \ 
    git \ 
    wget \ 
    bc \
    vim \ 
    cmake \ 
    libatlas-base-dev \ 
    libatlas-dev \ 
    libboost-all-dev \ 
    libopencv-dev \ 
    libprotobuf-dev \ 
    libgoogle-glog-dev \ 
    libgflags-dev \ 
    protobuf-compiler \ 
    libhdf5-dev \ 
    libleveldb-dev \ 
    liblmdb-dev \ 
    libsnappy-dev \ 
    python-dev \ 
    python-pip \ 
    python-numpy \ 
    gfortran > /dev/null 


# create a user cudacaffe with suitable home directory
ENV HOME /home/cudacaffe

RUN useradd --create-home --home-dir $HOME --shell /bin/bash cudacaffe 

# make user a sudoer
RUN chmod +w /etc/sudoers && \
    echo "cudacaffe   ALL=(ALL)       NOPASSWD:ALL" >> /etc/sudoers && \
    chmod -w /etc/sudoers 

# install miniconda
RUN \
    mkdir $HOME/Downloads && \
    cd $HOME/Downloads && \
    wget --quiet https://repo.continuum.io/miniconda/Miniconda-latest-Linux-x86_64.sh && \
    bash Miniconda-latest-Linux-x86_64.sh -b  && \ 
    echo "PATH=$HOME/miniconda2/bin:$PATH" >> ~/.bashrc 


# make miniconda default python
env PATH $HOME/miniconda2/bin:$PATH

# install miniconda dependencies
COPY pythondep.txt $HOME/Downloads/pythondep.txt
RUN cd $HOME/Downloads && \
    for req in `cat pythondep.txt`; do conda install $req; done

# Clone Caffe repo and move into it 
RUN cd $HOME && git clone https://github.com/BVLC/caffe.git  

# Install python dependencies 
RUN cd $HOME/caffe && \
    for req in `cat python/requirements.txt`; do conda install $req; done

# copy the makefile.config to the caffe folder
COPY Makefile.config $HOME/caffe/Makefile.config

# change ownership to cudacaffe user
RUN chown -R cudacaffe:cudacaffe $HOME 

USER cudacaffe
