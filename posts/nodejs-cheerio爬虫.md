---
title: nodejs cheerio爬虫
index_img: https://xinhaojin.github.io/imgs-host/past/2021/12/image-3.png
tags: []
id: '2107'
categories:
  - - 爬虫
date: 2021-12-14 19:29:28
---

## 需求

获取美国各州疫情数据[美国疫情（每小时更新） 各州新增新冠确诊案例、疫苗接种率、实时新闻、疫情地图 - 咕噜美国通 (Guruin.com)](https://www.guruin.com/guides/covid19)

![](https://xinhaojin.github.io/imgs-host/past/2021/12/image-3.png)

本来用的数据来源不是这个网站，但原先的数据源不让跨域爬虫，http和request都没法获取HTML源码，只好换了一个数据源

## request获取目标网页源代码
```js
var request = require('request');
request({
        url: 'https://www.guruin.com/guides/covid19',
        method: 'GET',                   // 请求方法
        headers: {                       // 指定请求头
            'User-Agent':
                'Mozilla/5.0 (Windows NT 6.1; WOW64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/46.0.2490.80 Safari/537.36',
        }
    }, function (error, response, body) {
        if (!error && response.statusCode == 200) {
            // console.log(body) // body即为HTML内容
        }
    });
```
## 转换为cheerio对象，利用jquery选择器规则选取需要的元素
```js
var cheerio = require('cheerio');
var $ = cheerio.load(body);// body为html内容

[jQuery 选择器 菜鸟教程 (runoob.com)](https://www.runoob.com/jquery/jquery-ref-selectors.html)

$('选择器')即为筛选的元素，要获取其文本，用.text()方法

如$('[data-total-positive]').text()，得到具有 data-total-positive 属性的元素的文本

要遍历所有符合的元素进行单独操作，使用 $('选择器').each(function(i,elem){$(this).text()...})

//把有data-total-positive属性的元素遍历，其文本内容放到数组
var positive = []
$('[data-total-positive]').each(function (i, elem) {
                positive[i] = $(this).text();
            });
```
针对需求分析，可以选择用以下三个属性来筛选，分别对应确诊、死亡和接种率，然后分别遍历放到三个数组中

......写到这刚发现忘记爬取州名了，问题不大。

![](https://xinhaojin.github.io/imgs-host/past/2021/12/image-4.png)

## 完整代码
```js
var request = require('request');
//  Cheerio 是一个Node.js的库， 它可以从html的片断中构建DOM结构，然后提供像jquery一样的css选择器查询
var cheerio = require('cheerio');
//包装成模块函数
export { getData }
function getData() {
    request({
        url: 'https://www.guruin.com/guides/covid19',
        method: 'GET',                   // 请求方法
        headers: {                       // 指定请求头
            'User-Agent':
                'Mozilla/5.0 (Windows NT 6.1; WOW64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/46.0.2490.80 Safari/537.36',
        }
    }, function (error, response, body) {
        if (!error && response.statusCode == 200) {
            // console.log(body) // 输出网页内容
            // 接下来用cheerio来提取网页中的数据
            // 通过 load 方法把 HTML 代码转换成一个 jQuery 对象
            var $ = cheerio.load(body);//table-row .collapsed
            var positive = []
            var death = []
            var detection_rate = []
            $('[data-total-positive]').each(function (i, elem) {
                positive[i] = $(this).text();
            });
            $('[data-total-death]').each(function (i, elem) {
                death[i] = $(this).text();
            });
            $('[data-detection-rate]').each(function (i, elem) {
                detection_rate[i] = $(this).text();
            });
            console.log(positive);
            console.log(death);
            console.log(detection_rate);
        }
    });
}
```
![](https://xinhaojin.github.io/imgs-host/past/2021/12/image-5-1024x241.png)