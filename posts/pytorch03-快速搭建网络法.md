---
title: PyTorch03-快速搭建网络法
tags: []
id: '2230'
categories:
  - - pytorch
date: 2022-04-03 14:32:45
---

原文：[快速搭建法 - PyTorch 莫烦Python (mofanpy.com)](https://mofanpy.com/tutorials/machine-learning/torch/fast-nn/)

## 方法一
```py
import torch
import torch.nn.functional as F
class Network(torch.nn.Module):
    def __init__(self,n_features,n_hidden,n_output):
        super(Network,self).__init__()
        self.hidden = torch.nn.Linear(n_features,n_hidden)
        self.predict = torch.nn.Linear(n_hidden,n_output)

    def forward(self,x):
        x=F.relu(self.hidden(x))
        x=self.predict(x)
        return x

net1=Network(2,10,2)
```
## 方法二（快速）
```py
import torch
net2=torch.nn.Sequential(
    torch.nn.Linear(2,10),
    torch.nn.ReLU(),
    torch.nn.Linear(10,2)
)
```
## 比较
```py
print(net1)
print(net2)

Network(
  (hidden): Linear(in_features=2, out_features=10, bias=True) 
  (predict): Linear(in_features=10, out_features=2, bias=True)
)
Sequential(
  (0): Linear(in_features=2, out_features=10, bias=True)
  (1): ReLU()
  (2): Linear(in_features=10, out_features=2, bias=True)
)
```
第一种方法由我们指定了网络层名，第二种方法则是数字索引，第一种方法中Relu是一个function，第二种方法中Relu是torch.nn的一种网络层

**两者完全等价**