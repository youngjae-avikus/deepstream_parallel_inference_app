FROM nvcr.io/nvidia/deepstream:6.2-triton

ENV DEBIAN_FRONTEND=noninteractive
ENV LANG C.UTF-8
ENV PATH="/usr/local/cuda/bin:${PATH}"
ENV LD_LIBRARY_PATH="/opt/nvidia/deepstream/deepstream/lib:/usr/local/cuda/lib64:${LD_LIBRARY_PATH}"

# public gpg key error issue
# https://github.com/NVIDIA/nvidia-docker/issues/1631#issuecomment-1112682423

# RUN apt-key del 7fa2af80

RUN apt-get update && apt-get remove --autoremove -y \
    libgstreamer1.0*

RUN	apt-get update && \
    apt-get install -y \
    libopencv-dev \
    software-properties-common \
    libgstrtspserver-1.0-dev \
    gstreamer1.0-rtsp \
    gstreamer1.0-tools \
    libapr1 \
    libapr1-dev \
    libaprutil1 \
    libaprutil1-dev \
    libgeos-dev \
    libcurl4-openssl-dev \
    libjson-glib-1.0-0 \
    libsoup-gnome2.4-dev \
    libgstreamer-plugins* \
    gstreamer1.0-plugins-base \
    gstreamer1.0-plugins-good \
    gstreamer1.0-plugins-bad \
    gstreamer1.0-plugins-ugly \
    graphviz \
    imagemagick


COPY nvdspreprocess_solve_interval.patch /
RUN patch -p0 /opt/nvidia/deepstream/deepstream/sources/gst-plugins/gst-nvinfer/gstnvinfer.cpp < /nvdspreprocess_solve_interval.patch

WORKDIR /opt/nvidia/deepstream/deepstream/sources/gst-plugins/gst-nvinfer

RUN CUDA_VER=11.8 make -j4
RUN CUDA_VER=11.8 make install

WORKDIR /workspace

RUN update-alternatives --install /usr/bin/python python /usr/bin/python3.8 1

RUN python -m pip install --upgrade pip
RUN python -m pip install numpy opencv-python

WORKDIR /opt

ARG version=1.1.6
ENV URL="https://github.com/NVIDIA-AI-IOT/deepstream_python_apps/releases/download/v$version/pyds-$version-py3-none-linux_x86_64.whl"

RUN apt-get update && apt-get install -y wget python3.8-dev

RUN wget $URL \
    && pip install pyds-$version-py3-none-linux_x86_64.whl

# Install User package
RUN apt-get update && \
    apt-get install -y \
    vim \ 
    silversearcher-ag \ 
    iftop \
    net-tools \
    iproute2 \
    iputils-ping

# Add the environment variable assignments and sourcing to ~/.bashrc
RUN echo "export GST_DEBUG=1,DSL:4" >> ~/.bashrc
