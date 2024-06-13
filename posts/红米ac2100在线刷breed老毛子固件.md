---
title: 红米AC2100在线刷breed+老毛子固件
tags: []
id: '2064'
categories:
  - - 杂
date: 2021-10-25 10:16:36
---

纯记录，不放图了

#### 刷breed

登录路由器后台192.168.31.1，复制地址栏的stock

![](https://xinhaojin.github.io/imgs-host/past/2021/10/stock-1024x286.png)

替换掉下面地址中的stock，直接在浏览器里输入并回车，返回一个code:0即成功

http://192.168.31.1/cgi-bin/luci/;stok=dcd5c3196a7d5e620bd382328bf36218/api/misystem/set_config_iotdev?bssid=Xiaomi&user_id=longdike&ssid=%0Acd%20%2Ftmp%0Acurl%20-o%20B%20-O%20https%3A%2F%2Fbreed.hackpascal.net%2Fbreed-mt7621-xiaomi-r3g.bin%20-k%0A%5B%20-z%20%22%24(sha256sum%20B%20%7C%20grep%20242d42eb5f5aaa67ddc9c1baf1acdf58d289e3f792adfdd77b589b9dc71eff85)%22%20%5D%20%7C%7C%20mtd%20-r%20write%20B%20Bootloader%0A

等待路由器灯变成橘色，断开电源，按下reset，同时接通电源，等待10秒钟松开reset，等待一下输入192.168.1.1进入breed后台

#### 刷老毛子固件

在breed后台选择固件更新，上传固件即可

[固件下载地址](https://xinhaojin.lanzoui.com/ixnETvqlrfa)

[固件精华帖](https://www.right.com.cn/forum/forum.php?mod=forumdisplay&fid=171&filter=digest&digest=1&typeid=43)

![](https://xinhaojin.github.io/imgs-host/past/2021/10/微信截图_20210306233855.png)

提示更新完成后等待两分钟进入192.168.123.1，用户名admin，密码admin，进入老毛子后台。