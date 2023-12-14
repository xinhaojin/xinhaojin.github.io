---
title: Linux设置ROOT用户以及允许第三方SSH登录
tags: []
id: '665'
categories:
  - - 杂
date: 2020-07-03 17:54:36
---

#### 切换到root帐号

    sudo -i

#### 设置root密码

    passwd

然后会要求输入新密码，然后再重复一次密码，输入密码的时候不会显示出来，所以直接输入密码，然后回车，再然后重复输入密码回车

#### 开启SSH权限

    vim /etc/ssh/sshd\_config #打开 ssh 配置文件
    PermitRootLogin yes #默认为no，需要开启root用户访问改为yes
    PasswordAuthentication yes    #默认为no，改为yes开启密码登陆

#### 重启服务器

    reboot