# https://hub.docker.com/r/nvidia/cuda/tags?page=1&name=11.7.1-base-ubuntu
ARG BASE_IMAGE=nvidia/cuda:11.7.1-base-ubuntu22.04

FROM ${BASE_IMAGE} as init-env
ENV DEBIAN_FRONTEND noninteractive
COPY <<EOF /etc/apt/sources.list
# https://mirrors.tuna.tsinghua.edu.cn/help/ubuntu/
deb https://mirrors.tuna.tsinghua.edu.cn/ubuntu/ jammy main restricted universe multiverse
# deb-src https://mirrors.tuna.tsinghua.edu.cn/ubuntu/ jammy main restricted universe multiverse
deb https://mirrors.tuna.tsinghua.edu.cn/ubuntu/ jammy-updates main restricted universe multiverse
# deb-src https://mirrors.tuna.tsinghua.edu.cn/ubuntu/ jammy-updates main restricted universe multiverse
deb https://mirrors.tuna.tsinghua.edu.cn/ubuntu/ jammy-backports main restricted universe multiverse
# deb-src https://mirrors.tuna.tsinghua.edu.cn/ubuntu/ jammy-backports main restricted universe multiverse

# deb https://mirrors.tuna.tsinghua.edu.cn/ubuntu/ jammy-security main restricted universe multiverse
# # deb-src https://mirrors.tuna.tsinghua.edu.cn/ubuntu/ jammy-security main restricted universe multiverse

deb http://security.ubuntu.com/ubuntu/ jammy-security main restricted universe multiverse
# deb-src http://security.ubuntu.com/ubuntu/ jammy-security main restricted universe multiverse

EOF
RUN apt-get update -y && apt-get upgrade -y && apt-get install -y libgl1 libglib2.0-0 wget git git-lfs python3-pip python-is-python3 ffmpeg && rm -rf /var/lib/apt/lists/*

FROM init-env as app-env

RUN adduser --disabled-password --gecos '' user
RUN mkdir /content && chown -R user:user /content
WORKDIR /content
USER user

ENV PIP_INDEX_URL=https://pypi.tuna.tsinghua.edu.cn/simple
RUN pip3 install --upgrade pip

RUN git clone https://github.com/AUTOMATIC1111/stable-diffusion-webui.git
RUN sed -i -e 's/    start()/    #start()/g' /content/stable-diffusion-webui/launch.py

ENV PATH="$PATH:/home/user/.local/bin"

ENV TORCH_COMMAND="pip install torch torchvision"
ENV GFPGAN_PACKAGE="git+https://gitee.com/gh-proxy/GFPGAN.git@8d2447a2d918f8eba5a4a01463fd48e45126a379"
ENV CLIP_PACKAGE="git+https://gitee.com/gh-proxy/CLIP.git@d50d76daa670286dd6cacf3bcd80b5e4823fc8e1"
ENV OPENCLIP_PACKAGE="git+https://gitee.com/gh-proxy/open_clip.git@bb6e834e9c70d9c27d0dc3ecedeebeaeb1ffad6b"
ENV STABLE_DIFFUSION_REPO="https://gitee.com/gh-proxy/stablediffusion.git"
ENV TAMING_TRANSFORMERS_REPO="https://gitee.com/gh-proxy/taming-transformers.git"
ENV CODEFORMER_REPO="https://gitee.com/gh-proxy/CodeFormer.git"
ENV BLIP_REPO="https://gitee.com/gh-proxy/BLIP"
RUN cd stable-diffusion-webui && python launch.py --skip-torch-cuda-test

RUN pip install xformers numexpr rich pycloudflared

EXPOSE 7860

CMD cd /content/stable-diffusion-webui && python webui.py --xformers --listen --enable-insecure-extension-access --gradio-queue

