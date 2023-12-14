---
title: yolox小目标检测改进（删除感受野大的特征图）
tags: []
categories:
  - 目标检测
date: 2022-10-24 11:30:00
---
## 问题
yolox中有3个检测分支，分别预测小、中、大的目标
![](https://cdn.staticaly.com/gh/xinhaojin/imgs-host@master/20221010/41.png)
但在我的数据集上，并不需要预测大目标，甚至还要手动去掉错误的大预测框，因此有一个想法是去掉一个最深的检测分支，使网络预测大目标的能力下降，整体感受野变小，对小目标更加敏感。
## 方法
**修改yolox/models/yolo_head.py**
```py
class YOLOXHead(nn.Module):
    def __init__(
        self,
        num_classes,
        width=1.0,
        # strides=[8, 16, 32],
        # in_channels=[256, 512, 1024],
        strides=[8, 16],
        in_channels=[256, 512],
        act="silu",
        depthwise=False,
    ):
        """
        Args:
            act (str): activation type of conv. Defalut value: "silu".
            depthwise (bool): whether apply depthwise conv in conv branch. Defalut value: False.
        """
        super().__init__()
```
**修改yolox/models/darknet.py**
```py
class CSPDarknet(nn.Module):
    def __init__(
        self,
        dep_mul,
        wid_mul,
        out_features=("dark3", "dark4"),
        # out_features=("dark3", "dark4", "dark5"),
        depthwise=False,
        act="silu",
    ):
        super().__init__()
    #......
    def forward(self, x):
        outputs = {}
        x = self.stem(x)
        outputs["stem"] = x
        x = self.dark2(x)
        outputs["dark2"] = x
        x = self.dark3(x)
        outputs["dark3"] = x
        x = self.dark4(x)
        outputs["dark4"] = x
        # x = self.dark5(x)
        # outputs["dark5"] = x
        return {k: v for k, v in outputs.items() if k in self.out_features}
```
**修改yolox/models/yolo_pafpn.py**
```py
class YOLOPAFPN(nn.Module):
    """
    YOLOv3 model. Darknet 53 is the default backbone of this model.
    """

    def __init__(
        self,
        depth=1.0,
        width=1.0,
        # in_features=("dark3", "dark4", "dark5"),
        # in_channels=[256, 512, 1024],
        in_features=("dark3", "dark4"),
        in_channels=[256, 512],
        depthwise=False,
        act="silu",
    ):
        super().__init__()
        self.backbone = CSPDarknet(depth, width, depthwise=depthwise, act=act)
        self.in_features = in_features
        self.in_channels = in_channels
        Conv = DWConv if depthwise else BaseConv

        self.upsample = nn.Upsample(scale_factor=2, mode="nearest")
        # self.lateral_conv0 = BaseConv(
        #     int(in_channels[2] * width), int(in_channels[1] * width), 1, 1, act=act
        # )
        # self.C3_p4 = CSPLayer(
        #     int(2 * in_channels[1] * width),
        #     int(in_channels[1] * width),
        #     round(3 * depth),
        #     False,
        #     depthwise=depthwise,
        #     act=act,
        # )  # cat

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
        # self.bu_conv1 = Conv(
        #     int(in_channels[1] * width), int(in_channels[1] * width), 3, 2, act=act
        # )
        # self.C3_n4 = CSPLayer(
        #     int(2 * in_channels[1] * width),
        #     int(in_channels[2] * width),
        #     round(3 * depth),
        #     False,
        #     depthwise=depthwise,
        #     act=act,
        # )
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
        #[x2, x1, x0] = features
        [x2,x1] = features#256,512

        # fpn_out0 = self.lateral_conv0(x0)  # 1024->512/32
        # f_out0 = self.upsample(fpn_out0)  # 512/16
        # f_out0 = torch.cat([f_out0, x1], 1)  # 512->1024/16
        # f_out0 = self.C3_p4(f_out0)  # 1024->512/16

        fpn_out1 = self.reduce_conv1(x1)  # 512->256/16
        # fpn_out1 = self.reduce_conv1(f_out0)  # 512->256/16
        f_out1 = self.upsample(fpn_out1)  # 256/8
        f_out1 = torch.cat([f_out1, x2], 1)  # 256->512/8
        pan_out2 = self.C3_p3(f_out1)  # 512->256/8

        p_out1 = self.bu_conv2(pan_out2)  # 256->256/16
        p_out1 = torch.cat([p_out1, fpn_out1], 1)  # 256->512/16
        pan_out1 = self.C3_n3(p_out1)  # 512->512/16

        # p_out0 = self.bu_conv1(pan_out1)  # 512->512/32
        # p_out0 = torch.cat([p_out0, fpn_out0], 1)  # 512->1024/32
        # pan_out0 = self.C3_n4(p_out0)  # 1024->1024/32

        # outputs = (pan_out2, pan_out1, pan_out0)
        outputs = (pan_out2, pan_out1)
        return outputs
```
**修改yolox/exp/yolox_base.py**
```py
class Exp(BaseExp):
    def __init__(self):
        super().__init__()

        # ---------------- model config ---------------- #
        #......
    def get_model(self):
        from yolox.models import YOLOX, YOLOPAFPN, YOLOXHead
        def init_yolo(M):
            for m in M.modules():
                if isinstance(m, nn.BatchNorm2d):
                    m.eps = 1e-3
                    m.momentum = 0.03

        if getattr(self, "model", None) is None:
            in_channels = [256, 512]
            # in_channels = [256, 512, 1024]
            backbone = YOLOPAFPN(self.depth, self.width, in_channels=in_channels, act=self.act)
            head = YOLOXHead(self.num_classes, self.width, in_channels=in_channels, act=self.act)
            self.model = YOLOX(backbone, head)
```
## 效果
使用tiny模型，没有AP提升，也没有明显下降，但模型减小了近一半，是有效的改进。
## 参考
 [https://blog.csdn.net/qq_45207837/article/details/124840732](https://blog.csdn.net/qq_45207837/article/details/124840732)