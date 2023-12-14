---
title: wordpress一键搬迁
tags: []
id: '1753'
categories:
  - - 杂
date: 2021-06-01 20:21:29
---

#### 背景

之前用的阿里云服务器快到期了，续费不起，买了台腾讯云服务器，3年300块，真香。香的同时，问题也来了，之前的网站怎么搬迁过来，同时我对之前的网站域名还不满意，想重新换一个新的。

#### 解决方案

本来想把踩坑过程记录一下的，想想还是算了吧，总感觉罗里吧嗦的。直接把最正确最完美的一次做对的方案贴出来！

##### 导出.wpress文件

首先安装[All in one WP migration](https://wordpress.org/plugins/all-in-one-wp-migration/)插件，然后选择导出，导出的时候可以添加条件，把数据库的文件内容进行自定义的替换，特别适合要改域名的我，比如

![](https://cdn.staticaly.com/gh/xinhaojin/imgs-host@master/past/2021/06/image-1024x457.png)

然后下载到本地电脑，文件后缀是.wpress

##### 安装老版本插件

在新wordpress站点上，安装旧版本的All in one WP Migration插件（新版本的插件不能免费恢复备份）

下载地址[All-in-on WP Migration Plugin 6.7](https://webhostingadvices.com/wp-content/uploads/2020/05/all-in-one-wp-migration-6.7.zip)，下载完后通过本地上传的方式安装插件

![](https://cdn.staticaly.com/gh/xinhaojin/imgs-host@master/past/2021/06/image-1.png)

##### 上传.wpress

启用插件后，通过FTP工具把之前下载的.wpress文件上传到服务器这个文件夹中（网站根目录下的wp-content/ai1wm-backups）

![](https://cdn.staticaly.com/gh/xinhaojin/imgs-host@master/past/2021/06/image-2.png)

##### 提高文件上传上限

查找php.ini

    php -i  grep 'php.ini'

![](https://cdn.staticaly.com/gh/xinhaojin/imgs-host@master/past/2021/06/image-3.png)

打开php.ini

    vim /www/server/php/70/etc/php.ini

修改以下几个参数，由于我的.wpress大概780M，所以我把filesize改成了1024M，其他参数也可以根据需求调很大

    upload_max_filesize 128M
    post_max_size 128M
    memory_limit 256M
    max_execution_time 300
    max_input_time 300

到这里只是解除了php服务的限制，插件本身还有限制，需要对插件进行编辑，选择插件-插件编辑器，右侧选中All in one WP Migration,点击选择，然后选择constants.php，编辑284行，size修改为如图（数字30原本是28），增大1就增大一倍，30的话限制上传是2G，够了

![](https://cdn.staticaly.com/gh/xinhaojin/imgs-host@master/past/2021/06/image-4-1024x409.png)

##### 恢复备份

![](https://cdn.staticaly.com/gh/xinhaojin/imgs-host@master/past/2021/06/image-5-1024x455.png)

![](https://cdn.staticaly.com/gh/xinhaojin/imgs-host@master/past/2021/06/QQ截图20210601193705-1024x435.png)

Done.