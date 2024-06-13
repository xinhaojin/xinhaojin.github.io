---
title: 树莓派4B接USB摄像头发送视频流
tags: []
id: '1326'
categories:
  - - 树莓派
date: 2021-01-11 18:46:03
---

#### 添加摄像头设备

插拔摄像头分别执行一次以下命令

    pi@raspberrypi:~ $ lsusb

![](https://xinhaojin.github.io/imgs-host/past/2021/01/image-31.png)

![](https://xinhaojin.github.io/imgs-host/past/2021/01/image-32.png)

或者

    pi@raspberrypi:~ $ ls /dev

插上USB摄像头有video0即可

![](https://xinhaojin.github.io/imgs-host/past/2021/01/image-30.png)

不插的情况

![](https://xinhaojin.github.io/imgs-host/past/2021/01/image-33.png)

#### 安装MJPG-Streamer

依次通过以下命令安装

    pi@raspberrypi:~ $ sudo apt-get install cmake libjpeg8-dev
    pi@raspberrypi:~ $ git clone https://e.coding.net/fivecc/mjpg-streamer/mjpg-streamer.git
    pi@raspberrypi:~ $ cd mjpg-*
    pi@raspberrypi:~/mjpg-streamer-master $ cd mjpg-*
    pi@raspberrypi:~/mjpg-streamer-master/mjpg-streamer-experimental $ make
    pi@raspberrypi:~/mjpg-streamer-master/mjpg-streamer-experimental $ sudo make install 
    pi@raspberrypi:~/mjpg-streamer-master/mjpg-streamer-experimental $ cd 
    pi@raspberrypi:~ $ 

#### 启动 MJPG-Streamer

    pi@raspberrypi: ~ $ /usr/local/bin/mjpg_streamer -i "/usr/local/lib/mjpg-streamer/input_uvc.so -n -f 30 -r 1280x720" -o "/usr/local/lib/mjpg-streamer/output_http.so -p 8080 -w /usr/local/share/mjpg-streamer/www"

正常的结果

![](https://xinhaojin.github.io/imgs-host/past/2021/01/image-34.png)

#### 参数说明

   -i "/usr/local/lib/mjpg-streamer/input_uvc.so -n -f 30 -r 1280x720"

  \-i 输入

         input_uvc.so：UVC输入组件

       -f  30             ：表示30帧

       -r 1280*720     ：分辨率

       -y                   ：YUV格式输入（有卡顿），不加表示MJPG输入（需要摄像头支持）

    -o "/usr/local/lib/mjpg-streamer/output_http.so -p 8080 -w /usr/local/share/mjpg-streamer/www"

          -o 输出

            output_http.so          ：网页输出组件

            -w www                                  : 网页输出

           -p 8080                                   ：端口   8080

             -d 1000                                   ： 时间1S

#### 浏览器实时视频接收

创建一个html文件
```html
<!DOCTYPE html>
<html>
<head>
<title>实时视频</title>
<style>
     #webcam{
          width: 80%;
          height: 80%;
          display: block;
          margin: 10% auto;
          text-align: center;
          position: relative;
     }
     #webcam img{
         width: 100%;
         height: auto;
         display: block;
         margin: 0 auto;
     }
</style>
</head>
<body>
 
<div id="webcam">
     <div>
     </div>
</div>
 
<script type="text/javascript">
    var imageNr = 0; // 图片的索引号
    var finished = new Array(); // 下载图片的队列
    var paused = false; //
     
    function createImageLayer() {
      var img = new Image();
      img.style.position = "absolute";
      img.style.zIndex = -1;
      img.onload = imageOnload;
      img.onclick = imageOnclick;
         //填你对应的ip和端口
      img.src = "http://192.16.34.182:8080/?action=snapshot&n=" + (++imageNr);
      var webcam = document.getElementById("webcam");
      webcam.insertBefore(img, webcam.firstChild);
    }
     
     
    function imageOnload() {
      this.style.zIndex = imageNr; 
      while (1 < finished.length) {
        var del = finished.shift(); // 删除旧照片
        del.parentNode.removeChild(del);
      }
      finished.push(this);
      if (!paused) createImageLayer();
    }
     
    function imageOnclick() { 
      paused = !paused;
      if (!paused) createImageLayer();
    } 
    createImageLayer() 
</script>
</body>
</html>
```
把IP部分改成树莓派的IP，打开网页即可

其实只需要关注“http://192.16.34.182:8080/?action=snapshot&n=”，这后面是视频帧的索引，只需要给一个足够大的数就可以获取最新的视频帧

如“http://192.16.34.182:8080/?action=snapshot&n=100000000”

当然，也可直接在浏览器输入视频流地址“http://192.16.34.182:8080/?action=streamer”