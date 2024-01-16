---
title: frp内网穿透
tags: []
id: '2342'
categories:
  - - 杂
date: 2022-04-14 13:58:34
---

## 目的

在公网访问实验室服务器

## 环境

服务端：腾讯云服务器（centos7）,有公网IP

客户端：内网服务器（ubuntu20.04）

## 下载frp

两台机器都下载同一个东西

[Releases · fatedier/frp (github.com)](https://github.com/fatedier/frp/releases)

    wget https://github.com/fatedier/frp/releases/download/v0.38.0/frp_0.38.0_linux_amd64.tar.gz
    tar -zxvf frp_0.38.0_linux_amd64.tar.gz
    cd frp_0.38.0_linux_amd64

## 服务端配置

### 修改frps.ini

    [common]
    bind_port = 7000

### 放行端口

    firewall-cmd --zone=public --add-port=7000/tcp --permanent
    firewall-cmd --zone=public --add-port=6000/tcp --permanent

云服务器还需要在管理面板安全策略中再次放行

### 启动服务

./frps -c ./frps.ini

## 客户端配置

### 修改frpc.ini

    [common]
        server_addr = xxx.xxx.xxx.xxx # frps 所在服务器的公网 IP
        server_port = 7000

    [ssh]
    type = tcp
    local_ip = 127.0.0.1    # 本地需要暴露到公网的服务地址
    local_port = 22         # 本地需要暴露到公网的服务地址
    remote_port = 6000      # 在 frp 服务端监听的端口，访问此端口的流量将会被转发到本地服务对应的端口

### 放行端口（这里是22，已经默认放行）

    firewall-cmd --zone=public --add-port=22/tcp --permanent

### 启动客户端程序

    ./frpc -c ./frpc.ini

## 远程登录

    ssh username@xxx.xxx.xxx.xxx -p 6000

![](https://xinhaojin.github.io/imgs-host/past/2022/04/image-24.png)

用内网服务器的用户名和密码

用公网服务器的IP和端口

## 设置开启自启动

### 注册服务

服务端

    sudo vim /lib/systemd/system/frps.service

    [Unit]
    Description=fraps service
    After=network.target network-online.target syslog.target
    Wants=network.target network-online.target

    [Service]
    Type=simple

    #启动服务的命令（此处写你的frps的实际安装目录）
    ExecStart=/your/path/frps -c /your/path/frps.ini

    [Install]
    WantedBy=multi-user.target

### 启动服务

    sudo systemctl start frps#启动
    sudo systemctl enable frps#自启动

客户端一模一样的操作，只不过把frps改成frpc

### 其他

    sudo apt install firewall                 //安装防火墙
    sudo systemctl start firewalld.service   //开启防火墙
    sudo systemctl stop firewalld.service   //关闭防火墙
    sudo systemctl enable firewalld.service  //设置开机自动启动
    sudo systemctl disable firewalld.service   //设置关闭开机制动启动
    sudo firewall-cmd --reload  //在不改变状态的条件下重新加载防火墙