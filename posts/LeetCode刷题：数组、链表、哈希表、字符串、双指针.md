---
title: LeetCode刷题：数组、链表、哈希表、字符串、双指针
categories:
  - Java
  - LeetCode
date: 2023-05-20 14:30:00
---
# 数组
## 二分查找
给定一个 n 个元素有序的（升序）整型数组 nums 和一个目标值 target  ，写一个函数搜索 nums 中的 target，如果目标值存在返回下标，否则返回 -1。

https://leetcode.cn/problems/binary-search
### 思路
```
1.区间定义
定义target在[left, right]区间:
    right=nums.length-1
    while(left<=right)

定义target在[left, right)区间:
    right=nums.length
    while(left<right)

2.用middle和target比较
3.调整left or right,
4.重新计算middle
```
### 代码
```java
public int search(int[] nums, int target) {
        int left=0;
        int right=nums.length-1;
        int middle=right/2;
        while(left<=right){
            if(nums[middle]==target){
                return middle;
            }
            else if(nums[middle]>target){
                right=middle-1;
            }
            else{
                left=middle+1;
            }
            middle=(left+right)/2;
        }
        return -1;
    }
```
## 搜索插入位置
给定一个排序数组和一个目标值，在数组中找到目标值，并返回其索引。如果目标值不存在于数组中，返回它将会被按顺序插入的位置。

请必须使用时间复杂度为 O(log n) 的算法。

https://leetcode.cn/problems/search-insert-position
### 思路
```
1.区间定义
定义target在[left, right]区间:
    right=nums.length-1
    while(left<=right)
2.结束条件
left>right，即left=right+1
此时left左边的值全部小于target,因此target应该在left所在位置
```
### 代码
```java
public int searchInsert(int[] nums, int target) {
        int left=0;
        int right=nums.length-1;
        int middle=right/2;
        while(left<=right){
            if(nums[middle]<target){
                left=middle+1;
            }
            else{//nums[middle]>=target
                right=middle-1;
            }
            middle=(left+right)/2;
        }
        //最后一次循环开始前left=right=middle
        //left最终都等于right+1
        //left左边的值全部小于target,因此target应该在left所在位置
        return left;
    }
```
## 在排序数组中查找元素的第一个和最后一个位置
给你一个按照非递减顺序排列的整数数组 nums，和一个目标值 target。请你找出给定目标值在数组中的开始位置和结束位置。

如果数组中不存在目标值 target，返回 [-1, -1]。

你必须设计并实现时间复杂度为 O(log n) 的算法解决此问题。

https://leetcode.cn/problems/find-first-and-last-position-of-element-in-sorted-array

### 思路
```
二分查找元素，然后向前向后查找相同值的元素
```
### 代码
```java
public int[] searchRange(int[] nums, int target) {
        int left=0;
        int right=nums.length-1;
        int middle=(left+right)/2;
        int result=-1;//记录二分查找结果
        while(left<=right){
            if(nums[middle]==target){
                result=middle;
                break;
            }
            else if(nums[middle]<target){
                left=middle+1;
                middle=(left+right)/2;
            }
            else{
                right=middle-1;
                middle=(left+right)/2;
            }
        }
        if(result==-1){
            return new int[]{-1,-1};
        }
        else{
            //往右找
            for(int i=result;i<nums.length;i++){
                if(nums[i]==nums[result]){
                    right=i;
                }
                else{
                    break;
                }
            }
            //往左找
            for(int i=result;i>=0;i--){
                if(nums[i]==nums[result]){
                    left=i;
                }
                else{
                    break;
                }
            }
        }
        return new int[]{left,right};
    }
```
##  x 的平方根 
给你一个非负整数 x ，计算并返回 x 的 算术平方根 。

由于返回类型是整数，结果只保留 整数部分 ，小数部分将被 舍去 。

注意：不允许使用任何内置指数函数和算符，例如 pow(x, 0.5) 或者 x ** 0.5 。

https://leetcode.cn/problems/sqrtx
### 思路
```
1.在0-x/2之间二分查找
2.x=1时,right=0是错误的一种情况
3.(long)middle*middle<x比较时需要做类型转换，int是不够的
```
### 代码
```java
public int mySqrt(int x) {
        int left=0;
        int right=x/2;
        if(right==0){
            right=1;
        }
        int middle=right/2;
        while(left<=right){
            if((long)middle*middle==x){
                return middle;
            }
            else if((long)middle*middle<x){
                left=middle+1;
                middle=(left+right)/2;
            }
            else{
                right=middle-1;
                middle=(left+right)/2;
            }
        }
        //此时left=right+1
        return right;
    }
```
## 有效的完全平方数
给你一个正整数 num 。如果 num 是一个完全平方数，则返回 true ，否则返回 false 。

完全平方数 是一个可以写成某个整数的平方的整数。换句话说，它可以写成某个整数和自身的乘积。

不能使用任何内置的库函数，如  sqrt 。

https://leetcode.cn/problems/valid-perfect-square
### 思路
```
1.二分查找1-num/2
```
### 代码
```java
public boolean isPerfectSquare(int num) {
        int left=0;
        int right=num/2;
        if(right==0){
            right=1;
        }
        int middle=(left+right)/2;
        while(left<=right){
            if((long)middle*middle==num){
                return true;
            }
            else if((long)middle*middle<num){
                left=middle+1;
                middle=(left+right)/2;
            }
            else{
                right=middle-1;
                middle=(left+right)/2;
            }
        }
        return false;
    }
```
## 移除元素
给你一个数组 nums 和一个值 val，你需要 原地 移除所有数值等于 val 的元素，并返回移除后数组的新长度。

不要使用额外的数组空间，你必须仅使用 O(1) 额外空间并 原地 修改输入数组。

元素的顺序可以改变。你不需要考虑数组中超出新长度后面的元素。

