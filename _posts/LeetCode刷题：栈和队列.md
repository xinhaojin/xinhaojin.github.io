---
title: LeetCode刷题：栈和队列
categories:
  - Java
  - LeetCode
date: 2023-05-20 14:30:00
---
## 用栈实现队列
请你仅使用两个栈实现先入先出队列。队列应当支持一般队列支持的所有操作（push、pop、peek、empty）：

实现 MyQueue 类：

void push(int x) 将元素 x 推到队列的末尾
int pop() 从队列的开头移除并返回元素
int peek() 返回队列开头的元素
boolean empty() 如果队列为空，返回 true ；否则，返回 false
说明：

你 只能 使用标准的栈操作 —— 也就是只有 push to top, peek/pop from top, size, 和 is empty 操作是合法的。
你所使用的语言也许不支持栈。你可以使用 list 或者 deque（双端队列）来模拟一个栈，只要是标准的栈操作即可。

https://leetcode.cn/problems/implement-queue-using-stacks
### 思路
```
1.使用了两个栈 stk1 和 stk2，其中 stk1 用于存储队列中的元素，stk2 用于辅助实现队列的 pop() 和 peek() 操作
```
### 代码
```java
class MyQueue {
    Stack<Integer> stk1, stk2;

    /** Initialize your data structure here. */
    public MyQueue() {
        // 初始化两个栈
        stk1 = new Stack<>();
        stk2 = new Stack<>();
    }

    /** Push element x to the back of queue. */
    public void push(int x) {
        // 将元素压入 stk1 栈中
        stk1.push(x);
    }

    /** Removes the element from in front of queue and returns that element. */
    public int pop() {
        // 如果 stk2 栈为空，则将 stk1 中的元素依次弹出并压入 stk2 栈中
        if (stk2.isEmpty()) {
            while(!stk1.isEmpty()){
                int top = stk1.pop();
                stk2.push(top);
            }
        }
        // 从 stk2 栈中弹出队首元素
        int result = stk2.pop();
        // 将 stk2 中的元素再次依次弹出并压入 stk1 栈中，以保持栈的状态不变
        while(!stk2.isEmpty()){
            int top = stk2.pop();
            stk1.push(top);
        }
        // 返回队首元素
        return result;
    }

    /** Get the front element. */
    public int peek() {
        // 如果 stk2 栈为空，则将 stk1 中的元素依次弹出并压入 stk2 栈中
        if (stk2.isEmpty()) {
            while(!stk1.isEmpty()){
                int top = stk1.pop();
                stk2.push(top);
            }
        }
        // 获取 stk2 栈的栈顶元素
        int result = stk2.peek();
        // 将 stk2 中的元素再次依次弹出并压入 stk1 栈中，以保持栈的状态不变
        while(!stk2.isEmpty()){
            int top = stk2.pop();
            stk1.push(top);
        }
        // 返回队首元素
        return result;
    }

    /** Returns whether the queue is empty. */
    public boolean empty() {
        // 判断 stk1 是否为空即可
        return stk1.isEmpty();
    }
}
```
## 用队列实现栈
请你仅使用两个队列实现一个后入先出（LIFO）的栈，并支持普通栈的全部四种操作（push、top、pop 和 empty）。

实现 MyStack 类：

void push(int x) 将元素 x 压入栈顶。
int pop() 移除并返回栈顶元素。
int top() 返回栈顶元素。
boolean empty() 如果栈是空的，返回 true ；否则，返回 false 。
 

注意：

你只能使用队列的基本操作 —— 也就是 push to back、peek/pop from front、size 和 is empty 这些操作。
你所使用的语言也许不支持队列。 你可以使用 list （列表）或者 deque（双端队列）来模拟一个队列 , 只要是标准的队列操作即可。

