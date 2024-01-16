---
title: Ubuntu22宝塔面板安装docker
tags: []
categories:
  - - 杂
date: 2023-07-04 13:30:00
---
## Ubuntu22宝塔面板安装docker

官网[宝塔面板下载，免费全能的服务器运维软件 (bt.cn)](https://www.bt.cn/new/download.html)

``````shell
wget -O install.sh https://download.bt.cn/install/install-ubuntu_6.0.sh && sudo bash install.sh ed8484bec
``````

复制保存初始化用户密码

![](https://xinhaojin.github.io/imgs-host/20230704/image.5xjeaa9urio0.webp)

放行端口

``````shell
sudo ufw allow 23252/tcp
sudo ufw allow 23252/udp
sudo ufw enable
``````

有可能还是无法访问，需要重启宝塔面板，或重启服务器

``````
systemctl status bt
systemctl daemon-reload
systemctl restart bt
``````

![](https://xinhaojin.github.io/imgs-host/20230704/image.7jei1ok5yo40.webp)

使用初始化用户密码登录进去之后，在面板设置中修改用户名和密码

### 安装docker

在面板软件商店中搜索docker安装

