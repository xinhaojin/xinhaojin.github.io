---
title: wordcloud词云
tags: []
id: '2071'
categories:
  - - python
date: 2021-11-10 20:55:36
---

#### 加载停用词

stopwords.txt是一个自定义的停用词列表，里面的词都会被过滤

```py
"""读取停用词"""
with open("stopwords.txt", "r", encoding="utf-8") as fp:
    stopwords = set([s.rstrip() for s in fp.readlines()])
```
![](https://cdn.staticaly.com/gh/xinhaojin/imgs-host@master/past/2021/11/image.png)

#### 读取文本内容，过滤停用词
```py
"""获取文本内容"""
with open("input.txt", "r", encoding="utf-8") as fp:
    content = fp.read()

"""中文分词"""
content = jieba.lcut(content)

"""去除停用词"""
text = []
for word in content:
    if word not in stopwords:
        text.append(word)
```
#### 计算词频

词频格式是字典{词：数量}，text是一个去掉停用词后的词数组，直接统计
```py
frequency = dict(Counter(text))  # 去掉停用词后的词频统计
```
计算词频的目的是根据词频来生成词云
```py
wordcloud.fit_words(frequency)
```
#### wordcloud（）参数

具体API请查阅[wordcloud.WordCloud — wordcloud 1.8.1 documentation (amueller.github.io)](https://amueller.github.io/word_cloud/generated/wordcloud.WordCloud.html)

常用的有这些
```py
wc = WordCloud(font_path='C:\\Windows\\Fonts\\STZHONGS.TTF',  # 字体
               background_color="white",  # 背景色
               mask=mask_image,  # 遮罩
               prefer_horizontal=0.6,  # 水平文字比例
               width=800,  # 宽度
               height=1000,  # 高度
               colormap="tab10"  # 指定字体颜色
               )
```
其中遮罩是一个白底的图片，非白色部分就是词云的形状

有时候找的背景图不是白色或者不够白，可以在画图工具中打开图片，画笔-填充-颜色默认-白色-点击背景色

![](https://cdn.staticaly.com/gh/xinhaojin/imgs-host@master/past/2021/11/image-1-1024x792.png)

colormap就是指定的颜色集合，参数填的是string类型，可选的值有下面这些，左侧列表都可作为参数值，对应的颜色是右边这些

![](https://cdn.staticaly.com/gh/xinhaojin/imgs-host@master/past/2021/11/colormaps_reference_00.png)

![](https://cdn.staticaly.com/gh/xinhaojin/imgs-host@master/past/2021/11/colormaps_reference_01.png)

![](https://cdn.staticaly.com/gh/xinhaojin/imgs-host@master/past/2021/11/colormaps_reference_02.png)

![](https://cdn.staticaly.com/gh/xinhaojin/imgs-host@master/past/2021/11/colormaps_reference_03.png)

![](https://cdn.staticaly.com/gh/xinhaojin/imgs-host@master/past/2021/11/colormaps_reference_04.png)

![](https://cdn.staticaly.com/gh/xinhaojin/imgs-host@master/past/2021/11/colormaps_reference_05.png)

#### 结果

![](https://cdn.staticaly.com/gh/xinhaojin/imgs-host@master/past/2021/11/output.png)

#### 代码
```py
from wordcloud import WordCloud
import jieba
from collections import Counter
from imageio import imread
import matplotlib.pyplot as plt

"""读取停用词"""
with open("stopwords.txt", "r", encoding="utf-8") as fp:
    stopwords = set([s.rstrip() for s in fp.readlines()])  # 数组转集合

"""获取文本内容"""
with open("input.txt", "r", encoding="utf-8") as fp:
    content = fp.read()

"""中文分词"""
content = jieba.lcut(content)

"""去除停用词"""
text = []
for word in content:
    if word not in stopwords:
        text.append(word)

frequency = dict(Counter(text))  # 去掉停用词后的词频统计

mask_image = imread("map.jpg")

wc = WordCloud(font_path='C:\\Windows\\Fonts\\STZHONGS.TTF',  # 字体
               background_color="white",  # 背景色
               mask=mask_image,  # 遮罩
               prefer_horizontal=0.6,  # 水平文字比例
               width=800,  # 宽度
               height=1000,  # 高度
               colormap="tab10"
               )

wc.fit_words(frequency)

plt.imshow(wc, interpolation="bilinear")
plt.axis("off")
plt.show()
wc.to_file("output.png")
```