---
title: LR(0)文法：构造DFA、分析表，句子推导
tags: []
id: '492'
categories:
  - - 编译原理
  - - c++
date: 2020-05-19 13:02:55
---

### LR分析法介绍

预测分析算法LR(k):

*   L: 从左到右扫描；
*   R:最右推导的逆过程；
*   (k): 向前看k个token。

LR分析法的优点：

*   LR分析器能识别所有可用上下文无关文法描述的程序设计语言的结构；
*   LR分析法是已知的最一般的无回溯移进一归约分析法,而且可以和其他移进归约分析法，而且可以和其他移进规约分析法一样有效地实现；
*   LR分析法分析的文法类包含预测分析法所能分析的文法类；
*   在自左向右扫描输入时,LR分析器能及时察觉语法错误。

LR分析法的缺点：

对典型的程序设计语言文法，手工构造LR分析器的工作量太大。

### 问题描述

输入多条产生式，构造出DFA、LR(0)分析表；

在上面的基础上输入一个句子，判别是否符合LR(0)文法。

### 主要算法设计

3.1构造DFA(确定有限自动机)

两个个参数：当前状态节点current_state,节点编号num

![](https://xinhaojin.github.io/imgs-host/past/2020/05/image.png)

图3-1

采用深度优先的递归遍历：遍历当前状态节点的产生式，如果某一条能产生一个新的状态，则新建一个状态节点，然后添加一条指向新状态的连接，数据结构如图3-2；

![](https://xinhaojin.github.io/imgs-host/past/2020/05/image-1.png)

图3-2

![](https://xinhaojin.github.io/imgs-host/past/2020/05/image-2.png)

图3-3

例如：当前状态是2；状态2遍历到第一条产生式时应生成新的状态节点3（这里的编号和算法实际生成的编号顺序不同），此时暂停外层的遍历，应该进行第二层遍历，从这条产生式的后一条开始遍历，查看是否有可以归并到新建节点里的产生式，在这个例子中，只需查看E->E.+T中“.”之后的符号是不是“$”；

第二层遍历到最后一条产生式时，应计算新状态的闭包，这个例子在状态5体现：完成对状态2的遍历后，状态5只有第一条E->E+.T，此时计算它的闭包，从初始状态中查找，添加对应的产生式进来；

新建一个状态完成后，把新状态放入状态节点向量中（便于查找），节点编号自+1，然后对新状态节点执行该算法。

![](https://xinhaojin.github.io/imgs-host/past/2020/05/image-3.png)

图3-4

3.2构造LR(0)分析表

DFA构造完成后，其实像如下形式的分析表也已经构造完成（与教材上的不同），Goto表实际上就是各个状态之间的联系，DFA中已经记录，Action表只需查看某个状态的特点即可归纳:是句柄就Reduce，是句柄且以结束符$结尾就是Accept，其余都是Shift。

![](https://xinhaojin.github.io/imgs-host/past/2020/05/image-4.png)

图3-5

3.3判断句子是否符合LR(0)文法

首先还是把句子转为token流存储在一个向量中，然后反向存储到一个栈中，标记为未处理字符栈，另有一个已处理字符栈，如图3-6所示：

![](https://xinhaojin.github.io/imgs-host/past/2020/05/image-5.png)

图3-6

初始状态要做的是：把状态1压入状态栈，然后重复做如下操作：

取状态栈栈顶S和未处理字符栈栈顶a,

*   如果是Shift:
    *   新状态压入状态栈；
    *   已处理字符栈压入未处理字符栈栈顶元素；
    *   未处理字符栈弹出栈顶元素；
*   如果是Reduce A->w(w中含有n个token)：
    *   状态栈弹出栈顶元素，重复n次；
    *   已处理字符栈弹出栈顶元素，重复n次；
    *   把A压入未处理字符栈；
*   如果是Accept：
    *   跳出循环，程序结束。

### 测试结果

以下用两个例子来验证结果的正确性：

![](https://xinhaojin.github.io/imgs-host/past/2020/05/image-6.png)

图4-1 例题1

![](https://xinhaojin.github.io/imgs-host/past/2020/05/image-7.png)

图4-2 例题1

![](https://xinhaojin.github.io/imgs-host/past/2020/05/1.png)

图4-3 例题1

![](https://xinhaojin.github.io/imgs-host/past/2020/05/image-9.png)

图4-4 例题2

![](https://xinhaojin.github.io/imgs-host/past/2020/05/2.png)

图4-5 例题2

测试结果均正确无误，说明算法及程序基本正确。

### 实验总结

单单构造DFA就遇到了很多问题，比如计算闭包的问题，建立所有状态联系的问题，经过不断地调试修改才得以解决。后来构造分析表，发现教材上的分析表和课上讲的分析表样式不同，算法和也不能直接套用，只能靠自己摸索总结，经过很多次纸上推导，终于总结出了正确的算法。

程序的优点：

*   支持字符串形式的token，支持产生式用“”连接，扩展性强；
*   可能程序效率不高，但数据结构相对较合理，易查易用易懂；
*   DFA、分析表、句子推导过程输出格式简洁美观；
*   多个样例测试均没有发现问题，程序可靠性强。

### 实验代码
```c++

// LR(0)table.cpp : 此文件包含 "main" 函数。程序执行将在此处开始并结束。
//
#include <iostream>
#include <string>
#include <vector>
#include <stack>
#include <iomanip>
#include <algorithm>
#include <sstream>
#include <cstdio>
#pragma warning(disable : 4996)
#pragma warning(disable : 26495)
#pragma warning(disable : 6001)
#pragma warning(disable : 6031)
using namespace std;
struct ca
{ //产生式
    string str;
    vector<string> vec;
    int index = -1;
};
struct cs
{ //情况--新状态
    string condition;
    int state_number;
};
struct state_node
{ //状态节点
    int state_number = 0;
    vector<ca> ca;
    vector<cs> cs;
};
class LR_0
{
public:
    vector<ca> CA;                      //产生式condition_action
    vector<string> T;                   //终结符
    vector<string> N;                   //非终结符
    vector<state_node*> state_table; //所有状态节点
    state_node*first;                 //初始状态指针
    vector<vector<int>> Goto;
    vector<string> Action;
    void init();                                          //初始化
    void display_state_table();                         //输出DFA
    void cal_closure(state_node* node);                //计算闭包
    int state_exist(ca* ca);                            //判断状态是否已存在
    void create_automaton(state_node* node, int &num); //构造自动机
    void display_parse_table();                         //输出分析表
    bool judge(string str);                               //判别句子是否符合LR0文法
};
string pointNext(ca ca)
{ //返回.后的token
    int len = ca.vec.size();
    for (int i = 0; i < len; i++)
    {
        if (ca.vec[i] == ".")
            if (i < len - 1)
                return ca.vec[i + 1];
    }
    return "";
}
ca*copyca(ca item)
{ //复制产生式
    ca*newca = new ca();
    newca->index = 0;
    newca->str = item.str; //复制
    for (int j = 0; j < int(item.vec.size()); j++)
    {
        newca->vec.push_back(item.vec[j]);
    }
    return newca;
}
void LR_0::cal_closure(state_node*node)
{ //计算DFA每个节点的闭包
    int m = first->ca.size();
    int*arr = new int[m];
    for (int i = 0; i < m; i++)
        arr[i] = 0; //初始化
    int s = node->ca.size();
    int k = s;
    do
    {
        s = node->ca.size();
        for (int i = 0; i < s; i++)
        {
            string temp = pointNext(node->ca[i]);
            int vsize = node->ca[i].vec.size();
            if (node->ca[i].vec[vsize - 1] == "$")
            { // eg:A->B.C$  C开头的产生式也要加入
                if (node->ca[i].vec[vsize - 3] == ".")
                    temp = node->ca[i].vec[vsize - 2];
            }
            if (temp != "")
            {
                for (int j = 0; j < m; j++)
                {
                    if (first->ca[j].str == temp && arr[j] == 0)
                    {
                        node->ca.push_back(* copyca(first->ca[j]));
                        arr[j] = 1;
                        k += 1;
                    }
                }
            }
        }
    } while (k > s); //不再增加时，闭包计算完成
}
void setPoint(ca &v, int x)
{ //给产生式加点
    v.vec.insert(v.vec.begin() + x, ".");
    return;
}
void shiftPoint(ca &v)
{ //产生式移点
    int temp = 0;
    for (int i = 0; i < int(v.vec.size()); i++)
    {
        if (v.vec[i] == ".")
        {
            temp = i;
            break;
        }
    }
    v.vec.erase(v.vec.begin() + temp);
    v.vec.insert(v.vec.begin() + temp + 1, ".");
    return;
}
int LR_0::state_exist(ca*ca)
{ //判断状态是否已经存在
    int len = state_table.size();
    for (int i = 0; i < len; i++)
    {
        if (state_table[i] -> ca[0].str == ca->str)
        {
            if (state_table[i] -> ca[0].vec.size() == ca->vec.size())
            {
                int k = 0;
                for (int j = 0; j < int(state_table[i] -> ca[0].vec.size()); j++)
                {
                    if (state_table[i] -> ca[0].vec[j] == ca->vec[j])
                        k++;
                }
                if (k == int(ca->vec.size()))
                    return i; //已经存在的话返回下标
            }
        }
    }
    return -1; //不存在则返回-1
}
void LR_0::init()
{ //初始化
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
        ca new_item;
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
    int num = 1;
    state_node*first_node = new state_node();
    //先构造初始状态
    first_node->state_number = num++;
    for (int i = 0; i < int(CA.size()); i++)
    {
        ca*p = copyca(CA[i]);
        setPoint(* p, 0);
        first_node->ca.push_back(* p);
    }
    first = first_node;
    //初始状态构造完成
    state_table.push_back(first);
    // S.push(first);
    create_automaton(first, num);
    //递归构造自动机
    return;
}
void LR_0::create_automaton(state_node *current_state, int &num)
{ //构造自动机
    if (int(current_state->ca.size()) == 1)
    { //归约的情况
        int d = int(current_state->ca[0].vec.size());
        if (current_state->ca[0].vec[d - 1] == ".")
            return;
    }
    vector<string> str;
    vector<int*> index;
    int x = 0;
    int s = int(current_state->ca.size());
    int len = 0;
    for (int i = 0; i < s; i++)
    {
        bool flag = true;   //是不是新的状态
        bool flag2 = false; //需不需要跳出循环
        string temp = pointNext(current_state->ca[i]);
        if (temp == "")
            return;
        if (i > 0)
            len = int(str.size());
        for (int j = 0; j < len; j++)
        {
            if (str[j] == temp)
            {
                flag = false;
                break;
            }
        }
        if (flag)
        {
            str.push_back(temp);
            state_node*newstate = new state_node();
            newstate->state_number = num++;
            cs *newcs = new cs();
            newcs->condition = temp;
            newcs->state_number = num - 1;
            for (int k = i; k < s; k++)
            {
                if (pointNext(current_state->ca[k]) == temp)
                {
                    ca*p = copyca(current_state->ca[k]);
                    int ss = p->vec.size();
                    shiftPoint(* p);
                    int res = state_exist(p);
                    if (res != -1)
                    {
                        num--;
                        newcs->state_number = state_table[res] -> state_number;
                        current_state->cs.push_back(* newcs);
                        flag2 = true;
                        break;
                    }
                    newstate->ca.push_back(* p);
                }
                if (k == s - 1)
                { //最后一条完成后，计算闭包
                    cal_closure(newstate);
                }
            }
            if (flag2) //需要跳出循环
                continue;
            current_state->cs.push_back(* newcs);
            state_table.push_back(newstate);
            create_automaton(newstate, num); //递归
        }
    }
    return;
}
void LR_0::display_parse_table()
{ //输出分析表
    int len = state_table.size();
    int s = T.size() + N.size();
    string*str = new string[s];
    int k = 0;
    vector<int> line(s, 0);
    for (int i = 0; i < len; i++)
    {
        Goto.push_back(line);
    }
    for (int i = 0; i < int(T.size()); i++)
    {
        str[k++] = T[i];
    }
    for (int i = 0; i < int(N.size()); i++)
    {
        str[k++] = N[i];
    }
    for (int i = 0; i < len; i++)
    {
        if (state_table[i] -> cs.empty())
        {
            bool flag = false;
            string temp = "Reduce " + state_table[i] -> ca[0].str + "->";
            int l = state_table[i] -> ca[0].vec.size();
            for (int k = 0; k < l; k++)
            {
                temp = temp.append(state_table[i] -> ca[0].vec[k]);
            }
            if (state_table[i] -> ca[0].vec[l - 1] == "." && state_table[i] -> ca[0].vec[l - 2] == "$")
                Action.push_back("Accept");
            else
                Action.push_back(temp);
        }
        else
        {
            Action.push_back("Shift");
        }
        for (int j = 0; j < int(state_table[i] -> cs.size()); j++)
        {
            string temp = state_table[i] -> cs[j].condition;
            for (int k = 0; k < s; k++)
            {
                if (temp == str[k])
                {
                    Goto[state_table[i] -> state_number - 1][k] = state_table[i] -> cs[j].state_number;
                }
            }
        }
    }
    string temp(90, '-');
    cout << setw(8) << "State"
         << "";
    for (int i = 0; i < s; i++)
    {
        cout << setw(8) << str[i];
    }
    cout << setw(16) << "Action";
    cout << "\n";
    cout << temp << endl;
    for (int i = 0; i < len; i++)
    {
        cout << setw(8) << i + 1 << "";
        for (int j = 0; j < s; j++)
        {
            if (Goto[i][j] != 0)
                cout << setw(8) << Goto[i][j];
            else
                cout << setw(8) << " ";
        }
        cout << setw(16) << Action[i];
        cout << '\n';
        cout << temp;
        cout << "\n";
    }
}
void LR_0::display_state_table()
{ //输出DFA
    for (int i = 0; i < int(state_table.size()); i++)
    {
        cout << "State" << state_table[i] -> state_number << ":" << endl;
        for (int j = 0; j < int(state_table[i] -> ca.size()); j++)
        {
            cout << setw(20) << state_table[i] -> ca[j].str << "->";
            for (int k = 0; k < int(state_table[i] -> ca[j].vec.size()); k++)
                cout << state_table[i] -> ca[j].vec[k];
            cout << endl;
        }
        cout << endl;
    }
    cout << "Relation:\n";
    for (int i = 0; i < int(state_table.size()); i++)
    {
        for (int j = 0; j < int(state_table[i] -> cs.size()); j++)
        {
            cout << setw(20) << state_table[i] -> state_number << "-> ";
            cout << state_table[i] -> cs[j].condition << " ->" << state_table[i] -> cs[j].state_number;
            cout << endl;
        }
    }
    cout << endl;
    return;
}
bool LR_0::judge(string w)
{ //判断句子是否符合文法
    bool flag = false;
    // getline(cin, str);//读取一行命令参数
    vector<string> it;
    istringstream temp(w);
    string out;
    while (temp >> out)
        it.push_back(out);
    //以上，转为token流
    stack<string> buffer;
    for (int i = int(it.size()) - 1; i >= 0; i--)
        buffer.push(it[i]);
    //待处理字符存在一个栈中
    stack<state_node*> state;
    state.push(first);
    stack<string> str;
    string*arr = new string[int(T.size() + N.size())];
    int j = 0;
    for (int i = 0; i < int(T.size()); i++)
    {
        arr[j++] = T[i];
    }
    for (int i = 0; i < int(N.size()); i++)
    {
        arr[j++] = N[i];
    }
    int a = 0;
    cout << "\n";
    string tstr(90, '-');
    cout << setw(20) << "State_stack" << setw(20) << "Processed" << setw(30) << "Unprocessed" << setw(20) << "Action_required" << endl;
    cout << tstr << endl;
    while (1)
    {
        string outputstr1 = "";
        stack<state_node*> temp_state_stack;
        while (!state.empty())
        {
            temp_state_stack.push(state.top());
            state.pop();
        }
        while (!temp_state_stack.empty())
        {
            state.push(temp_state_stack.top());
            outputstr1.append(to_string(temp_state_stack.top()->state_number) + " ");
            temp_state_stack.pop();
        }
        cout << setw(20) << outputstr1;
        string outputstr2 = "";
        stack<string> temp_string_stack;
        while (!str.empty())
        {
            temp_string_stack.push(str.top());
            str.pop();
        }
        while (!temp_string_stack.empty())
        {
            str.push(temp_string_stack.top());
            outputstr2.append(temp_string_stack.top() + " ");
            temp_string_stack.pop();
        }
        cout << setw(20) << outputstr2;
        string outputstr3 = "";
        while (!buffer.empty())
        {
            temp_string_stack.push(buffer.top());
            outputstr3.append(buffer.top() + " ");
            buffer.pop();
        }
        while (!temp_string_stack.empty())
        {
            buffer.push(temp_string_stack.top());
            temp_string_stack.pop();
        }
        cout << setw(30) << outputstr3;
        if (buffer.empty()) //输入串已到末尾
        {
            flag = true;
            cout << setw(20) << "Accept!\n";
            break;
        }
        string action = "";
        int s = state.top()->state_number - 1;
        for (int i = 0; i < int(T.size() + N.size()); i++)
        {
            if (buffer.top() == arr[i]) //待输入的字符栈顶元素
            {
                a = i; //获取在表格中的列号
                break;
            }
        }
        // s:行号 a:列号
        if (Goto[s][a] != 0) // shift
        {
            for (int i = 0; i < int(state_table.size()); i++)
            {
                if (state_table[i] -> state_number == Goto[s][a])
                {
                    state.push(state_table[i]);
                    break;
                }
            }
            str.push(buffer.top());
            buffer.pop();
            action = "Shift,go to" + to_string(Goto[s][a]);
        }
        else
        {
            if (Action[s][0] == 'R')
            {
                int index = 0;
                for (int i = 0; i < int(state_table.size()); i++)
                {
                    if (state_table[i] -> state_number == s + 1)
                    {
                        index = i; //找到行号
                        break;
                    }
                }
                int vsize = state_table[index] -> ca[0].vec.size() - 1;
                while (vsize--)
                {
                    state.pop();
                    str.pop();
                }
                buffer.push(state_table[index] -> ca[0].str);
                int q = Action[s].length();
                action = Action[s].substr(0, q - 1);
            }
            else
                action = Action[s];
        }
        //输出

        cout << setw(20) << action << endl;
        cout << tstr << endl;
        if (flag)
            return flag;
    }
    return flag;
}
int main()
{
    // freopen("in.txt", "r", stdin);
    LR_0 tag;
    tag.init();
    tag.display_state_table();
    tag.display_parse_table();
    // tag.judge("int +  ( int + int ) $");
    cout << "请输入句子，每个token间用空格分隔，以$结尾:\n";
    string str;
    getline(cin, str);
    tag.judge(str);
    return 0;
}
```