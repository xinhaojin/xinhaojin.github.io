---
title: 给指定端口的网页应用绑定域名并强制SSL（申请证书+nginx配置）
tags: []
categories:
  - 杂
date: 2023-07-04 19:30:00
---
## 给指定端口的网页应用绑定域名并强制SSL（申请证书+nginx配置）

### 背景

刚刚注册了一个新的chatgpt账号，有5美金余额，又可以用API了，在甲骨文云的Ubuntu22服务器上使用docker部署了一下，之前也写过一篇相关的文章[centos7使用docker部署chatgpt - xinhaojin's blog](https://xinhaojin.github.io/2023/04/03/centos7使用docker部署chatgpt/)，默认部署在3002端口，只能使用http访问。

使用的是[Chanzhaoyu/chatgpt-web: 用 Express 和 Vue3 搭建的 ChatGPT 演示网页 (github.com)](https://github.com/Chanzhaoyu/chatgpt-web)这个项目，可以使用railway一键部署，绑定域名、使用HTTPS都很方便，但是不清楚railway使用会不会超过免费额度，还是决定用自己的服务器小小折腾一下，因为经常搞这套流程，所以还是记录一下。

### 解析域名到服务器

给域名添加A记录绑定服务器公网IP

### 申请SSL证书

``````shell
sudo apt install certbot python3-certbot-nginx
sudo certbot --nginx -d chat.xinhaojin.top
``````

输出：证书申请成功，但没法自动安装，因为没有在nginx配置中找到域名相关配置

``````text
Successfully received certificate.
Certificate is saved at: /etc/letsencrypt/live/chat.xinhaojin.top/fullchain.pem
Key is saved at:         /etc/letsencrypt/live/chat.xinhaojin.top/privkey.pem
This certificate expires on 2023-10-02.
These files will be updated when the certificate renews.
Certbot has set up a scheduled task to automatically renew this certificate in the background.

Deploying certificate
Could not install certificate

NEXT STEPS:
- The certificate was saved, but could not be installed (installer: nginx). After fixing the error shown below, try installing it again by running:
  certbot install --cert-name chat.xinhaojin.top

Could not automatically find a matching server block for chat.xinhaojin.top. Set the `server_name` directive to use the Nginx installer.
Ask for help or search for solutions at https://community.letsencrypt.org. See the logfile /var/log/letsencrypt/letsencrypt.log or re-run Certbot with -v for more details.
``````

### 修改nginx配置

/etc/nginx/nginx.conf原配置

``````nginx

user  nginx;
worker_processes  auto;

error_log  /var/log/nginx/error.log notice;
pid        /var/run/nginx.pid;


events {
    worker_connections  1024;
}


http {
    include       /etc/nginx/mime.types;
    default_type  application/octet-stream;

    log_format  main  '$remote_addr - $remote_user [$time_local] "$request" '
                      '$status $body_bytes_sent "$http_referer" '
                      '"$http_user_agent" "$http_x_forwarded_for"';

    access_log  /var/log/nginx/access.log  main;

    sendfile        on;
    #tcp_nopush     on;

    keepalive_timeout  65;

    #gzip  on;

    include /etc/nginx/conf.d/*.conf;
}
``````

#### 1.重定向80端口的HTTP请求到443端口HTTPS

``````nginx
server {
    listen       80;
    server_name  chat.xinhaojin.top; 
    return 301 https://$host$request_uri;
}
``````

#### 2.配置443端口使用SSL证书，配置SSL参数

``````nginx
server {
    listen 443 ssl; 
    server_name chat.xinhaojin.top;
    
    ssl_certificate /etc/letsencrypt/live/chat.xinhaojin.top/fullchain.pem;   
    ssl_certificate_key /etc/letsencrypt/live/chat.xinhaojin.top/privkey.pem; 
    
    ssl_protocols TLSv1.2;  
    ssl_prefer_server_ciphers on;
    ssl_ciphers ECDHE-RSA-AES128-GCM-SHA256;
}
``````

#### 3.在location中配置代理至应用

``````nginx
location / {
           proxy_pass http://0.0.0.0:3002;
           proxy_http_version 1.1;
           proxy_set_header Upgrade $http_upgrade;
           proxy_set_header Connection 'upgrade';
        }
``````

修改后配置

``````nginx

user  nginx;
worker_processes  auto;

error_log  /var/log/nginx/error.log notice;
pid        /var/run/nginx.pid;


events {
    worker_connections  1024;
}


http {
    include       /etc/nginx/mime.types;
    default_type  application/octet-stream;

    log_format  main  '$remote_addr - $remote_user [$time_local] "$request" '
                      '$status $body_bytes_sent "$http_referer" '
                      '"$http_user_agent" "$http_x_forwarded_for"';

    access_log  /var/log/nginx/access.log  main;

    sendfile        on;
    #tcp_nopush     on;

    keepalive_timeout  65;

    #gzip  on;
    server {
        listen       80;
        server_name  chat.xinhaojin.top; 
        return 301 https://$host$request_uri;
    }
    
    server {
        listen       443 ssl; 
        server_name   chat.xinhaojin.top;  
        
        ssl_certificate /etc/letsencrypt/live/chat.xinhaojin.top/fullchain.pem;
        ssl_certificate_key /etc/letsencrypt/live/chat.xinhaojin.top/privkey.pem;
    
        ssl_protocols TLSv1.2;  
        ssl_prefer_server_ciphers on;
        ssl_ciphers ECDHE-RSA-AES128-GCM-SHA256;  

        location / {
           proxy_pass http://0.0.0.0:3002;
           proxy_http_version 1.1;
           proxy_set_header Upgrade $http_upgrade;
           proxy_set_header Connection 'upgrade';
        }   
     }  

    include /etc/nginx/conf.d/*.conf;
}
``````

### 重载nginx配置

``````shell
nginx -s reload
``````

