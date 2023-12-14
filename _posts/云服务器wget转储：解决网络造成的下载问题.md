---
title: 云服务器+wget+转储：解决网络造成的下载问题
tags: []
id: '1726'
categories:
  - - 杂
date: 2021-05-21 15:37:06
---

下载经常是一件让人头痛的事情，尤其是下载国外网站的资源，能用的国内镜像站很少且不稳定，归根到底还是网络环境不够好。

一种无需代理的解决方案是利用云服务器下载资源，再转存到本地， 因为云服务器的网络环境通常非常好（部分资源在国内云服务器上仍然无法下载，可以考虑用境外服务器下载）

下面举个例子，我特意找了一个之前下载列表里失败的一个条目，再次尝试下载，果然还是失败的，正好来拿测试

![](https://cdn.staticaly.com/gh/xinhaojin/imgs-host@master/past/2021/05/QQ截图20210521151432.png)

复制下载链接，登上服务器，使用wget命令下载，畅通无阻，由于带宽限制不算特别快，但至少解决了完全无法下载的问题

![](https://cdn.staticaly.com/gh/xinhaojin/imgs-host@master/past/2021/05/image-31.png)

接下来就是从自己的服务器下载资源到本地了，例如我使用finalshell,直接选择下载即可

![](https://cdn.staticaly.com/gh/xinhaojin/imgs-host@master/past/2021/05/image-32-1024x678.png)

以后碰到下载方面的疑难杂症，优先考虑此方法，特此记录。