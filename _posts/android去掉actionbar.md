---
title: android去掉ActionBar
tags: []
id: '1342'
categories:
  - - 安卓
date: 2021-01-17 22:54:16
---

只需要把style.xml中的默认主题修改一下即可

(style.xml在app-res-values目录下)

    <style name="AppTheme" parent="Theme.AppCompat.Light.DarkActionBar">

修改成

    <style name="AppTheme" parent="Theme.AppCompat.Light.NoActionBar">
完成

![](https://xinhaojin.github.io/imgs-host/past/2021/01/image-37.png)