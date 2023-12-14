---
title: python3 socket自动重连、心跳包
tags: []
id: '1374'
categories:
  - - python
date: 2021-02-14 20:20:44
---

#### 客户端异常处理

服务端一般都不会有网络问题，而客户端有可能因为网络不稳定等原因会导致连接断开，因此有时需要设置客户端自动重连

客户端无法直接判断连接是否断开，如果连接断开，则继续操作会产生异常，在异常处理中进行重连即可
```py
#!/usr/bin/python3
# -*- coding: UTF-8 -*-
import sys
import time
from socket import *

HOST = '127.0.0.1' #服务端ip
PORT = 9999 #服务端端口号
BUFSIZ = 1024
ADDR = (HOST, PORT)
tcpCliSock=None
def connect():
    global tcpCliSock
    tcpCliSock = socket(AF_INET, SOCK_STREAM) #创建socket对象
    tcpCliSock.connect(ADDR) #连接服务器

while True:
    try:
        connect()
        while True:
            data = tcpCliSock.recv(BUFSIZ) #读取消息
            #data处理
    except:
        tcpCliSock.close()
        continue
```
这样处理之后，一旦网络恢复正常，就会自动重连

#### 在服务端中判断客户端是否断开连接
```py
from socket import *
import time
......
try:
    conn, addr = tcpS.accept() 
    conn.settimeout(10)#10s没有收到数据，进入timeout异常处理
    data = conn.recv(1024)
    if len(data)==0:
        #客户端主动关闭连接时，返回空字符串
except timeout:
    conn.close()
    #其他处理
```
这里涉及了两种情况：

##### 1.客户端主动关闭连接，服务端读取数据时会返回空字符串

##### 2.客户端网络异常导致连接断开，这时服务端recv会一直等待，可以设置超时时间，超时后在异常处理中断开连接，进行重连

#### 心跳包

上面这种处理的缺点是就算连接一切正常也会在无操作一段时间后进行重连，这样就多了很多不必要的重连，一种更好的相对复杂的策略是心跳包机制，约定每隔一段时间发送指定数据表明连接正常，如果超过约定时间未收到该心跳包，则认为连接断开，此时重连。显然这需要用到定时器，为了不影响主线程运行，应该在子线程中实现心跳包比较合适。
```py

class myThread (threading.Thread):
    def __init__(self, threadID, name, counter):
        threading.Thread.__init__(self)
        self.threadID = threadID
        self.name = name
        self.counter = counter
    def run(self):
        print ("心跳启动")
        heartBeat()
        print ("心跳停止")

def heartBeat():
    while not exitFlag:
        time.sleep(20)#20s发一次心跳包
        #发送数据

# 创建新线程
thread1 = myThread(1, "heartbeat", 1)

# 开启新线程
thread1.start()

#原来写的代码在此处不受干扰
```
设置心跳包之后，配合超时检测就可以实现仅在必要情况下重连，最大程度维护了长连接。

注意心跳包间隔略小于超时时间即可，如心跳包间隔20s，timeout设置为30s