https://leetcode.cn/problems/remove-element
### 思路
```
1.遍历一遍数组，把需要保留的值重新写入数组
```
### 代码
```java
public int removeElement(int[] nums, int val) {
        int left=0;
        for(int i=0;i<nums.length;i++){
            if(nums[i]!=val){
                nums[left++]=nums[i];
            }
        }
        return left;
    }
```
## 删除排序数组中的重复项
给你一个 升序排列 的数组 nums ，请你 原地 删除重复出现的元素，使每个元素 只出现一次 ，返回删除后数组的新长度。元素的 相对顺序 应该保持 一致 。然后返回 nums 中唯一元素的个数。

考虑 nums 的唯一元素的数量为 k ，你需要做以下事情确保你的题解可以被通过：

更改数组 nums ，使 nums 的前 k 个元素包含唯一元素，并按照它们最初在 nums 中出现的顺序排列。nums 的其余元素与 nums 的大小不重要。
返回 k 。

https://leetcode.cn/problems/remove-duplicates-from-sorted-array
### 思路
```
1.遍历一遍数组，把需要保留的值重新写入数组
```
### 代码
```java
public int removeDuplicates(int[] nums) {
        int left=0;
        for(int i=1;i<nums.length;i++){
            if(nums[i]!=nums[left]){
                nums[++left]=nums[i];
            }
        }
        return left+1;
    }
```
## 移动零
给定一个数组 nums，编写一个函数将所有 0 移动到数组的末尾，同时保持非零元素的相对顺序。

请注意 ，必须在不复制数组的情况下原地对数组进行操作。
https://leetcode.cn/problems/move-zeroes/
### 思路
```
1.遍历一遍数组，把需要保留的值重新写入数组
2.剩余部分填0
```
### 代码
```java
public void moveZeroes(int[] nums) {
        int left=0;
        for(int i=0;i<nums.length;i++){
            if(nums[i]!=0){
                nums[left++]=nums[i];
            }
        }
        for(int i=left;i<nums.length;i++){
            nums[i]=0;
        }
    }
```
## 比较含退格的字符串
给定 s 和 t 两个字符串，当它们分别被输入到空白的文本编辑器后，如果两者相等，返回 true 。# 代表退格字符。

注意：如果对空文本输入退格字符，文本继续为空。
https://leetcode.cn/problems/backspace-string-compare
### 思路
```
1.写一个convert转换函数，从后往前遍历
2.使用toCharArray()把字符串转为字符数组
3.使用StringBuilder可变字符串接收最终转换的字符串结果
4.把需要保留的字符重新写进StringBuilder
```
### 代码
```java
class Solution {
    public boolean backspaceCompare(String s, String t) {
        // 将 s 和 t 转换为最终形式，比较它们是否相等
        return convert(s).equals(convert(t));
    }

    // 将字符串转换为最终形式
    private String convert(String s) {
        StringBuilder sb = new StringBuilder();//可变字符串
        char[] cs = s.toCharArray();//字符串转为字符数组
        int size = 0; // 记录当前需要删除的字符数
        for (int i = cs.length - 1; i >= 0; i--) {
            if (cs[i] == '#') { // 如果是退格符，则需要删除一个字符
                size++;
            } else if (size == 0) { // 如果不是退格符，并且没有需要删除的字符，则将该字符加入最终字符串中
                sb.append(cs[i]);
            } else { // 如果不是退格符，但有需要删除的字符，则跳过该字符
                size--;
            }
        }
        return sb.toString(); // 返回最终字符串
    }
}
```
## 有序数组的平方
给你一个按 非递减顺序 排序的整数数组 nums，返回 每个数字的平方 组成的新数组，要求也按 非递减顺序 排序。
https://leetcode.cn/problems/squares-of-a-sorted-array/
### 思路
```
1.最大的在两边，两个指针指两边
2.循环比较两个指针，哪边绝对值大就选哪边
3.创建等长新数组，从末尾开始添加
```
### 代码
```java
public int[] sortedSquares(int[] nums) {
        int left=0;
        int right=nums.length-1;
        //开一个等长新数组
        int[] result=new int[nums.length];
        int k=nums.length-1;
        while(left<=right){
            if(Math.abs(nums[left])<=Math.abs(nums[right])){
                result[k--]=nums[right]*nums[right];
                right--;
            }
            else{
                result[k--]=nums[left]*nums[left];
                left++;
            }
        }
        return result;
    }
```
## 长度最小的子数组
给定一个含有 n 个正整数的数组和一个正整数 target 。