https://leetcode.cn/problems/implement-stack-using-queues
### 思路
```
1.使用了两个队列 que1 和 que2 来实现栈。在每一次执行 push() 操作时，我们先将新元素 x 添加到 que2 队列中，然后将 que1 中的所有元素依次添加到 que2 队列中，这样可以保证 que2 队列中的元素是按照后进先出的顺序排列的。接着，我们再将 que2 中的所有元素依次添加回到 que1 队列中，这样就实现了栈的状态
```
### 代码
```java
class MyStack {
    // 定义两个队列
    Queue<Integer> que1, que2;

    public MyStack() {
        // 初始化队列
        que1 = new LinkedList<>();
        que2 = new LinkedList<>();
    }
    
    public void push(int x) {
        // 将元素添加到 que2 队列中
        que2.add(x);
        // 将 que1 中的元素依次添加到 que2 队列中
        while (!que1.isEmpty()) {
            que2.add(que1.remove());
        }
        // 将 que2 中的元素依次添加到 que1 队列中，从而维持栈的状态
        while (!que2.isEmpty()) {
            que1.add(que2.remove());
        }
    }
    
    public int pop() {
        // 直接从 que1 队列中删除并返回队首元素即可
        return que1.remove();
    }
    
    public int top() {
        // 返回 que1 队列的队首元素即可
        return que1.peek();
    }
    
    public boolean empty() {
        // 判断 que1 是否为空即可
        return que1.isEmpty();
    }
}
```
## 有效的括号
给定一个只包括 '('，')'，'{'，'}'，'['，']' 的字符串 s ，判断字符串是否有效。

有效字符串需满足：

左括号必须用相同类型的右括号闭合。
左括号必须以正确的顺序闭合。
每个右括号都有一个对应的相同类型的左括号。

https://leetcode.cn/problems/valid-parentheses
### 思路
```
1.遍历字符串，遇到左括号入栈，遇到右括号查看与栈顶元素是否匹配
2.字符串长度为奇数肯定false,处理右括号时栈空肯定false,最后栈不空肯定false
```
### 代码
```java
public boolean isValid(String s) {
    // 如果字符串长度为奇数，一定不是有效的括号序列
    if(s.length()%2==1) return false;
    // 创建一个栈来保存左括号
    Stack<Character> stk=new Stack<>();
    char[] arr=s.toCharArray();
    // 遍历字符串中的每一个字符
    for(char c : arr){
        // 如果遇到左括号，将其入栈
        if(c=='(' || c=='{' || c=='['){
            stk.push(c);
        }
        // 如果遇到右括号
        else{
            // 如果栈为空，说明没有与之匹配的左括号，返回 false
            if(stk.isEmpty()) return false;
            // 取出栈顶元素
            char top=stk.peek();
            // 如果右括号与栈顶元素匹配，将栈顶元素弹出
            if(top=='(' && c==')' || top=='{' && c=='}' || top=='[' && c==']'){
                stk.pop();
            }
            // 如果右括号与栈顶元素不匹配，返回 false
            else return false;
        }
    }
    // 如果栈为空，说明所有的左括号都有与之匹配的右括号，是一个有效的括号序列，返回 true，否则返回 false
    if(stk.isEmpty()) return true;
    else return false;
}
```
## 删除字符串中的所有相邻重复项
给出由小写字母组成的字符串 S，重复项删除操作会选择两个相邻且相同的字母，并删除它们。

在 S 上反复执行重复项删除操作，直到无法继续删除。

