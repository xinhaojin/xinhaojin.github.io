---
title: python与RPA（转载）
tags: []
id: '2022'
categories:
  - - python
date: 2021-09-14 10:26:54
---

RPA，是Robotic Process Automation的英文缩写，中文是机器人流程自动化，利用软件将重复性的工作自动化。基本目标是让人从重复性强的繁琐工作中解放出来去做更有意义更具创造性的工作。比如自动爬取网站内容、将十几个Excel中数据合并分析、定时发送邮件等都是简单的RPA应用的例子。

加上如今AI技术的快速发展，能极大地扩展软件自动化的应用范围，可以实现更复杂的功能。比如，可以通过图像识别、语义理解等技术将图片、特定格式的文档等非结构化数据转换为结构化数据，自动提交到数据中心，然后基于机器学习算法定时分析结果生成报表并发送到邮箱。

RPA的本质是让计算机自动为人服务，RPA就像一个粘合剂，通过RPA人不需要对计算机操作细节，只需要指挥计算机执行某个任务就行了。

现在国内外有不少功能在研发RPA软件用于实现流程自动化。但作为一个Python程序员，我觉得Python是实现RPA的天然工具。

Python作为RPA工具有以下优势：

一方面，Python语法简单，容易上手。而且早期Python起源就是作为c语言和shell脚本之间的中间层而被开发出来（详见漫画Python三十年），可见它本身就有这方面的基因；

另一方面，Python应用范围广阔，包括系统管理、文件处理、邮件发送、机器学习、深度学习等，基本上都有免费开源的类库可以使用，RPA需要的功能Python基本上都可以支持，不需要自己再重新开发。

有兴趣的同学可以基于Python根据自己的需要定制一套强大的自动化系统。但根据业务不同实现方式也不能一概而论。这里收集整理了几个常用自动化处理工具库供大家参考学习，能力有限，不足之处请多指正。

概要如下图：