找出该数组中满足其和 ≥ target 的长度最小的 连续子数组 [numsl, numsl+1, ..., numsr-1, numsr] ，并返回其长度。如果不存在符合条件的子数组，返回 0 。
https://leetcode.cn/problems/minimum-size-subarray-sum
示例1
```
输入：target = 7, nums = [2,3,1,2,4,3]
输出：2
解释：子数组 [4,3] 是该条件下的长度最小的子数组。
```
### 思路
```
1.滑动窗口,双指针
2.两个指针指向开头,right向右移动直到子数组和>=target,然后left向右移动，直到子数组和<target,期间不断更新最小子数组和
3.两层while，第一层控制窗口右扩，第二层控制左缩
4.int最大值Integer.MAX_VALUE
```
### 代码
```java
public int minSubArrayLen(int target, int[] nums) {
        if(nums.length==0){
            return 0;
        }
        int left=0;
        int right=0;
        int result=Integer.MAX_VALUE;//最小子数组长度
        int sum=0;//子数组元素和
        while(right<nums.length){
            sum+=nums[right++];//窗口右扩
            while(sum>=target){//窗口左缩
                //达到target条件，比较子数组长度
                if(right-left<result){
                    result=right-left;//right自增了，相减正好是长度
                }
                sum-=nums[left++];
            }
        }
        if(result==Integer.MAX_VALUE){//target太大，整个数组都没法满足
            return 0;
        }
        return result;
    }
```
## 螺旋矩阵 II
给你一个正整数 n ，生成一个包含 1 到 n2 所有元素，且元素按顺时针顺序螺旋排列的 n x n 正方形矩阵 matrix 。
https://leetcode.cn/problems/spiral-matrix-ii/
### 思路
```
1.模拟过程
2.建立上下左右边界
3.while填充次数，始终按照右下左上的顺序填写，填完一边就更新边界
```
### 代码
```java
public int[][] generateMatrix(int n) {
    int l = 0, r = n - 1, t = 0, b = n - 1; // l、r、t、b分别表示当前螺旋矩阵的左边界、右边界、上边界和下边界
    int[][] mat = new int[n][n]; // 创建一个n * n的二维数组
    int num = 1, tar = n * n; // num表示当前要填充的数字，tar表示要填充的数字的总个数
    while(num <= tar){
        for(int i = l; i <= r; i++) mat[t][i] = num++; // 从左向右填充数字
        t++;
        for(int i = t; i <= b; i++) mat[i][r] = num++; // 从上向下填充数字
        r--;
        for(int i = r; i >= l; i--) mat[b][i] = num++; // 从右向左填充数字
        b--;
        for(int i = b; i >= t; i--) mat[i][l] = num++; // 从下向上填充数字
        l++;
    }
    return mat; // 返回填充完数字的二维数组
}
```
## 螺旋矩阵
给你一个 m 行 n 列的矩阵 matrix ，请按照 顺时针螺旋顺序 ，返回矩阵中的所有元素。
https://leetcode.cn/problems/spiral-matrix/
### 思路
```
1.使用一个列表result来存储螺旋访问的元素
2.按照从左到右、从上到下、从右到左、从下到上的顺序，依次访问矩阵中的元素，并将其添加到result列表中
3.每次访问完一行或一列，就需要更新对应的边界值
4.每访问完一行或一列，就需要判断当前是否越界。如果越界了，就直接退出循环
```
### 代码
```java
public List<Integer> spiralOrder(int[][] matrix) {
    List<Integer> result = new ArrayList<>(); // 用于存储螺旋访问的元素

    if (matrix == null || matrix.length == 0 || matrix[0].length == 0) {
        return result; // 处理特殊情况，矩阵为空或者行数或列数为0时，直接返回空列表
    }

    int m = matrix.length, n = matrix[0].length; // 获取矩阵的行数和列数
    int l = 0, r = n - 1, t = 0, b = m - 1; // 初始化上下左右四个边界

    while (true) {
        // 从左到右访问上边
        for (int i = l; i <= r; i++) {
            result.add(matrix[t][i]);
        }
        if (++t > b) break; // 如果上边界越过下边界，直接退出循环

        // 从上到下访问右边
        for (int i = t; i <= b; i++) {
            result.add(matrix[i][r]);
        }
        if (--r < l) break; // 如果右边界越过左边界，直接退出循环

        // 从右到左访问下边
        for (int i = r; i >= l; i--) {
            result.add(matrix[b][i]);
        }
        if (--b < t) break; // 如果下边界越过上边界，直接退出循环

        // 从下到上访问左边
        for (int i = b; i >= t; i--) {
            result.add(matrix[i][l]);
        }
        if (++l > r) break; // 如果左边界越过右边界，直接退出循环
    }

    return result; // 返回螺旋访问的元素列表
}
```
## 顺时针打印矩阵
### 思路
```
1.使用一个列表result来存储螺旋访问的元素
2.按照从左到右、从上到下、从右到左、从下到上的顺序，依次访问矩阵中的元素，并将其添加到result列表中
3.每次访问完一行或一列，就需要更新对应的边界值
4.每访问完一行或一列，就需要判断当前是否越界。如果越界了，就直接退出循环
5.考虑空数组情况
```
### 代码
```java
class Solution {
    public int[] spiralOrder(int[][] matrix) {
        if(matrix.length==0)return new int[0];
        int m=matrix.length;
        int n=matrix[0].length;
        int[] result=new int[m*n];
        int num=0;
        int t=0;
        int b=m-1;
        int l=0;
        int r=n-1;
        while(num<m*n){
            for(int i=l;i<=r;i++)result[num++]=matrix[t][i];
            if(++t>b)break;
            for(int i=t;i<=b;i++)result[num++]=matrix[i][r];
            if(--r<l)break;
            for(int i=r;i>=l;i--)result[num++]=matrix[b][i];
            if(--b<t)break;
            for(int i=b;i>=t;i--)result[num++]=matrix[i][l];
            if(++l>r)break;
        }
        return result;
    }
}
```
# 链表
## 移除链表元素
给你一个链表的头节点 head 和一个整数 val ，请你删除链表中所有满足 Node.val == val 的节点，并返回 新的头节点 。
示例
```
输入：head = [1,2,6,3,4,5,6], val = 6
输出：[1,2,3,4,5]
```
### 思路
```
1.节点定义:val、next、三个构造函数(空、val、val&next) 
public class ListNode {
    int val;     
    ListNode next;     
    ListNode() {}     
    ListNode(int val) { this.val = val; }     
    ListNode(int val, ListNode next) { this.val = val; this.next = next; }
}
2.头节点单独处理
3.修改next
```
### 代码
```java
public ListNode removeElements(ListNode head, int val) {
    // 处理头节点中值为val的情况
    while (head != null && head.val == val) {
        head = head.next;
    }
    // 使用指针p遍历链表
    ListNode p = head;
    while (p != null && p.next != null) {
        // 如果下一个节点的值为val，将p的next指向下一个节点的next，即删除下一个节点
        if (p.next.val == val) {
            p.next = p.next.next;
        } else {
            // 否则将p指向下一个节点
            p = p.next;
        }
    }
    // 返回头节点
    return head;
}
```
## 设计链表
你可以选择使用单链表或者双链表，设计并实现自己的链表。

单链表中的节点应该具备两个属性：val 和 next 。val 是当前节点的值，next 是指向下一个节点的指针/引用。

如果是双向链表，则还需要属性 prev 以指示链表中的上一个节点。假设链表中的所有节点下标从 0 开始。

实现 MyLinkedList 类：

