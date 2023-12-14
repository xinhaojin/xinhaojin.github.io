---
title: git配置
tags: []
id: '2137'
categories:
  - - 软件安装配置
date: 2022-02-25 20:51:38
---
```shell
git config --global user.name "yourname"
git config --global user.email "youremail"
ssh-keygen -t rsa -C "youremail"
```
找到用户目录下的.ssh文件夹下的id_rsa.pub，复制内容到github设置中添加sshkey
![](https://cdn.staticaly.com/gh/xinhaojin/imgs-host@master/past/2022/02/image-5-1024x442.png)