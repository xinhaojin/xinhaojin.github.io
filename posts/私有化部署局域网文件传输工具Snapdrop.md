---
title: 私有化部署局域网文件传输工具Snapdrop
tags: []
categories:
  - 软件安装配置
date: 2024-06-04 22:55:56
---
## 前言
之前想过很多次做一个局域网文件传输工具，但是始终停留在需求阶段哈哈哈哈哈，毕竟啥也不会。

网上有很多软件可以用，但基本都是要安装各平台的软件才行，作为偶尔用一下的工具，必不可能有被安装在设备上的资格。

之前用过一个很好用的工具Snapdrop，以网页形式存在，https://Snapdrop.net/ ，完全满足了我的需求，但后面有段时间无法访问，我以为他凉了。

因此我又重新想做一个类似的东西，结果发现这个网页现在又可以用了，关于页指向GitHub https://github.com/RobinLinus/snapdrop ，竟然是开源的，大喜过望，于是有了本篇。
## 前置环境
docker，nginx，域名
## docker配置
```shell
docker pull linuxserver/snapdrop
docker run -d --restart=always --name snapdrop -p 8088:80 linuxserver/snapdrop #8088是自定义端口
```
然后就可以在客户端双方访问http://ip:8088 来使用，若要绑定域名，进行nginx设置
## 申请SSL证书
先解析一个二级域名如file.xinhaojin.top到主机上,然后申请证书
```
sudo apt install certbot python3-certbot-nginx
sudo certbot --nginx -d file.xinhaojin.top
```
## nginx配置
在http标签中添加两个server
```json
  server {
	    listen 80;
	    server_name file.xinhaojin.top;
	    return 301 https://$host$request_uri;
	}

	server {
	    listen 443 ssl;
	    server_name file.xinhaojin.top;
	
	    ssl_certificate /etc/letsencrypt/live/file.xinhaojin.top/fullchain.pem;
	    ssl_certificate_key /etc/letsencrypt/live/file.xinhaojin.top/privkey.pem;
	    ssl_protocols TLSv1.2 TLSv1.3;
	    ssl_prefer_server_ciphers off;
	    ssl_ciphers ECDHE-RSA-AES128-GCM-SHA256:ECDHE-RSA-AES256-GCM-SHA384;
	
	    location / {
	        proxy_pass http://127.0.0.1:8088;  # Change to your Spring Boot app's IP and port
	        proxy_connect_timeout 300;
	        proxy_set_header Connection "upgrade";
	        proxy_set_header Upgrade $http_upgrade;
	        proxy_set_header X-Forwarded-for $remote_addr;
	}
	
	location ~ /.well-known {
	    allow all;
	}
```
重启nginx
```https://file.xinhaojin.top
sudo nginx -s reload
sudo systemctl restart nginx
```
## Done
现在就拥有了一个私有化的局域网文件传输工具，https://file.xinhaojin.top