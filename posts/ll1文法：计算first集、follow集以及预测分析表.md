---
title: LL(1)文法：计算First集、Follow集以及预测分析表
tags: []
id: '349'
categories:
  - - 编译原理
  - - c++
date: 2020-04-22 02:04:04
---

#### 问题描述

预测分析算法LL(1):

*   L: Left-to-right scan of the tokens从左到右扫描
*   L: Leftmost derivation最左推导
*   (1): One token of lookahead只向前看一个token

输入若干个字符流形式的产生式，计算非终结符的First集和Follow集，并构造预测分析表。

#### 算法设计

2.1注意点

*   输入为字符流，应首先转为token流；
*   输入产生式可能很长，用‘’连接，最好全部转为最短形式，去掉‘’；
*   产生式、终结符、非终结符、First集、Follow集等应当用合适的数据结构存储，一次计算便写入，便于查找和调用；
*   First集和Follow集计算应采用递归算法。

2.2用到的算法

      2.2.1First集

*   如果 X 是终结符号，那么FIRST(X)={X}
*   如果 X 是非终结符号，且 X -> Y1Y2Y3…Yk 是产生式
*   如果a在FIRST(Yi)中，且 ε 在FIRST(Y1)，FIRST(Y2)，…，FIRST(Yi-1)中，那么a也在FIRST(X)中；
*   如果ε 在FIRST(Y1)，FIRST(Y2)，…，FIRST(Yk)中，那么ε在FIRST(X)中；
*   如果X是非终结符号，且有X->ε，那么ε在FIRST(X)中。

       2.2.2Follow集

*   将右端结束标记 $ 放到 Follow (S) 中 ；
*   按照下面两个规则不断迭代，知道所有的Follow集合都不再增长为止 ；
*   如果存在产生式A -> αBβ ，那么 FIRST(β)中所有非ε的符号都在Follow (B)中；
*   如果存在产生式A -> αB，或者A -> αBβ 且FIRST(β)包含ε，那么Follow (A)中的所有符号都加入到Follow (B)中。

              2.2.3预测分析表

*   对于每个产生式A->α：
*   对于First(α)中每个终结符α，将A->α加入到Table[A，a]中；
*   如果ε在First(α)中，那么对于Follow(A)中的每个终结符b，将A->α加入到Table[A，b]中；如果ε在First(α)中，且$在Follow(A)中，也将A->α加入到Table[A，$]中。

#### 测试结果

为了方便验证结果的正确性，测试时采用了4组课件中提到的例题，结果对比如下：