MyLinkedList() 初始化 MyLinkedList 对象。
int get(int index) 获取链表中下标为 index 的节点的值。如果下标无效，则返回 -1 。
void addAtHead(int val) 将一个值为 val 的节点插入到链表中第一个元素之前。在插入完成后，新节点会成为链表的第一个节点。
void addAtTail(int val) 将一个值为 val 的节点追加到链表中作为链表的最后一个元素。
void addAtIndex(int index, int val) 将一个值为 val 的节点插入到链表中下标为 index 的节点之前。如果 index 等于链表的长度，那么该节点会被追加到链表的末尾。如果 index 比长度更大，该节点将 不会插入 到链表中。
void deleteAtIndex(int index) 如果下标有效，则删除链表中下标为 index 的节点。
https://leetcode.cn/problems/design-linked-list
### 思路
```
1.增加一个多余的头节点
2.注意空链表
3.使用size记录链表长度
4.着重写好addAtIndex(int index, int val)，其他方法可复用
```
### 代码
```java
class MyLinkedList {
    int size; // 链表的长度
    ListNode head; // 链表的头节点

    public MyLinkedList() { // 构造函数，初始化链表
        size = 0;
        head = new ListNode(0);
    }

    public int get(int index) { // 获取链表中下标为 index 的节点的值
        if (index < 0 || index >= size) { // 下标无效，返回 -1
            return -1;
        }
        ListNode cur = head;
        for (int i = 0; i <= index; i++) { // 遍历找到第 index 个节点
            cur = cur.next;
        }
        return cur.val;
    }

    public void addAtHead(int val) { // 在链表头插入一个值为 val 的节点
        addAtIndex(0, val);
    }

    public void addAtTail(int val) { // 在链表尾追加一个值为 val 的节点
        addAtIndex(size, val);
    }

    public void addAtIndex(int index, int val) { // 在链表中下标为 index 的节点之前插入一个值为 val 的节点
        if (index > size) { // 如果 index 比长度更大，该节点将不会插入到链表中
            return;
        }
        index = Math.max(0, index); // 如果 index 小于 0，就插入到头节点之前
        size++; // 插入节点后，链表长度加 1
        ListNode pred = head;
        for (int i = 0; i < index; i++) { // 遍历找到第 index 个节点的前驱节点
            pred = pred.next;
        }
        ListNode toAdd = new ListNode(val); // 创建要插入的节点
        toAdd.next = pred.next; // 将要插入的节点的 next 指针指向第 index 个节点
        pred.next = toAdd; // 将第 index-1 个节点的 next 指针指向要插入的节点
    }

    public void deleteAtIndex(int index) { // 删除链表中下标为 index 的节点
        if (index < 0 || index >= size) { // 如果下标无效，直接返回
            return;
        }
        size--; // 删除节点后，链表长度减 1
        ListNode pred = head;
        for (int i = 0; i < index; i++) { // 遍历找到第 index 个节点的前驱节点
            pred = pred.next;
        }
        pred.next = pred.next.next; // 将第 index-1 个节点的 next 指针指向第 index+1 个节点，即跳过要删除的节点
    }
}

class ListNode { // 定义链表节点
    int val; // 节点的值
    ListNode next; // 指向下一个节点的指针

    public ListNode(int val) { // 构造函数，初始化节点
        this.val = val;
    }
}
```
## 反转链表
给你单链表的头节点 head ，请你反转链表，并返回反转后的链表。
https://leetcode.cn/problems/reverse-linked-list/
### 思路
```
1.使用三个指针，prev、cur和tmp,tmp用于指向cur的next节点，防止断连
2.循环条件是cur!=null
3.先移动tmp，然后调整cur.next，再移动prev和cur
```
### 代码
```java
public ListNode reverseList(ListNode head) {
		//申请节点，pre和 cur，pre指向null
		ListNode pre = null;
		ListNode cur = head;
		ListNode tmp = null;
		while(cur!=null) {
			//记录当前节点的下一个节点
			tmp = cur.next;
			//然后将当前节点指向pre
			cur.next = pre;
			//pre和cur节点都前进一位
			pre = cur;
			cur = tmp;
		}
		return pre;
	}
```
递归方法
```java
public ListNode reverseList(ListNode head) {
    if (head == null || head.next == null) return head;
    ListNode newHead = reverseList(head.next);//先反转最后两个节点才能得到真正的newHaed
    head.next.next = head;//后一节点的next指向当前节点
    head.next = null;//断开原来的连接
    return newHead;
}
```
## 两两交换链表中的节点
给你一个链表，两两交换其中相邻的节点，并返回交换后链表的头节点。你必须在不修改节点内部的值的情况下完成本题（即，只能进行节点交换）。
https://leetcode.cn/problems/swap-nodes-in-pairs/
### 思路
```
1.if node1==null or node.next==null return head
2.节点node1->node2->node3
node2.next=node1
node1.next=fun(node3)
```
### 代码
```java
public ListNode swapPairs(ListNode head) {
    // 如果链表为空或只有一个节点，直接返回
    if (head == null || head.next == null)  
        return head;
    // 保存剩余的节点
    ListNode rest = head.next.next;
    // 交换相邻的两个节点，并将新的头节点保存在 newHead 中
    ListNode newHead = head.next;
    newHead.next = head;
    // 递归调用 swapPairs() 处理剩余的节点
    head.next = swapPairs(rest);
    // 返回新的头节点
    return newHead;
}
```
## 删除链表的导数第N个节点
给你一个链表，删除链表的倒数第 n 个结点，并且返回链表的头结点。
https://leetcode.cn/problems/remove-nth-node-from-end-of-list/
### 思路
```
1.最重要的是找到要删除的节点的前一个节点
2.如果要删除的是头结点，他没有前一节点，特殊处理
3.先遍历一遍计算链表长度size，如果N等于size，返回第二个节点
4.找到prev，修改next
```
### 代码
```java
public ListNode removeNthFromEnd(ListNode head, int n) {
    // 如果链表只有一个节点，直接删除并返回 null
    if (head.next == null) {
        return null;
    }
    int size = 0;
    ListNode p = head;
    // 计算链表的长度
    while (p != null) {
        p = p.next;
        size++;
    }
    // 如果要删除的是头节点，直接返回头节点的下一个节点
    if (n == size) {
        return head.next;
    }
    p = head;
    ListNode prev = null;
    // 找到要删除的节点的前一个节点 prev
    for (int i = 0; i < size - n; i++) {
        prev = p;
        p = p.next;
    }
    // 删除节点 p
    prev.next = p.next;
    // 返回头节点
    return head;
}
```
## 链表相交
给你两个单链表的头节点 headA 和 headB ，请你找出并返回两个单链表相交的起始节点。如果两个链表没有交点，返回 null 。
https://leetcode.cn/problems/intersection-of-two-linked-lists-lcci/
### 思路
```
1.创建哈希表Set<ListNode> visited = new HashSet<ListNode>();
2.加入链表A的所有元素
3.遍历链表B，查看元素是否在哈希表中
```
### 代码
```java
public ListNode getIntersectionNode(ListNode headA, ListNode headB) {
    // 定义一个哈希集合 visited 用于存放链表 A 中的节点，初始为空
    Set<ListNode> visited = new HashSet<ListNode>();
    // 遍历链表 A，将其中所有节点都加入哈希集合 visited 中
    ListNode temp = headA;
    while (temp != null) {
        visited.add(temp);
        temp = temp.next;
    }
    // 遍历链表 B，找到第一个出现在哈希集合 visited 中的节点，即为相交节点
    temp = headB;
    while (temp != null) {
        if (visited.contains(temp)) {
            return temp;
        }
        temp = temp.next;
    }
    // 如果没有相交节点，返回 null
    return null;
}
```
## 环形链表II
给定一个链表的头节点  head ，返回链表开始入环的第一个节点。 如果链表无环，则返回 null。

