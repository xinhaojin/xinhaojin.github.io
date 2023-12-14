---
title: CentOS下Nginx静态资源代理配置
tags: []
id: '660'
categories:
  - - 杂
date: 2020-07-01 20:55:54
---

打开nginx.conf,我的路径是/www/server/nginx/conf

```
user root#第一行用户这里改成root
......
http
{
    ......
    server
    {
        listen 888;#监听端口，可修改，记得放行
        server_name localhost;#主机IP
        .......
        #有以下两行的话注释掉，取消身份验证
        #auth_basic "Authorization";
        #auth_basic_user_file /www/server/pass/phpmyadmin.pass;
        #添加一个location
        location /upload/
        {
            root /ftp;#路径映射
            autoindex on;//开启目录自动索引
        }
        ......
    }
    ......
}
```

如上配置之后重启Ngnix服务，http://IP:888/upload/test.txt就可以访问到/ftp/upload/test.txt

Done.