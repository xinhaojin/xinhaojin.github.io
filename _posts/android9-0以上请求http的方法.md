---
title: Android9.0以上请求HTTP的方法
index_img: https://xinhaojin.github.io/imgs-host/past/2020/06/image-3.png
tags: []
id: '610'
categories:
  - - 安卓
date: 2020-06-01 12:32:56
---

> 在电脑模拟器上跑得好好的APP，用手机真机调试时，发现在发出HTTP请求时出现闪退，突然想起之前移动应用开发的老师讲过高版本SDK可能请求方式不一样，版本问题...OMG，才想起来我练手时都是在安卓4.4开发，现在要适配到安卓9，10...
> 
> OK，凡事不要慌，开始面向搜索引擎编程。

先放一张版本图

![](https://xinhaojin.github.io/imgs-host/past/2020/06/image-1.png)

在Android9.0以上的设备上，如果应用使用的是非加密的明文流量的HTTP网络请求，则会导致该应用无法进行网络请求，HTTPS则不会受影响，如果应用嵌套了webview，webview也只能使用HTTPS请求。

但是，我们可以通过APP配置来解除限制

在Manifest下的application标签下，与activity并列，加入：

<**uses-library android:name="org.apache.http.legacy" android:required="false"** />

在Manifest下的application标签内，加入：

**android****:usesCleartextTraffic=****"true"**

![](https://xinhaojin.github.io/imgs-host/past/2020/06/image-3.png)

到此，HTTP网络问题应该已经解决。

另外，我在调试时还发现有一个日历窗口弹不出，会直接finish整个activity，在Android4.4版本也没有问题，原因好像是：android 9.0上使用前台服务，需要添加权限

在Manifest里面加上这权限

<uses-permission android:name="android.permission.FOREGROUND_SERVICE" />

然后就解决啦