如果链表中有某个节点，可以通过连续跟踪 next 指针再次到达，则链表中存在环。 为了表示给定链表中的环，评测系统内部使用整数 pos 来表示链表尾连接到链表中的位置（索引从 0 开始）。如果 pos 是 -1，则在该链表中没有环。注意：pos 不作为参数进行传递，仅仅是为了标识链表的实际情况。

不允许修改 链表。
https://leetcode.cn/problems/linked-list-cycle-ii/submissions/
### 思路
```
1.遍历节点写入HashSet，如果已经包含该节点，则返回该节点
```
### 代码
```java
public ListNode detectCycle(ListNode head) {
    Set<ListNode> visited=new HashSet<ListNode>();
    ListNode p=head;
    while(p!=null && !visited.contains(p)){
        visited.add(p);
        p=p.next;
    }
    return p;
}
```
# 哈希表
## 有效的字母异位词
给定两个字符串 s 和 t ，编写一个函数来判断 t 是否是 s 的字母异位词。

注意：若 s 和 t 中每个字符出现的次数都相同，则称 s 和 t 互为字母异位词。
https://leetcode.cn/problems/valid-anagram
### 思路
```
1.排序后比较
2.维护一个长度26的数组，写入s中每个字母出现的次数，减去t中字母出现的次数，有数字小于0的话返回false
```
### 代码
```java
public boolean isAnagram(String s, String t) {
    if (s.length() != t.length()) {
        return false;
    }
    char[] str1 = s.toCharArray();
    char[] str2 = t.toCharArray();
    Arrays.sort(str1);
    Arrays.sort(str2);
    return Arrays.equals(str1, str2);
}
```
```java
public boolean isAnagram(String s, String t) {
    if (s.length() != t.length()) {
        return false;
    }
    int[] table = new int[26];
    for (int i = 0; i < s.length(); i++) {
        table[s.charAt(i) - 'a']++;
    }
    for (int i = 0; i < t.length(); i++) {
        table[t.charAt(i) - 'a']--;
        if (table[t.charAt(i) - 'a'] < 0) {
            return false;
        }
    }
    return true;
}
```

## 两个数组的交集
给定两个数组 nums1 和 nums2 ，返回 它们的交集 。输出结果中的每个元素一定是 唯一 的。我们可以 不考虑输出结果的顺序 。
https://leetcode.cn/problems/intersection-of-two-arrays/
### 思路
```
1.判断某个元素是否在集合中
2.两层循环遍历的时间复杂度是O(mn),使用哈希集合存储的话，时间复杂度降到O(m+n)
```
### 代码
```java
public int[] intersection(int[] nums1, int[] nums2) {
    // 定义两个哈希集合 set1 和 set2 分别用于存放数组 nums1 和 nums2 中的元素
    Set<Integer> set1 = new HashSet<Integer>();
    Set<Integer> set2 = new HashSet<Integer>();
    // 遍历数组 nums1，将其中所有元素都加入哈希集合 set1 中
    for (int num : nums1) {
        set1.add(num);
    }
    // 遍历数组 nums2，将其中所有元素都加入哈希集合 set2 中
    for (int num : nums2) {
        set2.add(num);
    }
    // 定义一个新的哈希集合 result_set 用于存放 set1 和 set2 的交集
    Set<Integer> result_set = new HashSet<Integer>();
    // 找出 set1 和 set2 中大小较小的那个集合 min 和较大的那个集合 max
    Set<Integer> min = (set1.size() > set2.size()) ? set2 : set1;
    Set<Integer> max = (set1.size() > set2.size()) ? set1 : set2;
    // 遍历集合 min，如果集合 max 中也包含当前元素，则将其加入交集集合 result_set 中
    for (int num : min) {
        if (max.contains(num)) {
            result_set.add(num);
        }
    }
    // 将交集集合转化为数组 result 返回
    int[] result = new int[result_set.size()];
    int index = 0;
    for (int num : result_set) {
        result[index++] = num;
    }
    return result;
}
```

## 快乐数
编写一个算法来判断一个数 n 是不是快乐数。

「快乐数」 定义为：

