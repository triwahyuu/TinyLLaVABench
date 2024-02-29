FROM pytorch/pytorch:2.0.1-cuda11.7-cudnn8-devel

ARG DEBIAN_FRONTEND=noninteractive

RUN apt-get update \
    && apt-get install -y locales \
    && locale-gen en_US.UTF-8 \
    && update-locale LC_ALL=en_US.UTF-8 LANG=en_US.UTF-8 \
    && ln -fs /usr/share/zoneinfo/UTC /etc/localtime \
    && export DEBIAN_FRONTEND=noninteractive \
    && apt-get update \
    && apt-get install -y tzdata \
    && dpkg-reconfigure --frontend noninteractive tzdata
ENV LANG en_US.UTF-8

RUN apt-get update && apt-get -y upgrade \
    && apt-get install -y --no-install-recommends \
        sudo curl wget gnupg2 lsb-release software-properties-common \
        ssh git git-lfs jq ffmpeg dkms build-essential libxcb-xinerama0 \
        libqt5gui5 libx11-xcb-dev libglu1-mesa-dev libxrender-dev \
        libxi-dev libxkbcommon-dev libxkbcommon-x11-dev \
    && rm -rf /var/lib/apt/lists/*

ARG USERNAME=vscode
ARG USER_UID=1000
ARG USER_GID=$USER_UID
RUN groupadd --gid $USER_GID $USERNAME \
    && useradd --uid $USER_UID --gid $USER_GID -m $USERNAME \
    && apt-get update \
    && apt-get install -y sudo \
    && echo $USERNAME ALL=\(root\) NOPASSWD:ALL > /etc/sudoers.d/$USERNAME \
    && chmod 0440 /etc/sudoers.d/$USERNAME

RUN python -m pip install pip --upgrade && \
    pip install pre-commit==3.0.2 packaging jupyter ipywidgets setuptools && \
    pip install gradio==3.35.2 gradio_client==0.2.9 einops==0.6.1 einops-exts==0.0.4 timm==0.6.13 && \
    pip install transformers==4.37.2 accelerate==0.21.0 sentencepiece==0.1.99 bitsandbytes==0.41.0 && \
    pip install uvicorn fastapi scikit-learn==1.2.2 && \
    pip install flash-attn --no-build-isolation

ENV LD_LIBRARY_PATH=/opt/conda/lib/python3.10/site-packages/torch/lib:$LD_LIBRARY_PATH

WORKDIR /tmp/tinyllava
COPY pyproject.toml tinyllava/ scripts/ /tmp/tinyllava/
RUN pip install -e . && pip install -e ".[train]"

USER $USERNAME
WORKDIR /
