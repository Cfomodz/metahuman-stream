# Use NVIDIA's CUDA base image
#FROM nvidia/cuda:11.3.0-cudnn8-runtime-ubuntu18.04
#FROM m11007322/cuda11.3.0-cudnn8-devel-ubuntu20.04-jupyterlab
FROM nvidia/cuda:11.3.1-cudnn8-devel-ubuntu20.04

# Install Python and pip
RUN apt-get update && apt-get install -y --no-install-recommends \
        python3 \
        python3-pip \
    && rm -rf /var/lib/apt/lists/*

# Install Jupyter
RUN pip3 install --no-cache-dir jupyter

# Install basic tools
RUN apt-get update -yq --fix-missing \
    && DEBIAN_FRONTEND=noninteractive apt-get install -yq --no-install-recommends \
    pkg-config \
    wget \
    cmake \
    curl \
    git \
    vim

# Create a new Conda environment
RUN apt-get update && apt-get install -y wget \
    && wget https://repo.anaconda.com/miniconda/Miniconda3-latest-Linux-x86_64.sh \
    && /bin/bash Miniconda3-latest-Linux-x86_64.sh -b -p /opt/conda \
    && rm Miniconda3-latest-Linux-x86_64.sh \
    && apt-get remove --purge --auto-remove -y wget \
    && apt-get clean \
    && ln -s /opt/conda/bin/conda /usr/bin/conda \
    && conda update -n base -c defaults conda 

SHELL ["/bin/bash","-ic"]
# Add CUDA global variables
RUN echo "export CUDA_HOME=/usr/local/cuda" >> ~/.bashrc \
    && echo "export PATH=${CUDA_HOME}/bin:$PATH" >> ~/.bashrc \
    && echo "export LD_LIBRARY_PATH=${CUDA_HOME}/lib64:$LD_LIBRARY_PATH" >> ~/.bashrc \
    && source ~/.bashrc
    
    #&& echo "nameserver 8.8.8.8" >> /etc/resolv.conf

# Install cv2 dependencies, fix libGL.so.1 error
RUN apt-get update
RUN apt-get install ffmpeg libsm6 libxext6  -y

# Configure Jupyter
ENV JUPYTER_ENABLE_LAB=yes
ENV USER=root
ENV HOME=/home/$USER

# Set working directory
WORKDIR /root

# Set startup command
CMD ["jupyter", "lab", "--ip='*'", "--port=8888", "--no-browser", "--allow-root"]