对于一个正整数，每一次将该数替换为它每个位置上的数字的平方和。
然后重复这个过程直到这个数变为 1，也可能是 无限循环 但始终变不到 1。
如果这个过程 结果为 1，那么这个数就是快乐数。
如果 n 是 快乐数 就返回 true ；不是，则返回 false 。
https://leetcode.cn/problems/happy-number
### 思路
```
1.有无线循环，说明sum会重复出现
2.循环计算sum，写入HashSet中，如果有重复，返回false,如果结果为1，返回true
```
### 代码
```java
public boolean isHappy(int n) {
    Set<Integer> set=new HashSet<Integer>();
    while(!set.contains(getSum(n))){
        n=getSum(n);
        if(n==1){
            return true;
        }
        set.add(n);
    }
    return false;
}
public int getSum(int n){
    int sum=0;
    while(n>9){
        sum+=(n%10)*(n%10);
        n=n/10;
    }
    sum+=n*n;
    return sum;
}
```
## 两数之和
给定一个整数数组 nums 和一个整数目标值 target，请你在该数组中找出 和为目标值 target  的那 两个 整数，并返回它们的数组下标。

你可以假设每种输入只会对应一个答案。但是，数组中同一个元素在答案里不能重复出现。

你可以按任意顺序返回答案。
https://leetcode.cn/problems/two-sum/
### 思路
```
1.两层循环枚举
2.第二层循环目的是寻找target-x，可用HashMap替代
```
### 代码
**暴力枚举**
```java
class Solution {
    public int[] twoSum(int[] nums, int target) {
        int n = nums.length;
        for (int i = 0; i < n; ++i) {
            for (int j = i + 1; j < n; ++j) {
                if (nums[i] + nums[j] == target) {
                    return new int[]{i, j};
                }
            }
        }
        return new int[0];
    }
}
```
**哈希Map**
```java
class Solution {
    public int[] twoSum(int[] nums, int target) {
        Map<Integer, Integer> hashtable = new HashMap<Integer, Integer>();
        for (int i = 0; i < nums.length; ++i) {
            if (hashtable.containsKey(target - nums[i])) {
                return new int[]{hashtable.get(target - nums[i]), i};
            }
            hashtable.put(nums[i], i);
        }
        return new int[0];
    }
}
```
## 四数相加 II
给你四个整数数组 nums1、nums2、nums3 和 nums4 ，数组长度都是 n ，请你计算有多少个元组 (i, j, k, l) 能满足：

0 <= i, j, k, l < n
nums1[i] + nums2[j] + nums3[k] + nums4[l] == 0

https://leetcode.cn/problems/4sum-ii

**示例**
```
输入：nums1 = [1,2], nums2 = [-2,-1], nums3 = [-1,2], nums4 = [0,2]
输出：2
解释：
两个元组如下：
1. (0, 0, 0, 1) -> nums1[0] + nums2[0] + nums3[0] + nums4[1] = 1 + (-2) + (-1) + 2 = 0
2. (1, 1, 0, 0) -> nums1[1] + nums2[1] + nums3[0] + nums4[0] = 2 + (-1) + (-1) + 0 = 0
```
### 思路
```
1.和拆分为两组，一组使用HashMap存放，另一组和HashMap比较
2.两两相加分为两组的时间复杂度最小:O(n^2),暴力法O(n^4),三一分O(n^3)
```
### 代码
```java
public int fourSumCount(int[] nums1, int[] nums2, int[] nums3, int[] nums4) {
    Map<Integer,Integer> map=new HashMap<>();
    int result=0;
    for(int num1 : nums1){
        for(int num2 : nums2){
            int sum12=num1+num2;
            if(map.containsKey(sum12)){//两数和为key,组合数为value
                map.put(sum12,map.get(sum12)+1);
            }
            else{
                map.put(sum12,1);
            }
        }
    }
    for(int num3 : nums3){
        for(int num4 : nums4){
            int sum34=-(num3+num4);
            if(map.containsKey(sum34)){
                result+=map.get(sum34);//34中的每一种组合，可对应AB中的多种组合
            }
        }
    }
    return result;
}
```
## 赎金信
给你两个字符串：ransomNote 和 magazine ，判断 ransomNote 能不能由 magazine 里面的字符构成。

如果可以，返回 true ；否则返回 false 。

magazine 中的每个字符只能在 ransomNote 中使用一次。
全是小写英文字母
https://leetcode.cn/problems/ransom-note
### 思路
```
1.使用长度为26的数组存放magazine每个字母的出现次数
2.遍历ransomNote，减去对应字母的出现次数，模拟使用字母，如果减到<0；则false
```
### 代码
```java
public boolean canConstruct(String ransomNote, String magazine) {
    int[] table=new int[26];
    for(int i=0;i<magazine.length();i++){
        table[magazine.charAt(i)-'a']++;
    }
    for(int i=0;i<ransomNote.length();i++){
        table[ransomNote.charAt(i)-'a']--;
        if(table[ransomNote.charAt(i)-'a']<0){
            return false;
        }
    }
    return true;
}
```
## 三数之和
给你一个整数数组 nums ，判断是否存在三元组 [nums[i], nums[j], nums[k]] 满足 i != j、i != k 且 j != k ，同时还满足 nums[i] + nums[j] + nums[k] == 0 。请你返回所有和为 0 且不重复的三元组。