在完成所有重复项删除操作后返回最终的字符串。答案保证唯一。
**示例**
```
输入："abbaca"
输出："ca"
解释：
例如，在 "abbaca" 中，我们可以删除 "bb" 由于两字母相邻且相同，这是此时唯一可以执行删除操作的重复项。之后我们得到字符串 "aaca"，其中又只有 "aa" 可以执行重复项删除操作，所以最后的字符串为 "ca"。
```
https://leetcode.cn/problems/remove-all-adjacent-duplicates-in-string
### 思路
```
1.遍历字符串，如果当前字符与站定元素相同，pop
2.new一个栈size大小的char[]，循环pop栈顶元素，从数组末尾开始放
3.把字符数组转为String，使用构造函数new String(arr)
```
### 代码
```java
public String removeDuplicates(String s) {
    // 创建一个栈来保存字符
    Stack<Character> stk=new Stack<>();
    char[] arr=s.toCharArray();
    // 遍历字符串中的每一个字符
    for(char c : arr){
        // 如果栈不为空，且当前字符与栈顶元素相同，将栈顶元素弹出
        if(!stk.isEmpty() && stk.peek()==c){
            stk.pop();
        }
        // 否则，将当前字符入栈
        else{
            stk.push(c);
        }
    }
    // 将栈中的元素依次弹出，放入一个字符数组中
    char[] result=new char[stk.size()];
    int index=stk.size()-1;
    while(!stk.isEmpty()){
        result[index--]=stk.pop();
    }
    // 将字符数组转换为字符串并返回
    return new String(result);
}
```
## 逆波兰表达式求值(后缀转中缀)
给你一个字符串数组 tokens ，表示一个根据 逆波兰表示法 表示的算术表达式。

请你计算该表达式。返回一个表示表达式值的整数。

注意：

有效的算符为 '+'、'-'、'*' 和 '/' 。
每个操作数（运算对象）都可以是一个整数或者另一个表达式。
两个整数之间的除法总是 向零截断 。
表达式中不含除零运算。
输入是一个根据逆波兰表示法表示的算术表达式。
答案及所有中间计算结果可以用 32 位 整数表示。
**示例**
```
输入：tokens = ["2","1","+","3","*"]
输出：9
解释：该算式转化为常见的中缀算术表达式为：((2 + 1) * 3) = 9
```
https://leetcode.cn/problems/evaluate-reverse-polish-notation
### 思路
```
1.遍历字符串数组，遇到数字入栈，遇到操作符弹出两个操作数处理后入栈。
```
### 代码
```java
public int evalRPN(String[] tokens) {
    // 创建一个栈来保存操作数和中间计算结果
    Stack<Integer> stack = new Stack<>();
    // 遍历字符串数组中的每一个元素
    for (String token : tokens) {
        // 如果当前元素是操作符，则从栈中弹出两个操作数进行计算，并将结果压入栈中
        if ("+-*/".contains(token)) {
            int b = stack.pop();
            int a = stack.pop();
            switch (token) {
                case "+":
                    stack.push(a + b);
                    break;
                case "-":
                    stack.push(a - b);
                    break;
                case "*":
                    stack.push(a * b);
                    break;
                case "/":
                    stack.push(a / b);
                    break;
            }
        } else {
            // 如果当前元素不是操作符，则将其作为操作数压入栈中
            stack.push(Integer.parseInt(token));
        }
    }
    // 栈中最后剩下的元素就是计算结果，将其返回
    return stack.pop();
}
```
## 滑动窗口最大值
给你一个整数数组 nums，有一个大小为 k 的滑动窗口从数组的最左侧移动到数组的最右侧。你只可以看到在滑动窗口内的 k 个数字。滑动窗口每次只向右移动一位。

