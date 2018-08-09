FROM nvidia/cuda:9.0-cudnn7-devel-ubuntu16.04
ARG PYTHON_VERSION=3.6
RUN apt-get update && apt-get install -y --no-install-recommends \
         build-essential \
         cmake \
         git \
         curl \
         vim \
         ca-certificates \
         libjpeg-dev \
         libpng-dev \
	 protobuf-compiler \
	 libprotoc-dev &&\
     rm -rf /var/lib/apt/lists/*


RUN curl -o ~/miniconda.sh -O  https://repo.continuum.io/miniconda/Miniconda3-latest-Linux-x86_64.sh  && \
     chmod +x ~/miniconda.sh && \
     ~/miniconda.sh -b -p /opt/conda && \
     rm ~/miniconda.sh && \
     /opt/conda/bin/conda install -y python=$PYTHON_VERSION numpy pyyaml scipy ipython mkl mkl-include cython typing conda-build scikit-learn pandas && \
     /opt/conda/bin/conda install -y -c conda-forge imbalanced-learn && \
     /opt/conda/bin/conda install -y -c pytorch magma-cuda90 && \
     /opt/conda/bin/conda clean -ya
ENV PATH /opt/conda/bin:$PATH
# ONNX
RUN git clone --recursive https://github.com/onnx/onnx.git /opt/onnx
RUN cd /opt/onnx; git submodule update --init --recursive;python setup.py install

RUN pip install --upgrade pip

# This must be done before pip so that requirements.txt is available
RUN git clone --recursive https://github.com/pytorch/pytorch.git /opt/pytorch
WORKDIR /opt/pytorch
RUN git submodule update --init
COPY . .

RUN ./scripts/build_anaconda.sh --install-locally --cuda 9.0 --cudnn 7

RUN git submodule update --init
RUN TORCH_CUDA_ARCH_LIST="3.5 5.2 6.0 6.1 7.0+PTX" TORCH_NVCC_FLAGS="-Xfatbin -compress-all" \
    CMAKE_PREFIX_PATH="$(dirname $(which conda))/../" \
    pip install -v .

RUN git clone https://github.com/pytorch/vision.git && cd vision && pip install -v .

RUN apt-get install -y openssh-server

WORKDIR /workspace
RUN chmod -R a+w /workspace