https://leetcode.cn/problems/3sum
### 思路
```
1.排序：首先，将给定的数组进行排序，这样可以方便后面的操作。
2.遍历：然后，遍历数组中的每个数，将其作为三元组中的第一个数，然后在其右侧的数字中寻找符合条件的两个数，使得三个数的和为 0。
3.双指针：对于每个三元组，使用双指针技术来查找符合条件的另外两个数。左指针指向当前数的下一个位置，右指针指向数组末尾。如果当前三个数的和大于 0，则将右指针左移；如果当前三个数的和小于 0，则将左指针右移；如果当前三个数的和等于 0，则将该三元组添加到答案集合中。
4.去重：为避免重复，需要在添加三元组之前，判断该三元组的字符串形式是否已经出现过。如果已经出现过，则不添加。
```
### 代码
```java
public List<List<Integer>> threeSum(int[] nums) {
    Arrays.sort(nums); // 先将数组排序
    List<List<Integer>> ans = new ArrayList<>(); // 存放答案三元组的列表
    Set<String> strs = new HashSet<>(); // 存放已经出现过的三元组的字符串形式的集合
    int len = nums.length;
    for (int i = 0; i < len - 2; i++) { // i 遍历数组中的每个数，最后两个数作为 left 和 right
        if (i > 0 && nums[i] == nums[i - 1]) { // 如果当前数与前一个数相等，就跳过当前循环
            continue;
        }
        int left = i + 1; // left 初始值为 i+1
        int right = len - 1; // right 初始值为数组最后一个数
        while (left < right) { // 在 left 小于 right 的情况下循环
            int sum = nums[i] + nums[left] + nums[right]; // 计算当前三个数的和
            if (sum > 0) { // 如果和大于 0，就将 right 左移
                right--;
            } else if (sum < 0) { // 如果和小于 0，就将 left 右移
                left++;
            } else { // 如果和等于 0，就找到了一个符合条件的三元组
                List<Integer> tmp = Arrays.asList(nums[i], nums[left], nums[right]); // 将三个数放入列表中
                String str = Integer.toString(nums[i]) + Integer.toString(nums[left]) + Integer.toString(nums[right]); // 将三个数的字符串形式拼接成一个字符串
                if (!strs.contains(str)) { // 如果该三元组字符串形式没有出现过
                    ans.add(tmp); // 将该三元组添加到答案列表中
                    strs.add(str); // 将该三元组的字符串形式添加到已出现过的集合中
                }
                left++; // 将 left 右移
                right--; // 将 right 左移
            }
        }
    }
    return ans; // 返回答案列表
}
```
## 四数之和
给你一个由 n 个整数组成的数组 nums ，和一个目标值 target 。请你找出并返回满足下述全部条件且不重复的四元组 [nums[a], nums[b], nums[c], nums[d]] （若两个四元组元素一一对应，则认为两个四元组重复）：

0 <= a, b, c, d < n
a、b、c 和 d 互不相同
nums[a] + nums[b] + nums[c] + nums[d] == target
你可以按 任意顺序 返回答案 。

https://leetcode.cn/problems/4sum
### 思路
```
1.在上一题三数之和的基础上又增加了一层循环，时间复杂度为O(n^3)
2.不同之处在于target不再为0，不能用nums[i]>0就跳过遍历
3.计算四数之和时一定要用long类型
```
### 代码
```java
public List<List<Integer>> fourSum(int[] nums, int target) {
    Arrays.sort(nums); // 对数组进行排序
    int n = nums.length;
    Set<String> strs = new HashSet<>(); // 用于存储已经出现过的四元组
    List<List<Integer>> ans = new ArrayList<>(); // 用于存储符合条件的四元组
    int left, right;
    for (int i = 0; i < n - 3; i++) { // 枚举第一个数
        if (i > 0 && nums[i - 1] == nums[i]) { // 如果第一个数与前一个数相同，则跳过
            continue;
        }
        for (int j = i + 1; j < n - 2; j++) { // 枚举第二个数
            if (j > i + 1 && nums[j - 1] == nums[j]) { // 如果第二个数与前一个数相同，则跳过
                continue;
            }
            left = j + 1; // 左指针指向第三个数
            right = n - 1; // 右指针指向最后一个数
            while (left < right) { // 双指针遍历
                long sum = (long) nums[i] + nums[j] + nums[left] + nums[right]; // 计算四个数的和
                if (sum > target) { // 如果和大于目标值，则将右指针左移
                    right--;
                } else if (sum < target) { // 如果和小于目标值，则将左指针右移
                    left++;
                } else { // 如果和等于目标值，则将四元组加入答案集合中
                    List<Integer> tmp = new ArrayList<>();
                    tmp.add(nums[i]);
                    tmp.add(nums[j]);
                    tmp.add(nums[left]);
                    tmp.add(nums[right]);
                    String str = Integer.toString(nums[i]) + Integer.toString(nums[j])
                            + Integer.toString(nums[left]) + Integer.toString(nums[right]); // 将四元组转换为字符串形式
                    if (!strs.contains(str)) { // 如果该四元组没有出现过，则将其加入答案集合中
                        ans.add(tmp);
                        strs.add(str);
                    }
                    right--; // 将右指针左移
                    left++; // 将左指针右移
                }
            }
        }
    }
    return ans;
}
```
# 字符串
## 反转字符串
编写一个函数，其作用是将输入的字符串反转过来。输入字符串以字符数组 s 的形式给出。

不要给另外的数组分配额外的空间，你必须原地修改输入数组、使用 O(1) 的额外空间解决这一问题。

https://leetcode.cn/problems/reverse-string
### 思路
```
1.双指针反转
```
### 代码
```java
public void reverseString(char[] s) {
    int left=0;
    int right=s.length-1;
    while(left<right){
        char temp=s[left];
        s[left]=s[right];
        s[right]=temp;
        left++;
        right--;
    }
}
```
## 反转字符串II
给定一个字符串 s 和一个整数 k，从字符串开头算起，每计数至 2k 个字符，就反转这 2k 字符中的前 k 个字符。

如果剩余字符少于 k 个，则将剩余字符全部反转。
如果剩余字符小于 2k 但大于或等于 k 个，则反转前 k 个字符，其余字符保持原样。

https://leetcode.cn/problems/reverse-string-ii

### 思路
```
1.String转char数组：char[] arr = s.toCharArray()
2.循环遍历，调用reverse方法
```
### 代码
```java
public String reverseStr(String s, int k) {
    int n = s.length(); // 字符串 s 的长度
    char[] arr = s.toCharArray(); // 将字符串 s 转换成字符数组 arr
    for (int i = 0; i < n; i += 2 * k) { // 每次处理 2k 个字符
        reverse(arr, i, Math.min(i + k, n) - 1); // 反转每个 2k 个字符中的前 k 个字符
    }
    return new String(arr); // 将反转后的字符数组转换成字符串并返回
}

public void reverse(char[] arr, int left, int right) {
    while (left < right) { // 双指针遍历
        char temp = arr[left]; // 交换左右指针所指向的字符
        arr[left] = arr[right];
        arr[right] = temp;
        left++; // 左指针右移
        right--; // 右指针左移
    }
}
```
## 替换空格
请实现一个函数，把字符串 s 中的每个空格替换成"%20"。
### 思路
```
1.StringBuilder res = new StringBuilder();
2.
if(c == ' ') res.append("%20");
else res.append(c);
```
### 代码
```java
public String replaceSpace(String s) {
        StringBuilder res = new StringBuilder();
        for(Character c : s.toCharArray())
        {
            if(c == ' ') res.append("%20");
            else res.append(c);
        }
        return res.toString();
    }
```
## 反转字符串中的单词
给你一个字符串 s ，请你反转字符串中 单词 的顺序。

