---
title: opencv中文路径、图片名的读写
tags: []
id: '2198'
categories:
  - - python
  - - 图像处理
date: 2022-03-08 18:58:47
---

## 读

一般写法
```python
import cv2
img=cv2.imread('中文目录/中文名称.jpg')
```
报错

![](https://xinhaojin.github.io/imgs-host/past/2022/03/image-17.png)

改进写法
```py
import cv2
import numpy as np
img=cv2.imdecode(np.fromfile('中文目录/中文名称.jpg', dtype=np.uint8), -1)
```
## 写

一般写法
```python
cv2.imwrite('中文目录/中文名称.jpg',img)
```
不报错，但没用

改进写法，需要额外再写一遍后缀名
```python
cv2.imencode('.jpg',img)[1].tofile('中文目录/中文名称.jpg')
```