返回 滑动窗口中的最大值 。
**示例**
```
输入：nums = [1,3,-1,-3,5,3,6,7], k = 3
输出：[3,3,5,5,6,7]
解释：
滑动窗口的位置                最大值
---------------               -----
[1  3  -1] -3  5  3  6  7       3
 1 [3  -1  -3] 5  3  6  7       3
 1  3 [-1  -3  5] 3  6  7       5
 1  3  -1 [-3  5  3] 6  7       5
 1  3  -1  -3 [5  3  6] 7       6
 1  3  -1  -3  5 [3  6  7]      7
```
https://leetcode.cn/problems/sliding-window-maximum
### 思路
```
1.创建了一个双端队列 deque，用于维护滑动窗口中的元素。同时，我们创建了一个长度为 n - k + 1 的数组 result，用于存储每个滑动窗口中的最大值
2.将队列中比当前元素小的元素都删除，并将当前元素加入队列中。
3.判断队列头部的元素是否已经不在滑动窗口中，如果是，就将其从队列中删除。这样，队列头部的元素就始终是当前滑动窗口中的最大值。
4.判断当前滑动窗口的大小是否为 k。如果是，就将队列头部的元素加入结果数组中。
```
### 代码
```java
public int[] maxSlidingWindow(int[] nums, int k) {
    int n = nums.length;
    Deque<Integer> deque = new LinkedList<>();
    int[] result = new int[n - k + 1];
    int idx = 0;
    for (int i = 0; i < n; i++) {
        // 将队列中比当前元素小的元素都删除
        while (!deque.isEmpty() && deque.peekLast() < nums[i]) {
            deque.removeLast();
        }
        deque.addLast(nums[i]);
        // 如果队列头部的元素已经不在滑动窗口中，将其删除
        if (i >= k && deque.peekFirst() == nums[i - k]) {
            deque.removeFirst();
        }
        // 如果当前滑动窗口的大小为 k，将队列头部的元素加入结果数组中
        if (i >= k - 1) {
            result[idx++] = deque.peekFirst();
        }
    }
    return result;
}
```
## 前 K 个高频元素
给你一个整数数组 nums 和一个整数 k ，请你返回其中出现频率前 k 高的元素。你可以按 任意顺序 返回答案。

https://leetcode.cn/problems/top-k-frequent-elements/
### 思路
```
1.使用 HashMap 统计每个元素出现的次数，元素为键，出现次数为值。
2.创建一个 PriorityQueue（优先队列），用于存储出现频率前 k 高的元素。在 PriorityQueue 中，元素按照出现次数从大到小排序，因此它是一个大顶堆。遍历 HashMap，将每个键值对转化为 Map.Entry，并将其加入 PriorityQueue 中。如果 PriorityQueue 的大小超过了 k，就将其中出现次数最小的元素移除。
3.将 PriorityQueue 中前 k 个元素取出并存入结果数组中。由于 PriorityQueue 是一个大顶堆，取出的元素是按照出现次数从大到小排序的，因此结果数组中的元素是按照出现频率从高到低排序的。
```
### 代码
```java
public int[] topKFrequent(int[] nums, int k) {
    // 创建一个长度为 k 的数组，用于存储出现频率前 k 高的元素
    int[] result = new int[k];
    // 创建一个 HashMap，用于统计每个元素出现的次数
    HashMap<Integer, Integer> map = new HashMap<>();
    for (int num : nums) {
        // 统计每个元素出现的次数
        map.put(num, map.getOrDefault(num, 0) + 1);
    }

    // 将 map 中的元素转化为 set，便于遍历
    Set<Map.Entry<Integer, Integer>> entries = map.entrySet();
    // 创建一个 PriorityQueue（优先队列），用于存储出现频率前 k 高的元素
    // PriorityQueue 是一个优先队列，可以自动将元素按照一定规则进行排序
    // 在这里，我们将 PriorityQueue 中的元素按照出现次数从大到小排序，因此它是一个大顶堆
    PriorityQueue<Map.Entry<Integer, Integer>> queue = new PriorityQueue<>((o1, o2) -> o2.getValue() - o1.getValue());
    // 遍历 entries，将每个元素加入 PriorityQueue 中
    for (Map.Entry<Integer, Integer> entry : entries) {
        queue.offer(entry);
    }
    // 将 PriorityQueue 中前 k 个元素取出并存入 result 数组中
    for (int i = k - 1; i >= 0; i--) {
        result[i] = queue.poll().getKey();
    }
    // 返回出现频率前 k 高的元素
    return result;
}
```

