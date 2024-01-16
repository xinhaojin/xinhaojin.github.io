---
title: php发送SMTP邮件
index_img: https://xinhaojin.github.io/imgs-host/past/2021/07/image-2.png
tags: []
id: '1807'
categories:
  - - 杂
date: 2021-07-03 23:56:21
---

main.php
```php
<?php
use PHPMailer\\PHPMailer\\PHPMailer;
use PHPMailer\\PHPMailer\\Exception;

require './src/Exception.php';
require './src/PHPMailer.php';
require './src/SMTP.php';
function sendEmail($content){
    $mail = new PHPMailer(true);                              // Passing \`true\` enables exceptions
    try {
        //服务器配置
        $mail->CharSet ="UTF-8";                     //设定邮件编码
        $mail->SMTPDebug = 0;                        // 调试模式输出
        $mail->isSMTP();                             // 使用SMTP
        $mail->Host = 'smtp.qq.com';                // SMTP服务器
        $mail->SMTPAuth = true;                      // 允许 SMTP 认证
        $mail->Username = 'xinhaojin@qq.com';                // SMTP 用户名  即邮箱的用户名
        $mail->Password = 'tjhv*********xdjaj';             // SMTP 密码  部分邮箱是授权码(例如163邮箱)
        $mail->SMTPSecure = 'ssl';                    // 允许 TLS 或者ssl协议
        $mail->Port = 465;                            // 服务器端口 25 或者465 具体要看邮箱服务器支持

        $mail->setFrom('xinhaojin@qq.com', '张三');  //发件人
        $mail->addAddress('xinhaojin@qq.com', '张三');  // 收件人
        //$mail->addAddress('ellen@example.com');  // 可添加多个收件人
        $mail->addReplyTo('xinhaojin@qq.com', '张三'); //回复的时候回复给哪个邮箱 建议和发件人一致
        //$mail->addCC('cc@example.com');                    //抄送
        //$mail->addBCC('bcc@example.com');                    //密送

        //发送附件
        // $mail->addAttachment('../xy.zip');         // 添加附件
        // $mail->addAttachment('../thumb-1.jpg', 'new.jpg');    // 发送附件并且重命名

        //Content
        $mail->isHTML(true);                                  // 是否以HTML文档格式发送  发送后客户端可直接显示对应HTML内容
        $mail->Subject = '有新留言，请尽快审核！';
        $mail->Body    = '<h1>'.$content.'</h1>';
        $mail->AltBody = $content;

        $mail->send();
        echo '邮件发送成功';
    } catch (Exception $e) {
        echo '邮件发送失败: ', $mail->ErrorInfo;
    }
}
sendEmail('快来看看吧');
```
还需要一个src文件夹，内容如下

![](https://xinhaojin.github.io/imgs-host/past/2021/07/image-1.png)

可以在GitHub下载https://github.com/PHPMailer/PHPMailer，下载后解压，只需要用到src文件夹

蓝奏云下载链接https://xinhaojin.lanzoui.com/izMoEqz9fyj

![](https://xinhaojin.github.io/imgs-host/past/2021/07/image-2.png)