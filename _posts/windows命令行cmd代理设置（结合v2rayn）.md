---
title: windows命令行cmd代理设置（结合v2rayn）
tags: []
id: '2445'
categories:
  - - 杂
date: 2022-09-22 12:03:28
---

## v2rayn代理端口

    socks5:127.0.0.1:10808
    http:127.0.0.1:10809

## cmd临时代理（对当前cmd生效）

    set http_proxy=http://127.0.0.1:10809
    set https_proxy=http://127.0.0.1:10809

    set http_proxy=socks5://127.0.0.1:10808
    set https_proxy=socks5://127.0.0.1:10808

## cmd永久代理

    netsh winhttp import proxy source=ie

### 打开前后的对比

![](https://cdn.staticaly.com/gh/xinhaojin/imgs-host@master/past/2022/09/image-7.png)

### 测试代理

    curl www.google.com

### 控制代理开关，清除系统代理：关，自动配置系统代理：开。

![](https://cdn.staticaly.com/gh/xinhaojin/imgs-host@master/past/2022/09/image-6.png)