---
title: >-
  pytorch加载MNIST数据集图片Error #15: Initializing libiomp5md.dll, but found
  libiomp5md.dll already initialized.
tags: []
id: '1451'
categories:
  - - pytorch
  - - python
date: 2021-04-01 19:06:09
---

添加以下代码
```py
import os
os.environ\['KMP\_DUPLICATE\_LIB\_OK'\]='True'
#允许问题描述中dll副本存在
```