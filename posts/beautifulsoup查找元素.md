---
title: BeautifulSoup查找元素
tags: []
id: '1836'
categories:
  - - 爬虫
  - - python
date: 2021-07-14 19:56:59
---
```python
from bs4 import BeautifulSoup
#获取网页html代码
import urllib.request
import urllib.parse
url='https://blog.xinhaojin.top'
resp=urllib.request.urlopen(url)
data=resp.read()
html=data.decode()
#print(html)

soup=BeautifulSoup(html,'lxml')

#查找第一个title元素
tag=soup.find('title')
print(type(tag),tag)

#查找所有超链接
tags=soup.find_all('a')
for tag in tags:
    print(tag)

#查找class=xxx的div元素
tags=soup.find_all('div',attrs={'class':"entry excerpt entry-summary"})
for tag in tags:
    print(tag)

#查找符合条件的所有类型元素
tags=soup.find_all(name=None,attrs={'class':"entry excerpt entry-summary"})
for tag in tags:
    print(tag)
#获取tag属性值
    print(tag['class'])
#获取元素包含的文本值
    print(tag.text)

#自定义高级查找
def myFilter(tag):
    #print(tag.name)
    #利用class属性查找时,应注意:class为列表,而非字符串
    return (tag.name=='a' and tag.has_attr("href") and tag['href']=="https://blog.xinhaojin.top/2020/08/")

tag=soup.find_all(myFilter)
print(tag)
```