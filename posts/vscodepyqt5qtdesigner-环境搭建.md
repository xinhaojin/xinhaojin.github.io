---
title: VSCode+PyQt5+QtDesigner 环境搭建
tags: []
id: '371'
categories:
  - - 软件安装配置
date: 2020-04-30 15:33:06
---

## 一、安装PyQt5和PyQt5-tools

    pip3 install -i https://mirrors.aliyun.com/pypi/simple/ PyQt5
    pip3 install -i https://mirrors.aliyun.com/pypi/simple/ PyQt5-tools

## 二、找到QtDesigner 安装路径

![](https://xinhaojin.github.io/imgs-host/past/2020/04/image.png)

按黄色警告提示的路径中查找designer.exe生成桌面快捷方式，方便日后制作.ui文件。

## 三、打开designer.exe，绘制UI界面

![](https://xinhaojin.github.io/imgs-host/past/2020/04/22-1024x781.png)

绘制完后保存到你准备制作该项目的文件夹中，比如：welcome.ui。

## 四、用VSCode转化.ui文件为.py文件

首先要为vscode安装扩展

![](https://xinhaojin.github.io/imgs-host/past/2020/04/kuozhan.png)

修改PYQT Intergration的扩展设置，添加Qtdesigner路径

![](https://xinhaojin.github.io/imgs-host/past/2020/04/image-1.png)

右键UI文件可以直接选择编辑、编译、预览

![](https://xinhaojin.github.io/imgs-host/past/2020/04/image-2.png)

## Done.