---
title: PyTorch12-Dropout(缓解过拟合)
tags: []
id: '2279'
categories:
  - - pytorch
date: 2022-04-05 13:27:41
---

原文：[Dropout 缓解过拟合 - PyTorch 莫烦Python (mofanpy.com)](https://mofanpy.com/tutorials/machine-learning/torch/dropout/)

过拟合让人头疼, 明明训练时误差已经降得足够低, 可是测试的时候误差突然飙升

主要原因是训练数据太少，模型对训练数据过度学习了

可以用数据点的曲线回归问题来解释

## 定义数据集

![](https://cdn.staticaly.com/gh/xinhaojin/imgs-host@master/past/2022/04/5-3-2.png)
```py
import torch
import matplotlib.pyplot as plt

torch.manual_seed(1)    # reproducible

N_SAMPLES = 20
N_HIDDEN = 300

# training data
x = torch.unsqueeze(torch.linspace(-1, 1, N_SAMPLES), 1)
y = x + 0.3*torch.normal(torch.zeros(N_SAMPLES, 1), torch.ones(N_SAMPLES, 1))

# test data
test_x = torch.unsqueeze(torch.linspace(-1, 1, N_SAMPLES), 1)
test_y = test_x + 0.3*torch.normal(torch.zeros(N_SAMPLES, 1), torch.ones(N_SAMPLES, 1))

# show data
plt.scatter(x.data.numpy(), y.data.numpy(), c='magenta', s=50, alpha=0.5, label='train')
plt.scatter(test_x.data.numpy(), test_y.data.numpy(), c='cyan', s=50, alpha=0.5, label='test')
plt.legend(loc='upper left')
plt.ylim((-2.5, 2.5))
plt.show()
```
## 定义网络

我们在这里搭建两个神经网络, 一个没有 `dropout`, 一个有 `dropout`. 没有 `dropout` 的容易出现 过拟合, 那我们就命名为 `net_overfitting`, 另一个就是 `net_dropped`. `torch.nn.Dropout(0.5)` 这里的 0.5 指的是随机有 50% 的神经元会被关闭/丢弃.
```py
net_overfitting = torch.nn.Sequential(
    torch.nn.Linear(1, N_HIDDEN),
    torch.nn.ReLU(),
    torch.nn.Linear(N_HIDDEN, N_HIDDEN),
    torch.nn.ReLU(),
    torch.nn.Linear(N_HIDDEN, 1),
)

net_dropped = torch.nn.Sequential(
    torch.nn.Linear(1, N_HIDDEN),
    torch.nn.Dropout(0.5),  # drop 50% of the neuron
    torch.nn.ReLU(),
    torch.nn.Linear(N_HIDDEN, N_HIDDEN),
    torch.nn.Dropout(0.5),  # drop 50% of the neuron
    torch.nn.ReLU(),
    torch.nn.Linear(N_HIDDEN, 1),
)
```
## 训练
```py
optimizer_ofit = torch.optim.Adam(net_overfitting.parameters(), lr=0.01)
optimizer_drop = torch.optim.Adam(net_dropped.parameters(), lr=0.01)
loss_func = torch.nn.MSELoss()

for t in range(500):
    pred_ofit = net_overfitting(x)
    pred_drop = net_dropped(x)

    loss_ofit = loss_func(pred_ofit, y)
    loss_drop = loss_func(pred_drop, y)

    optimizer_ofit.zero_grad()
    optimizer_drop.zero_grad()
    loss_ofit.backward()
    loss_drop.backward()
    optimizer_ofit.step()
    optimizer_drop.step()
```
## 对比测试结果

在这个 `for` 循环里, 我们加上画测试图的部分. 注意在测试时, 要将网络改成 `eval()` 形式, 特别是 `net_dropped`, `net_overfitting` 改不改其实无所谓. 画好图再改回 `train()` 模式

![](https://cdn.staticaly.com/gh/xinhaojin/imgs-host@master/past/2022/04/5-3-1.gif)
```py
optimizer_ofit.step()
optimizer_drop.step()

# 接着上面来
if t % 10 == 0:     # 每 10 步画一次图
    # 将神经网络转换成测试形式, 画好图之后改回 训练形式
    net_overfitting.eval()
    net_dropped.eval()  # 因为 drop 网络在 train 的时候和 test 的时候参数不一样.

    ...
    test_pred_ofit = net_overfitting(test_x)
    test_pred_drop = net_dropped(test_x)
    ...

    # 将两个网络改回 训练形式
    net_overfitting.train()
    net_dropped.train()
```