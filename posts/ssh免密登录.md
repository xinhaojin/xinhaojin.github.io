---
title: ssh免密登录
tags: []
categories:
  - 软件安装配置
date: 2023-12-14 16:29:50
---

### 本地生成公钥私钥
```shell
ssh-keygen
```
### 把本地的.ssh/id_rsa.pub内文本内容粘贴到远程主机的.ssh/authorized_keys中新建一行