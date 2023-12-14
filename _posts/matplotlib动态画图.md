---
title: matplotlib动态画图
index_img: 
tags: []
id: '1845'
categories:
  - - python
  - - 图像处理
date: 2021-07-27 15:52:37
---

方法一
```python
import matplotlib.pyplot as plt
fig, ax = plt.subplots()
y = []
for i in range(50):
    y.append(i)     # 每迭代一次，追加y数组的内容
    ax.cla()        # 清除键
    ax.bar(y, label='test', height=y, width=0.3)#画图
    ax.legend()#标注图例
    plt.pause(0.1)#暂停0.1s

![](https://cdn.staticaly.com/gh/xinhaojin/imgs-host@master/past/2021/07/20180611135854497.gif)
```
方法二
```python
import numpy as np
import matplotlib.pyplot as plt
plt.axis([0, 100, 0, 1])#设置坐标轴
plt.ion()#打开交互模式，同时显示多张图或动态图，关闭：plt.ioff()

xs = [0, 0]
ys = [1, 1]

for i in range(100):
    y = np.random.random()#0-1的随机数
    xs[0] = xs[1]
    ys[0] = ys[1]
    xs[1] = i
    ys[1] = y
    plt.plot(xs, ys)
    plt.pause(0.1)#暂停0.1s
```
![](https://cdn.staticaly.com/gh/xinhaojin/imgs-host@master/past/2021/07/20180611135923916.gif)

原文https://blog.csdn.net/xyisv/article/details/80651334

以上均为转载，感觉例子不太具有普适性，可能以下方法更简单更普遍
```python
for i in range(num_iterations):
        plt.cla()        # 清除键
        #...
        plt.plot(...)
        plt.pause(0.1)
```