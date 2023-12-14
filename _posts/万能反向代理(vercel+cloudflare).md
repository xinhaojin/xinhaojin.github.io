---
title: 万能反向代理接口(vercel+cloudflare)
tags: []
categories:
  - - 杂
date: 2023-07-04 19:30:00
---

## 万能反向代理接口(vercel+cloudflare)

### vercel

[gaboolic/vercel-reverse-proxy: vercel反向代理|OpenAI/ChatGPT 免翻墙代理|github免翻墙代理|github下载加速|google代理|vercel万能代理](https://github.com/gaboolic/vercel-reverse-proxy)

点击一键部署

![](https://cdn.staticaly.com/gh/xinhaojin/imgs-host@master/20230704/image.6a2gkpr6v3g0.webp)

然后在项目设置中设置自定义域名

![](https://cdn.staticaly.com/gh/xinhaojin/imgs-host@master/20230704/image.3td9tigs2o80.webp)

按提示给域名添加CNAME记录即可

![](https://cdn.staticaly.com/gh/xinhaojin/imgs-host@master/20230704/image.61thfdz2zjo.webp)

#### 用法

eg1. <https://proxy.xinhaojin.top/proxy/www.google.com/search?q=xinhaojin.top>

等同于https://www.google.com/search?q=xinhaojin.top

![](https://cdn.staticaly.com/gh/xinhaojin/imgs-host@master/20230704/image.oh65m4nasu8.png)

### cloudflare

[gaboolic/cloudflare-reverse-proxy: cloudflare反向代理|OpenAI/ChatGPT 免翻墙代理|github免翻墙代理|github下载加速|google代理|cloudflare万能代理](https://github.com/gaboolic/cloudflare-reverse-proxy)

在cloudflare创建一个新的worker

![](https://cdn.staticaly.com/gh/xinhaojin/imgs-host@master/20230704/image.2urpb8qa4xe0.webp)编辑代码，把<https://github.com/gaboolic/cloudflare-reverse-proxy/blob/main/_worker.js>中的内容写进worker.js

``````js
addEventListener('fetch', event => {
    event.respondWith(handleRequest(event.request))
  })
  
  async function handleRequest(request) {
    const url = new URL(request.url);
    const actualUrlStr = url.pathname.replace("/proxy/","")
    console.log(actualUrlStr)
  
    const actualUrl = new URL(actualUrlStr)
  
    const modifiedRequest = new Request(actualUrl, {
      headers: request.headers,
      method: request.method,
      body: request.body,
      redirect: 'follow'
    });
  
    const response = await fetch(modifiedRequest);
    const modifiedResponse = new Response(response.body, response);
  
    // 添加允许跨域访问的响应头
    modifiedResponse.headers.set('Access-Control-Allow-Origin', '*');
  
    return modifiedResponse;
  }
``````

保存并部署

添加自定义域名（A记录，地址223.5.5.5 or 8.8.8.8   ...）或者使用原始域名，与vercel的域名同等看待即可。