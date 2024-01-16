---
title: Servlet允许跨域访问（Ajax请求Servlet数据）
index_img: https://xinhaojin.github.io/imgs-host/past/2020/09/image-31.png
tags: []
id: '1108'
categories:
  - - js
date: 2020-09-21 20:26:30
---

之前用Servlet写过一个用户信息管理的服务端程序，是用于安卓app上的HTTP请求，现在想把这个客户端部分内容在网页重现，尝试用Ajax请求url为“xxx.do”的数据发现不行，浏览器开发者模式提示不允许跨域访问。

于是在Java代码HttpServletResponse处添加如下代码，使用 CORS协议允许 `Response` 跨域。

![](https://xinhaojin.github.io/imgs-host/past/2020/09/image-31.png)
```javascript
/* 允许跨域的主机地址 */
response.setHeader("Access-Control-Allow-Origin", "*");  
/* 允许跨域的请求方法GET, POST, HEAD 等 */
response.setHeader("Access-Control-Allow-Methods", "*");  
/* 重新预检验跨域的缓存时间 (s) */
response.setHeader("Access-Control-Max-Age", "3600");  
/* 允许跨域的请求头 */
response.setHeader("Access-Control-Allow-Headers", "*");  
/* 是否携带cookie */
response.setHeader("Access-Control-Allow-Credentials", "true");  
```
再次尝试Ajax请求，就可以拿到servlet的数据啦

![](https://xinhaojin.github.io/imgs-host/past/2020/09/image-32-1024x225.png)
```html
<!DOCTYPE html>
<html>
<head>
<meta charset="utf-8">
<script>
function loadXMLDoc()
{
var xmlhttp;
if (window.XMLHttpRequest)
{
// IE7+, Firefox, Chrome, Opera, Safari 浏览器执行代码
xmlhttp=new XMLHttpRequest();
}
else
{
// IE6, IE5 浏览器执行代码
xmlhttp=new ActiveXObject("Microsoft.XMLHTTP");
}
xmlhttp.onreadystatechange=function()
{
if (xmlhttp.readyState==4 && xmlhttp.status==200)
{
document.getElementById("myDiv").innerHTML=xmlhttp.responseText;
}
}
xmlhttp.open("GET","http://xxxxxxxx/xxx.do",true);
xmlhttp.send();
}
</script>
</head>
<body>

<h2>AJAX</h2>
<button type="button" onclick="loadXMLDoc()">请求数据</button>
<div id="myDiv"></div>

</body>
</html>
```