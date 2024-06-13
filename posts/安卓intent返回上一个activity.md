---
title: 安卓Intent返回上一个activity
tags: []
id: '1856'
categories:
  - - 安卓
date: 2021-07-28 18:50:04
---

#### 需求

从Activity1跳转到 Activity2，再从 Activity2返回到 Activity1，返回后自动更新 Activity1

#### 分析

如果只使用常规的startActivity是不行的，按我的理解，他会保存跳转的状态栈，状态只增不减，无法实现真正的“返回”，即：

从1跳转到2使用 startActivity ，从2跳转到1也使用 startActivity ，那么这两次跳转之后，如果你按手机的返回按钮，会发现他返回到了2，如果再多跳转几次，按返回按钮，就会在1和2之间反复横跳，这显然不符合预期。

要实现真正的“返回”，可以使用startActivityForResult

#### 方法

在activity1中，startActivityForResult(Intent,requestCode)

其中requestCode用于区分是从哪个activity返回到当前activity的，是自定义的int类型值
```java
Intent intent = new Intent(Activity1.this, Activity2.class);
startActivityForResult(intent,1);
```
在activity2中
```java
setResult(RESULT_OK);//RESULT_OK表示正确返回
finish();//结束当前avtivity，返回上一个activity

当activity2执行finish后，会自动返回到activity1，此时会调用activity1中定义的onActivityResult方法做返回后的操作，如更新activity1

@Override
    protected void onActivityResult(int requestCode, int resultCode, Intent data) {
        super.onActivityResult(requestCode, resultCode, data);
        if(resultCode==RESULT_OK){//判断是否返回成功
            if(requestCode==1)//判断是否是哪个avtivity返回,一个avtivity对应一个code,上面定义过为1
            {
               //你的操作
            }
        }
```
当然，每次跳转都可以在intent中携带数据，这里没有写出