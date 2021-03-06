FROM nvidia/cuda:9.0-devel-ubuntu16.04

MAINTAINER CodeLibs Project

ENV DEBIAN_FRONTEND noninteractive

RUN apt update
RUN apt upgrade -y
RUN apt install -y locales

RUN locale-gen en_US.UTF-8
ENV LANG en_US.UTF-8
ENV LANGUAGE en_US:en
ENV LC_ALL en_US.UTF-8
ENV PIP_DEFAULT_TIMEOUT 180

ENV CUDNN_VERSION 7.0.5.15
LABEL com.nvidia.cudnn.version="${CUDNN_VERSION}"

RUN apt-get update && apt-get install -y --no-install-recommends \
            libcudnn7=$CUDNN_VERSION-1+cuda9.0 \
            libcudnn7-dev=$CUDNN_VERSION-1+cuda9.0

RUN apt-mark hold libcudnn7 libcudnn7-dev cuda-libraries-dev-$CUDA_PKG_VERSION cuda-nvml-dev-$CUDA_PKG_VERSION \
    cuda-minimal-build-$CUDA_PKG_VERSION cuda-command-line-tools-$CUDA_PKG_VERSION libnccl-dev \
    cuda-libraries-$CUDA_PKG_VERSION libnccl2 cuda-cudart-$CUDA_PKG_VERSION

RUN apt install -y build-essential curl git gcc make openssl libssl-dev libbz2-dev libreadline-dev libsqlite3-dev cmake \
    libxml2-dev wget bzip2 sudo vim unzip && \
    rm -rf /var/lib/apt/lists/*

ENV PATH=/opt/conda/bin:/usr/local/nvidia/bin:/usr/local/cuda/bin:$PATH
ENV LD_LIBRARY_PATH=/usr/local/cuda/lib64:/usr/local/cuda/extras/CUPTI/lib64:$LD_LIBRARY_PATH

RUN wget -q https://repo.continuum.io/miniconda/Miniconda3-4.5.4-Linux-x86_64.sh -O /tmp/miniconda.sh  && \
    echo 'a946ea1d0c4a642ddf0c3a26a18bb16d */tmp/miniconda.sh' | md5sum -c - && \
    bash /tmp/miniconda.sh -f -b -p /opt/conda && \
    /opt/conda/bin/conda install --yes -c conda-forge python=3.6 sqlalchemy tornado jinja2 traitlets requests pip nodejs configurable-http-proxy && \
    /opt/conda/bin/pip install --upgrade pip && \
    rm /tmp/miniconda.sh

RUN python3 -m pip install notebook
RUN python3 -m pip install jupyter
RUN python3 -m pip install jupyterhub
RUN python3 -m pip install oauthenticator
RUN python3 -m pip install jupyterhub-ldapauthenticator
RUN python3 -m pip install jupyter_contrib_nbextensions
RUN python3 -m pip install autopep8
RUN python3 -m pip install yapf

RUN jupyter contrib nbextension install --system

RUN mkdir -p /opt/oauthenticator
WORKDIR /opt/oauthenticator
ENV OAUTHENTICATOR_DIR /opt/oauthenticator
ADD res/addusers.sh /opt/oauthenticator/addusers.sh
ADD res/userlist /opt/oauthenticator/userlist
RUN ["sh", "/opt/oauthenticator/addusers.sh"]
RUN chmod 700 /opt/oauthenticator

RUN mkdir -p /opt/jupyterhub/
WORKDIR /opt/jupyterhub/
ADD res/jupyterhub_config.py jupyterhub_config.py
# LDAP
#ADD res/server.key server.key
#ADD res/server.crt server.crt

EXPOSE 8000

LABEL org.jupyter.service="jupyterhub"

CMD ["jupyterhub"]

