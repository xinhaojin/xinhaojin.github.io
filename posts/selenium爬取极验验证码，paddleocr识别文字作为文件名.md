---
title: selenium爬取极验验证码，PaddleOCR识别文字作为文件名
index_img: https://xinhaojin.github.io/imgs-host/past/2022/05/image-2.png
tags: []
id: '2361'
categories:
  - - python
  - - 爬虫
date: 2022-05-15 20:09:35
---

## 问题描述

自动爬取验证码图片，图片文件名需要包含右上角的标题文字（在网页上是图片类型，不是文本）

![](https://xinhaojin.github.io/imgs-host/past/2022/05/image-2.png)

## Edge driver初始化

第一种
```py
from msedge.selenium_tools import Edge, EdgeOptions
        edge_options = EdgeOptions()
        edge_options.use_chromium = True
        edge_options.add_argument('headless')#后台运行
        edge_options.add_experimental_option('excludeSwitches',['enable-automation','enable-logging'])
        driver = Edge(options=edge_options)
        return driver
```
第二种
```py
from selenium import webdriver
driver=webdriver.Edge(executable_path='msedgedriver.exe')
#在https://developer.microsoft.com/en-us/microsoft-edge/tools/webdriver/下载对应驱动
```
## PaddleOCR文字识别
```py
from paddleocr import PaddleOCR
ocr = PaddleOCR(use_angle_cls=True, lang="ch")#启动ocr引擎
result = ocr.ocr('temp.jpg')
text=result[0][1][0]#只有一个字的情况
```
## 图片处理

图片下载下来后，用paddleOCR识别，发现无结果，用opencv读取，发现有4通道，应该是有一个透明的通道

把4通道分别显示出来后发现有信息的是第4通道，所以需要把下载下来的图片读取第4通道保存为灰度图，再给paddleOCR识别即可。
```py
img=cv2.imdecode(np.fromfile('temp.jpg',dtype=np.uint8),-1)#读取图片
img=img[:,:,3]#取第四个通道
cv2.imwrite('temp.jpg',img)#保存包含汉字信息的灰度图
```
![](https://static.geetest.com/nerualpic/original_word_pic/jiangchengzhengjunti/fd6a0e7d3606d6c328cd552c2bb52fa3.png)

## 源代码
```py
# -*- coding: utf-8 -*-
import os
import time
import urllib.request

import cv2
import numpy as np
from paddleocr import PaddleOCR
from selenium import webdriver


def downloadPic(url, filename):# 下载图片
    try:
        # 下载图片，并保存到文件夹中
        urllib.request.urlretrieve(url=url, filename=filename)
    except IOError as e:
        print("IOError")
    except Exception as e:
        print("Exception")

def transfer(num):# 序号前加0
    temp = num
    count = 0
    while True:
        temp = int(temp/10)
        if temp > 0:
            count = count+1
        else:
            break
    return '0'*(4-count)+str(num)

def getTitleText(titleImgs,ocr):
    title = ''
    for i in range(3):
        downloadPic(titleImgs[i],'temp.jpg') # 下载图片,保存到当前文件夹
        img=cv2.imdecode(np.fromfile('temp.jpg',dtype=np.uint8),-1)#读取图片
        img=img[:,:,3]#取第四个通道
        cv2.imwrite('temp.jpg',img)#保存包含汉字信息的灰度图
        time.sleep(0.2)
        result = ocr.ocr('temp.jpg')
        if len(result)==0:#识别失败，不知道什么原因，跳过即可
            return ''
        else:
            title+=result[0][1][0]
    return title

def init(mode):#初始化driver
    if mode ==1:#不用driver.exe
        from msedge.selenium_tools import Edge, EdgeOptions
        edge_options = EdgeOptions()
        edge_options.use_chromium = True
        edge_options.add_argument('headless')
        edge_options.add_experimental_option('excludeSwitches',['enable-automation','enable-logging'])
        driver = Edge(options=edge_options)
        return driver
    else:
        driver=webdriver.Edge(executable_path='msedgedriver.exe')
        driver.maximize_window()
        return driver

def main():
    try:
        ocr = PaddleOCR(use_angle_cls=True, lang="ch")#启动ocr引擎
        dir='datasets/jiyan_new/'#保存图片的文件夹
        driver=init(0)
        driver.get('https://www.geetest.com/adaptive-captcha-demo')
        driver.implicitly_wait(10)  # 最多等待时间
        driver.find_element_by_css_selector('.tab-item-2').click()
        time.sleep(1)
        driver.find_element_by_css_selector('.geetest_btn_click').click()

        if not os.path.exists(dir):
            os.mkdir(dir)
        files = os.listdir(dir)  # 获取文件数目
        num = len(files)+1  # 起始序号

        index = 0
        last_img_src = ''
       

        while index < 500:  # 需要获取的验证码数量
            time.sleep(0.8)
            title_imgs=driver.find_element_by_css_selector('.geetest_ques_tips')
            #获取子元素
            title_imgs=title_imgs.find_elements_by_tag_name('img')
            #获取子元素的src属性 标题三个字的图片url
            title_imgs=[title_img.get_attribute('src') for title_img in title_imgs]
            title=getTitleText(title_imgs,ocr)
            if title=='':
                driver.find_element_by_css_selector(
                '.geetest_refresh').click()  # 刷新按钮
                continue
            img_src = driver.find_element_by_css_selector('.geetest_bg').get_attribute('style')  # 图片url
            '''
            background-image: 
            url("https://static.geetest.com/captcha_v4/policy/fdd2aaa4a429487381bd673b104f152d/word/14/2022-03-22T17/9cc1499b9d0646618f0b12863e6410ce.jpg");
            '''
            img_src = img_src.split('"')[1]
            if img_src == last_img_src:
                continue
            last_img_src = img_src
            filename = transfer(num+index)+title+'.png'
            downloadPic(img_src, dir+filename)
            time.sleep(0.2)
            print('序号：'+str(index))
            print('文件名：'+filename)
            index += 1
            driver.find_element_by_css_selector(
                '.geetest_refresh').click()  # 刷新按钮
        driver.quit()
    except Exception as e:
        print(e)
        driver.quit()

if __name__ == '__main__':
    main()
```