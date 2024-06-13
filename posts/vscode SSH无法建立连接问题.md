---
title: vscode SSH无法建立连接问题
date: 2023-04-14 19:40:12
tags: []
categories: 
  - 软件安装配置
---
## 问题
其他SSH工具能连上，vscode的RemoteSSH插件显示无法建立连接
## 解决方法
在`C:\Users\yourname\.ssh`下有一个known_hosts文件，使用记事本打开，删除你要连接的IP相关的所有内容即可
<br>
![192903](https://xinhaojin.github.io/imgs-host/20230414/192903.6tmyxirogzs0.webp)