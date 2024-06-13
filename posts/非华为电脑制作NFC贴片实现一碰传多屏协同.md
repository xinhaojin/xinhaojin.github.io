---
title: 非华为电脑制作NFC贴片实现一碰传多屏协同
categories:
  - - 软件安装配置
date: 2024-05-25 21:40:12
---

## 安装华为电脑管家
网卡要求：英特尔AX200系列确认没问题，其余网卡可能无法安装华为电脑管家。

手机必须是华为手机，鸿蒙2.0以上，处理器在麒麟990以上。

具体可参考B站博主空降猫咪的教程安装：https://www.bilibili.com/video/BV19H4y1G7Dg

相关文件在作者视频简介中有网盘链接

需要注意的一点是SN码设置(后续会用到)，SN码选择自定义，设置为16位长的字母数字组合，然后再选择安装包安装。

![](https://xinhaojin.github.io/picx-images-hosting/20240525/微信截图_20240525220224.5c0t6wg2x3.jpg)
## 给NFC贴纸写入数据，并激活
### 生成二维码
使用PCManagerInstaller生成二维码。(就是上一步骤中的安装器):https://xinhaojin.lanzouj.com/iYXPD1zqz68h
![](https://xinhaojin.github.io/picx-images-hosting/20240525/image.7ljtqefn2j.jpg)
然后使用微信或者浏览器先扫描二维码内容，查看数据格式是否形如“SN=HUAWEILAPTOP2020|MAC=BC8385DC117F|MODELID=00000506”其中SN是上一步自己设置的。
### 使用一碰传助手扫码激活
安装华为一碰传助手：https://xinhaojin.lanzouj.com/iHSYu1zqz6dc，如果打开后不能用请在设置里授予相机权限，扫这个生成的二维码
![](https://xinhaojin.github.io/picx-images-hosting/20240525/9bd0d3780c47789217054243dbd5bc9.9gwej12key.jpg)
然后把NFC贴纸放在感应区两秒，提示激活成功即可。
![](https://xinhaojin.github.io/picx-images-hosting/20240525/dc35512f61d64ca61f7c60932cee80e.7p4hn63a1.jpg)
NFC tools(用于NFC写卡错误时删除标签数据，华为手机似乎无法直接使用，其他安卓机可用):https://xinhaojin.lanzouj.com/itbaa1zqz6ih