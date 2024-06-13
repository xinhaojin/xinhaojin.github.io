---
title: 'ubuntu终端source: not found'
tags: []
id: '2117'
categories:
  - - 杂
date: 2022-01-07 21:27:12
---

## 错误

shell脚本中含有source命令运行时提示 source: not found

运行 ls -l /bin/sh 后显示/bin/sh -> dash  
这说明是用dash来进行解析的。

## 解决方案

命令行执行：（需要root权限）

    dpkg-reconfigure dash
在界面中选择no  
再运行ls -l /bin/sh 后显示/bin/sh -> bash  
  
原文链接：https://blog.csdn.net/buynow123/article/details/51774018