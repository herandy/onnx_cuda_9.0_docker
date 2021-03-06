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
         google-mock \
         libleveldb-dev\
         libopencv-dev \
         libeigen3-dev &&\
     rm -rf /var/lib/apt/lists/*


RUN curl -o ~/miniconda.sh -O  https://repo.continuum.io/miniconda/Miniconda3-latest-Linux-x86_64.sh  && \
     chmod +x ~/miniconda.sh && \
     ~/miniconda.sh -b -p /opt/conda && \
     rm ~/miniconda.sh && \
     /opt/conda/bin/conda install -y python=$PYTHON_VERSION numpy pyyaml scipy ipython mkl mkl-include cython typing conda-build scikit-learn pandas protobuf=3.5.2 opencv=3.3.1 && \
     /opt/conda/bin/conda install -y -c conda-forge imbalanced-learn feather-format && \
     /opt/conda/bin/conda install -y -c pytorch magma-cuda90 && \
     /opt/conda/bin/conda clean -ya
ENV PATH /opt/conda/bin:$PATH
# ONNX
RUN git clone --recursive https://github.com/onnx/onnx.git /opt/onnx
RUN cd /opt/onnx; git submodule update --init --recursive;python setup.py install

RUN pip install --upgrade pip
RUN pip install sklearn_pandas

RUN conda install -c caffe2 -y pytorch-caffe2-cuda9.0-cudnn7

RUN apt-get update && apt-get install -y openssh-server

RUN git clone --recursive https://github.com/leonardvandriel/caffe2_cpp_tutorial.git /opt/caffe2_cpp_tutorial
RUN cd /opt/caffe2_cpp_tutorial; make

RUN mkdir /var/run/sshd
RUN echo 'root:testssh' | chpasswd
RUN sed -i 's/PermitRootLogin prohibit-password/PermitRootLogin yes/' /etc/ssh/sshd_config
RUN sed 's@session\s*required\s*pam_loginuid.so@session optional pam_loginuid.so@g' -i /etc/pam.d/sshd
RUN echo "export VISIBLE=now" >> /etc/profile
RUN service ssh restart

WORKDIR /workspace
RUN chmod -R a+w /workspace
