---
title: ubuntu20安装cuda&cudnn
tags: []
id: '1908'
categories:
  - - 软件安装配置
date: 2021-08-14 17:31:02
---

#### 安装驱动

在软件和更新-附加驱动里，选择显卡驱动

![](https://cdn.staticaly.com/gh/xinhaojin/imgs-host@master/past/2021/08/2021-08-14-15-39-14屏幕截图.png)

禁用自带显卡驱动

    sudo gedit /etc/modprobe.d/blacklist.conf

在最后一行加上:  blacklist nouveau ，将Ubuntu自带的显卡驱动加入黑名单，修改后保存

还需执行一条命令使他生效

    sudo update-initramfs -u

然后重启，输入以下命令查看支持的cuda版本

    nvidia-smi

![](https://cdn.staticaly.com/gh/xinhaojin/imgs-host@master/past/2021/08/2021-08-14-16-18-59屏幕截图.png)

所以我可以安装的是cuda11.4版本

#### 下载cuda

[https://developer.nvidia.com/cuda-toolkit-archive](https://developer.nvidia.com/cuda-toolkit-archive)

选择正确的版本

![](https://cdn.staticaly.com/gh/xinhaojin/imgs-host@master/past/2021/08/2021-08-14-16-23-17屏幕截图.png)

选择操作系统、架构等，依次执行下面生成的命令

![](https://cdn.staticaly.com/gh/xinhaojin/imgs-host@master/past/2021/08/2021-08-14-16-24-23屏幕截图-1024x545.png)

我这里就是这样

    wget https://developer.download.nvidia.com/compute/cuda/11.4.0/local_installers/cuda_11.4.0_470.42.01_linux.run
    sudo sh cuda_11.4.0_470.42.01_linux.run

bash命令执行要等一会儿，出现这个界面后选择continue

![](https://cdn.staticaly.com/gh/xinhaojin/imgs-host@master/past/2021/08/2021-08-14-16-47-50屏幕截图.png)

下个界面输入accept

![](https://cdn.staticaly.com/gh/xinhaojin/imgs-host@master/past/2021/08/2021-08-14-16-49-42屏幕截图.png)

再下个界面先按空格，取消安装驱动，因为上面我们已经安装好了，然后选择install

![](https://cdn.staticaly.com/gh/xinhaojin/imgs-host@master/past/2021/08/2021-08-14-16-50-48屏幕截图.png)

等待安装完成后是这个界面

![](https://cdn.staticaly.com/gh/xinhaojin/imgs-host@master/past/2021/08/2021-08-14-16-52-43屏幕截图.png)

#### 配置环境变量

输入gedit ~/.bashrc命令打开文件，在文件结尾输入以下语句，保存

    export PATH=/usr/local/cuda-11.4/bin${PATH:+:${PATH}}
    export LD_LIBRARY_PATH=/usr/local/cuda-11.4/lib64${LD_LIBRARY_PATH:+:${LD_LIBRARY_PATH}}

![](https://cdn.staticaly.com/gh/xinhaojin/imgs-host@master/past/2021/08/2021-08-14-16-54-30屏幕截图.png)

更新环境变量配置

    source ~/.bashrc

至此，cuda安装完成，输入nvcc -V 查看是否成功

![](https://cdn.staticaly.com/gh/xinhaojin/imgs-host@master/past/2021/08/2021-08-14-16-56-54屏幕截图.png)

#### 安装cudnn

[https://developer.nvidia.com/rdp/cudnn-download](https://developer.nvidia.com/rdp/cudnn-download)

需要登录(比较麻烦，建议看最后一步的阿里云镜像源)

![](https://cdn.staticaly.com/gh/xinhaojin/imgs-host@master/past/2021/08/2021-08-14-17-01-39屏幕截图.png)

解压后进入解压目录，将cuda/include/cudnn.h文件复制到usr/local/cuda/include文件夹，将cuda/lib64/下所有文件复制到/usr/local/cuda/lib64文件夹中,再添加读取权限

可以在解压后的cudnn-11.4...../cuda目录下执行

    sudo cp include/cudnn.h /usr/local/cuda/include/cudnn.h
    sudo cp -r lib64/* /usr/local/cuda/lib64
    sudo chmod a+r /usr/local/cuda/lib64/libcudnn*
    sudo chmod a+r /usr/local/cuda/include/cudnn.h

至此，cudnn安装完成
#### 阿里云镜像deb安装方法
https://mirrors.aliyun.com/nvidia-cuda/

选择对应的操作系统版本，查找cuda版本对应的cudnn，dev和非dev两个版本，都需要下载
![](https://cdn.staticaly.com/gh/xinhaojin/imgs-host@master/20221025/xxx.4tdndxgkm1k0.png)
安装命令
```
sudo dpkg -i libcudnn8-dev_8.2.4.15-1+cuda11.4_amd64.deb
sudo dpkg -i libcudnn8_8.2.4.15-1+cuda11.4_amd64.deb
```
检查是否安装成功，有以上两个的安装信息即可
```
sudo dpkg -l | grep cudnn
```