![](https://cdn.staticaly.com/gh/xinhaojin/imgs-host@master/past/2021/09/aHR0cHM6Ly90dmExLnNpbmFpbWcuY24vbGFyZ2UvMDA3UzhaSWxseTFnaHp3YjYxeTV6ajMweXUwdTBqd3MuanBn-1024x882.jpg)

![](https://cdn.staticaly.com/gh/xinhaojin/imgs-host@master/past/2021/09/aHR0cHM6Ly90dmExLnNpbmFpbWcuY24vbGFyZ2UvMDA3UzhaSWxseTFnaHp3YjVubjd6ajMxMnUwaGdqdDMuanBn-1024x460.jpg)

#### Excel自动化

**功能**：实现对excel文件的增删改写、格式修改等

**常用库**：xlwings、xlrd、xlwt

*   xlrd用于读Excel文件([官方文档](https://xlrd.readthedocs.io/en/latest/))
*   xlwt用于写Excel文件([官方文档](https://xlwt.readthedocs.io/en/latest/))
*   xlwings实现了Excel中调用Python，python中调用Excel的骚操作，还支持VBA。（[官方文档](https://www.xlwings.org/)）

![](https://cdn.staticaly.com/gh/xinhaojin/imgs-host@master/past/2021/09/aHR0cHM6Ly90dmExLnNpbmFpbWcuY24vbGFyZ2UvMDA3UzhaSWxneTFnaTB4N3B4djNiajMyNGgwdTBha2suanBn-1024x402.jpg)

#### Word自动化

**实现功能**：对word文件的增删改写、格式修改等

**常用库**：python-docx

官方示例：

![](https://cdn.staticaly.com/gh/xinhaojin/imgs-host@master/past/2021/09/aHR0cHM6Ly90dmExLnNpbmFpbWcuY24vbGFyZ2UvMDA3UzhaSWxseTFnaTBpemdyOGxlajMwd2owdTBxYzMuanBn-1024x944.jpg)

[官方文档](https://python-docx.readthedocs.io/en/latest/)

#### PPT自动化

**实现功能**：对PPT的创建和修改

**常用库**：python-pptx

简单示例：

![](https://cdn.staticaly.com/gh/xinhaojin/imgs-host@master/past/2021/09/aHR0cHM6Ly90dmExLnNpbmFpbWcuY24vbGFyZ2UvMDA3UzhaSWxseTFnaTBpemhiYXE5ajMxY3EwdTBncDMuanBn-1024x631.jpg)

[官方文档](https://python-pptx.readthedocs.io/en/latest/user/quickstart.html)

#### 邮件自动化

**实现功能**：构造邮件和发送邮件

**常用库**：smtplib和email

**smtplib**提供了一种很方便的途径发送电子邮件。它对smtp协议进行了简单的封装。使用如下：

import smtplib
smtp = smtplib.SMTP( \[host \[, port \[, local\_hostname\]\]\] )

其中参数说明如下：

host: SMTP 服务器主机。 你可以指定主机的ip地址或者域名如: runoob.com，这个是可选参数。  
port: 如果你提供了 host 参数, 你需要指定 SMTP 服务使用的端口号，一般情况下 SMTP 端口号为25。  
local\_hostname: 如果 SMTP 在你的本机上，你只需要指定服务器地址为 localhost 即可。  
然后使用sendmail发送邮件，示例如下：

smtp.sendmail(from\_addr, to\_addrs, msg\[, mail\_options, rcpt\_options\])

参数说明如下：

*   from\_addr: 邮件发送者地址。
*   to\_addrs: 字符串列表，邮件发送地址。
*   msg: 发送消息

**email**库可以读取、写入和发送电子邮件内容，示例如下：

import smtplib

# Import the email modules we'll need
from email.message import EmailMessage

# Open the plain text file whose name is in textfile for reading.
with open(textfile) as fp:
    # Create a text/plain message
    msg = EmailMessage()
    msg.set\_content(fp.read())

# me == the sender's email address
# you == the recipient's email address
msg\['Subject'\] = f'The contents of {textfile}'
msg\['From'\] = me
msg\['To'\] = you

# Send the message via our own SMTP server.
s = smtplib.SMTP('localhost')
s.send\_message(msg)
s.quit()

#### 文件处理

**实现功能**：批量修改文件等重复性操作

**常用库**：os

**os**库提供了一种使用与操作系统相关的功能的便捷式途径，常用操作如下：

函数

功能

os.listdir()

列出当前目录下的所有文件和文件夹（包括被隐藏的）

os.system()

运行shell命令；可将 指令 以 字符串 / 字符串拼接 的形式喂进去

os.sep()

更改操作系统中的路径分隔符

os.getcwd()

获取当前路径(中间会自动添上一个路径分隔符)

os.walk

循环遍历目录，返回tuple表，表中每一个tuple包含该层文件、文件夹及该层父节点

os.path.isfile()

是否是文件

os.path.isdir()

是否是文件夹

os.path.exists()

路径是否存在

os.path.abspath()

如果输入路径是相对路径，则转换为绝对路径

os.path.dirname()

获取指定目录的父目录路径

os.path.pardir

获取当前目录的父目录路径

os.pardir()

获取当前目录的父目录路径

os.path.split()

将目录和文件名分割开，组成二元组返回

os.remove()

删除指定文件

os.rmdir()

删除空文件夹

os.mkdir()

新建文件夹

os.makedirs( , exist\_ok=True)

创建递归的目录树(exist\_ok是py3.2才加入的参数)

os.chdir()

改变当前目录到指定目录中

os.rename(path1 ,path2)

重命名文件

os.chmod(path ,mode)

改变文件权限模式

os.access(path ,mode)

检验文件权限模式

os.sep

输出操作系统特定的路径分隔符。win下为"",macx下为"/"

os.linesep

输出当前平台使用的行终止符

os.pathsep

输出用于分割文件路径的字符串

os.name

输出字符串指示当前使用平台。win->‘nt’; mac->‘posix’

os.environ

获取系统环境变量

#### 爬虫

**实现功能**：爬取豆瓣、知乎、微博等数据用于数据分析

**常用库**：requests、scrapy

**requests**是一个Python HTTP库，在Apache License 2.0下发布。该项目的目标是使HTTP请求更简单，更人性化。示例如下：

\>>> r = requests.get('https://api.github.com/user', auth=('user', 'pass'))
>>> r.status\_code
200
>>> r.headers\['content-type'\]
'application/json; charset=utf8'
>>> r.encoding
'utf-8'
>>> r.text
'{"type":"User"...'
>>> r.json()
{'private\_gists': 419, 'total\_private\_repos': 77, ...}

[官方文档](https://requests.readthedocs.io/en/master/)

**Scrapy** 是用Python 实现的一个为了爬取网站数据、提取结构性数据而编写的应用框架，其架构如下：

![](https://cdn.staticaly.com/gh/xinhaojin/imgs-host@master/past/2021/09/aHR0cHM6Ly90dmExLnNpbmFpbWcuY24vbGFyZ2UvMDA3UzhaSWxseTFnaTBqbmNva2xhajMwamcwZHE3NWQuanBn.jpg)

[官方文档](https://scrapy-chs.readthedocs.io/zh_CN/0.24/intro/tutorial.html)

#### 数据分析

实现功能：处理统计各种数据，并进行可视化

常用库：pandas、matplotlib

pandas是为Python编程语言编写的软件库，用于数据处理和分析。特别是，它提供了用于操作数值表和时间序列的数据结构和操作。(十分钟入门pandas)

Matplotlib 是 Python 的一个绘图库。它包含了大量的工具，你可以使用这些工具创建各种图形，包括简单的散点图，正弦曲线，甚至是三维图形。

![](https://cdn.staticaly.com/gh/xinhaojin/imgs-host@master/past/2021/09/aHR0cHM6Ly90dmExLnNpbmFpbWcuY24vbGFyZ2UvMDA3UzhaSWxseTFnaTBrNzEyNGw5ajMxZzIwdHNqdHkuanBn-1024x586.jpg)

[官方教程](https://matplotlib.org/)

#### 图像识别

**实现功能**：识别出文本等信息

**常用库**：‎pytesseract，opencv

**Python-tesseract** 是一个基于 google’s Tesseract-OCR 的独立封装包，功能是识别图片文件中文字，并作为返回参数返回识别结果。

![](https://cdn.staticaly.com/gh/xinhaojin/imgs-host@master/past/2021/09/aHR0cHM6Ly90dmExLnNpbmFpbWcuY24vbGFyZ2UvMDA3UzhaSWxseTFnaTBrYXN1OTZtajMwemwwdTBuMHEuanBn-1024x863.jpg)

OpenCV的全称是Open Source Computer Vision Library，是一个跨平台的计算机视觉库，用于图像处理、分析、机器视觉方面的开源函数库。OpenCV采用C/C++语言编写，python-opencv是Python对OpenCV的封装，可以让Python调用OpenCV的功能。

[官方教程](https://docs.opencv.org/master/d6/d00/tutorial_py_root.html)

#### 定时任务

**实现功能**：定制执行某个动作

**常用库**：APScheduler

**APScheduler**实现了一个定时任务框架，提供了非常丰富而且方便易用的定时任务接口，可以实现基于日期、固定时间间隔以及crontab 类型的任务。

[官方文档](https://apscheduler.readthedocs.io/en/stable/)

#### RPA框架

**实现功能**：模拟鼠标键盘操作，控制浏览器、桌面应用等软件，让电脑上操作流程自动化

**常用库**：python-rpa

![](https://cdn.staticaly.com/gh/xinhaojin/imgs-host@master/past/2021/09/aHR0cHM6Ly90dmExLnNpbmFpbWcuY24vbGFyZ2UvMDA3UzhaSWxseTFnaTBqbmN5NGN3ajMwazAwZGNxNmYuanBn.jpg)

[官方文档](https://github.com/tebelorg/RPA-Python)

原文链接：https://blog.csdn.net/pyfun/article/details/108216876