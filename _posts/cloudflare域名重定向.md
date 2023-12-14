---
title: cloudflare域名重定向
index_img: https://cdn.staticaly.com/gh/xinhaojin/imgs-host@master/past/2021/05/image-8.png
tags: []
id: '1599'
categories:
  - - 杂
date: 2021-05-11 16:54:29
---

#### 需求

将https://www.xinhaojin.top,https://xinhaojin.top两个域名都重定向到https://main.xinhaojin.top

#### 方法

使用cloudflare管理域名

修改DNS的A记录，地址解析到任意DNS解析服务器，如114.114.114,8.8.8.8,223.5.5.5等(建议使用阿里云公共DNS)，代理状态选择代理

![](https://cdn.staticaly.com/gh/xinhaojin/imgs-host@master/past/2021/05/image-6-1024x471.png)

选择页面规则，新建规则

![](https://cdn.staticaly.com/gh/xinhaojin/imgs-host@master/past/2021/05/image-7-1024x490.png)

选择转发URL，301重定向

![](https://cdn.staticaly.com/gh/xinhaojin/imgs-host@master/past/2021/05/image-8.png)

这就完成了从www.xinhaojin.top到main.xinhaojin.top的重定向

Done.