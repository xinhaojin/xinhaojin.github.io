---
title: 甲骨文云永久免费服务器允许root远程ssh登录
tags: []
categories:
  - - 软件安装配置
date: 2023-05-20 13:30:00
---

## 甲骨文云永久免费服务器允许root远程ssh登录

虽然操作过好多次，但还是会忘记，每次都要去查，还是自己记录一遍吧

### 创建新的服务器

甲骨文云在不同地区的登录地址是不一样的，我的登录地址是[Cloud Sign In (oracle.com)](https://www.oracle.com/cloud/sign-in.html?redirect_uri=https%3A%2F%2Fcloud.oracle.com%2Fcompute%2Finstances%3Fregion%3Dap-seoul-1)

还需要先填名字，这是外国人很奇怪的一点，记不住设置的什么名字就登不上了

![](https://cdn.staticaly.com/gh/xinhaojin/imgs-host@master/20230704/微信截图_20230704142856.1tmnrvwfijwg.webp)

在计算-实例页面选择创建实例

![](https://cdn.staticaly.com/gh/xinhaojin/imgs-host@master/20230704/image.7is8xyw41fw0.webp)

映像和配置选择免费的就好

![](https://cdn.staticaly.com/gh/xinhaojin/imgs-host@master/20230704/image.3dlmlm2nlqm0.webp)

保存密钥对到本地

![](https://cdn.staticaly.com/gh/xinhaojin/imgs-host@master/20230704/image.2s5dhhh68g60.webp)

创建成功后在 https://ping.chinaz.com/  ping一下服务器公网IP 本机也ping一下检查是否畅通 本地网络ping畅通的服务器才有意义

![](https://cdn.staticaly.com/gh/xinhaojin/imgs-host@master/20230704/image.3jbm4dedc500.webp)

### SSH连接

``````shell
ssh -i <private_key> user@remote_host
``````

默认用户名会在实例创建时显示，Ubuntu系统是ubuntu，Centos是opc

有时候本地即使ping通也很难建立ssh连接，因为ssh使用TCP协议，而ping使用的是ICMP，所以可以使用网络环境更好的其他云服务器来建立连接，私钥权限不能放开，要设置成400

``````shell
chmod 400 <private_key>
``````

登录成功后修改root密码

``````shell
sudo -i
passwd
``````

设置允许root远程ssh登录

``````shell
sudo sed -i 's/^#\?PermitRootLogin.*/PermitRootLogin yes/g' /etc/ssh/sshd_config;
sudo sed -i 's/^#\?PasswordAuthentication.*/PasswordAuthentication yes/g' /etc/ssh/sshd_config;
sudo service sshd restart
``````























