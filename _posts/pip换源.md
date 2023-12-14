---
title: pip换源、设置多个源并添加信任
tags: []
id: '1292'
categories:
  - - 杂
date: 2021-01-06 23:48:32
---

#### linux

    cd ~ # 进入家目录  
    mkdir .pip # 新建.pip隐藏文件夹  
    cd .pip # 进入.pip文件夹  
    touch pip.conf # 新建pip.conf文件  
    vim pip.conf # 用vim编辑pip.conf文件

    [global]
    index-url=https://mirror.baidu.com/pypi/simple
    extra-index-url=https://pypi.tuna.tsinghua.edu.cn/simple/
                    http://mirrors.aliyun.com/pypi/simple/
                    
    [install]
    trusted-host=mirrors.baidu.com
                pypi.tuna.tsinghua.edu.cn
                mirrors.aliyun.com

#### windows

在这个目录下

![](https://cdn.staticaly.com/gh/xinhaojin/imgs-host@master/past/2021/08/image-2.png)

找到pip文件夹，没有pip文件夹就新建一个

在pip文件夹里面新建一个txt,内容同上

命名为pip.ini保存

#### 临时换源

    pip install xxx -i http://mirrors.baidu.com/pypi/simple --trusted-host mirrors.baidu.com