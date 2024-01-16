---
title: ubuntu20.04安装配置vncserver
tags: []
id: '2365'
categories:
  - - 软件安装配置
date: 2022-05-18 21:01:40
---

## 更新系统

    sudo apt update
    sudo apt upgrade

## 安装软件

    sudo apt install tigervncserver

打开端口

    sudo firewall-cmd --zone=public --add-port=5900/tcp --permanent
    sudo firewall-cmd --zone=public --add-port=5901/tcp --permanent

## 配置

运行 vncserver 命令，该命令将创建初始配置并设置密码。运行以下命令时不要使用 sudo

    vncserver

![](https://xinhaojin.github.io/imgs-host/past/2022/05/image-3-1024x386.png)

系统将提示您输入并确认密码，以及是否将其设置为仅查看密码。如果您选择设置仅查看密码，则用户将无法使用鼠标和键盘与 VNC 实例进行交互

第一次 vncserver 运行命令时，它将创建密码文件并将其存储在 ~/.vnc 目录中，如果不存在，将创建该目录。

注意上面输出中主机名之后的 :1 。这表示运行 vnc 服务器的显示端口号。在我们的例子中，服务器在 TCP 端口 5901 (5900 + 1)上运行。如果您创建第二个实例， vncserver 它将在下一个空闲端口上运行，即 :2 这意味着服务器正在端口 5902 (5900 + 2)上运行。

重要的是要记住，当使用 VNC 服务器时， :X 是一个引用的显示端口 5900+X 。

注：如果启动时出现 vnc 启动异常： is taken because of /tmp/.X11-unix/X1

此时只需要将提示的文件删除即可。再次输入 vncserver。

在继续下一步之前，首先使用带有 -kill 选项和服务器编号作为参数的 vncserver 命令停止 VNC 实例。在我们的例子中，服务器在端口 5901 (:1)中运行，因此我们将使用以下命令停止它：

    vncserver -kill :1

![](https://xinhaojin.github.io/imgs-host/past/2022/05/image-4.png)

现在我们已经在服务器上安装了 Xfce 和 TigerVNC ，我们需要配置 TigerVNC 来使用 Xfce 。为此，请创建以下文件 〜/.vnc/xstartup：

    vi ~/.vnc/xstartup

    #!/bin/sh
    unset SESSION_MANAGER
    unset DBUS_SESSION_BUS_ADDRESS
    exec startxfce4 

保存并关闭文件。无论何时启动或重启 TigerVNC 服务器，都将自动执行上述命令。

~/.vnc/xstartup 文件还需要具有执行权限。运行以下命令以确保权限正确：

    chmod u+x ~/.vnc/xstartup

如果需要将[附加选项](https://xie.infoq.cn/link?target=http%3A%2F%2Ftigervnc.org%2Fdoc%2Fvncserver.html)传递给 VNC 服务器，则可以创建一个名为 config 的文件，并为每行添加一个选项。这是一个例子：

文件 ~/.vnc/config

    geometry=1920x1084
    dpi=96

## 创建 Systemd 单元文件

我们将创建一个 systemd 单元文件，使我们能够根据需要轻松启动，停止和重新启动 VNC 服务，与任何其他 systemd 服务相同。

打开文本编辑器，将以下配置复制并粘贴到其中。

    sudo vi /etc/systemd/system/vncserver@.service

请务必更改第 7 行中的用户名以匹配您的用户名。

    [Unit]
    Description=Remote desktop service (VNC)
    After=syslog.target network.target

    [Service]
    Type=simple
    User=username
    PAMName=login
    PIDFile=/home/%u/.vnc/%H%i.pid
    ExecStartPre=/bin/sh -c '/usr/bin/vncserver -kill :%i > /dev/null 2>&1  :'
    ExecStart=/usr/bin/vncserver :%i -geometry 1440x900 -alwaysshared -fg
    ExecStop=/usr/bin/vncserver -kill :%i

    [Install]
    WantedBy=multi-user.target

保存并关闭文件。

通知 systemd 我们创建了一个新的单元文件：

    sudo systemctl daemon-reload

下一步是使用以下命令启用单元文件：

    sudo systemctl enable vncserver@1.service

符号 1 后面的数字 @ 定义了运行 VNC 服务的显示端口。这意味着 VNC 服务器将侦听端口 5901 ，正如我们在上一节中讨论的那样。

执行以下命令启动 VNC 服务：

    sudo systemctl start vncserver@1.service

![](https://xinhaojin.github.io/imgs-host/past/2022/05/image-5-1024x237.png)

    sudo systemctl start vncserver@1.service#启动
    sudo systemctl enable vncserver@1.service#自启动