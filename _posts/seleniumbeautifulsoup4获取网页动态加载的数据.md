---
title: selenium+beautifulsoup4获取网页动态加载的数据
tags: []
id: '2089'
categories:
  - - python
  - - 爬虫
date: 2021-11-25 16:28:47
---

## 问题

想要获取一个网页中的表格数据，但是不提供下载

![](https://xinhaojin.github.io/imgs-host/past/2021/11/image-5-1024x475.png)

网页源码中能看到表格信息，但beautiful获取源码结果中没有表格

![](https://xinhaojin.github.io/imgs-host/past/2021/11/image-6.png)

原因是表格数据从后台动态加载，beautifulsoup无法获取

## 使用selenium获取源码

selenium是浏览器自动化工具，可以模拟人的操作，能够正常获取所有源码

为了程序运行更优雅，设置浏览器在后台静默运行
```py
from selenium import webdriver
options=webdriver.ChromeOptions()
#后台静默运行
options.add_argument('--headless')
#忽略无用的报错
options.add_experimental_option('excludeSwitches',['enable-automation','enable-logging'])
driver=webdriver.Chrome(chrome_options=options,executable_path='./driver/chromedriver.exe')
#把驱动文件放到根目录，或把路径当作参数传入，chrome驱动下载地址如下：
#http://npm.taobao.org/mirrors/chromedriver/

'''
driver=webdriver.Edge(executable_path='./driver/edgedriver.exe')
https://developer.microsoft.com/en-us/microsoft-edge/tools/webdriver/
'''
#driver.set_window_size(1000,800)#打开浏览器的窗口尺寸
#driver.maximize_window()

url='https://www.heywhale.com/mw/dataset/5fe1c7d183e4460030ab6c08/file'
driver.get(url)
driver.implicitly_wait(10)#最多等待时间
html=driver.page_source#html源码
driver.quit()#退出浏览器

![](https://xinhaojin.github.io/imgs-host/past/2021/11/image-7.png)

## 转为beautifulsoup对象

soup = BeautifulSoup(html,'lxml')
#print(soup.prettify())#prettify是格式化结果

## 获取表格数据

results=[]#表格结果保存到二维数组
tags=soup.find_all('tr')
for tag in tags:#tag表示一行数据
    result=[]
    for content in tag.contents:#content表示每一行的每一个数据
        result.append(content.string)
    results.append(result)
#print(results)

![](https://xinhaojin.github.io/imgs-host/past/2021/11/image-8-1024x336.png)

## 写入Excel

import xlwt
wk=xlwt.Workbook()
sheet = wk.add_sheet("数据", cell_overwrite_ok=True)
for i in range(len(results)):
    for j in range(len(results[0])):
        sheet.write(i , j, results[i][j])
wk.save("数据.xls")

![](https://xinhaojin.github.io/imgs-host/past/2021/11/image-9-1024x263.png)
```
## 完整代码
```py
#coding=utf-8
from bs4 import BeautifulSoup
from selenium import webdriver
options=webdriver.ChromeOptions()
#后台静默运行
options.add_argument('--headless')
#忽略无用的报错
options.add_experimental_option('excludeSwitches',['enable-automation','enable-logging'])
driver=webdriver.Chrome(chrome_options=options,executable_path='./driver/chromedriver.exe')
#把驱动文件放到根目录，或把路径当作参数传入，chrome驱动下载地址如下：
#http://npm.taobao.org/mirrors/chromedriver/

'''
driver=webdriver.Edge(executable_path='./driver/edgedriver.exe')
https://developer.microsoft.com/en-us/microsoft-edge/tools/webdriver/
'''
#driver.set_window_size(1000,800)#打开浏览器的窗口尺寸
#driver.maximize_window()

url='https://www.heywhale.com/mw/dataset/5fe1c7d183e4460030ab6c08/file'
driver.get(url)
driver.implicitly_wait(10)#最多等待时间
html=driver.page_source
driver.quit()
soup = BeautifulSoup(html,'lxml')
#print(soup.prettify())#prettify是格式化结果

results=[]#表格结果保存到二维数组
tags=soup.find_all('tr')
for tag in tags:#tag表示一行数据
    result=[]
    for content in tag.contents:#content表示每一行的每一个数据
        result.append(content.string)
    results.append(result)

if len(results)==0:
    print("Error!")
    exit()
    
#写入excel
import xlwt
wk=xlwt.Workbook()
sheet = wk.add_sheet("数据", cell_overwrite_ok=True)
for i in range(len(results)):
    for j in range(len(results[0])):
        sheet.write(i , j, results[i][j])
wk.save("数据.xls")
print("Success!")
```
## 备注

因为这次需求中的表格没有完整显示在网页中，所以只获取了一部分数据，没有满足需求，但对于数据完全显示在网页上的需求，有参考价值，特此记录

![](https://xinhaojin.github.io/imgs-host/past/2021/11/image-11.png)