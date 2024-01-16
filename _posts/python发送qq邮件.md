---
title: python发送QQ邮件
tags: []
id: '1784'
categories:
  - - python
date: 2021-06-30 17:47:55
---

现在有个需求，需要在后台数据库变化时主动提醒我，自动发邮件是个很好的选择

#### 开启QQ邮箱的SMTP服务

![](https://xinhaojin.github.io/imgs-host/past/2021/06/image-8.png)

#### 获取授权码

![](https://xinhaojin.github.io/imgs-host/past/2021/06/image-9.png)

#### 代码如下

完全参考了菜鸟教程的代码，修改参数即可[https://www.runoob.com/python3/python3-smtp.html](https://www.runoob.com/python3/python3-smtp.html)
```py
#!/usr/bin/python3
 
import smtplib
from email.mime.text import MIMEText
from email.header import Header
 
# 第三方 SMTP 服务
mail_host="smtp.qq.com"  #设置服务器，这里是QQ的smtp服务器地址
mail_user="xinhaojin@qq.com"    #用户名
mail_pass="tj***********j"   #口令 ，即授权码
 
 
sender = 'xinhaojin@qq.com'#发送者
receivers = ['xinhaojin@qq.com']  # 接收邮件，可设置为你的QQ邮箱或者其他邮箱
 
message = MIMEText('Python 邮件发送测试...', 'plain', 'utf-8')
message['From'] = Header("菜鸟教程", 'utf-8')
message['To'] =  Header("测试", 'utf-8')
 
subject = 'Python SMTP 邮件测试'
message['Subject'] = Header(subject, 'utf-8')
 
 
try:
    smtpObj = smtplib.SMTP() 
    smtpObj.connect(mail_host, 25)    # 25 为 SMTP 端口号
    smtpObj.login(mail_user,mail_pass)
    smtpObj.sendmail(sender, receivers, message.as_string())
    print ("邮件发送成功")
except smtplib.SMTPException as e:
    print(e)
    print ("Error: 无法发送邮件")
```
![](https://xinhaojin.github.io/imgs-host/past/2021/06/image-10.png)