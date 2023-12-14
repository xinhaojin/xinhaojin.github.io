---
title: 获取PDF中的可复制的文本信息
tags: []
id: '2348'
categories:
  - - python
date: 2022-04-14 14:27:45
---
```py
import PyPDF2
pdfFile = open(filePath, 'rb')
pdfReader = PyPDF2.PdfFileReader(pdfFile)
page0=pdfReader.getPage(0)#获取第几页的内容
page1=pdfReader.getPage(1)
###########
text = page.extractText()  # 获得所有可复制的文本
text = re.sub("\\s", "", text)  # 去掉所有空格
###########
```
## 例：PDF提取表格保存到Excel

![](https://cdn.staticaly.com/gh/xinhaojin/imgs-host@master/past/2022/04/image-25.png)

![](https://cdn.staticaly.com/gh/xinhaojin/imgs-host@master/past/2022/04/2297a99239f8bdc75b1b263f6941e40.png)