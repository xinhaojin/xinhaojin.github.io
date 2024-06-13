---
title: CentOS7.4+DockerCE+酷q=QQ机器人
tags: []
id: '278'
categories:
  - - 杂
date: 2020-04-03 18:03:08
---

#### 一、安装及运行docker

    sudo yum install docker#安装

    sudo systemctl start docker#运行

#### 二、下载酷Q镜像，后台运行容器

    sudo docker pull coolq/wine-coolq#镜像文件较大，大约2GB

    sudo docker images#查看酷Q镜像详情

#### 三、创建一个用于存放coolq数据的目录，用于持久化存放coolq（酷Q应用）的数据

    sudo mkdir /coolq-data#创建目录

#当然可以自定义任意目录，但要记住，因为之后的酷q应用文件.cpk都是要上传到此目录下的app文件夹下的

#### 四、后台运行酷Q容器

    sudo docker run --name=coolq -d -p 8080:9000 -v /coolq-data:/home/user/coolq -e VNC_PASSWD=12345678 -e COOLQ_ACCOUNT=2333456789 coolq/wine-coolq

#其中部分参数根据自己的需求替换：

#远程监听端口

8080(9000映射成8080)

#数据存放位置

/coolq-data（/home/user/coolq 映射到/coolq-data）

#远程访问密码

12345678

#机器人QQ 帐号

2333456789

#远程监听的端口，也就是自定义的web端口需要在服务器防火墙和云安全组都放行，例如阿里云服务器，我需要在宝塔面板和阿里云控

制台都放行8080端口

#-d 指的是后台运行容器

#-v后面接的就是将服务器的自己定义的coolq数据目录映射到容器内的/home/user/coolq目录

#-e是设置容器的系统环境

#因为之后要通过vnc连接，所以要指定VNC_PASSWD

#酷Q要登录的qq小号(充当机器人)也可以通过-e COOLQ_ACCOUNT 去指定

#### 五、登录vnc进行酷Q操作

#在浏览器中访问 http://你的服务器IP:你定义的web端口,即可看到noVNC远程登录页面

#点击Connect进行连接，在弹出的密码框中输入密码,可以看到酷QAir 的登录界面

#输入QQ密码登录

#在登录后，右键点击悬浮窗 -> 你的 QQ 昵称 -> 勾选[开机启动]和[快速登录]即可保证酷Q能自动登录

#### 六、设置docker服务器开机自启动

    systemctl is-enabled docker.service #检查服务是否开机启动  

    systemctl enable docker.service#将服务配置成开机启动  

    systemctl start docker.service#启动服务  

    systemctl disable docker.service#禁止开机启动  

    systemctl stop docker.service#停止  

    systemctl restart docker.service#重启

#### 七、设置docker容器开机自启动

    docker update --restart=always xxx#xxx为容器name，在这里是coolq

#### 八、结语

酷q官方推荐是Ubuntu16.04兼容性好一些，我的CentOS7.4目前测试的酷q应用还是可以用的

远程SSH连接服务器推荐使用FinalShell,海外服务器有内置加速，字符显示也没有乱码，还可以直接本地拖拽上传文件

复制以上命令时注意‘-’可能会有变化，出现问题时优先考虑重新手打