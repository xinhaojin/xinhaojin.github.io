---
title: python flask实现前后端交互
index_img: https://xinhaojin.github.io/imgs-host/past/2021/07/image-6.png
tags: []
id: '1827'
categories:
  - - python
date: 2021-07-09 00:55:31
---

#### 后端代码
```py
import flask
app=flask.Flask(__name__)

from flask_cors import *
CORS(app, supports_credentials=True)#接收跨域请求

@app.route("/path")
def index():
    try:
        p=flask.request.args.get("name")
        c=flask.request.args.get("sex")
        print(p,c)
        return p+","+c
    except Exception as err:
        return str(err)

if __name__=="__main__":
    app.run(host="0.0.0.0", port=5000)#想要外部客户端访问，必须是0.0.0.0
```
#### 前端代码
```html
<html>
  <head>
    <meta http-equiv="Content-Type" content="text/html; charset=utf-8" />
    <title>flask测试页</title>
  </head>
  <body>
    name:<input id="name" type="text" ><br>
    sex:<input id="sex" type="text" >
    <input type="submit" value="测试" onclick="submit()">
  </body>
</html>
<script>
function submit()
{
    if (window.XMLHttpRequest)
    {
        // IE7+, Firefox, Chrome, Opera, Safari 浏览器执行的代码
        xmlhttp=new XMLHttpRequest();
    }
    else
    {    
        //IE6, IE5 浏览器执行的代码
        xmlhttp=new ActiveXObject("Microsoft.XMLHTTP");
    }
    xmlhttp.onreadystatechange=function()
    {
        if (xmlhttp.readyState==4 && xmlhttp.status==200)
        {
            var res=JSON.parse( xmlhttp.responseText );
            //res是返回的数据，可以做后续处理
        }
    }
    var name=document.getElementById("name").value;
    var sex=document.getElementById("sex").value;
    var url='http://127.0.0.1:5000/path?name='+name+'&sex='+sex;
    xmlhttp.open("GET",url,true);
    xmlhttp.send();
}
</script>
```
#### 直接测试url

![](https://xinhaojin.github.io/imgs-host/past/2021/07/image-4.png)

![](https://xinhaojin.github.io/imgs-host/past/2021/07/image-3.png)

#### HTML测试GET

![](https://xinhaojin.github.io/imgs-host/past/2021/07/image-5.png)

![](https://xinhaojin.github.io/imgs-host/past/2021/07/image-6.png)