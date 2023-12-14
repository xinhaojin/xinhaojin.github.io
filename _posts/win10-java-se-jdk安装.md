---
title: win10 Java JDK安装（Oracle/OpenJDK）
tags: []
id: '1537'
categories:
  - - 软件安装配置
date: 2021-04-27 20:49:02
---

### Oracle版本（安装相对较复杂）

#### 下载

[https://www.oracle.com/java/technologies/javase-downloads.html](https://www.oracle.com/java/technologies/javase-downloads.html)

选择版本

![](https://cdn.staticaly.com/gh/xinhaojin/imgs-host@master/past/2021/04/image-33.png)

选择平台

![](https://cdn.staticaly.com/gh/xinhaojin/imgs-host@master/past/2021/04/QQ截图20210427200447-1024x375.png)

现在下载好像要Oracle账号了......没有的注册一个吧

#### 安装

选择路径即可

![](https://cdn.staticaly.com/gh/xinhaojin/imgs-host@master/past/2021/04/QQ截图20210427201237.png)

#### 配置环境变量

高级系统设置-环境变量-用户变量，添加或修改3个变量：

JAVA_HOME      C:\\Program Files\\Java\\jdk1.8.0_291\\

![](https://cdn.staticaly.com/gh/xinhaojin/imgs-host@master/past/2021/04/QQ截图20210427202303-1.png)

Path       %JAVA_HOME%\\bin;%JAVA_HOME%\\jre\\bin

![](https://cdn.staticaly.com/gh/xinhaojin/imgs-host@master/past/2021/04/QQ截图20210427202436.png)

CLASSPATH    .;%JAVA_HOME%\\lib\\dt.jar;%JAVA_HOME%\\lib\\tools.jar

![](https://cdn.staticaly.com/gh/xinhaojin/imgs-host@master/past/2021/04/QQ截图20210427202529.png)

#### 验证

cmd输入java有内容就对了

![](https://cdn.staticaly.com/gh/xinhaojin/imgs-host@master/past/2021/04/image-35.png)

java -version查看版本

![](https://cdn.staticaly.com/gh/xinhaojin/imgs-host@master/past/2021/04/image-36.png)

### OpenJDK版本（超级方便）

从GitHub下载，如[openjdk15](https://github.com/AdoptOpenJDK/openjdk15-binaries/releases/)，windows直接选择msi下载

![](https://cdn.staticaly.com/gh/xinhaojin/imgs-host@master/past/2021/04/image-47.png)

安装时下面两项默认没有勾选，可以选一下，然后一直下一步就行

![](https://cdn.staticaly.com/gh/xinhaojin/imgs-host@master/past/2021/04/image-48.png)

这样就安装好了

![](https://cdn.staticaly.com/gh/xinhaojin/imgs-host@master/past/2021/04/image-49.png)