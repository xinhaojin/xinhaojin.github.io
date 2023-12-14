---
title: centos7使用docker部署chatgpt
tags: []
categories:
  - 杂
date: 2023-04-03 14:36:00
---

## 安装docker
```shell
sudo curl -fsSL https://get.docker.com | bash -s docker --mirror Aliyun
```
### 查看安装版本
```shell
docker -v
```
### 卸载docker
```shell
sudo yum remove docker-ce docker-ce-cli containerd.io
sudo yum remove docker #删除早期版本
sudo rm -rf /var/lib/docker #删除Docker数据目录
```
### 使用docker
```shell
systemctl start docker #启动docker服务
systemctl stop docker #关闭docker服务
sudo systemctl enable docker #开机启动
docker ps -a #查看所有容器
docker stop myname #停止容器名为'myname'的容器
```
## 安装docker-compose
发布地址 https://github.com/docker/compose/releases
```shell
sudo curl -L "https://github.com/docker/compose/releases/download/v2.2.2/docker-compose-$(uname -s)-$(uname -m)" -o /usr/local/bin/docker-compose
```
### 授予执行权限
```shell
sudo chmod +x /usr/local/bin/docker-compose
```
### 创建软连接
```
sudo ln -s /usr/local/bin/docker-compose /usr/bin/docker-compose
```
### 查看安装版本
```shell
docker-compose version
```
### 卸载
```shell
sudo rm /usr/local/bin/docker-compose
```
## 部署chatgpt项目
https://github.com/Chanzhaoyu/chatgpt-web
### 新建文件夹
```shell
mkdir chatgpt
cd chatgpt
```
### 新建docker-compose.yml
```yml
version: '3'

services:
  app:
    image: chenzhaoyu94/chatgpt-web # 总是使用 latest ,更新时重新 pull 该 tag 镜像即可
    ports:
      - 3002:3002
    environment:
      # 二选一
      #OPENAI_API_KEY: XXXXXX
      OPENAI_ACCESS_TOKEN: XXXXXXX
      API_REVERSE_PROXY: https://bypass.churchless.tech/api/conversation
      # 超时，单位毫秒，可选
      TIMEOUT_MS: 60000
```
### 环境变量
```yml
API 可用：

OPENAI_API_KEY 和 OPENAI_ACCESS_TOKEN 二选一
OPENAI_API_MODEL 设置模型，可选，默认：gpt-3.5-turbo
OPENAI_API_BASE_URL 设置接口地址，可选，默认：https://api.openai.com
OPENAI_API_DISABLE_DEBUG 设置接口关闭 debug 日志，可选，默认：empty 不关闭

ACCESS_TOKEN 可用：

OPENAI_ACCESS_TOKEN 和 OPENAI_API_KEY 二选一，同时存在时，OPENAI_API_KEY 优先
API_REVERSE_PROXY 设置反向代理，可选 https://bypass.churchless.tech/api/conversation 或 https://api.pawan.krd/backend-api/conversation
通用：

AUTH_SECRET_KEY 访问权限密钥，可选
MAX_REQUEST_PER_HOUR 每小时最大请求次数，可选，默认无限
TIMEOUT_MS 超时，单位毫秒，可选
SOCKS_PROXY_HOST 和 SOCKS_PROXY_PORT 一起时生效，可选
SOCKS_PROXY_PORT 和 SOCKS_PROXY_HOST 一起时生效，可选
HTTPS_PROXY 支持 http，https, socks5，可选
ALL_PROXY 支持 http，https, socks5，可选
```
OPENAI_API_KEY获取地址 https://platform.openai.com/overview
<br>ACCESS_TOKEN获取地址 https://chat.openai.com/api/auth/session
<br>免费账户选择ACCESS_TOKEN设置反向代理即可
### 部署
在新建的目录下执行
```shell
systemctl start docker
docker-compose up -d
```
默认容器名为chatgpt-web-app-1
### 放行端口
检查系统是否安装了firewalld
```shell
sudo systemctl status firewalld
```
如果该命令返回firewalld服务的状态，则说明firewalld已经安装。如果没有安装，请使用以下命令安装firewalld
```shell
sudo yum install -y firewalld
```
打开需要放行的端口（例如3002）
```shell
sudo firewall-cmd --zone=public --add-port=3002/tcp --permanent
```
重新加载防火墙规则，以便它们在系统重启后仍然生效
```shell
sudo firewall-cmd --reload
```
云服务器额外在控制台放行端口



