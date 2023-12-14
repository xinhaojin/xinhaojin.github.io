---
title: android把图片url转为drawable
index_img: https://cdn.staticaly.com/gh/xinhaojin/imgs-host@master/past/2021/01/image-38.png
tags: []
id: '1346'
categories:
  - - 安卓
date: 2021-01-18 15:00:50
---
```java
InputStream in = (InputStream) new URL("http://xxx.jpg").openStream();
final Drawable d = Drawable.createFromStream(in, "src");
in.close();
```

以下示例给linearlayout设置网络图片为背景
```java
linear=(LinearLayout)findViewById(R.id.linear);
        new Thread(new Runnable() {
            @Override
            public void run() {
                try {
                    InputStream in = (InputStream) new URL("http://xxx.jpg").openStream();
                    final Drawable d = Drawable.createFromStream(in, "src");
                    in.close();
                    runOnUiThread(new Runnable() {
                        @Override
                        public void run() {
                            linear.setBackground(d);
                        }
                    });
                } catch (IOException e) {
                    e.printStackTrace();
                }
            }
        }).start();
```
注意，http请求需要在子线程中完成，UI更新需要在主线程中完成

android9.0以上版本中允许http请求需要在AndroidManifest.xml中的application标签中添加

android:usesCleartextTraffic="true"

![](https://cdn.staticaly.com/gh/xinhaojin/imgs-host@master/past/2021/01/image-38.png)