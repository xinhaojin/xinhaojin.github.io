---
title: 使用PandoraNext项目Docker部署ChatGPT官网镜像站(支持Token登录)，同时为Chatgpt-web项目提供稳定代理服务
tags: []
categories:
  - 杂
  - 软件安装配置
date: 2024-01-17 14:23:01
---
## 背景
chatgpt-web项目<https://github.com/Chanzhaoyu/chatgpt-web>稳定性太差，经常出现代理失效的问题，在issues中看到pandoranext项目<https://github.com/pandora-next/deploy>可以自建代理为chatgpt-web提供服务，同时本身可作为chatgpt官网镜像站，支持accesstoken登录，无需付费apikey，相当于官网网页对话使用。
## 前置条件
一台境外服务器(可直连OpenAI官网，有Docker、Nginx)，一个半年以上的github账号，一个域名
## 部署镜像站
### 创建根目录
```shell
mkdir pandora-next
cd pandora-next
```
### 获取License
访问<https://dash.pandoranext.com>登录github账号获取授权![1705471881491](https://xinhaojin.github.io/imgs-host/20240117/1705471881491.jpg)
### 复制网页上的curl命令并执行
项目根目录下出现一个jwt文件
### 创建docker-compose.yml
把PANDORA_NEXT_LICENSE后的xxx改成刚刚获取的jwt文件中的值
```yml
version: '3'
services:
  pandora-next:
    image: pengzhile/pandora-next
    container_name: PandoraNext
    network_mode: bridge
    restart: always
    ports:
      - "8181:8181"
    environment:
      - PANDORA_NEXT_LICENSE=xxx
    volumes:
      - ./data:/data
```
### 创建其他文件（夹）
```shell
mkdir data
cd data
vi config.json
vi tokens.json
```
config.json
修改license_id，授权网页上有
```json
{
  "bind": "0.0.0.0:8181",
  "tls": {
    "enabled": false,
    "cert_file": "",
    "key_file": ""
  },
  "timeout": 600,
  "license_id": "-L1xxxxxxxi9w",
  "proxy_url": "",
  "public_share": false,
  "site_password": "",
  "setup_password": "",
  "server_tokens": true,
  "proxy_api_prefix": "",
  "isolated_conv_title": "*",
  "captcha": {
    "provider": "",
    "site_key": "",
    "site_secret": "",
    "site_login": false,
    "setup_login": false,
    "oai_username": false,
    "oai_password": false
  },
  "whitelist": null
}
```
tokens.json
可以修改预设登录方式，默认即可
```json
{
  "test-1": {
    "token": "access token / session token / refresh token",
    "shared": true,
    "show_user_info": false
  },
  "test-2": {
    "token": "access token / session token / refresh token",
    "shared": true,
    "show_user_info": true,
    "plus": true
  },
  "test2": {
    "token": "access token / session token / refresh token / share token",
    "password": "12345"
  }
}
```
### 在项目根目录下（docker-compose.yml所在文件夹）执行docker命令
```shell
docker-compose up -d
docker-compose ps #查看运行状态
```
### 放开8181端口，访问http://IP:8181
如果顺利的话，可以看到chatgpt镜像站登录页面，可以使用注册邮箱登录，或者使用accesstoken登录（accesstoken可以在https://chat.openai.com/api/auth/session获取）。
![1705473448886](https://xinhaojin.github.io/imgs-host/20240117/1705473448886.jpg)
如果确认端口放开但仍旧不能访问，使用docker logs命令查看日志信息，会有错误提示
```shell
docker logs PandoraNext
```
## 绑定域名
### 解析一个二级域名到服务器IP
如pass.xinhaojin.top
### 申请SSL证书
sudo apt install certbot python3-certbot-nginx
sudo certbot --nginx -d pass.xinhaojin.top
### 添加nginx配置
/etc/nginx/nginx.conf
添加两个server，对应修改ssl证书位置
```json
server {
        listen       80;
        server_name  pass.xinhaojin.top; 
        return 301 https://$host$request_uri;
    }
    
server {
        listen       443 ssl; 
        server_name   pass.xinhaojin.top;  
        
        ssl_certificate /etc/letsencrypt/live/pass.xinhaojin.top/fullchain.pem;
        ssl_certificate_key /etc/letsencrypt/live/pass.xinhaojin.top/privkey.pem;
    
        ssl_protocols TLSv1.2;  
        ssl_prefer_server_ciphers on;
        ssl_ciphers ECDHE-RSA-AES128-GCM-SHA256;  

        location / {
           proxy_pass http://0.0.0.0:8181;
           proxy_http_version 1.1;
           proxy_set_header Upgrade $http_upgrade;
           proxy_set_header Connection 'upgrade';
        }   
    } 
```
到此为止已经可以使用域名访问chatgpt镜像站了。
## 为chatgpt-web项目提供代理服务
### 修改pandora-next/data/config.json
添加代理前缀，注意英文数字都要有，长度>=8,这也是看了logs才发现的规则
```yml
"proxy_api_prefix": "proxy2openai",
```
### 重启docker容器
```shell
docker restart PandoraNext
```
代理接口为https://域名/前缀/backend-api/conversation,如<https://pass.xinhaojin.top/proxy2openai/backend-api/conversation>，访问正常会返回
```json
{
    "detail": "Method Not Allowed"
}
```
chatgpt-web部署参考之前的文章
<https://xinhaojin.github.io/2023/04/03/centos7%E4%BD%BF%E7%94%A8docker%E9%83%A8%E7%BD%B2chatgpt/>，只需在docker-compose.yml中修改API_REVERSE_PROXY即可
## 注意定期更新accesstoken