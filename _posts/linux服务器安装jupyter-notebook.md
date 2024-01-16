---
title: linux服务器安装jupyter notebook
index_img: https://xinhaojin.github.io/imgs-host/past/2021/05/image-34-1024x332.png
tags: []
id: '1733'
categories:
  - - 软件安装配置
date: 2021-05-22 12:54:15
---

用pip安装

    sudo pip3 install jupyter

进入python3环境，设置密码，生成秘钥，复制下来
```python
from notebook.auth import passwd
passwd()
```
![](https://xinhaojin.github.io/imgs-host/past/2021/05/image-33.png)

查找配置文件所在路径

    jupyter notebook --generate-config

如果提示未找到命令，先找一下jupyter的安装目录

![](https://xinhaojin.github.io/imgs-host/past/2021/10/image.png)

根据路径打开配置文件jupyter_notebook_config.py,输入
```python
#允许root用户启动
c.NotebookApp.allow_root = True
#允许任意IP访问
c.NotebookApp.ip='*'
#默认不打开浏览器，仅启动服务
c.NotebookApp.open_browser=False
#密钥
c.NotebookApp.password='argI******你的密钥**********zQ'
#端口号，记得放行
c.NotebookApp.port=8888
#工作目录
c.NotebookApp.notebook_dir='/notebook_space'
```
然后在任意终端的浏览器输入IP端口号就能打开了，密码是自己设置的passwd

![](https://xinhaojin.github.io/imgs-host/past/2021/05/image-34-1024x332.png)

为了方便，直接把jupyter notebook加入开机启动项

在/etc/rc.d/rc.local中添加

    jupyter notebook