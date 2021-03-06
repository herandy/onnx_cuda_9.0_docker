# Using this script to build your ONNX image.
# Execute "docker build dir", where dir contains this Dockerfile. Make sure you give docker container at least 8GB memory.
# Execute "docker run -i -t image_id", where image_id is the id of the image you just generated.
# Try "python tutorial-without-mobile-part.py".

FROM nvidia/cuda:9.0-cudnn7-devel-ubuntu16.04

RUN apt-get update && apt-get install -y --no-install-recommends \
         build-essential \
         cmake \
         git \
         ssh \
         vim \
	 wget \
         curl \
         ca-certificates \
         unzip \
         libjpeg-dev \
         libpng-dev \
	 libgflags-dev \
         libgoogle-glog-dev \
         liblmdb-dev \
         cmake && \
     rm -rf /var/lib/apt/lists/*

# download conda
RUN mkdir -p /root/programs
RUN wget https://repo.continuum.io/archive/Anaconda3-5.2.0-Linux-x86_64.sh -O /root/programs/anaconda.sh
RUN chmod 0755 /root/programs/anaconda.sh
RUN /root/programs/anaconda.sh -b -p /root/programs/anaconda3
ENV PATH="/root/programs/anaconda3/bin:$PATH"
RUN rm /root/programs/anaconda.sh

RUN pip install --upgrade pip
RUN pip install wheel ipython==5.0 numpy scipy pyyaml scipy scikit-learn numpy future pydot python-gflags scikit-image six

RUN git clone --recursive https://github.com/onnx/onnx.git /root/programs/onnx
RUN git clone --recursive https://github.com/pytorch/pytorch.git /root/programs/pytorch

ENV CMAKE_PREFIX_PATH="$(dirname $(which conda))/../"
RUN conda install -y numpy pyyaml mkl mkl-include setuptools cmake cffi typing protobuf
RUN conda install -c mingfeima -y mkldnn
RUN conda install -c pytorch -y magma-cuda90
RUN conda install -c conda-forge -y pybind11

RUN cd /root/programs/onnx; python setup.py develop
RUN cd /root/programs/pytorch; ./scripts/build_anaconda.sh --install-locally --cuda 9.0 --cudnn 7
RUN cd /root/programs/pytorch; python setup.py install

WORKDIR /root/programs
