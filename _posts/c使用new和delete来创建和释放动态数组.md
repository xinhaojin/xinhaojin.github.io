---
title: c++使用new和delete来创建和释放动态数组
tags: []
id: '1022'
categories:
  - - c++
date: 2020-09-17 14:11:00
---

#### 一、创建并释放一维数组
```c++
#include<iostream>
using namespace std;
int main()
{
    int n;
    cin>>n;
    //分配动态一维数组 
    int \*arr=new int\[n\];

    for(int i=0;i<n;i++)
        cin>>arr\[i\];
    for(int i=0;i<n;i++)
       cout<<arr\[i\]<<" ";
    //释放arr数组 
    delete\[\] arr;
    //注意：delete后面的\[\]不能少!!
    //若少了\[\],则只会释放第一个元素。
    return 0;
}
```

#### 二、创建并释放二维数组
```c++
#include<iostream>
using namespace std;
int main()
{
    int row,col;
    cin>>row>>col;
    //为行指针分配空间 
    int \*\*arr=new int \*\[row\];    
    for(int i=0;i<row;i++)
         arr\[i\]= new int\[col\];//为每行分配空间（每行中有col个元素） 
    //输入二维数组的数 
    for(int i=0;i<row;i++)
        for(int j=0;j<col;j++) 
        cin>>arr\[i\]\[j\];
    cout<<"\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*"<<endl;
     //输出二维数组中的数  
    for(int i=0;i<row;i++)
    {
         for(int j=0;j<col;j++) 
          cout<<arr\[i\]\[j\]<<" ";
        cout<<endl;
    } 
    //释放二维数组（反过来） 
    for(int i=0;i<row;i++)
        delete\[\] arr\[i\]; 
    delete\[\] arr;
    return 0;
}
```