---
title: PyTorch02-分类问题（划分数据点类别）
tags: []
id: '2226'
categories:
  - - pytorch
date: 2022-04-03 14:26:35
---

原文[区分类型 (分类) - PyTorch 莫烦Python (mofanpy.com)](https://mofanpy.com/tutorials/machine-learning/torch/classification/)

![](https://xinhaojin.github.io/imgs-host/past/2022/04/1-1-3.gif)

## 定义数据集
```py
#x是数据点
#y是标签
n_data = torch.ones(100, 2)#100*2的全1矩阵
#均值为2，方差为1的离散正态分布随机点
x0 = torch.normal(2*n_data, 1)      # class0 x data (tensor), shape=(100, 2) 
#标签为0
y0 = torch.zeros(100)               # class0 y data (tensor), shape=(100, 1)

x1 = torch.normal(-2*n_data, 1)     # class1 x data (tensor), shape=(100, 2)
#标签为1
y1 = torch.ones(100)                # class1 y data (tensor), shape=(100, 1)

#cat 连接两个tensor
x = torch.cat((x0, x1), 0).type(torch.FloatTensor)  # shape (200, 2) FloatTensor = 32-bit floating 
y = torch.cat((y0, y1), ).type(torch.LongTensor)    # shape (200,) LongTensor = 64-bit integer

## 定义网络结构

class Net(torch.nn.Module):  # 继承 torch 的 Module
    def __init__(self, n_feature, n_hidden, n_output):
        super(Net, self).__init__()     # 继承 __init__ 功能
        # 定义每层用什么样的形式
        self.hidden = torch.nn.Linear(n_feature, n_hidden)   # 隐藏层线性输出
        self.predict = torch.nn.Linear(n_hidden, n_output)   # 输出层线性输出

    def forward(self, x):   # 这同时也是 Module 中的 forward 功能
        # 正向传播输入值, 神经网络分析出输出值
        x = F.relu(self.hidden(x))      # 激励函数(隐藏层的线性值)
        x = self.predict(x)             # 输出值
        return x

net = Net(n_feature=2, n_hidden=10, n_output=2)
```
把一堆数据点分为两类

输入是一个点的坐标[x,y]，输出是一个预测的类别概率[属于第一类的概率,属于第二类的概率]，所以输入输出维度都是2

## 定义优化器和损失函数
```py
optimizer = torch.optim.SGD(net.parameters(), lr=0.02)
loss_func = torch.nn.CrossEntropyLoss()#softmax概率，用于标签误差计算
```
## 训练
```py
# optimizer 是训练的工具
optimizer = torch.optim.SGD(net.parameters(), lr=0.02)  # 传入 net 的所有参数, 学习率
# 算误差的时候, 注意真实值!不是! one-hot 形式的, 而是1D Tensor, (batch,)
# 但是预测值是2D tensor (batch, n_classes)
loss_func = torch.nn.CrossEntropyLoss()

for t in range(100):
    out = net(x)     # 喂给 net 训练数据 x, 输出分析值

    loss = loss_func(out, y)     # 计算两者的误差

    optimizer.zero_grad()   # 清空上一步的残余更新参数值
    loss.backward()         # 误差反向传播, 计算参数更新值
    optimizer.step()        # 将参数更新值施加到 net 的 parameters
```
## 源代码
```py
import torch
import torch.nn.functional as F
import matplotlib.pyplot as plt

#x是数据
#y是标签
n_data = torch.ones(100, 2)#100*2的全1矩阵
#均值为2，方差为1的离散正态分布随机点
x0 = torch.normal(2*n_data, 1)      # class0 x data (tensor), shape=(100, 2) 
#标签为0
y0 = torch.zeros(100)               # class0 y data (tensor), shape=(100, 1)

x1 = torch.normal(-2*n_data, 1)     # class1 x data (tensor), shape=(100, 2)
#标签为1
y1 = torch.ones(100)                # class1 y data (tensor), shape=(100, 1)

#cat 连接两个tensor
x = torch.cat((x0, x1), 0).type(torch.FloatTensor)  # shape (200, 2) FloatTensor = 32-bit floating 
y = torch.cat((y0, y1), ).type(torch.LongTensor)    # shape (200,) LongTensor = 64-bit integer

class Net(torch.nn.Module):
    def __init__(self, n_feature, n_hidden, n_output):
        super(Net, self).__init__()
        self.hidden = torch.nn.Linear(n_feature, n_hidden)
        self.out =torch.nn.Linear(n_hidden, n_output)

    def forward(self, x):
        x = F.relu(self.hidden(x))
        x =self.out(x)
        return x

net = Net(n_feature=2, n_hidden=10, n_output=2)
print(net)

optimizer = torch.optim.SGD(net.parameters(), lr=0.02)
loss_func = torch.nn.CrossEntropyLoss()#softmax概率，用于标签误差计算

plt.ion()#实时打印

for t in range(100):
    out = net(x)#输出是类别概率[0.3,0.7]
    loss = loss_func(out, y)#先写预测值，后写真实值

    optimizer.zero_grad()
    loss.backward()
    optimizer.step()

    if t % 2 == 0:
        plt.cla()
        prediction = torch.max(out, 1)[1]#1是最大值索引，[0]是最大值
        pred_y = prediction.data.numpy()
        target_y = y.data.numpy()
        plt.scatter(x.data.numpy()[:, 0], x.data.numpy()[:, 1], c=pred_y, s=100, lw=0, cmap='RdYlGn')
        accuracy = float((pred_y == target_y).astype(int).sum()) / float(target_y.size)
        plt.text(1.5, -4, 'Accuracy=%.2f' % accuracy, fontdict={'size': 20, 'color':  'red'})
        plt.pause(0.1)

plt.ioff()
plt.show()
```