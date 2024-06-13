---
title: Android设置横屏/避免旋转屏幕导致avtivity重载
index_img: https://xinhaojin.github.io/imgs-host/past/2021/04/image-24.png
tags: []
id: '1320'
categories:
  - - 安卓
date: 2021-01-08 20:13:34
---

在AndroidManifest.xml的activity标签中添加如下片段：

android:screenOrientation="portrait"

横屏改成android:screenOrientation="landscape"

另一个问题，有时把程序切换到后台或旋转屏幕使布局改变时，会重载onCreate方法导致activity重载，避免这种情况的方法是在AndroidManifest.xml中activity标签中添加

android:configChanges="orientationkeyboardHiddenscreenSize"

![](https://xinhaojin.github.io/imgs-host/past/2021/04/image-24.png)