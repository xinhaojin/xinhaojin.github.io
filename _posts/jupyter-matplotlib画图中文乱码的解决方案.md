---
title: jupyter matplotlib画图中文乱码的解决方案
index_img: https://cdn.staticaly.com/gh/xinhaojin/imgs-host@master/past/2021/10/image-1-1024x416.png
tags: []
id: '2053'
categories:
  - - python
  - - 图像处理
date: 2021-10-14 11:19:36
---

在jupyter notebook中使用matplotlib画图时，中文字体显示乱码

![](https://cdn.staticaly.com/gh/xinhaojin/imgs-host@master/past/2021/10/image-1-1024x416.png)

看提示是缺少字体

#### 查找matplotlib字体配置文件的目录

import matplotlib
matplotlib.matplotlib\_fname()

![](https://cdn.staticaly.com/gh/xinhaojin/imgs-host@master/past/2021/10/image-2.png)

#### 下载字体，放入对应目录中

下载地址：https://xinhaojin.lanzoui.com/ibUMJvbo7ib

![](https://cdn.staticaly.com/gh/xinhaojin/imgs-host@master/past/2021/10/image-3.png)

#### 修改配置文件，启用新字体

打开matplotliblibrc

取消font.family前面的注释

取消font.sans-serif前面的注释，冒号后面增加SimHei

取消axes.unicode\_minus前面的注释，True改为False

![](https://cdn.staticaly.com/gh/xinhaojin/imgs-host@master/past/2021/10/image-4.png)

![](https://cdn.staticaly.com/gh/xinhaojin/imgs-host@master/past/2021/10/image-5.png)

#### 清除缓存

删除~/.cache/matplotlib

#### 重启服务器

中文出现

![](https://cdn.staticaly.com/gh/xinhaojin/imgs-host@master/past/2021/10/image-6.png)