![](https://xinhaojin.github.io/imgs-host/past/2020/04/3.1-1024x614.png)

图3-1例题1

![](https://xinhaojin.github.io/imgs-host/past/2020/04/3.2-1024x739.png)

图3-2例题1

![](https://xinhaojin.github.io/imgs-host/past/2020/04/3.3.png)

图3-3例题2

![](https://xinhaojin.github.io/imgs-host/past/2020/04/3.4-1024x853.png)

图3-4例题2

![](https://xinhaojin.github.io/imgs-host/past/2020/04/3.5-1024x668.png)

图3-5例题3

![](https://xinhaojin.github.io/imgs-host/past/2020/04/3.6-1024x853.png)

图3-6例题3

![](https://xinhaojin.github.io/imgs-host/past/2020/04/3.7-1024x709.png)

图3-7例题4

![](https://xinhaojin.github.io/imgs-host/past/2020/04/3.8-1024x263.png)

图3-8例题4

![](https://xinhaojin.github.io/imgs-host/past/2020/04/3.9-1024x925.png)

图3-9例题4

经过多次调试修改，4个例题测试结果都已经正确，应当足以说明程序的可靠性。

#### 实验代码
```c++
// LL(1)Parse_Tables.cpp : 此文件包含 "main" 函数。程序执行将在此处开始并结束。
#include <iostream>
#include <sstream>
#include <string>
#include <stack>
#include <vector>
#include <iomanip>
using namespace std;
struct item
{ //辅助数据类型
    string str;
    vector<string> vec;
};
class LL_1
{
public:
    vector<item> CA;                              //产生式condition_action
    vector<string> T;                             //终结符
    vector<string> N;                             //非终结符
    vector<item> FIRST_SET;                       //非终结符FIRST(A)={tA->*tv}
    vector<item> FOLLOW_SET;                      //哪些终结符会跟在非终结符后面FOLLOW(A)={tB->*wAtv}
    int PARSE_TABLE[100][100];                    //分析表
    void init();                                  //输入及初始化
    void display_state();                         //输出初始化后的状态
    void display_first();                         //输出first集
    void display_follow();                        //输出follow集
    void display_table();                         //输出LL_1分析表
    bool isTerminal(string str);                  //判断是否为终结符
    bool containEpsilon(string str);              //判断first集是否包含epsilon
    int find_index(string str);                   //获取某个符号在终结符向量或非终结符向量中的下标
    bool find_first(string str, item &new_item);  // first集
    bool find_follow(string str, item &new_item); // follow集
    bool create_parse_table(int i);               //构造预测分析表
};
void LL_1::display_state()
{ //输出等价产生式、归纳出终结符和非终结符
    int len1 = int(CA.size());
    int len2 = int(T.size());
    int len3 = int(N.size());
    cout << "\n等价产生式：\n";
    for (int i = 0; i < len1; i++)
    {
        cout << i + 1 << ". " << CA[i].str << " -> ";
        for (int j = 0; j < int(CA[i].vec.size()); j++)
        {
            cout << CA[i].vec[j] << " ";
        }
        cout << endl;
    }
    cout << "\n终结符：\n";
    for (int i = 0; i < len2; i++)
    {
        cout << T[i];
        if (i < len2 - 1)
            cout << " ,";
    }
    cout << endl;
    cout << "\n非终结符：\n";
    for (int i = 0; i < len3; i++)
    {
        cout << N[i];
        if (i < len3 - 1)
            cout << " ,";
    }
    cout << endl;
}
void LL_1::display_first()
{ //输出first集
    int len = int(FIRST_SET.size());
    cout << "\n非终结符的FIRST集:\n";
    for (int i = 0; i < len; i++)
    {
        int k = int(FIRST_SET[i].vec.size());
        cout << "FIRST(" << FIRST_SET[i].str << ")={ ";
        for (int j = 0; j < k; j++)
        {
            cout << FIRST_SET[i].vec[j];
            if (j < k - 1)
                cout << ",";
        }
        cout << " }\n";
    }
}
void LL_1::display_follow()
{ //输出follow集
    int len = int(FOLLOW_SET.size());
    cout << "\n非终结符的FOLLOW集:\n";
    for (int i = 0; i < len; i++)
    {
        int k = int(FOLLOW_SET[i].vec.size());
        cout << "FOLLOW(" << FOLLOW_SET[i].str << ")={ ";
        for (int j = 0; j < k; j++)
        {
            cout << FOLLOW_SET[i].vec[j];
            if (j < k - 1)
                cout << ",";
        }
        cout << " }\n";
    }
}
void LL_1::display_table()
{ //输出分析表
    cout << "\nLL(1)分析表：\n";
    int len_T = int(T.size());
    int len_N = int(N.size());
    cout << setw(8) << " ";
    for (int i = 0; i < len_T; i++)
    {
        cout << setw(8) << T[i];
    }
    cout << endl;
    for (int i = 0; i < len_N; i++)
    {
        cout << setw(8) << N[i];
        for (int j = 0; j < len_T; j++)
        {
            if (PARSE_TABLE[i][j] == 0)
                cout << setw(8) << "x";
            else
                cout << setw(8) << PARSE_TABLE[i][j];
        }
        cout << endl;
    }
}
bool LL_1::create_parse_table(int i)
{ //构造分析表
    int len_T = int(T.size());
    int len_N = int(N.size());
    int len_CA = int(CA.size());
    int x = 0, y = 0, content = 0; // table的第x行，第y列,填入第content条产生式
    content = i + 1;               //从1开始
    x = find_index(CA[i].str);
    string temp = CA[i].vec[0];
    if (isTerminal(temp))
    {
        if (temp != "epsilon")
        {
            y = find_index(temp);
            if (PARSE_TABLE[x][y] == content)
                return true; //同一内容写第二次，进入了递归死循环，直接跳出
            else
                PARSE_TABLE[x][y] = content;
        }
        else
        { //是空产生式
            vector<string> &vec = FOLLOW_SET[x].vec;
            for (int j = 0; j < int(vec.size()); j++)
            { //遍历Follow集
                y = find_index(vec[j]);
                if (PARSE_TABLE[x][y] == content)
                    return true; //同一内容写第二次，进入了递归死循环，直接跳出
                else
                    PARSE_TABLE[x][y] = content;
            }
        }
    }
    else
    { //非终结符
        int len = int(CA[i].vec.size());
        int p = 1;
        bool flag = false;
        do
        {
            vector<string> &vec = FIRST_SET[find_index(temp)].vec;
            int s = int(vec.size());
            for (int j = 0; j < s; j++)
            { //遍历First集
                if (vec[j] == "epsilon")
                {
                    flag = true;
                    continue;
                }
                y = find_index(vec[j]);
                if (PARSE_TABLE[x][y] == content)
                    return true; //同一内容写第二次，进入了递归死循环，直接跳出
                else
                    PARSE_TABLE[x][y] = content;
            }
            if (p < len && flag)
            {
                temp = CA[i].vec[p];
                if (!isTerminal(temp))
                {
                    for (int j = 0; j < len_CA; j++)
                    {
                        if (CA[j].str == temp)
                        {
                            create_parse_table(j); //函数进入递归
                        }
                    }
                    p++;
                }
                else
                {
                    y = find_index(temp);
                    if (PARSE_TABLE[x][y] == content)
                        return true; //同一内容写第二次，进入了递归死循环，直接跳出
                    else
                        PARSE_TABLE[x][y] = content;
                    flag = false;
                }
            }
            if (p == len && flag)
            { //产生式右边所有都是非终结符，且都能推出epsilon
                vector<string> &vec_temp = FOLLOW_SET[x].vec;
                for (int j = 0; j < int(vec_temp.size()); j++)
                { //遍历Follow集
                    y = find_index(vec_temp[j]);
                    if (PARSE_TABLE[x][y] == content)
                        return true; //同一内容写第二次，进入了递归死循环，直接跳出
                    else
                        PARSE_TABLE[x][y] = content;
                }
            }
        } while (flag);
    }
    return true;
}
void LL_1::init()
{
    string str;
    vector<string> v;
    cout << "请输入若干条形如‘ STMT -> if EXPR then STMT  EXPR ’的产生式：\n";
    cout << "(每个token之间用空格分隔，输入0结束)\n";
    while (1)
    {
        getline(cin, str); //获取一行标准输入，即字符流形式的产生式
        if (str == "0")
            break;
        int l = str.length();
        vector<int> index;
        int begin = 0;
        int end = 0;
        //以下将每一行化成若干个最短产生式
        for (int i = 0; i < l; i++)
        {
            if (str[i] == '>' && i > 0)
            {
                if (str[i - 1] == '-')
                    begin = i;
            }
            if (str[i] == '')
            {
                index.push_back(i);
            }
        }
        string prefix = str.substr(0, begin + 1);
        int len = int(index.size());
        if (len == 0)
            v.push_back(str);
        else
        {
            for (int i = 0; i < len; i++)
            {
                if (i == 0)
                {
                    v.push_back(str.substr(0, index[0]));
                    end = index[0];
                    continue;
                }
                begin = index[i - 1];
                end = index[i];
                v.push_back(prefix + str.substr(begin + 1, end - begin - 1));
            }
            v.push_back(prefix + str.substr(end + 1, l - end));
        }
    } //以上将每一行化成若干个最短产生式，放到向量V中
    //以下给token归类，分为终结符和非终结符
    vector<string> left, right;
    int len = int(v.size());
    for (int i = 0; i < len; i++)
    {
        istringstream temp(v[i]);
        string out;
        int cal = 0;
        item new_item;
        while (temp >> out)
        {
            if (cal == 0)
            {
                left.push_back(out);
                new_item.str = out;
            }
            else if (cal == 1)
            {
                cal++;
                continue;
            }
            else
            {
                right.push_back(out);
                new_item.vec.push_back(out);
            }
            cal++;
        }
        CA.push_back(new_item);
    }
    int len1 = int(left.size());
    int len2 = int(right.size());
    vector<string>::iterator it;
    for (int k = 0; k < len1; k++)
    {
        it = find(N.begin(), N.end(), left[k]);
        if (it == N.end()) //不存在
        {
            N.push_back(left[k]); //产生式左边的一定是非终结符
        }
    }
    for (int i = 0; i < len2; i++)
    {
        bool flag = true; //默认是终结符
        for (int j = 0; j < len1; j++)
        {
            if (right[i] == left[j]) //产生式左边出现过
            {
                flag = false; //是非终结符
                break;
            }
        }
        if (flag)
        {
            it = find(T.begin(), T.end(), right[i]);
            if (it == T.end()) //不存在
            {
                T.push_back(right[i]);
            }
        }
        else
        {
            it = find(N.begin(), N.end(), right[i]);
            if (it == N.end()) //不存在
            {
                N.push_back(right[i]);
            }
        }
    }
    T.push_back("$");
    for (int iter = 0; iter < int(T.size()); iter++)
    {
        if (T[iter] == "epsilon")
        {
            T.erase(T.begin() + iter);
            break;
        }
    }
}
bool LL_1::isTerminal(string str)
{ //判断某个token是否为终结符
    if (str == "epsilon")
        return true;
    vector<string>::iterator it;
    it = find(T.begin(), T.end(), str);
    if (it == T.end()) //不存在
    {
        return false;
    }
    return true;
}
bool LL_1::containEpsilon(string str)
{ //判断某个非终结符的first集中是否包含epsilon
    for (int i = 0; i < int(CA.size()); i++)
    {
        if (CA[i].str == str)
        {
            string temp = CA[i].vec[0];
            if (!isTerminal(temp))
                containEpsilon(temp);
            else
            {
                if (temp == "epsilon")
                    return true;
            }
        }
    }
    return false;
}
int LL_1::find_index(string str)
{
    int len_N = int(N.size());
    int len_T = int(T.size());
    for (int i = 0; i < len_N; i++)
    {
        if (N[i] == str)
            return i;
    }
    for (int i = 0; i < len_T; i++)
    {
        if (T[i] == str)
            return i;
    }
    return -1;
}
bool LL_1::find_first(string str, item &new_item)
{ //计算某个非终结符的first集
    for (int i = 0; i < int(CA.size()); i++)
    {
        if (CA[i].str == str)
        {
            string temp = CA[i].vec[0];
            if (!isTerminal(temp))
            { //非终结符
                find_first(temp, new_item);
                if (containEpsilon(temp))
                { //能产生epsilon
                    int p = 1;
                    int s = int(CA[i].vec.size());
                    while (s > p)
                    {
                        if (!isTerminal(CA[i].vec[p]))
                        {
                            if (containEpsilon(CA[i].vec[p]))
                            {
                                find_first(CA[i].vec[p], new_item);
                                for (int iter = 0; iter < int(new_item.vec.size()); iter++)
                                {
                                    if (new_item.vec[iter] == "epsilon")
                                    {
                                        new_item.vec.erase(new_item.vec.begin() + iter);
                                        break;
                                    }
                                }
                                p++;
                            }
                            else
                                break;
                        }
                        else
                        {
                            new_item.vec.push_back(CA[i].vec[p]);
                            break;
                        }
                    }
                    if (p == s - 1)
                    {
                        vector<string>::iterator it;
                        for (int k = 0; k < s; k++)
                        {
                            it = find(N.begin(), N.end(), "epsilon");
                            if (it == N.end()) //不存在
                            {
                                N.push_back("epsilon");
                            }
                        }
                    }
                }
            }
            else
            { //是终结符，直接加入，去重
                vector<string>::iterator it;
                it = find(new_item.vec.begin(), new_item.vec.end(), temp);
                if (it == new_item.vec.end()) //不存在
                {
                    new_item.vec.push_back(temp);
                }
            }
        }
    }
    return true;
}
bool LL_1::find_follow(string str, item &new_item)
{ //计算某个非终结符的follow集
    if (str == CA[0].str)
    { //是第一个产生式的左边符号，加入结束符$
        vector<string>::iterator it;
        it = find(new_item.vec.begin(), new_item.vec.end(), "$");
        if (it == new_item.vec.end()) //不存在
        {                             //开始符号FOLLOW集中加入结束符$
            new_item.vec.push_back("$");
        }
    }
    for (int i = 0; i < int(CA.size()); i++)
    { //遍历所有产生式，在其右边找目标非终结符
        int s = int(CA[i].vec.size());
        for (int j = 0; j < s; j++)
        {
            if (CA[i].vec[j] == str)
            {                  //找到
                if (j + 1 < s) // B->wAt
                {
                    if (!isTerminal(CA[i].vec[j + 1])) //下一个是非终结符
                    {
                        if (containEpsilon(CA[i].vec[j + 1]))   // first集包含epsilon//B->wA
                            find_follow(CA[i].str, new_item);   // follow(A)=follow(B)+follow(A)
                        find_first(CA[i].vec[j + 1], new_item); // follow(A)=first(A)-epsilon
                        for (int iter = 0; iter < int(new_item.vec.size()); iter++)
                        {
                            if (new_item.vec[iter] == "epsilon")
                            {
                                new_item.vec.erase(new_item.vec.begin() + iter);
                                break;
                            }
                        }
                    }
                    else
                    { //下一个是终结符,直接加入，去重
                        vector<string>::iterator it;
                        it = find(new_item.vec.begin(), new_item.vec.end(), CA[i].vec[j + 1]);
                        if (it == new_item.vec.end()) //不存在
                        {
                            new_item.vec.push_back(CA[i].vec[j + 1]);
                        }
                    }
                }
                else                               // B->wA
                {                                  // follow(A)=follow(B)+follow(A)
                    if (CA[i].str != CA[i].vec[j]) //防止A->wA形式死循环
                        find_follow(CA[i].str, new_item);
                }
            }
        }
    }
    return true;
}
int main()
{
    LL_1 tag;
    tag.init();
    tag.display_state();
    int len = int(tag.N.size());
    for (int i = 0; i < len; i++)
    { //遍历非终结符，逐个计算其first集
        item new_item;
        item &new_ = new_item;
        new_item.str = tag.N[i];
        tag.find_first(tag.N[i], new_); // FIRST
        tag.FIRST_SET.push_back(new_);
    }
    tag.display_first();
    for (int i = 0; i < len; i++)
    { //遍历非终结符，逐个计算其follow集
        item new_item;
        item &new_ = new_item;
        new_item.str = tag.N[i];
        tag.find_follow(tag.N[i], new_); // FOLLOW
        tag.FOLLOW_SET.push_back(new_);
    }
    tag.display_follow();
    for (int i = 0; i < int(tag.CA.size()); i++)
        tag.create_parse_table(i); //按产生式填写table
    tag.display_table();
    return 0;
}
```
#### 实验总结

本次实验相比较于之前的实验难度较大，用到的算法较多，情况判别也有些复杂，其中有3处要使用递归思想计算（First集和Follow集，以非终结符为单位进行计算；Parse_table，以最短产生式为单位进行计算），否则比较难处理。实验耗时较长，但好在完成度比较高，也达到了预期效果，能够实现对几若干条字符流形式的产生式进行自动分析计算：转为token流、列出等价最短产生式、自动归类终结符和非终结符、列出每个非终结符的First集和Follow集、列出准确的预测分析表，且有令人舒适的输出格式。不足之处：多次递归使用了较大的堆栈空间、预测分析表用了100*100的静态表存储…总的来讲本次实验让我对LL(1)算法有了深刻理解，帮助很大。