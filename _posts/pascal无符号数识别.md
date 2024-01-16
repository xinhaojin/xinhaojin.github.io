---
title: Pascal无符号数识别
tags: []
id: '264'
categories:
  - - 编译原理
  - - c++
date: 2020-03-29 20:46:59
---

**Pascal无符号数识别-C++实现**

1.  问题描述
    
    设计识别Pascal的无符号数的算法。对于每一个输入，给出一个true or false的判定。
    
2.  算法设计
    
    由正规式推出自动机，如图2.1和图2.2：
    
    ![](https://xinhaojin.github.io/imgs-host/past/2020/03/032920_1246_Pascal1.png)
    
    图2.1 无符号数识别-正规式
    

![](https://xinhaojin.github.io/imgs-host/past/2020/03/032920_1246_Pascal2.png)

图2.2 无符号数识别-自动机

该算法严格依据自动机进行条件跳转，并实时更新记录当前状态，然后根据最后所在状态判别是否是无符号数：如果最后状态是如图状态19，那么返回false，否则符合Pascal无符号数的正规式，返回true。

1.  源代码
    
```c++

// pascal_unsigned_number_judge.cpp : 此文件包含 "main" 函数。程序执行将在此处开始并结束。

//

#include <string>

#include <iostream>

using namespace std;

bool judge(string str)

{

    int len = str.length();

    int state = 12;

    for (int i = 0; i < len; i++)

    {

        if (state == 12)

        {

            if (str[i] >= '0' && str[i] <= '9')

            {

                state = 13;
            }

            else

            {

                state = 19;
            }
        }

        else if (state == 13)

        {

            if (str[i] >= '0' && str[i] <= '9')

            {

                state = 13;
            }

            else if (str[i] == 'E')

            {

                state = 16;
            }

            else if (str[i] == '.')

            {

                state = 14;
            }

            else

            {

                state = 19;
            }
        }

        else if (state == 14)

        {

            if (str[i] >= '0' && str[i] <= '9')

            {

                state = 15;
            }

            else

            {

                state = 19;
            }
        }

        else if (state == 15)

        {

            if (str[i] >= '0' && str[i] <= '9')

            {

                state = 15;
            }

            else if (str[i] == 'E')

            {

                state = 16;
            }

            else

            {

                state = 19;
            }
        }

        else if (state == 16)

        {

            if (str[i] >= '0' && str[i] <= '9')

            {

                state = 18;
            }

            else if (str[i] == '+' str[i] == '-')

            {

                state = 17;
            }

            else

            {

                state = 19;
            }
        }

        else if (state == 17)

        {

            if (str[i] >= '0' && str[i] <= '9')

            {

                state = 18;
            }

            else

            {

                state = 19;
            }
        }

        else if (state == 18)

        {

            if (str[i] >= '0' && str[i] <= '9')

            {

                state = 18;
            }

            else

            {

                state = 19;
            }
        }

        else if (state == 19)

        {

            return false;
        }
    }

    if (state == 19)

        return false;

    else

        return true;
}

int main()

{

    string str;

    while (cin \>> str)

    {

        cout << (judge(str) ? "TRUE" : "FALSE") << endl;
    }

    return 0;
}
```

1.  测试结果
    

测试结果如图4.1所示：

![](https://xinhaojin.github.io/imgs-host/past/2020/03/032920_1246_Pascal3.png)

图4.1 测试结果

从测试结果来看，该算法可以有效识别无符号数，尚未发现错误。