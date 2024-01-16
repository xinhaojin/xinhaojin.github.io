---
title: 列出局域网内可Ping通的IP
tags: []
id: '2132'
categories:
  - - python
date: 2022-02-25 14:52:48
---
```py
# -*- coding: utf-8 -*-
 
import platform
import os
import time
import threading
import socket
 
live_ip = 0
 
 
def get_os():
    os = platform.system()
    if os == "Windows":
        return "n"
    else:
        return "c"
 
 
def ping_ip(ip_str):
    cmd = ["ping", "-{op}".format(op=get_os()),
           "1", ip_str]
    output = os.popen(" ".join(cmd)).readlines()
    for line in output:
        if str(line).upper().find("TTL") >= 0:
            print("ip: %s 在线" % ip_str)
            global live_ip
            live_ip += 1
            break
 
 
def find_ip(ip_prefix):
    '''''
    给出当前的ip地址段 ，然后扫描整个段所有地址
    '''
    threads = []
    for i in range(1, 256):
        ip = '%s.%s' % (ip_prefix, i)
        threads.append(threading.Thread(target=ping_ip, args={ip, }))
    for i in threads:
        i.start()
    for i in threads:
        i.join()
 
 
def find_local_ip():
    """
    获取本机当前ip地址
    :return: 返回本机ip地址
    """
    myname = socket.getfqdn(socket.gethostname())
    myaddr = socket.gethostbyname(myname)
    return myaddr
 
 
if __name__ == "__main__":
    print("开始扫描时间: %s" % time.ctime())
    addr = find_local_ip()
    args = "".join(addr)
    ip_pre = '.'.join(args.split('.')[:-1])
    find_ip(ip_pre)
    print("扫描结束时间 %s" % time.ctime())
    print('本次扫描共检测到本网络存在%s台设备' % live_ip)
 ```

![](https://xinhaojin.github.io/imgs-host/past/2022/02/image-4.png)