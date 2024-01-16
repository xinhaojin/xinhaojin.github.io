---
title: JS动态添加div
index_img: https://xinhaojin.github.io/imgs-host/past/2020/08/image.png
tags: []
id: '804'
categories:
  - - js
date: 2020-08-12 15:04:24
---

本文实例讲述了jQuery中prepend()方法。

此方法可向所有匹配元素的内部的前面追加HTML内容。

#### **特别说明:**

此方法是追加内容，也就是原来的内容还在。  
HTML内容就是内容中可以包含HTML标签，并且能够被浏览器渲染。  
文本内容是先将内容中的html预定义字符转换成html字符实体，这样html标签就不会被渲染。

#### **语法结构:**

代码如下:$(selector).prepend(content)

#### **实例代码:**
```html
<!DOCTYPE html>
<html>
<head>
<meta charset=" utf-8">
<script src="https://cdn.staticfile.org/jquery/1.10.2/jquery.min.js"></script>
<script>
$(document).ready(function(){
  $("div").prepend("<b>新增内容</b>");
})
function submit()
{
  $("#divid").prepend('<div>按钮添加的新内容</div>');
}
</script>
</head>
<body>
<div id='divid'>原来内容</div>
<input type="submit" value="添加" onclick="submit()">
</body>
</html>
```
#### 效果

![](https://xinhaojin.github.io/imgs-host/past/2020/08/image.png)