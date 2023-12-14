---
title: ssh反向代理实现内网穿透
tags: []
id: '1409'
categories:
  - - 杂
date: 2021-03-05 15:42:21
---

#### 环境说明
| 服务器 |      IP     |  用户名 | 备注|
|--------|:-------------:|--------:| :-------------:|
| A |  192.168.3.241 | pi |树莓派4b，Raspbian GNU/Linux 10，处于内网，需要被远程访问 |
| B |  101.133.xxx.xxx | root |阿里云服务器，centos7，有公网IP |


#### 配置方法

在B上执行

    vi /etc/ssh/sshd_config

找到Gatewayports,取消注释，改为yes

![](https://cdn.staticaly.com/gh/xinhaojin/imgs-host@master/past/2021/02/image.png)

保存，重启服务器

建立A到B的反向代理,这里把A的22端口映射到了B的2222端口，记得放行B的端口

在A上执行

    sudo ssh -fCNR 2222:localhost:22 root@101.133.xxx.xxx

然后输入B的root用户密码即可

检验一下是否成功

在B上查看

    netstat -anp  grep 2222

![](https://cdn.staticaly.com/gh/xinhaojin/imgs-host@master/past/2021/02/image-1-1024x68.png)

有结果说明已经成功

此时可以在任意一台能访问公网的电脑上远程访问A

    ssh -p 2222 pi@101.133.xxx.xxx

![](https://cdn.staticaly.com/gh/xinhaojin/imgs-host@master/past/2021/02/image-2.png)

要注意的是，此处**用到的是A的用户名和密码，B的IP和端口号**

这种方法的缺点是不稳定，ssh连接容易断开

#### 改进的方法

改进的方向是：**开机自启，免密登录，断线重连**。需要用到autossh

安装autossh,在A上执行

    sudo apt-get install autossh

安装成功后，把上面的ssh命令改成autossh格式的,用到了另一个端口2223，用于监听ssh连接情况，记得放行

    autossh -M 2223 -NR 2222:localhost:22 root@101.133.xxx.xxx

输入密码即可同样可在B上用netstat -anp grep 2222命令查看是否成功

    netstat -anp  grep 2222

继续改进，实现免密登录,在A上分别执行

    ssh-keygen
    ssh-copy-id root@101.133.xxx.xxx

要输入一次密码，成功后可直接免密登录B,如下图

    ssh root@101.133.xxx.xxx

![](https://cdn.staticaly.com/gh/xinhaojin/imgs-host@master/past/2021/02/image-3.png)

接下来使用autossh就可以免密码了，把autossh命令设置成开启启动

    sudo vim /etc/rc.local

在exit0前面增加一行

    sudo su pi -c "autossh -M 2223 -fNR 2222:localhost:22 root@101.133.xxx.xxx"

![](https://cdn.staticaly.com/gh/xinhaojin/imgs-host@master/past/2021/02/image-4.png)

这里要注意执行命令的用户，如果直接把上面的autossh写在这里是不行的，因为写在rc.local里的命令是root用户执行的，我们上面免密登录的执行用户是是普通用户pi，所以要用sudo su [用户名] -c "[命令]"

autossh的参数中添加了f，意思是后台执行

这样配置之后，只要A开机联网，B正常运行，就可以在任何能访问到B的主机上用ssh远程登录A的pi用户

**用到的是A的用户名和密码，B的IP和端口号**

#### 结语

这个功能当然不仅限于远程ssh登录，根本上是NAT端口映射，如内网上A主机的8080端口上的数据流是我的目标，但是公网无法访问，通过这种方法，我就可以把8080端口映射到公网服务器B的指定端口如9090上，相当于一次转发，这时访问B_IP:9090等价于访问A_IP:8080，缺点是相应的延时也会增大，如果转发数据较大，对公网服务器的带宽也是一种消耗

Done.