---
title: Anaconda国内镜像源3个
tags: []
id: '1705'
categories:
  - - 软件安装配置
date: 2021-05-20 13:30:00
---

#### 北京外国语大学镜像源

    channels:
      - defaults
    show_channel_urls: true
    channel_alias: https://mirrors.bfsu.edu.cn/anaconda
    default_channels:
      - https://mirrors.bfsu.edu.cn/anaconda/pkgs/main
      - https://mirrors.bfsu.edu.cn/anaconda/pkgs/free
      - https://mirrors.bfsu.edu.cn/anaconda/pkgs/r
      - https://mirrors.bfsu.edu.cn/anaconda/pkgs/pro
      - https://mirrors.bfsu.edu.cn/anaconda/pkgs/msys2
    custom_channels:
      conda-forge: https://mirrors.bfsu.edu.cn/anaconda/cloud
      msys2: https://mirrors.bfsu.edu.cn/anaconda/cloud
      bioconda: https://mirrors.bfsu.edu.cn/anaconda/cloud
      menpo: https://mirrors.bfsu.edu.cn/anaconda/cloud
      pytorch: https://mirrors.bfsu.edu.cn/anaconda/cloud
      simpleitk: https://mirrors.bfsu.edu.cn/anaconda/cloud

#### 上海交通大学镜像源

    channels:
      - defaults
    show_channel_urls: true
    channel_alias: https://anaconda.mirrors.sjtug.sjtu.edu.cn/
    default_channels:
      - https://anaconda.mirrors.sjtug.sjtu.edu.cn/pkgs/main
      - https://anaconda.mirrors.sjtug.sjtu.edu.cn/pkgs/free
      - https://anaconda.mirrors.sjtug.sjtu.edu.cn/pkgs/mro
      - https://anaconda.mirrors.sjtug.sjtu.edu.cn/pkgs/msys2
      - https://anaconda.mirrors.sjtug.sjtu.edu.cn/pkgs/pro
      - https://anaconda.mirrors.sjtug.sjtu.edu.cn/pkgs/r
    custom_channels:
      conda-forge: https://anaconda.mirrors.sjtug.sjtu.edu.cn/conda-forge
      soumith: https://anaconda.mirrors.sjtug.sjtu.edu.cn/cloud/soumith
      bioconda: https://anaconda.mirrors.sjtug.sjtu.edu.cn/cloud/bioconda
      menpo: https://anaconda.mirrors.sjtug.sjtu.edu.cn/cloud/menpo
      viscid-hub: https://anaconda.mirrors.sjtug.sjtu.edu.cn/cloud/viscid-hub
      atztogo: https://anaconda.mirrors.sjtug.sjtu.edu.cn/cloud/atztogo

#### 清华大学镜像源

    channels:
      - defaults
    show_channel_urls: true
    channel_alias: https://mirrors.tuna.tsinghua.edu.cn/anaconda
    default_channels:
      - https://mirrors.tuna.tsinghua.edu.cn/anaconda/pkgs/main
      - https://mirrors.tuna.tsinghua.edu.cn/anaconda/pkgs/free
      - https://mirrors.tuna.tsinghua.edu.cn/anaconda/pkgs/r
      - https://mirrors.tuna.tsinghua.edu.cn/anaconda/pkgs/pro
      - https://mirrors.tuna.tsinghua.edu.cn/anaconda/pkgs/msys2
    custom_channels:
      conda-forge: https://mirrors.tuna.tsinghua.edu.cn/anaconda/cloud
      msys2: https://mirrors.tuna.tsinghua.edu.cn/anaconda/cloud
      bioconda: https://mirrors.tuna.tsinghua.edu.cn/anaconda/cloud
      menpo: https://mirrors.tuna.tsinghua.edu.cn/anaconda/cloud
      pytorch: https://mirrors.tuna.tsinghua.edu.cn/anaconda/cloud
      simpleitk: https://mirrors.tuna.tsinghua.edu.cn/anaconda/cloud