---
title: selenium Edge后台静默运行
tags: []
id: '2303'
categories:
  - - python
date: 2022-04-05 20:16:27
---

webdriver中只有ChromeOptions而没有EdgeOptions,无法添加headless参数，需要借助外部工具

pip install msedge-selenium-tools

然后就可以了
```py
from selenium import webdriver
from msedge.selenium_tools import EdgeOptions
from msedge.selenium_tools import Edge

edge_options = EdgeOptions()
edge_options.use_chromium = True
# 设置无界面模式，也可以添加其它设置
edge_options.add_argument('headless')
driver = Edge(options=edge_options)
r = driver.get('https://www.baidu.com')
print(driver.title)
driver.quit()
```
![](https://cdn.staticaly.com/gh/xinhaojin/imgs-host@master/past/2022/04/image-3.png)