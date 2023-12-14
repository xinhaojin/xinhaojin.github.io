---
title: linux关闭占用端口的进程
tags: []
id: '1323'
categories:
  - - 杂
date: 2021-01-10 21:22:26
---

    [root@izuf64lmg25t75c8db3hxcz workspace\]# lsof -i:9999
    COMMAND   PID USER   FD   TYPE  DEVICE SIZE/OFF NODE NAME
    python3 11902 root    3u  IPv4 9435173      0t0  TCP *:distinct (LISTEN)
    python3 11902 root    5u  IPv4 9451420      0t0  TCP izuf64lmg25t75c8db3hxcz:distinct->115.200.48.232:ibm-mgr (CLOSE\_WAIT)
    [root@izuf64lmg25t75c8db3hxcz workspace\]# kill -9 11902

![](https://cdn.staticaly.com/gh/xinhaojin/imgs-host@master/past/2021/01/image-29-1024x173.png)

lsof -i:端口号

kill -9 pid

当然也有另一种不用查看pid的方法

kill -9 \`lsof -i:9999  awk 'NR==2{print $2}'\`