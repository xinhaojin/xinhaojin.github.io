---
title: 解决wordpress页面改为固定链接后403的问题
tags: []
id: '1803'
categories:
  - - 杂
date: 2021-07-03 21:33:40
---

修改nginx配置文件，在站点目录字段后添加
```php
if (-f $request_filename/index.html){
    rewrite (.*) $1/index.html break;
    }
    if (-f $request_filename/index.php){
    rewrite (.*) $1/index.php;
    }
    if (!-f $request_filename){
    rewrite (.*) /index.php;
    }
    rewrite /wp-admin$ $scheme://$host$uri/ permanent;
```
![](https://xinhaojin.github.io/imgs-host/past/2021/07/image.png)