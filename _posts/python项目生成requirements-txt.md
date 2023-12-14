---
title: python项目生成requirements.txt
tags: []
id: '1774'
categories:
  - - python
date: 2021-06-26 11:26:54
---

#### 导出当前环境所有依赖

    pip freeze > requirements.txt

但这些不一定都是当前项目必需的包，更好的方法是下面这种

#### 导出当前目录项目的依赖

    pip install pipreqs
    pipreqs . --encoding=utf8 --force

![](https://cdn.staticaly.com/gh/xinhaojin/imgs-host@master/past/2021/06/image-6.png)

![](https://cdn.staticaly.com/gh/xinhaojin/imgs-host@master/past/2021/06/image-7.png)