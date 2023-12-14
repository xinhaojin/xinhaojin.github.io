---
title: hexo博客迁移
tags: []
categories:
  - 杂
date: 2023-07-04 19:30:00
---
## hexo博客迁移

### 背景

最开始使用wordpress写博客，方便是方便，但感觉不够干净、不够优雅，所以后来开始写markdown格式的博客，使用hexo部署在github上，但是平时使用的电脑有很多台，GitHub上公开的又只有public文件夹的静态页面，没有把markwodn文件一并提交，因此也不能通过github来同步，所以就想干脆把hexo项目放在云服务器上，要写新文章就写好markdown上传到云服务器去提交，也省的在各个电脑上配置。本文简要介绍把hexo项目迁移到云服务器上的步骤。

### 复制文件夹上传到云服务器

除了node_modules,其他文件全部上传

![](https://cdn.staticaly.com/gh/xinhaojin/imgs-host@master/20230704/image.71u2etwvmq40.webp)

### 安装nodejs、Hexo和其他依赖

``````shell
curl -fsSL https://deb.nodesource.com/setup_18.x | sudo -E bash -
sudo apt-get install -y nodejs
sudo npm install -g hexo-cli
sudo npm install
``````

### 建立git连接

``````shell
git config --global user.name "xinhaojin"
git config --global user.email "xinhaojin@qq.com"
ssh-keygen -t rsa -b 4096 -C "xinhaojin@qq.com"
``````

把公钥/root/.ssh/id_rsa.pub中的内容添加到github-setting-ssh key

``````shell
git init
git remote add origin https://github.com:xinhaojin/xinhaojin.github.io.git
``````

### 更新博客
上传markdown到blog/source/_posts文件夹
然后就可以发布新文章了
```shell
hexo clean
hexo g
hexo d
```
markdown模板
```
---
title: 文章标题
tags: []
categories:
  - - 软件安装配置
date: 2023-05-20 13:30:00
---
## xxx
### xxx
![](url)
<url>
```

