---
title: yolox添加注意力机制
tags: []
categories:
  - 目标检测
date: 2022-10-24 11:24:00
---
[原文链接](https://blog.csdn.net/qq_43114108/article/details/126533757)
## Coordinate Attention
[论文来源](http://arxiv.org/abs/2103.02907)

[官方代码](https://github.com/Andrew-Qibin/CoordAttention)

注意力机制广泛用于深度神经网络中来提高模型的性能。然而，因为其昂贵的计算代价，很难应用在一些轻量级网络，但不乏有一些注意力模块脱颖而出，具有代表性的有SE、CBAM等。SE模块通过2D全局池化来计算通道注意力，在非常低的计算成本下达到了提升网络性能的目的，遗憾的是，SE模块忽视了捕获位置信息的注意力；CBAM模块通过使用大尺寸卷积来获得位置信息的注意力，但只偏向于捕获局部的位置信息。
CAM模块来源于2021CVPR，该模块通过将位置信息嵌入到通道注意力中，因为其较少的计算代价，使轻量级网可以较大的区域中获得注意力。为了缓解位置信息丢失的问题，论文作者将2D全局池化替换成分别在特征的w和h并行提取特征的两个1D池化，可以有效捕获空间坐标信息；而后这两个并行的特征图通过两个卷积来生成两个独立方向的注意力图；通过将两个注意力图乘入到原始特征图中，以达到增强特征图的表征能力。
![](https://xinhaojin.github.io/imgs-host/20221019/xxx.44vbajj18g40.png)
## 在yolox/models/下添加attention.py
```py
#!/usr/bin/env python
# -*- encoding: utf-8 -*-
import torch
import torch.nn as nn

class CAM(nn.Module):
    def __init__(self, channels, reduction=32):
        super(CAM, self).__init__()
        self.conv_1x1 = nn.Conv2d(in_channels=channels, out_channels=channels // reduction, kernel_size=1, stride=1,
                                  bias=False)                        
        self.mish = nn.Mish() # 可用自行选择激活函数
        self.bn = nn.BatchNorm2d(channels // reduction)  
        self.F_h = nn.Conv2d(in_channels=channels // reduction, out_channels=channels, kernel_size=1, stride=1, bias=False)
        self.F_w = nn.Conv2d(in_channels=channels // reduction, out_channels=channels, kernel_size=1, stride=1, bias=False)  
        self.sigmoid_h = nn.Sigmoid() 
        self.sigmoid_w = nn.Sigmoid()
 
    def forward(self, x):
        h, w = x.shape[2], x.shape[3]
        avg_pool_x = nn.AdaptiveAvgPool2d((h, 1))
        avg_pool_y = nn.AdaptiveAvgPool2d((1, w))
        x_h = avg_pool_x(x).permute(0, 1, 3, 2) 
        x_w = avg_pool_y(x)  
        x_cat_conv_relu = self.mish(self.conv_1x1(torch.cat((x_h, x_w), 3))) 
        x_cat_conv_split_h, x_cat_conv_split_w = x_cat_conv_relu.split([h, w], 3)
        s_h = self.sigmoid_h(self.F_h(x_cat_conv_split_h.permute(0, 1, 3, 2)))
        s_w = self.sigmoid_w(self.F_w(x_cat_conv_split_w))
        out = x * s_h.expand_as(x) * s_w.expand_as(x)
        return out
```
## 在yolox/models/yolo_pafpn.py中添加CAM
```py
#!/usr/bin/env python
# -*- encoding: utf-8 -*-
# Copyright (c) 2014-2021 Megvii Inc. All rights reserved.

import torch
import torch.nn as nn

from .darknet import CSPDarknet
from .network_blocks import BaseConv, CSPLayer, DWConv
from .attention import CAM


class YOLOPAFPN(nn.Module):
    """
    YOLOv3 model. Darknet 53 is the default backbone of this model.
    """

    def __init__(
        self,
        depth=1.0,
        width=1.0,
        in_features=("dark3", "dark4", "dark5"),
        in_channels=[256, 512, 1024],
        depthwise=False,
        act="silu",
    ):
        super().__init__()
        self.backbone = CSPDarknet(depth, width, depthwise=depthwise, act=act)
        self.in_features = in_features
        self.in_channels = in_channels
        Conv = DWConv if depthwise else BaseConv

        self.upsample = nn.Upsample(scale_factor=2, mode="nearest")
        self.lateral_conv0 = BaseConv(
            int(in_channels[2] * width), int(in_channels[1] * width), 1, 1, act=act
        )
        self.C3_p4 = CSPLayer(
            int(2 * in_channels[1] * width),
            int(in_channels[1] * width),
            round(3 * depth),
            False,
            depthwise=depthwise,
            act=act,
        )  # cat

        self.reduce_conv1 = BaseConv(
            int(in_channels[1] * width), int(in_channels[0] * width), 1, 1, act=act
        )
        self.C3_p3 = CSPLayer(
            int(2 * in_channels[0] * width),
            int(in_channels[0] * width),
            round(3 * depth),
            False,
            depthwise=depthwise,
            act=act,
        )

        # bottom-up conv
        self.bu_conv2 = Conv(
            int(in_channels[0] * width), int(in_channels[0] * width), 3, 2, act=act
        )
        self.C3_n3 = CSPLayer(
            int(2 * in_channels[0] * width),
            int(in_channels[1] * width),
            round(3 * depth),
            False,
            depthwise=depthwise,
            act=act,
        )

        # bottom-up conv
        self.bu_conv1 = Conv(
            int(in_channels[1] * width), int(in_channels[1] * width), 3, 2, act=act
        )
        self.C3_n4 = CSPLayer(
            int(2 * in_channels[1] * width),
            int(in_channels[2] * width),
            round(3 * depth),
            False,
            depthwise=depthwise,
            act=act,
        )
        ##################################################
        self.CAM0 = CAM(int(in_channels[2] * width))
        self.CAM1 = CAM(int(in_channels[1] * width))
        self.CAM2 = CAM(int(in_channels[0] * width))
        ##################################################
    def forward(self, input):
        """
        Args:
            inputs: input images.

        Returns:
            Tuple[Tensor]: FPN feature.
        """

        #  backbone
        out_features = self.backbone(input)
        features = [out_features[f] for f in self.in_features]
        [x2, x1, x0] = features
        ##################################################
        x0 = self.CAM0(x0)
        x1 = self.CAM1(x1)
        x2 = self.CAM2(x2)
        ##################################################

        fpn_out0 = self.lateral_conv0(x0)  # 1024->512/32
        f_out0 = self.upsample(fpn_out0)  # 512/16
        f_out0 = torch.cat([f_out0, x1], 1)  # 512->1024/16
        f_out0 = self.C3_p4(f_out0)  # 1024->512/16

        fpn_out1 = self.reduce_conv1(f_out0)  # 512->256/16
        f_out1 = self.upsample(fpn_out1)  # 256/8
        f_out1 = torch.cat([f_out1, x2], 1)  # 256->512/8
        pan_out2 = self.C3_p3(f_out1)  # 512->256/8

        p_out1 = self.bu_conv2(pan_out2)  # 256->256/16
        p_out1 = torch.cat([p_out1, fpn_out1], 1)  # 256->512/16
        pan_out1 = self.C3_n3(p_out1)  # 512->512/16

        p_out0 = self.bu_conv1(pan_out1)  # 512->512/32
        p_out0 = torch.cat([p_out0, fpn_out0], 1)  # 512->1024/32
        pan_out0 = self.C3_n4(p_out0)  # 1024->1024/32

        outputs = (pan_out2, pan_out1, pan_out0)
        return outputs
```
## 其他注意力机制（SE、CBAM、ECA）同理，代码如下,插入使用方法相同
```py
#!/usr/bin/env python
# -*- encoding: utf-8 -*-
import torch
import torch.nn as nn

class CAM(nn.Module):
    def __init__(self, channels, reduction=32):
        super(CAM, self).__init__()
        self.conv_1x1 = nn.Conv2d(in_channels=channels, out_channels=channels // reduction, kernel_size=1, stride=1,
                                  bias=False)                        
        self.mish = nn.Mish() # 可用自行选择激活函数
        self.bn = nn.BatchNorm2d(channels // reduction)  
        self.F_h = nn.Conv2d(in_channels=channels // reduction, out_channels=channels, kernel_size=1, stride=1, bias=False)
        self.F_w = nn.Conv2d(in_channels=channels // reduction, out_channels=channels, kernel_size=1, stride=1, bias=False)  
        self.sigmoid_h = nn.Sigmoid() 
        self.sigmoid_w = nn.Sigmoid()
 
    def forward(self, x):
        h, w = x.shape[2], x.shape[3]
        avg_pool_x = nn.AdaptiveAvgPool2d((h, 1))
        avg_pool_y = nn.AdaptiveAvgPool2d((1, w))
        x_h = avg_pool_x(x).permute(0, 1, 3, 2) 
        x_w = avg_pool_y(x)  
        x_cat_conv_relu = self.mish(self.conv_1x1(torch.cat((x_h, x_w), 3))) 
        x_cat_conv_split_h, x_cat_conv_split_w = x_cat_conv_relu.split([h, w], 3)
        s_h = self.sigmoid_h(self.F_h(x_cat_conv_split_h.permute(0, 1, 3, 2)))
        s_w = self.sigmoid_w(self.F_w(x_cat_conv_split_w))
        out = x * s_h.expand_as(x) * s_w.expand_as(x)
        return out

class SE(nn.Module):
    def __init__(self, channel, ratio=16):
        super(SE, self).__init__()
        self.avg_pool = nn.AdaptiveAvgPool2d(1)
        self.fc = nn.Sequential(
                nn.Linear(channel, channel // ratio, bias=False),
                nn.ReLU(inplace=True),
                nn.Linear(channel // ratio, channel, bias=False),
                nn.Sigmoid()
        )
    def forward(self, x):
        b, c, _, _ = x.size()
        y = self.avg_pool(x).view(b, c)
        y = self.fc(y).view(b, c, 1, 1)
        return x * y

class ECA(nn.Module):
    def __init__(self, channel, b=1, gamma=2):
        super(ECA, self).__init__()
        kernel_size = int(abs((math.log(channel, 2) + b) / gamma))
        kernel_size = kernel_size if kernel_size % 2 else kernel_size + 1
        
        self.avg_pool = nn.AdaptiveAvgPool2d(1)
        self.conv = nn.Conv1d(1, 1, kernel_size=kernel_size, padding=(kernel_size - 1) // 2, bias=False) 
        self.sigmoid = nn.Sigmoid()
    def forward(self, x):
        y = self.avg_pool(x)
        y = self.conv(y.squeeze(-1).transpose(-1, -2)).transpose(-1, -2).unsqueeze(-1)
        y = self.sigmoid(y)
        return x * y.expand_as(x)

class ChannelAttention(nn.Module):
    def __init__(self, in_planes, ratio=8):
        super(ChannelAttention, self).__init__()
        self.avg_pool = nn.AdaptiveAvgPool2d(1)
        self.max_pool = nn.AdaptiveMaxPool2d(1)
        # 利用1x1卷积代替全连接
        self.fc1   = nn.Conv2d(in_planes, in_planes // ratio, 1, bias=False)
        self.relu1 = nn.ReLU()
        self.fc2   = nn.Conv2d(in_planes // ratio, in_planes, 1, bias=False)
        self.sigmoid = nn.Sigmoid()
    def forward(self, x):
        avg_out = self.fc2(self.relu1(self.fc1(self.avg_pool(x))))
        max_out = self.fc2(self.relu1(self.fc1(self.max_pool(x))))
        out = avg_out + max_out
        return self.sigmoid(out)
class SpatialAttention(nn.Module):
    def __init__(self, kernel_size=7):
        super(SpatialAttention, self).__init__()
        assert kernel_size in (3, 7), 'kernel size must be 3 or 7'
        padding = 3 if kernel_size == 7 else 1
        self.conv1 = nn.Conv2d(2, 1, kernel_size, padding=padding, bias=False)
        self.sigmoid = nn.Sigmoid()
    def forward(self, x):
        avg_out = torch.mean(x, dim=1, keepdim=True)
        max_out, _ = torch.max(x, dim=1, keepdim=True)
        x = torch.cat([avg_out, max_out], dim=1)
        x = self.conv1(x)
        return self.sigmoid(x)
class CBAM(nn.Module):
    def __init__(self, channel, ratio=8, kernel_size=7):
        super(CBAM, self).__init__()
        self.channelattention = ChannelAttention(channel, ratio=ratio)
        self.spatialattention = SpatialAttention(kernel_size=kernel_size)
    def forward(self, x):
        x = x*self.channelattention(x)
        x = x*self.spatialattention(x)
        return x

```
## 效果
试验了两个数据集，这些注意力机制通常都有零点几的AP提升，但不会有太大的作用。
