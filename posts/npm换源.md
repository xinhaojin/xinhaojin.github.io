---
title: npm换源
tags: []
categories:
  - 软件安装配置
date: 2023-03-21 14:07:56
---
# npm换源
## 查看当前源
```shell
npm config get registry
```
默认是指向 https://registry.npmjs.org/，也就是官方源。
## 更换源
新淘宝源，一些文章还是写着旧的淘宝 NPM 镜像 registry.npm.taobao.org，但它已于 2022 年 05 月 31 日 废弃，读者需要更换为新的 registry.npmmirror.com 源。
```shell
npm config set registry https://registry.npmmirror.com/
```
这个配置会持久化保存到 ~/.npmrc 文件中，你也可以通过手动改该文件来修改配置。