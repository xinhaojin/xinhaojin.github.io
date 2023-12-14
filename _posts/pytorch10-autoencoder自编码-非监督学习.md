---
title: PyTorch10-Autoencoder(自编码/非监督学习)
tags: []
id: '2259'
categories:
  - - pytorch
date: 2022-04-04 21:57:27
---

原文：[AutoEncoder (自编码/非监督学习) - PyTorch 莫烦Python (mofanpy.com)](https://mofanpy.com/tutorials/machine-learning/torch/autoencoder/)

## 编码器

![](https://cdn.staticaly.com/gh/xinhaojin/imgs-host@master/past/2022/04/auto4.png)

这部分也叫作 encoder 编码器. 编码器能得到原数据的精髓, 然后我们只需要再创建一个小的神经网络学习这个精髓的数据,不仅减少了神经网络的负担, 而且同样能达到很好的效果.

![](https://cdn.staticaly.com/gh/xinhaojin/imgs-host@master/past/2022/04/auto5.png)

这是一个通过自编码整理出来的数据, 他能从原数据中总结出每种类型数据的特征, 如果把这些特征类型都放在一张二维的图片上, 每种类型都已经被很好的用原数据的精髓区分开来. 如果你了解 PCA 主成分分析, 再提取主要特征时, 自编码和它一样,甚至超越了 PCA. 换句话说, 自编码 可以像 PCA 一样 给特征属性降维

## 解码器

至于解码器 Decoder, 我们也能那它来做点事情. 我们知道, 解码器在训练的时候是要将精髓信息解压成原始信息, 那么这就提供了一个解压器的作用, 甚至我们可以认为是一个生成器 (类似于GAN).

有一个例子就是让它能模仿并生成手写数字.

![](https://cdn.staticaly.com/gh/xinhaojin/imgs-host@master/past/2022/04/auto6.jpg)

## 非监督学习

神经网络也能进行非监督学习, 只需要训练数据, 不需要标签数据. 自编码就是这样一种形式. 自编码能自动分类数据, 而且也能嵌套在半监督学习的上面, 用少量的有标签样本和大量的无标签样本学习

这次我们还用 MNIST 手写数字数据来压缩再解压图片

![](https://cdn.staticaly.com/gh/xinhaojin/imgs-host@master/past/2022/04/4-4-1.gif)

然后用压缩的特征进行非监督分类

![](https://cdn.staticaly.com/gh/xinhaojin/imgs-host@master/past/2022/04/4-4-2.gif)

## 数据集

自编码只用训练集就好了, 而且只需要训练 training data 的 image, 不用训练 labels.
```py
import torch
import torch.nn as nn
import torch.utils.data as Data
import torchvision

# 超参数
EPOCH = 10
BATCH_SIZE = 64
LR = 0.005
DOWNLOAD_MNIST = True   # 下过数据的话, 就可以设置成 False
N_TEST_IMG = 5          # 到时候显示 5张图片看效果, 如上图一

# Mnist digits dataset
train_data = torchvision.datasets.MNIST(
    root='./mnist/',
    train=True,                                     # this is training data
    transform=torchvision.transforms.ToTensor(),    # Converts a PIL.Image or numpy.ndarray to
                                                    # torch.FloatTensor of shape (C x H x W) and normalize in the range [0.0, 1.0]
    download=DOWNLOAD_MNIST,                        # download it if you don't have it
)
```
## 网络
```py
AutoEncoder 形式很简单, 分别是 `encoder` 和 `decoder`, 压缩和解压, 压缩后得到压缩的特征值, 再从压缩的特征值解压成原图片.

class AutoEncoder(nn.Module):
    def __init__(self):
        super(AutoEncoder, self).__init__()

        # 压缩
        self.encoder = nn.Sequential(
            nn.Linear(28*28, 128),
            nn.Tanh(),
            nn.Linear(128, 64),
            nn.Tanh(),
            nn.Linear(64, 12),
            nn.Tanh(),
            nn.Linear(12, 3),   # 压缩成3个特征, 进行 3D 图像可视化
        )
        # 解压
        self.decoder = nn.Sequential(
            nn.Linear(3, 12),
            nn.Tanh(),
            nn.Linear(12, 64),
            nn.Tanh(),
            nn.Linear(64, 128),
            nn.Tanh(),
            nn.Linear(128, 28*28),
            nn.Sigmoid(),       # 激励函数让输出值在 (0, 1)
        )

    def forward(self, x):
        encoded = self.encoder(x)
        decoded = self.decoder(encoded)
        return encoded, decoded

autoencoder = AutoEncoder()
```
## 训练

训练, 并可视化训练的过程. 我们可以有效的利用 `encoder` 和 `decoder` 来做很多事, 比如这里我们用 `decoder` 的信息输出看和原图片的对比, 还能用 `encoder` 来看经过压缩后, 神经网络对原图片的理解. `encoder` 能将不同图片数据大概的分离开来. 这样就是一个无监督学习的过程
```py
optimizer = torch.optim.Adam(autoencoder.parameters(), lr=LR)
loss_func = nn.MSELoss()

for epoch in range(EPOCH):
    for step, (x, b_label) in enumerate(train_loader):
        b_x = x.view(-1, 28*28)   # batch x, shape (batch, 28*28)
        b_y = x.view(-1, 28*28)   # batch y, shape (batch, 28*28)

        encoded, decoded = autoencoder(b_x)

        loss = loss_func(decoded, b_y)      # mean square error
        optimizer.zero_grad()               # clear gradients for this training step
        loss.backward()                     # backpropagation, compute gradients
        optimizer.step()                    # apply gradients

#3D 的可视化图挺有趣的, 还能挪动观看, 更加直观, 好理解

# 要观看的数据
view_data = train_data.train_data[:200].view(-1, 28*28).type(torch.FloatTensor)/255.
encoded_data, _ = autoencoder(view_data)    # 提取压缩的特征值
fig = plt.figure(2)
ax = Axes3D(fig)    # 3D 图

# x, y, z 的数据值

X = encoded_data.data[:, 0].numpy()
Y = encoded_data.data[:, 1].numpy()
Z = encoded_data.data[:, 2].numpy()
values = train_data.train_labels[:200].numpy()  # 标签值
for x, y, z, s in zip(X, Y, Z, values):
    c = cm.rainbow(int(255*s/9))    # 上色
    ax.text(x, y, z, s, backgroundcolor=c)  # 标位子
ax.set_xlim(X.min(), X.max())
ax.set_ylim(Y.min(), Y.max())
ax.set_zlim(Z.min(), Z.max())
plt.show()
```
![](https://cdn.staticaly.com/gh/xinhaojin/imgs-host@master/past/2022/04/4-4-5.png)