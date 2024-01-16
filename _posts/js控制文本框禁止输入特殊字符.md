---
title: JS控制文本框禁止输入特殊字符
tags: []
id: '817'
categories:
  - - js
date: 2020-08-12 18:50:43
---

#### **JS 控制不能输入特殊字符**
```html
<input type="text"class="domain"onkeyup="this.value=this.value.replace(/[^u4e00-u9fa5w]/g,'')"；

this.value=this.value.replace(/[^u4e00-u9fa5w]/g,'')
```
#### **JS 控制文本框只能输入数字**
```html
<input onkeyup="value=value.replace(/[^0-9]/g,'')" onpaste="value=value.replace(/[^0-9]/g,'')" oncontextmenu = "value=value.replace(/[^0-9]/g,'')">
```
#### **JS 控制文本框只能输入数字、小数点**
```html
<input onkeyup="value=value.replace(/[^\\0-9\\.]/g,'')" onpaste="value=value.replace(/[^\\0-9\\.]/g,'')" oncontextmenu = "value=value.replace(/[^\\0-9\\.]/g,'')">
```
#### **JS 控制文本框只能输入英文**
```html
<input onkeyup="value=value.replace(/[^\\a-\\z\\A-\\Z]/g,'')" onpaste="value=value.replace(/[^\\a-\\z\\A-\\Z]/g,'')" oncontextmenu = "value=value.replace(/[^\\a-\\z\\A-\\Z]/g,'')">
```
#### **JS 控制文本框只能输入英文、数字**
```html
<input onkeyup="value=value.replace(/[^\\a-\\z\\A-\\Z0-9]/g,'')" onpaste="value=value.replace(/[^\\a-\\z\\A-\\Z0-9]/g,'')" oncontextmenu = "value=value.replace(/[^\\a-\\z\\A-\\Z0-9]/g,'')">
```
#### **JS 控制文本框只能输入中文**
```html
<input onkeyup="value=value.replace(/[^\\u4E00-\\u9FA5]/g,'')" onpaste="value=value.replace(/[^\\u4E00-\\u9FA5]/g,'')" oncontextmenu = "value=value.replace(/[^\\u4E00-\\u9FA5]/g,'')">
```
#### **JS 控制文本框只能输入中文、英文、数字**
```html
<input onkeyup="value=value.replace(/[^\\a-\\z\\A-\\Z0-9\\u4E00-\\u9FA5]/g,'')" onpaste="value=value.replace(/[^\\a-\\z\\A-\\Z0-9\\u4E00-\\u9FA5]/g,'')" oncontextmenu = "value=value.replace(/[^\\a-\\z\\A-\\Z0-9\\u4E00-\\u9FA5]/g,'')">
```
#### **JS 控制文本框只能输入中文、英文、数字、空格**
```html
<input onkeyup="value=value.replace(/[^\\a-\\z\\A-\\Z0-9\\u4E00-\\u9FA5\\ ]/g,'')" onpaste="value=value.replace(/[^\\a-\\z\\A-\\Z0-9\\u4E00-\\u9FA5\\ ]/g,'')" oncontextmenu = "value=value.replace(/[^\\a-\\z\\A-\\Z0-9\\u4E00-\\u9FA5\\ ]/g,'')">
```
#### **JS 控制文本框只能输入中文、英文、数字、小数点**
```html
<input onkeyup="value=value.replace(/[^\\a-\\z\\A-\\Z0-9\\u4E00-\\u9FA5\\.]/g,'')" onpaste="value=value.replace(/[^\\a-\\z\\A-\\Z0-9\\u4E00-\\u9FA5\\.]/g,'')" oncontextmenu = "value=value.replace(/[^\\a-\\z\\A-\\Z0-9\\u4E00-\\u9FA5\\.]/g,'')">
```
[https://www.jb51.net/article/110620.htm](https://www.jb51.net/article/110620.htm)