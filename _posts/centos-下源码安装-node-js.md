---
title: CentOS 下源码安装 Node.js
tags: []
id: '673'
categories:
  - - 软件安装配置
date: 2020-07-05 09:25:10
---

#### 第一步

     curl --silent --location https://rpm.nodesource.com/setup\_10.x  sudo bash

     curl --silent --location https://rpm.nodesource.com/setup\_10.x  sudo bash 

## Installing the NodeSource Node.js 10.x repo...


## Inspecting system...

+ rpm -q --whatprovides redhat-release  rpm -q --whatprovides centos-release  rpm -q --whatprovides cloudlinux-release  rpm -q --whatprovides sl-release
+ uname -m

## Confirming "el6-x86\_64" is supported...

+ curl -sLf -o /dev/null 'https://rpm.nodesource.com/pub\_10.x/el/6/x86\_64/nodesource-release-el6-1.noarch.rpm'

## Downloading release setup RPM...

+ mktemp
+ curl -sL -o '/tmp/tmp.aoQY5ifb9m' 'https://rpm.nodesource.com/pub\_10.x/el/6/x86\_64/nodesource-release-el6-1.noarch.rpm'

## Installing release setup RPM...

+ rpm -i --nosignature --force '/tmp/tmp.aoQY5ifb9m'

## Cleaning up...

+ rm -f '/tmp/tmp.aoQY5ifb9m'

## Checking for existing installations...

+ rpm -qa 'nodenpm'  grep -v nodesource

## Run \`sudo yum install -y nodejs\` to install Node.js 10.x and npm.
## You may also need development tools to build native addons:
     sudo yum install gcc-c++ make
## To install the Yarn package manager, run:
     curl -sL https://dl.yarnpkg.com/rpm/yarn.repo  sudo tee /etc/yum.repos.d/yarn.repo
     sudo yum install yarn

#### 第二步

     sudo yum -y install nodejs

     node -v查看版本

如果以上步骤不能安装，执行以下命令后再执行第二步：

     sudo yum clean all

如果存在多个 nodesoucre，执行以下命令删除，然后重新执行第一第二步：

     sudo rm -fv /etc/yum.repos.d/nodesource\*