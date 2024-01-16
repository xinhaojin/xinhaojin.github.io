---
title: 树莓派4B接超声波模块HC-SR05
tags: []
id: '1357'
categories:
  - - 树莓派
date: 2021-02-04 00:54:56
---

#### 接线

超声波模块HC-SR05相比于HC-SR04，多了一个OUT引脚，不太懂，没用到

所以，跟HC-SR04一样看待即可

VCC：接5V

GND：接地

Trig和Echo接任意两个GPIO口

#### 代码示例
```py
#-*- coding:UTF-8 -*-
import RPi.GPIO as GPIO
import time

#超声波引脚定义
EchoPin = 16   #GPIO16
TrigPin = 12   #GPIO12

#设置GPIO口为BCM编码方式
GPIO.setmode(GPIO.BCM)

#忽略警告信息
GPIO.setwarnings(False)

#超声波引脚初始化
def init():
    GPIO.setup(EchoPin,GPIO.IN)
    GPIO.setup(TrigPin,GPIO.OUT)

#超声波函数
def getDistance():
    GPIO.output(TrigPin,GPIO.HIGH)
    time.sleep(0.000015)
    GPIO.output(TrigPin,GPIO.LOW)
    while not GPIO.input(EchoPin):
        pass
    t1 = time.time()
    while GPIO.input(EchoPin):
        pass
    t2 = time.time()
    time.sleep(0.01)
    return ((t2 - t1)* 340 / 2) * 100

try:
    init()
    while True:
        print("distance is %d cm" % getDistance())
        time.sleep(0.3)
except KeyboardInterrupt:
    pass
GPIO.cleanup()
```
![](https://xinhaojin.github.io/imgs-host/past/2021/01/image-40.png)

发现这个传感器不是很灵敏，角度不能偏，遮挡物面需要够大且尽可能光滑，不太满意