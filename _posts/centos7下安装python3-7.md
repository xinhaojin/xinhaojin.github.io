---
title: CentOS7下安装Python3.7、
tags: []
id: '623'
categories:
  - - 软件安装配置
date: 2020-06-11 17:13:04
---

CentOS7默认已经安装了Python2.7，现安装3.7和它并存

#### 一、安装依赖

    yum -y groupinstall "Development tools"

若出现以下错误：No packages in any requested group available to install or update

解决方法：

    cd /etc/yum.repos.d  
    sudo wget http://public-yum.oracle.com/public-yum-el5.repo

    cd /etc/pki/rpm-gpg/  
    sudo wget https://public-yum.oracle.com/RPM-GPG-KEY-oracle-el5

然后重试即可

    yum -y install zlib-devel bzip2-devel openssl-devel ncurses-devel sqlite-devel readline-devel tk-devel gdbm-devel db4-devel libpcap-devel xz-devel

    yum install -y libffi-devel zlib1g-dev

    yum install zlib\* -y

#### 二、下载解压

    cd /tmp

    wget wget https://www.python.org/ftp/python/3.7.2/Python-3.7.2.tar.xz

    tar -xvJf Python-3.7.2.tar.xz

#### 三、创建编译安装目录

    mkdir /usr/local/python3

#### 四、安装

    cd Python-3.7.2

    ./configure --prefix=/usr/local/python3 --enable-optimizations --with-ssl

    make && make install

#### 五、建立软链接

    ln -s /usr/local/python3/bin/python3 /usr/local/bin/python3

    ln -s /usr/local/python3/bin/pip3 /usr/local/bin/pip3

#### 六、验证

    python3 -V

    pip3 -V

#### Done.