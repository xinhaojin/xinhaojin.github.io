---
title: Nginx+PHP配置
tags: []
id: '766'
categories:
  - - 杂
date: 2020-08-11 16:58:42
---

#### 在nginx.conf中添加一个server

    server {
        listen 999;
        server\_name blog.xinhaojin.top;
        location ~ \\.php?.\*$ {
            root /www/wwwroot/blog.xinhaojin.top;#php文件目录
            fastcgi\_pass unix:/tmp/php-cgi-70.sock;#.sock的形式
            fastcgi\_param SCRIPT\_FILENAME $document\_root$fastcgi\_script\_name;
            include fastcgi\_params;
        }
    }

#### 需要注意的是php-fpm.conf与nginx.conf的监听方式是否相同

方式1，统一成ip+port的形式:

    php-fpm.conf: 

     listen = 127.0.0.1:9000 

    nginx.conf:  

    fastcgi\_pass 127.0.0.1:9000;

  
方式2，统一成.sock的形式:

    php-fpm.conf:  

    listen = /tmp/php-fpm.sock 

    nginx.conf: 

    fastcgi\_pass unix:/tmp/php-fpm.sock;

然后http://xx.xx.xx.xx/test.php就可以正常运行了

#### Done.