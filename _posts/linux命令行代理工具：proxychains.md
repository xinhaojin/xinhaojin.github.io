---
title: linux命令行代理工具：proxychains
tags: []
id: '2357'
categories:
  - - 杂
date: 2022-05-09 22:03:09
---

## 安装

    sudo apt install proxychains4

编辑/etc/procychains.conf

在末尾添加代理服务器设置

![](https://cdn.staticaly.com/gh/xinhaojin/imgs-host@master/past/2022/05/image.png)

## 验证

    curl www.httpbin.org/ip

在命令前添加proxychains4来走代理

    proxychains4 curl www.httpbin.org/ip

![](https://cdn.staticaly.com/gh/xinhaojin/imgs-host@master/past/2022/05/image-1-1024x339.png)