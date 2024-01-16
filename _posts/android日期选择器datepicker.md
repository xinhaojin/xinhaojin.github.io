---
title: Android日期选择器DatePicker
index_img: https://xinhaojin.github.io/imgs-host/past/2020/06/image-5.png
tags: []
id: '616'
categories:
  - - 安卓
date: 2020-06-01 12:40:42
---

感觉这个窗口形式的日期选择器很实用，特此记录

![](https://xinhaojin.github.io/imgs-host/past/2020/06/image-5.png)

    public void dateSelect()
        {
            //Calendar cal=Calendar.getInstance();
            //获取年月日时分秒信息
            //int year = cal.get(Calendar.YEAR);
            //int month = cal.get(Calendar.MONTH)+1;//注意点 ，要加一
            //int day = cal.get(Calendar.DAY_OF_MONTH);
            //int hour = cal.get(Calendar.HOUR_OF_DAY);
            //int minute = cal.get(Calendar.MINUTE);
            Toast.makeText(register.this, "可在顶部选择年份哦~", Toast.LENGTH_SHORT).show();
            new DatePickerDialog(this, new DatePickerDialog.OnDateSetListener() {
                @Override
                public void onDateSet(DatePicker view, int year, int month, int day) {
                    //setTitle("当前时间："+year+"年-"+month+"月-"+day+"日 ");
                    //这里是点击确定后选择的日期
                }
            }, year, month, day).show(); //后三个参数用于日期初始化，即默认选择的日期
        }