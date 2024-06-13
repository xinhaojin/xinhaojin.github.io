---
title: B站视频合集下载
tags: []
categories:
  - - 软件安装配置
date: 2024-06-14 00:29:35
---
## 思路
在拥有视频链接的情况下，可以使用you-get直接下载视频，如`you-get https://www.bilibili.com/xxxxxxxx`非常方便。因此只要获取到视频合集中的所有视频链接即可
## 分析网页源代码
观察发现B站的视频唯一ID是这一串字符串，后面的可以删去，猜测可能是用户相关的信息，分享视频时用于标记分享用户之类的（所以分享B站视频时建议删去后面一长串，不然应该可以跟踪到分享者的账号信息），合集视频ID之间没有明显规律
![](https://xinhaojin.github.io/picx-images-hosting/20240613/微信截图_20240613234826.ibz20r8fm.jpg)
查看网页源代码，搜索这一段ID，可以获知视频ID这个属性被命名为`bvid`
![](https://xinhaojin.github.io/picx-images-hosting/20240613/微信截图_20240613234816.2yy7gxxy57.jpg)
搜索`bvid`，发现有180个，我的这个合集视频有46个，合理猜测46个链接应该在这180里面，搜索往下跳转翻到合集第一个视频的`bvid`，再往下翻发现确实合集所有链接都有
![](https://xinhaojin.github.io/picx-images-hosting/20240613/微信截图_20240613235054.esd4b1ky3.jpg)
观察前后代码发现这些信息存在一个名为`window.__INITIAL_STATE__`的JSON中，只要把这个JSON从HTML源代码中提取出来，再分析结构即可
## 获取bvid所在JSON
问一下GPT怎么写这个对应的正则表达式，把JSON从HTML中提取出来
```python
import requests
import re
import json
headers = {
    'referer': 'https://www.bilibili.com',
    'user-Agent': 'Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/122.0.0.0 Safari/537.36 Edg/122.0.0.0'
}
url='https://www.bilibili.com/video/BV1QG411z7gZ'
response=requests.get(url,headers=headers)
response.encoding='utf-8'
html=response.text
# 正则表达式匹配 window.__INITIAL_STATE__=到;(function的内容
json_str =re.findall(r"window\.__INITIAL_STATE__=(.*?);\(function\(",html)[0]
# json.loads将json字符串转换为字典
json_data=json.loads(json_str)
```
## 解析JSON获取视频链接
`bvid`在JSON中的结构如下
![](https://xinhaojin.github.io/picx-images-hosting/20240613/微信截图_20240613223912.lxdfga0l.jpg)
代码如下,其中`big_title`是合集标题
```python
big_title=videos=json_data["videoData"]["ugc_season"]["title"]
videos=json_data["videoData"]["ugc_season"]["sections"][0]["episodes"]
titles=[video['title'] for video in videos]
urls=['https://www.bilibili.com/video/'+video['bvid'] for video in videos]
print(big_title)
for i in range(len(titles)):
    print(titles[i]+' '+urls[i])
```
## 利用you-get自动批量下载
you-get支持下载B站视频（非会员视频），(之前尝试过西瓜视频不支持...)，默认似乎会下载480p视频，如果需要更高清的视频，需要指定cookie，默认会下载能够支持的最高清晰度。

主要参数：

* -o 保存文件夹
* -O 文件名(不加后缀)
* -c cookies.txt的地址(Netscape格式(不是JSON格式)，可以从浏览器插件EditCookies中导出复制到txt中)

代码如下，注意：不指定文件名，默认文件名是视频在网页中的大标题，与合集中的标题名称可能不同，这里代码中的title是合集列表中的标题
```python
basedir='G:/'
# 使用you-get下载视频
import os
if not os.path.exists(os.path.join(basedir,big_title)):
    os.makedirs(os.path.join(basedir,big_title))
    print('文件夹'+big_title+'创建成功')
for i in range(len(titles)):
    print("准备下载 "+os.path.join(basedir,big_title,titles[i]+'.mp4'))
    if(os.path.exists(os.path.join(basedir,big_title,titles[i]+'.mp4'))):
        print(titles[i]+'已存在')
        continue
    os.system('you-get -c cookies.txt -o '+os.path.join(basedir,big_title)+' -O '+titles[i]+' '+urls[i])
    print(titles[i]+' 下载完成')
```
另外，最新版的you-get会默认下载弹幕文件xml，没仔细看这是什么参数怎么取消下载，也无所谓吧，我手动删了一下