---
title: VL53L0X激光测距（树莓派）
index_img: https://xinhaojin.github.io/imgs-host/past/2021/04/image-32.png
tags: []
id: '1531'
categories:
  - - 树莓派
date: 2021-04-27 18:35:07
---

上篇曾提到超声波测距非常不准，所以花10块钱在淘宝买了个激光测距传感器VL53L0X（便宜才买的），特此记录配置过程

#### 接线

有4个引脚需要接

VIN接3-5V电源；GND接地；SDA和SCL接树莓派上对应的SDA和SCL

![](https://xinhaojin.github.io/imgs-host/past/2021/04/image-32.png)

那么怎么判断接线是否正确呢？可以查看I2C设备地址，如果接线后I2C设备多了一个，那就基本没错了

开启I2C

    sudo raspi-config #依次选择Interfacing Options---I2C---Enabled

查看I2C设备地址

    sudo i2cdetect -y 1

#### 安装环境

VL53L0X是有一个专门的包的，可以通过pip安装

但我尝试pip安装后提示没有可用的版本,查看pypi后发现仅支持到python3.5，而我用的是python3.7

![](https://xinhaojin.github.io/imgs-host/past/2021/04/image-31-1024x535.png)

抱着侥幸的心理下载3.5的包，本地安装时把包名改成3.7的，安装成功，但是跑了样例代码还是不对，判断下来是安装的问题，于是又把这个包卸载了

#### 正确方法

换个安装方法，上[GitHub](https://github.com/pimoroni/VL53L0X-python)一搜，果然有源码安装方法

    sudo apt-get install build-essential python-dev#前置条件
    pip install git+https://github.com/pimoroni/VL53L0X-python.git
    cd your_directory#选择你的目录
    git clone https://github.com/pimoroni/VL53L0X_rasp_python.git
    cd VL53L0X-python
    make

然后在VL53L0X-python文件夹中，有一个VL53L0X_example.py，就是样例代码，能跑通就可以了

整体精度表现碾压超声波没有悬念