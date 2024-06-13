---
title: flask使用https
index_img: https://xinhaojin.github.io/imgs-host/past/2021/09/image.png
tags: []
id: '1990'
categories: 
  - - python
date: 2021-09-01 14:36:51
---

有一个https页面，需要加载http资源（后台是flask），出于安全原因被浏览器禁止了

既然只能用https，那就干脆把flask的所有请求都改成https

代码方面，只需要在app.run方法中增加ssl_context='adhoc'，意思是使用服务器的本地SSL证书

if __name__=="__main__":
    app.run(host="0.0.0.0", port=5000,ssl_context='adhoc')

然后就可以使用https访问了

![](https://xinhaojin.github.io/imgs-host/past/2021/09/image.png)