单词 是由非空格字符组成的字符串。s 中使用至少一个空格将字符串中的 单词 分隔开。

返回 单词 顺序颠倒且 单词 之间用单个空格连接的结果字符串。

注意：输入字符串 s中可能会存在前导空格、尾随空格或者单词间的多个空格。返回的结果字符串中，单词间应当仅用单个空格分隔，且不包含任何额外的空格。

https://leetcode.cn/problems/reverse-words-in-a-string
### 思路
```
1.内置split,reverse,join等方法
String.trim()
String.split("\\s+")//\\s+表示匹配任意空白字符（包括空格、制表符、换行符等）并且出现一次或多次。因此，这个方法可以在分割字符串时忽略连续的空格、制表符或换行符等空白字符。
Array.asList(arr)
Collections.reverse(list)
String.join(str1,list)//给list添加分隔符
```
### 代码
```java
public String reverseWords(String s) {
    // 除去开头和末尾的空白字符
    s = s.trim();
    // 正则匹配连续的空白字符作为分隔符分割
    List<String> wordList = Arrays.asList(s.split("\\s+"));
    Collections.reverse(wordList);
    return String.join(" ", wordList);
}
```
## 左旋转字符串
字符串的左旋转操作是把字符串前面的若干个字符转移到字符串的尾部。请定义一个函数实现字符串左旋转操作的功能。比如，输入字符串"abcdefg"和数字2，该函数将返回左旋转两位得到的结果"cdefgab"。

https://leetcode.cn/problems/zuo-xuan-zhuan-zi-fu-chuan-lcof
### 思路
```
1.切片
2.相加
```
### 代码
```java
public String reverseLeftWords(String s, int n) {
    return s.substring(n, s.length()) + s.substring(0, n);
}
```
## 找出字符串中第一个匹配项的下标
给你两个字符串 haystack 和 needle ，请你在 haystack 字符串中找出 needle 字符串的第一个匹配项的下标（下标从 0 开始）。如果 needle 不是 haystack 的一部分，则返回  -1 。

https://leetcode.cn/problems/find-the-index-of-the-first-occurrence-in-a-string
**示例**
```
输入：haystack = "sadbutsad", needle = "sad"
输出：0
解释："sad" 在下标 0 和 6 处匹配。
第一个匹配项的下标是 0 ，所以返回 0 。
```
### 思路
```
1.haystack.indexOf(needle);
2.基于窗口滑动
首先判断子串是否为空，如果为空则直接返回 0。
通过遍历目标字符串，找到第一个与子串的首字符相等的位置。
如果找到了，则从该位置开始向后遍历目标字符串和子串，逐个字符进行比较，如果字符不匹配，则从下一个位置开始重新匹配，直到找到子串在目标字符串中的起始位置或者找完了整个目标字符串都没有找到子串，则返回 -1。
```
### 代码
```java
public int strStr(String haystack, String needle) {
    int m = needle.length(); // 子串的长度
    // 当 needle 是空字符串时我们应当返回 0
    if (m == 0) {
        return 0;
    }
    int n = haystack.length(); // 目标字符串的长度
    if (n < m) {
        return -1;
    }
    int i = 0; // 目标字符串中当前要匹配的字符的位置
    int j = 0; // 子串中当前要匹配的字符的位置
    while (i < n - m + 1) { // 窗口的右端点不能超过目标字符串的末尾
        // 找到目标字符串中与子串第一个字符相等的位置
        while (i < n && haystack.charAt(i) != needle.charAt(j)) {
            i++;
        }
        if (i == n) { // 如果目标字符串中没有与子串第一个字符相等的位置，则匹配失败
            return -1;
        }
        // 从第二个字符开始比较，如果匹配成功，则一起向后移动
        i++;
        j++;
        while (j < m && i < n && haystack.charAt(i) == needle.charAt(j)) {
            i++;
            j++;
        }
        if (j == m) { // 如果匹配成功，则返回子串在目标字符串中的起始位置
            return i - m;
        } else { // 否则回溯到下一个位置重新开始匹配
            i -= j - 1;
            j = 0;
        }
    }
    return -1; // 没有匹配成功，则返回-1
}
```
## 重复的子字符串
给定一个非空的字符串 s ，检查是否可以通过由它的一个子串重复多次构成。

https://leetcode.cn/problems/repeated-substring-pattern/
### 思路
```
1.如果字符串 S 包含一个重复的子字符串，那么这意味着可以多次 “移位和换行”`字符串，并使其与原始字符串匹配。基于这个思想，可以每次移动k个字符，直到匹配移动 length - 1 次。但是这样对于重复字符串很长的字符串，效率会非常低。在 LeetCode 中执行时间超时了。
2.为了避免这种无用的环绕，可以创建一个新的字符串 str，它等于原来的字符串 S 再加上 S 自身，这样其实就包含了所有移动的字符串。
比如字符串：S = acd，那么 str = S + S = acdacd
acd 移动的可能：dac、cda。其实都包含在了 str 中了。就像一个滑动窗口
一开始 acd (acd) ，移动一次 ac(dac)d，移动两次 a(cda)cd。循环结束
所以可以直接判断 str 中去除首尾元素之后，是否包含自身元素。如果包含。则表明存在重复子串。
```
### 代码
```java
public boolean repeatedSubstringPattern(String s) {
    String str = s + s;
    return str.substring(1, str.length() - 1).contains(s);
}
```