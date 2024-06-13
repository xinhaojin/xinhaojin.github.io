---
title: PyTorch01-回归问题（用曲线拟合数据点）
tags: []
id: '2223'
categories:
  - - pytorch
date: 2022-04-03 14:07:20
---

原文[关系拟合 (回归) - PyTorch 莫烦Python (mofanpy.com)](https://mofanpy.com/tutorials/machine-learning/torch/regression/)

![](https://xinhaojin.github.io/imgs-host/past/2022/04/1-1-2.gif)

## 定义数据点集
```py
x=torch.unsqueeze(torch.linspace(-1,1,100),dim=1)
y=x.pow(2)+0.2*torch.rand(x.size())
```
注：
```py
torch.linspace(start,end,step)构造等差数列
torch.unsqueeze()升维
```
x是(-1,1)区间内的100个等差数，y=x^2，外加了一些随机噪声

## 定义网络结构
```py
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

net = Net(n_feature=1, n_hidden=10, n_output=1)
```
注：

init是固定格式，不可修改，可以传参

forward是前向传播，函数名不可修改

输入是一个x，输出是一个预测值y，所以输入输出维度都是1

relu是激活函数，用于把多个线性单元转为非线性映射

## 定义优化器和损失函数

optimizer=torch.optim.SGD(net.parameters(),lr=0.5)
loss_func=torch.nn.MSELoss()#均方差，用在回归问题

注:

SGD是一种优化算法

net.parameters()是网络的所有参数，固定写法

lr是learning rate学习率

MSELOSS是均方差mean square error loss,适合用于计算回归问题的误差

## 训练
```py
for t in range(100):
    prediction = net(x)     # 喂给 net 训练数据 x, 输出预测值
    loss = loss_func(prediction, y)     # 计算两者的误差

    optimizer.zero_grad()   # 清空上一步的残余更新参数值
    loss.backward()         # 误差反向传播, 计算参数更新值
    optimizer.step()        # 将参数更新值施加到 net 的 parameters 上
```
固定写法

## 反馈/可视化
```py
plt.ion()#实时打印
plt.show()#显示画布

for t in range(100):
    prediction=net(x)
    loss=loss_func(prediction,y)

    optimizer.zero_grad()
    loss.backward()
    optimizer.step()

    if t%5==0:
        plt.cla()
        plt.scatter(x.data.numpy(),y.data.numpy())
        plt.plot(x.data.numpy(),prediction.data.numpy(),'r-',lw=5)
        plt.text(0.5,0,"Loss=%.4f"% loss.data,fontdict={'size':20,'color':'red'})
        plt.pause(0.1)

plt.ioff()
plt.show()
```
每隔5次训练就画一下预测曲线，实时打印在画布

## 源代码
```py
import torch
import torch.nn.functional as F   #激励函数都在这里
import matplotlib.pyplot as plt

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

net = Net(n_feature=1, n_hidden=10, n_output=1)

x=torch.unsqueeze(torch.linspace(-1,1,100),dim=1)
y=x.pow(2)+0.2*torch.rand(x.size())

optimizer=torch.optim.SGD(net.parameters(),lr=0.5)
loss_func=torch.nn.MSELoss()#均方差，用在回归问题

plt.ion()#实时打印
plt.show()#显示画布

for t in range(80):
    prediction=net(x)
    loss=loss_func(prediction,y)

    optimizer.zero_grad()
    loss.backward()
    optimizer.step()

    if t%5==0:
        plt.cla()
        plt.scatter(x.data.numpy(),y.data.numpy())
        plt.plot(x.data.numpy(),prediction.data.numpy(),'r-',lw=5)
        plt.text(0.5,0,"Loss=%.4f"% loss.data,fontdict={'size':20,'color':'red'})
        plt.pause(0.1)

plt.ioff()
plt.show()
```