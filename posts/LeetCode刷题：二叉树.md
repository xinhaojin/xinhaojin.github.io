---
title: LeetCode刷题：二叉树
categories:
  - Java
  - LeetCode
date: 2023-05-20 14:30:00
---
## 二叉树定义
```java
class Solution {
    public class TreeNode {
        int val;
        TreeNode left;
        TreeNode right;
        TreeNode() {}
        TreeNode(int val) { this.val = val; }
        TreeNode(int val, TreeNode left, TreeNode right) {
            this.val = val;
            this.left = left;
            this.right = right;
        }
    }
}
```
## 三种递归遍历方法
递归三部曲
```
1.确定递归函数的参数和返回值
2.确定终止条件
3.确定单层递归的逻辑
```
三种遍历方法的左中右顺序：左右保持不变

"前/中/后"序遍历表示的是**根(中)节点在三者中的位置**

前序遍历：**中**->左->右

中序遍历：左->**中**->右

后序遍历：左->右->**中**
### 二叉树的前序遍历
给你二叉树的根节点 root ，返回它节点值的 前序 遍历。

https://leetcode.cn/problems/binary-tree-preorder-traversal/
### 代码
```java
class Solution {
    public List<Integer> preorderTraversal(TreeNode root) {
        List<Integer> list=new ArrayList();
        order(root,list);
        return list;
    }
    public void order(TreeNode root,List<Integer> list){
        if(root==null) return;
        list.add(root.val);
        order(root.left,list);
        order(root.right,list);
    }
}
```
### 二叉树的中序遍历
给定一个二叉树的根节点 root ，返回 它的 中序 遍历 。

https://leetcode.cn/problems/binary-tree-inorder-traversal/
### 代码
```java
class Solution {
    public List<Integer> inorderTraversal(TreeNode root) {
        List<Integer> list=new ArrayList();
        order(root,list);
        return list;
    }
    public void order(TreeNode root,List<Integer> list){
        if(root==null) return;
        order(root.left,list);
        list.add(root.val);
        order(root.right,list);
    }
}
```
### 二叉树的后序遍历
给你一棵二叉树的根节点 root ，返回其节点值的 后序遍历 。

https://leetcode.cn/problems/binary-tree-postorder-traversal/
### 代码
```java
class Solution {
    public List<Integer> postorderTraversal(TreeNode root) {
        List<Integer> list=new ArrayList();
        order(root,list);
        return list;
    }
    public void order(TreeNode root,List<Integer> list){
        if(root==null) return;
        order(root.left,list);
        order(root.right,list);
        list.add(root.val);
    }
}
```
## 迭代遍历方法

### 前序遍历代码
```
前序遍历顺序：中-左-右，入栈顺序：中-右-左 出栈顺序：中-左-右
对每一个根节点：
    1.打印值
    2.向左下方遍历，遍历节点入栈，找到它的左子树最下边
    3.弹出栈顶
    4.其右子树为新的根节点
    重复上述步骤直到栈空
```
```java
class Solution {
    public List<Integer> preorderTraversal(TreeNode root) {
        // 创建一个 List，用于存储遍历结果
        List<Integer> result = new ArrayList<>();
        // 如果根节点为空，直接返回空的 List
        if (root == null){
            return result;
        }
        // 创建一个 Stack，用于存储遍历过程中的节点
        Stack<TreeNode> stack = new Stack<>();
        // 将根节点入栈
        stack.push(root);
        // 当栈不为空时，执行以下循环
        while (!stack.isEmpty()){
            // 取出栈顶元素
            TreeNode node = stack.pop();
            // 将栈顶元素的值加入结果列表中
            result.add(node.val);
            // 如果栈顶元素的右子节点不为空，将其入栈
            if (node.right != null){
                stack.push(node.right);
            }
            // 如果栈顶元素的左子节点不为空，将其入栈
            if (node.left != null){
                stack.push(node.left);
            }
        }
        // 返回遍历结果
        return result;
    }
}
```
### 中序遍历代码
```
中序遍历顺序: 左-中-右 入栈顺序： 左-右
 对每一个根节点：
    1.向左下方遍历，遍历节点入栈，找到它的左子树最下边
    2.弹出栈顶并打印
    3.其右子树为新的根节点
    重复上述步骤直到栈空
```
```java
class Solution {
    public List<Integer> inorderTraversal(TreeNode root) {
        // 创建一个 List，用于存储遍历结果
        List<Integer> result = new ArrayList<>();
        // 如果根节点为空，直接返回空的 List
        if (root == null){
            return result;
        }
        // 创建一个 Stack，用于存储遍历过程中的节点
        Stack<TreeNode> stack = new Stack<>();
        // 定义一个指针 cur，初始值为根节点
        TreeNode cur = root;
        // 当 cur 不为空或者栈不为空时，执行以下循环
        while (cur != null || !stack.isEmpty()){
        // 如果 cur 不为空，将其入栈，并将 cur 指向其左子节点
        if (cur != null){
            stack.push(cur);
            cur = cur.left;
        }else{
            // 如果 cur 为空，取出栈顶元素，将其值加入结果列表中，并将 cur 指向其右子节点
            cur = stack.pop();
            result.add(cur.val);
            cur = cur.right;
        }
        }
        // 返回遍历结果
        return result;
    }
}
```
### 后序遍历代码
```
后序遍历顺序 左-右-中 入栈顺序：中-左-右 出栈顺序：中-右-左， 最后翻转结果
对每一个根节点：
    1.向左下方遍历，遍历节点入栈，找到它的左子树最下边
    2.如果无右子树或者右子树刚被访问，直接打印根节点
      否则根节点再次入栈，先处理右子树
    3.其右子树为新的根节点
    重复上述步骤直到栈空
```
```java
class Solution {
    public List<Integer> postorderTraversal(TreeNode root) {
        // 创建一个 List，用于存储遍历结果
        List<Integer> result = new ArrayList<>();
        // 如果根节点为空，直接返回空的 List
        if (root == null){
            return result;
        }
        // 创建一个 Stack，用于存储遍历过程中的节点
        Stack<TreeNode> stack = new Stack<>();
        // 将根节点入栈
        stack.push(root);
        // 当栈不为空时，执行以下循环
        while (!stack.isEmpty()){
            // 取出栈顶元素
            TreeNode node = stack.pop();
            // 将栈顶元素的值加入结果列表中
            result.add(node.val);
            // 如果栈顶元素的左子节点不为空，将其入栈
            if (node.left != null){
                stack.push(node.left);
            }
            // 如果栈顶元素的右子节点不为空，将其入栈
            if (node.right != null){
                stack.push(node.right);
            }
        }
        // 将结果列表翻转，得到后序遍历结果
        Collections.reverse(result);
        // 返回遍历结果
        return result;
    }
}
```
### 统一风格迭代方法
```
只需要修改right、left、node的顺序
```
**前序遍历**
```java
class Solution {
    public List<Integer> preorderTraversal(TreeNode root) {
        List<Integer> result = new LinkedList<>();
        Stack<TreeNode> st = new Stack<>();
        if (root != null) st.push(root);
        while (!st.empty()) {
            TreeNode node = st.peek();
            if (node != null) {
                st.pop(); // 将该节点弹出，避免重复操作，下面再将右中左节点添加到栈中
                if (node.right!=null) st.push(node.right);  // 添加右节点（空节点不入栈）
                if (node.left!=null) st.push(node.left);    // 添加左节点（空节点不入栈）
                st.push(node);                          // 添加中节点
                st.push(null); // 中节点访问过，但是还没有处理，加入空节点做为标记。
                
            } else { // 只有遇到空节点的时候，才将下一个节点放进结果集
                st.pop();           // 将空节点弹出
                node = st.peek();    // 重新取出栈中元素
                st.pop();
                result.add(node.val); // 加入到结果集
            }
        }
        return result;
    }
}
```
**中序遍历**
```java
class Solution {
    public List<Integer> inorderTraversal(TreeNode root) {
            List<Integer> result = new LinkedList<>();
        Stack<TreeNode> st = new Stack<>();
        if (root != null) st.push(root);
        while (!st.empty()) {
            TreeNode node = st.peek();
            if (node != null) {
                st.pop(); // 将该节点弹出，避免重复操作，下面再将右中左节点添加到栈中
                if (node.right!=null) st.push(node.right);  // 添加右节点（空节点不入栈）
                st.push(node);                          // 添加中节点
                st.push(null); // 中节点访问过，但是还没有处理，加入空节点做为标记。

                if (node.left!=null) st.push(node.left);    // 添加左节点（空节点不入栈）
            } else { // 只有遇到空节点的时候，才将下一个节点放进结果集
                st.pop();           // 将空节点弹出
                node = st.peek();    // 重新取出栈中元素
                st.pop();
                result.add(node.val); // 加入到结果集
            }
        }
        return result;
    }
}
```
**后序遍历**
```java
class Solution {
    public List<Integer> postorderTraversal(TreeNode root) {
        List<Integer> result = new LinkedList<>();
        Stack<TreeNode> st = new Stack<>();
        if (root != null) st.push(root);
        while (!st.empty()) {
            TreeNode node = st.peek();
            if (node != null) {
                st.pop(); // 将该节点弹出，避免重复操作，下面再将右中左节点添加到栈中
                st.push(node);                          // 添加中节点
                st.push(null); // 中节点访问过，但是还没有处理，加入空节点做为标记。
                if (node.right!=null) st.push(node.right);  // 添加右节点（空节点不入栈）
                if (node.left!=null) st.push(node.left);    // 添加左节点（空节点不入栈）         
                                
            } else { // 只有遇到空节点的时候，才将下一个节点放进结果集
                st.pop();           // 将空节点弹出
                node = st.peek();    // 重新取出栈中元素
                st.pop();
                result.add(node.val); // 加入到结果集
            }
        }
        return result;
    }
}
```
## 二叉树的层序遍历
给你二叉树的根节点 root ，返回其节点值的 层序遍历 。 （即逐层地，从左到右访问所有节点）。

https://leetcode.cn/problems/binary-tree-level-order-traversal/
### 思路
```
1.创建一个空的 List，用于存储层序遍历的结果。
2.如果根节点为空，直接返回空的 List（因为树为空，不存在遍历结果）。
3.创建一个队列，用于存储遍历过程中的节点。首先将根节点入队。
4.当队列不为空时，记录当前层的节点个数 size，初始值为队列中的节点个数。
5.在处理队列中的节点时，每取出一个节点，就将 size 变量减 1。
6.将取出的节点的值加入当前层的遍历结果列表中。
7.如果取出的节点的左子节点不为空，将其左子节点入队。
8.如果取出的节点的右子节点不为空，将其右子节点入队。
9.当 size 变量的值为 0 时，说明当前层的所有节点都已经处理完毕，可以进入下一层的遍历了。
10.将当前层的遍历结果列表加入到总的遍历结果列表中。
11.进入下一层的遍历，重复步骤 4-10，直到遍历完整棵树。
12.返回遍历结果列表。
```
### 代码
```java
class Solution {
    public List<List<Integer>> levelOrder(TreeNode root) {
        // 创建一个 List，用于存储遍历结果
        List<List<Integer>> result = new ArrayList<>();
        // 如果根节点为空，直接返回空的 List
        if (root == null) {
            return result;
        }
        // 创建一个 Queue，用于存储遍历过程中的节点
        Queue<TreeNode> queue = new LinkedList<>();
        // 将根节点入队
        queue.add(root);
        // 当队列不为空时，执行以下循环
        while (!queue.isEmpty()) {
            // 获取当前层的节点个数
            int size = queue.size();
            // 创建一个 List，用于存储当前层的遍历结果
            List<Integer> list = new ArrayList();
            // 对当前层的每个节点依次进行处理
            while (size-- > 0) {
                // 取出队首节点
                TreeNode node = queue.poll();
                // 将队首节点的值加入当前层的遍历结果列表中
                list.add(node.val);
                // 如果队首节点的左子节点不为空，将其入队
                if (node.left != null) {
                    queue.add(node.left);
                }
                // 如果队首节点的右子节点不为空，将其入队
                if (node.right != null) {
                    queue.add(node.right);
                }
            }
            // 将当前层的遍历结果列表加入到总的遍历结果列表中
            result.add(list);
        }
        // 返回遍历结果
        return result;
    }
}
```
## 二叉树的层序遍历 II 
给你二叉树的根节点 root ，返回其节点值 自底向上的层序遍历 。 （即按从叶子节点所在层到根节点所在的层，逐层从左向右遍历）

https://leetcode.cn/problems/binary-tree-level-order-traversal/

### 思路
```
1.上一题的list反转
```
### 代码
```java
class Solution {
    public List<List<Integer>> levelOrderBottom(TreeNode root) {
        List<List<Integer>> result=new ArrayList<List<Integer>>();
        if(root==null) return result;
        Queue<TreeNode> que=new LinkedList<>();
        que.add(root);
        while(!que.isEmpty()){
            int size=que.size();
            List<Integer> list=new ArrayList<>();
            while(size-->0){
                TreeNode node=que.poll();
                list.add(node.val);
                if(node.left!=null) que.add(node.left);
                if(node.right!=null) que.add(node.right);
            }
            result.add(list);
        }
        Collections.reverse(result);
        return result;
    }
}
```
## 二叉树的右视图
给定一个二叉树的 根节点 root，想象自己站在它的右侧，按照从顶部到底部的顺序，返回从右侧所能看到的节点值。

https://leetcode.cn/problems/binary-tree-right-side-view/
```
输入: [1,2,3,null,5,null,4]
输出: [1,3,4]
```
### 思路
```
1.同上
```
### 代码
```java
class Solution {
    public List<Integer> rightSideView(TreeNode root) {
        List<Integer> result=new ArrayList<>();
        if(root==null) return result;
        Queue<TreeNode> que=new LinkedList<>();
        que.add(root);
        while(!que.isEmpty()){
            int size=que.size();
            List<Integer> list=new ArrayList<>();
            while(size-->0){
                TreeNode node=que.poll();
                // 如果 size 变量的值为 0，说明当前节点是当前层的最后一个节点，将其值加入到 result 中
                if(size==0) result.add(node.val);
                if(node.left!=null) que.add(node.left);
                if(node.right!=null) que.add(node.right);
            }
        }
        return result;
    }
}
```
## 二叉树的层平均值
给定一个非空二叉树的根节点 root , 以数组的形式返回每一层节点的平均值。与实际答案相差 10-5 以内的答案可以被接受。

https://leetcode.cn/problems/average-of-levels-in-binary-tree/

### 思路
```
1.同上
```
### 代码
```java
class Solution {
    public List<Double> averageOfLevels(TreeNode root) {
        List<Double> result=new ArrayList<>();//返回值类型修改为double
        if(root==null) return result;
        Queue<TreeNode> que=new LinkedList<>();
        que.add(root);
        while(!que.isEmpty()){
            int size=que.size();
            int tmp=size;
            double sum=0;
            while(tmp-->0){
                TreeNode node=que.poll();
                sum+=node.val;
                if(node.left!=null) que.add(node.left);
                if(node.right!=null) que.add(node.right);
            }
            result.add(sum/size);//计算平均值
        }
        return result;
    }
}
```
## N 叉树的层序遍历
给定一个 N 叉树，返回其节点值的层序遍历。（即从左到右，逐层遍历）。

树的序列化输入是用层序遍历，每组子节点都由 null 值分隔（参见示例）。

https://leetcode.cn/problems/n-ary-tree-level-order-traversal/
**示例**
```
输入：root = [1,null,3,2,4,null,5,6]
输出：[[1],[3,2,4],[5,6]]
```
### 思路
```
1.同上
```
### 代码
```java
class Solution {
    public List<List<Integer>> levelOrder(Node root) {
        List<List<Integer>> result=new ArrayList<>();
        if(root==null) return result;
        Queue<Node> que=new LinkedList<>();
        que.add(root);
        while(!que.isEmpty()){
            int size=que.size();
            List<Integer> list=new ArrayList<>();
            while(size-->0){
                Node node=que.poll();
                list.add(node.val);
                for(Node child : node.children){
                    que.add(child);//
                }
            }
            result.add(list);
        }
        return result;
    }
}
```
## 在每个树行中找最大值
给定一棵二叉树的根节点 root ，请找出该二叉树中每一层的最大值。

https://leetcode.cn/problems/find-largest-value-in-each-tree-row/
### 思路
```
1.同上
```
### 代码
```java
class Solution {
    public List<Integer> largestValues(TreeNode root) {
        List<Integer> result=new ArrayList<>();
        if(root==null) return result;
        Queue<TreeNode> que=new LinkedList<>();
        que.add(root);
        while(!que.isEmpty()){
            int size=que.size();
            int max=Integer.MIN_VALUE;//初始化为最小值
            while(size-->0){
                TreeNode node=que.poll();
                max=node.val>max?node.val:max;
                if(node.left!=null) que.add(node.left);
                if(node.right!=null) que.add(node.right);
            }
            result.add(max);
        }
        return result;
    }
}
```
## 填充每个节点的下一个右侧节点指针
给定一个 完美二叉树 ，其所有叶子节点都在同一层，每个父节点都有两个子节点。二叉树定义如下：
```
struct Node {
  int val;
  Node *left;
  Node *right;
  Node *next;
}
```
填充它的每个 next 指针，让这个指针指向其下一个右侧节点。如果找不到下一个右侧节点，则将 next 指针设置为 NULL。

初始状态下，所有 next 指针都被设置为 NULL。

https://leetcode.cn/problems/populating-next-right-pointers-in-each-node
### 思路
```
1.同上
```
### 代码
```java
class Solution {
    public Node connect(Node root) {
        if(root==null) return root;
        Queue<Node> que=new LinkedList<>();
        que.add(root);
        while(!que.isEmpty()){
            int size=que.size();
            Node prev=null;//标记prev节点
            while(size-->0){
                Node node=que.poll();
                if(prev!=null) prev.next=node;
                prev=node;//指向当前节点
                if(node.left!=null) que.add(node.left);
                if(node.right!=null) que.add(node.right);
            }
        }
        return root;
    }
}
```
## 填充每个节点的下一个右侧节点指针 II
给定一个二叉树：
```
struct Node {
  int val;
  Node *left;
  Node *right;
  Node *next;
}
```
填充它的每个 next 指针，让这个指针指向其下一个右侧节点。如果找不到下一个右侧节点，则将 next 指针设置为 NULL 。

初始状态下，所有 next 指针都被设置为 NULL 。

https://leetcode.cn/problems/populating-next-right-pointers-in-each-node-ii
### 思路
```
1.同上
```
### 代码
同上
```java
class Solution {
    public Node connect(Node root) {
        if(root==null) return root;
        Queue<Node> que=new LinkedList<>();
        que.add(root);
        while(!que.isEmpty()){
            int size=que.size();
            Node prev=null;//标记prev节点
            while(size-->0){
                Node node=que.poll();
                if(prev!=null) prev.next=node;
                prev=node;//指向当前节点
                if(node.left!=null) que.add(node.left);
                if(node.right!=null) que.add(node.right);
            }
        }
        return root;
    }
}
```
## 二叉树的最大深度
给定一个二叉树，找出其最大深度。

二叉树的深度为根节点到最远叶子节点的最长路径上的节点数。

说明: 叶子节点是指没有子节点的节点。

**示例**
```
给定二叉树 [3,9,20,null,null,15,7]
返回它的最大深度 3 
```

https://leetcode.cn/problems/maximum-depth-of-binary-tree

### 思路
```
1.同上
```
### 代码
```java
class Solution {
    public int maxDepth(TreeNode root) {
        if(root==null) return 0;
        int depth=0;//初始化深度为0
        Queue<TreeNode> que=new LinkedList<>();
        que.add(root);
        while(!que.isEmpty()){
            int size=que.size();
            while(size-->0){
                TreeNode node= que.poll();
                if(size==0) depth++;//每遍历完一层，深度+1
                if(node.left!=null) que.add(node.left);
                if(node.right!=null) que.add(node.right);
            }
        }
        return depth;
    }
}
```
## 二叉树的最小深度
给定一个二叉树，找出其最小深度。

最小深度是从根节点到最近叶子节点的最短路径上的节点数量。

说明：叶子节点是指没有子节点的节点。

https://leetcode.cn/problems/minimum-depth-of-binary-tree/

### 思路
```
1.同上
```
### 代码
```java
class Solution {
    public int minDepth(TreeNode root) {
        if(root==null) return 0;
        int depth=0;//初始化最短路径上的节点数量为0
        Queue<TreeNode> que=new LinkedList<>();
        que.add(root);
        while(!que.isEmpty()){
            int size=que.size();
            while(size-->0){
                TreeNode node= que.poll();
                if(node.left==null && node.right==null) return depth+1;//是叶子结点，直接返回
                if(size==0) depth++;//遍历完一层，没有叶子结点，深度+1
                if(node.left!=null) que.add(node.left);
                if(node.right!=null) que.add(node.right);
            }
        }
        return depth;
    }
}
```
## 翻转二叉树
给你一棵二叉树的根节点 root ，翻转这棵二叉树，并返回其根节点。

https://leetcode.cn/problems/invert-binary-tree/

### 思路
```
1.递归遍历，交换左右节点
```
### 代码
```java
class Solution {
    public TreeNode invertTree(TreeNode root) {
        invertNode(root);
        return root;
    }
    public void invertNode(TreeNode node){
        if(node==null) return;
        TreeNode tmp=node.left;
        node.left=node.right;
        node.right=tmp;
        invertNode(node.left);
        invertNode(node.right);
    }
}
```
## 对称二叉树
给你一个二叉树的根节点 root ， 检查它是否轴对称。

https://leetcode.cn/problems/symmetric-tree/
### 思路
```
1.写一个函数比较两个节点是否对称
2.递归遍历
```
### 代码
```java
class Solution {
    public boolean isSymmetric(TreeNode root) {
        if (root == null) {
            return true;
        }
        return check(root.left, root.right);
    }

    public boolean check(TreeNode p, TreeNode q) {
        if (p == null && q == null) {//空节点对称
            return true;
        }
        if (p == null || q == null || p.val != q.val) {
            return false;//有一边空或者值不相同
        }
        return check(p.left, q.right) && check(p.right, q.left);
    }
}
```
## 相同的树
给你两棵二叉树的根节点 p 和 q ，编写一个函数来检验这两棵树是否相同。

如果两个树在结构上相同，并且节点具有相同的值，则认为它们是相同的。

https://leetcode.cn/problems/same-tree/
### 思路
```
1.写一个函数比较两个节点是否对称
2.递归遍历
```
### 代码
```java
class Solution {
    public boolean isSameTree(TreeNode p, TreeNode q) {
        if(p==null && q==null) return true;//都空，true
        if(p==null || q==null || p.val!=q.val) return false;//一个空或值不同，false
        return isSameTree(p.left,q.left) && isSameTree(p.right,q.right);//不空且值相同，进一步比较子节点
    }
}
```
## 另一棵树的子树
给你两棵二叉树 root 和 subRoot 。检验 root 中是否包含和 subRoot 具有相同结构和节点值的子树。如果存在，返回 true ；否则，返回 false 。

二叉树 tree 的一棵子树包括 tree 的某个节点和这个节点的所有后代节点。tree 也可以看做它自身的一棵子树。

https://leetcode.cn/problems/subtree-of-another-tree
### 思路
```
1.dfs函数用于递归遍历s的子树
2.check函数用于检查s和t在当前节点是否相等
```
### 代码
```java
class Solution {
    public boolean isSubtree(TreeNode s, TreeNode t) {
        return dfs(s, t); // 调用dfs函数
    }

    public boolean dfs(TreeNode s, TreeNode t) {
        if (s == null) { // 如果s为null，表示已经遍历完了s的子树，返回false
            return false;
        }
        return check(s, t) || dfs(s.left, t) || dfs(s.right, t); // 如果s和t相等，或者s的左子树包含t，或者s的右子树包含t，返回true
    }

    public boolean check(TreeNode s, TreeNode t) {
        if (s == null && t == null) { // 如果s和t都为null，表示已经遍历完了两个树，返回true
            return true;
        }
        if (s == null || t == null || s.val != t.val) { // 如果s和t有一个为null，或者s的节点值不等于t的节点值，返回false
            return false;
        }
        return check(s.left, t.left) && check(s.right, t.right); // 如果s的左子树和t的左子树相等，且s的右子树和t的右子树相等，返回true
    }
}
```
## 完全二叉树的节点个数
给你一棵 完全二叉树 的根节点 root ，求出该树的节点个数。

完全二叉树 的定义如下：在完全二叉树中，除了最底层节点可能没填满外，其余每层节点数都达到最大值，并且最下面一层的节点都集中在该层最左边的若干位置。若最底层为第 h 层，则该层包含 1~ 2h 个节点。

https://leetcode.cn/problems/count-complete-tree-nodes

### 思路
```
1.维护一个size
2.递归遍历
```
### 代码
```java
class Solution {
    public int countNodes(TreeNode root) {
        int size=0;
        return count(root,size);
    }
    public int count(TreeNode node,int size){
        if(node==null) return size;
        else size++;
        if(node.left!=null) size=count(node.left,size);
        if(node.right!=null) size=count(node.right,size);
        return size;
    }
}
```
## 平衡二叉树
给定一个二叉树，判断它是否是高度平衡的二叉树。

本题中，一棵高度平衡二叉树定义为：

一个二叉树每个节点 的左右两个子树的高度差的绝对值不超过 1 。

https://leetcode.cn/problems/balanced-binary-tree/

### 思路
```
1.isBalanced() 方法通过递归判断左右子树的高度差是否不超过 1，并且左右子树也都是高度平衡的，来判断当前二叉树是否为高度平衡的二叉树
2.height() 方法用于计算二叉树的高度。该方法通过递归计算左右子树的高度，并取其中的最大值加 1，来计算当前节点的高度。
```
### 代码
```java
class Solution {
    public boolean isBalanced(TreeNode root) {
        // 如果二叉树为空，则认为是高度平衡的二叉树
        if (root == null) {
            return true;
        }
        // 判断当前二叉树的左右子树的高度差是否不超过 1，并且左右子树也都是高度平衡的
        return Math.abs(height(root.left) - height(root.right)) <= 1 && isBalanced(root.left) && isBalanced(root.right);
    }

    // 计算二叉树的高度
    public int height(TreeNode root) {
        // 如果二叉树为空，则高度为 0
        if (root == null) {
            return 0;
        }
        // 递归计算左右子树的高度，并取其中的最大值加 1，来计算当前节点的高度
        return Math.max(height(root.left), height(root.right)) + 1;
    }
}
```
## 二叉树的所有路径
给你一个二叉树的根节点 root ，按 任意顺序 ，返回所有从根节点到叶子节点的路径。

叶子节点 是指没有子节点的节点。
**示例**
```
输入：root = [1,2,3,null,5]
输出：["1->2->5","1->3"]
输入：root = [1]
输出：["1"]
```
https://leetcode.cn/problems/binary-tree-paths/
### 思路
```
1.dfs() 方法中，首先判断当前节点是否为叶子节点，如果是，则将访问过的路径添加到 res 列表中，并返回。

否则，对当前节点的左右子节点进行递归搜索，并在搜索过程中，将访问过的路径拼接起来
```
### 代码
```java
class Solution {
    public List<String> binaryTreePaths(TreeNode root) {
        List<String> res = new ArrayList<>();
        if (root == null) {
            return res;
        }
        dfs(root, "", res);
        return res;
    }

    private void dfs(TreeNode node, String path, List<String> res) {
        if (node.left == null && node.right == null) {
            // 当前节点是叶子节点
            res.add(path + node.val);
            return;
        }
        if (node.left != null) {
            // 递归遍历左子树
            dfs(node.left, path + node.val + "->", res);
        }
        if (node.right != null) {
            // 递归遍历右子树
            dfs(node.right, path + node.val + "->", res);
        }
    }
}
```
## 左叶子之和
给定二叉树的根节点 root ，返回所有左叶子之和。

https://leetcode.cn/problems/sum-of-left-leaves/
### 思路
```
1.首先判断当前节点的左子节点是否为左叶子节点，如果是，则将该节点的值累加到 sum 中。否则，对当前节点的左右子节点进行递归搜索，并在搜索过程中，累加所有左叶子节点的值。最终返回左右子树的累加和。
```
### 代码
```java
class Solution {
    public int sumOfLeftLeaves(TreeNode root) {
        if(root==null) return 0;
        return dfs(root,0);
    }
    public int dfs(TreeNode node,int sum){
        if(node.left!=null && node.left.left==null && node.left.right==null){
            // 如果当前节点的左子节点是叶子节点，则将该节点的值累加到 sum 中
            sum+=node.left.val;
        }
        if(node.left!=null) sum=dfs(node.left,sum); // 递归遍历左子树
        if(node.right!=null) sum=dfs(node.right,sum); // 递归遍历右子树
        return sum;
    }
}
```
## 找树左下角的值
给定一个二叉树的 根节点 root，请找出该二叉树的 最底层 最左边 节点的值。

假设二叉树中至少有一个节点。

https://leetcode.cn/problems/find-bottom-left-tree-value/

### 思路
```
1.层序遍历，找最后一层的第一个节点
```
### 代码
```java
class Solution {
    public int findBottomLeftValue(TreeNode root) {
        List<List<Integer>> result=new ArrayList<>();
        Queue<TreeNode> que=new LinkedList<>();
        que.add(root);
        while(!que.isEmpty()){
            int size=que.size();
            List<Integer> list=new ArrayList<>();
            while(size-->0){
                TreeNode node=que.poll();
                list.add(node.val);
                if(node.left!=null) que.add(node.left);
                if(node.right!=null) que.add(node.right);
            }
            result.add(list);
        }
        return result.get(result.size()-1).get(0);
    }
}
```
## 路径总和
给你二叉树的根节点 root 和一个表示目标和的整数 targetSum 。判断该树中是否存在 根节点到叶子节点 的路径，这条路径上所有节点值相加等于目标和 targetSum 。如果存在，返回 true ；否则，返回 false 。

叶子节点 是指没有子节点的节点。
**示例**
```
输入：root = [5,4,8,11,null,13,4,7,2,null,null,null,1], targetSum = 22
输出：true
解释：等于目标和的根节点到叶节点路径如上图所示。
```

https://leetcode.cn/problems/path-sum

### 思路
```
1.假定从根节点到当前节点的值之和为 val，我们可以将这个大问题转化为一个小问题：是否存在从当前节点的子节点到叶子的路径，满足其路径和为 sum - val。
```
### 代码
```java
class Solution {
    public boolean hasPathSum(TreeNode root, int sum) {
        // 如果根节点为空，返回 false
        if (root == null) {
            return false;
        }
        // 如果根节点是叶子节点，并且节点值等于目标和，返回 true
        if (root.left == null && root.right == null) {
            return sum == root.val;
        }
        // 如果根节点不是叶子节点，递归判断左右子树是否存在符合条件的路径
        return hasPathSum(root.left, sum - root.val) || hasPathSum(root.right, sum - root.val);
    }
}
```
## 从中序与后序遍历序列构造二叉树
给定两个整数数组 inorder 和 postorder ，其中 inorder 是二叉树的中序遍历， postorder 是同一棵树的后序遍历，请你构造并返回这颗 二叉树 。
**示例**
```
输入：inorder = [9,3,15,20,7], postorder = [9,15,7,20,3]
输出：[3,9,20,null,null,15,7]
```

https://leetcode.cn/problems/construct-binary-tree-from-inorder-and-postorder-traversal

### 思路
```
给定一棵二叉树的中序遍历数组和后序遍历数组，构造出这棵二叉树。

1.确定根节点
由于后序遍历的顺序是左子树 -> 右子树 -> 根节点，因此后序遍历数组的最后一个元素一定是根节点。在中序遍历数组中找到根节点的位置，可以将整个数组分成左子树和右子树两部分。

2.递归构造左子树和右子树
通过根节点在中序遍历数组中的位置，可以确定左子树和右子树的中序遍历数组。在后序遍历数组中，左子树和右子树的位置也可以根据左子树的长度确定。然后可以递归构造左子树和右子树，直到叶子节点为止。

3.返回根节点
将左子树和右子树递归构造出来后，就可以将根节点和左右子树连接起来，构造出整棵二叉树。最后返回根节点即可。
```
### 代码
```java
class Solution {
    // 创建一个哈希表，用于存储中序遍历数组中每个元素的值和下标
    HashMap<Integer,Integer> inorderArrayMap = new HashMap<>();
    // 定义后序遍历数组
    int[] post;

    public TreeNode buildTree(int[] inorder, int[] postorder) {
        // 将中序遍历数组中每个元素的值和下标存储到哈希表中
        for(int i = 0;i < inorder.length; i++) {
            inorderArrayMap.put(inorder[i], i);
        }
        // 将后序遍历数组存储到成员变量 post 中
        post = postorder;
        // 调用递归方法构造二叉树
        TreeNode root = buildTree(0, inorder.length - 1, 0, post.length - 1);
        return root;
    }

    // 递归构造二叉树的方法，接受四个参数分别表示中序遍历数组的左右边界和后序遍历数组的左右边界
    public TreeNode buildTree(int inorderStart, int inorderEnd, int postorderStart, int postorderEnd) {
        // 如果中序遍历数组或后序遍历数组为空，返回空节点
        if(inorderEnd < inorderStart || postorderEnd < postorderStart) return null;

        // 取得后序遍历数组中的最后一个元素作为根节点
        int root = post[postorderEnd];
        // 在哈希表中查找根节点对应的索引
        int rootIndexInInorderArray = inorderArrayMap.get(root);

        // 创建新的根节点
        TreeNode node = new TreeNode(root);
        // 递归构造左子树，中序遍历数组的左边界为 inorderStart，右边界为 rootIndexInInorderArray - 1，后序遍历数组的左边界为 postorderStart，右边界为 postorderStart + rootIndexInInorderArray - inorderStart - 1
        node.left = buildTree(inorderStart, rootIndexInInorderArray - 1, postorderStart, postorderStart + rootIndexInInorderArray - inorderStart - 1);
        // 递归构造右子树，中序遍历数组的左边界为 rootIndexInInorderArray + 1，右边界为 inorderEnd，后序遍历数组的左边界为 postorderStart + rootIndexInInorderArray - inorderStart，右边界为 postorderEnd - 1
        node.right = buildTree(rootIndexInInorderArray + 1, inorderEnd, postorderStart + rootIndexInInorderArray - inorderStart, postorderEnd - 1);
        return node; // 返回新建的节点
    }
}
```
## 最大二叉树
给定一个不重复的整数数组 nums 。 最大二叉树 可以用下面的算法从 nums 递归地构建:

创建一个根节点，其值为 nums 中的最大值。
递归地在最大值 左边 的 子数组前缀上 构建左子树。
递归地在最大值 右边 的 子数组后缀上 构建右子树。
返回 nums 构建的 最大二叉树 。
**示例**
```
输入：nums = [3,2,1,6,0,5]
输出：[6,3,5,null,2,0,null,null,1]
解释：递归调用如下所示：
- [3,2,1,6,0,5] 中的最大值是 6 ，左边部分是 [3,2,1] ，右边部分是 [0,5] 。
    - [3,2,1] 中的最大值是 3 ，左边部分是 [] ，右边部分是 [2,1] 。
        - 空数组，无子节点。
        - [2,1] 中的最大值是 2 ，左边部分是 [] ，右边部分是 [1] 。
            - 空数组，无子节点。
            - 只有一个元素，所以子节点是一个值为 1 的节点。
    - [0,5] 中的最大值是 5 ，左边部分是 [0] ，右边部分是 [] 。
        - 只有一个元素，所以子节点是一个值为 0 的节点。
        - 空数组，无子节点。
```
https://leetcode.cn/problems/maximum-binary-tree

### 思路
```
1.定义递归函数
定义一个递归函数 constructMaximumBinaryTree(int[] nums, int start, int end)，其中 nums 表示给定的数组，start 和 end 表示当前递归的数组的起始位置和终止位置。

2.找到最大值及其索引
在数组 nums 的范围 [start, end] 中找到最大值及其索引。

3.构造根节点
用最大值创建一个根节点 root。

4.递归构造左子树
将数组 nums 中范围 [start, maxIndex-1] 的部分作为一个新的子数组，递归调用 constructMaximumBinaryTree(nums, start, maxIndex-1) 方法，返回值作为当前节点的左子节点。

5.递归构造右子树
将数组 nums 中范围 [maxIndex+1, end] 的部分作为一个新的子数组，递归调用 constructMaximumBinaryTree(nums, maxIndex+1, end) 方法，返回值作为当前节点的右子节点。

6.返回根节点
```
### 代码
```java
class Solution {
    public TreeNode constructMaximumBinaryTree(int[] nums) {
        // 调用重载的方法，传入数组、左边界 0 和右边界 nums.length - 1
        return constructMaximumBinaryTree(nums, 0, nums.length - 1);
    }
    // 定义重载的方法，传入数组、左右边界
    private TreeNode constructMaximumBinaryTree(int[] nums, int start, int end) {
        // 如果左边界大于右边界，返回 null
        if (start > end) {
            return null;
        }
        // 找到数组中的最大值及其索引
        int maxIndex = start;
        for (int i = start + 1; i <= end; i++) {
            if (nums[i] > nums[maxIndex]) {
                maxIndex = i;
            }
        }
        // 创建根节点，以最大值为节点的值
        TreeNode root = new TreeNode(nums[maxIndex]);
        // 递归构造左子树，左子树的范围是[start, maxIndex - 1]
        root.left = constructMaximumBinaryTree(nums, start, maxIndex - 1);
        // 递归构造右子树，右子树的范围是[maxIndex + 1, end]
        root.right = constructMaximumBinaryTree(nums, maxIndex + 1, end);
        // 返回根节点
        return root;
    }
}
```
## 合并二叉树
给你两棵二叉树： root1 和 root2 。

想象一下，当你将其中一棵覆盖到另一棵之上时，两棵树上的一些节点将会重叠（而另一些不会）。你需要将这两棵树合并成一棵新二叉树。合并的规则是：如果两个节点重叠，那么将这两个节点的值相加作为合并后节点的新值；否则，不为 null 的节点将直接作为新二叉树的节点。

返回合并后的二叉树。

注意: 合并过程必须从两个树的根节点开始。

https://leetcode.cn/problems/merge-two-binary-trees

### 思路
```
1.定义递归函数
定义一个递归函数 mergeTrees(TreeNode root1, TreeNode root2)，其中 root1 和 root2 分别表示两棵给定的二叉树。

2.处理特殊情况
如果其中一个节点为 null，则返回另一个节点。

3.合并节点值
如果两个节点都不为 null，则将它们的值相加，然后继续递归合并它们的左右子树。

4.返回合并后的根节点
递归结束后，返回合并后的根节点即可。

这个解题思路主要采用了递归的方式来合并两棵二叉树。每次递归都将原始的两个节点进行合并，并且递归合并它们的左右子树。由于每次递归都会比较左右节点的情况，因此递归深度最大为两棵树中节点数较大的一棵，时间复杂度为 $O(n)$。
```
### 代码
```java
class Solution {
    public TreeNode mergeTrees(TreeNode root1, TreeNode root2) {
        // 当其中一个节点为 null 时，返回另一个节点
        if (root1 == null || root2 == null) {
            return root1 == null ? root2 : root1;
        }
        // 递归合并两个树
        return dfs(root1, root2);
    }

    // 定义递归函数，合并两个树
    public TreeNode dfs(TreeNode node1, TreeNode node2) {
        // 当其中一个节点为 null 时，返回另一个节点
        if (node1 == null || node2 == null) {
            return node1 == null ? node2 : node1;
        }
        // 值相加
        node1.val += node2.val;
        // 递归合并左子树
        node1.left = dfs(node1.left, node2.left);
        // 递归合并右子树
        node1.right = dfs(node1.right, node2.right);
        // 返回合并后的根节点
        return node1;
    }
}
```
## 二叉搜索树中的搜索
给定二叉搜索树（BST）的根节点 root 和一个整数值 val。

你需要在 BST 中找到节点值等于 val 的节点。 返回以该节点为根的子树。 如果节点不存在，则返回 null 。

https://leetcode.cn/problems/search-in-a-binary-search-tree

### 思路
```
1.递归遍历搜索
2.如果target更大，在右子树中找，否则在左子树中找
```
### 代码
```java
class Solution {
    public TreeNode searchBST(TreeNode root, int val) {
        if (root == null) {
            return null;
        }
        if (root.val == val) {
            return root;
        } else if (root.val > val) {
            return searchBST(root.left, val);
        } else {
            return searchBST(root.right, val);
        }
    }
}
```
## 验证二叉搜索树
给你一个二叉树的根节点 root ，判断其是否是一个有效的二叉搜索树。

有效 二叉搜索树定义如下：

节点的左子树只包含 小于 当前节点的数。
节点的右子树只包含 大于 当前节点的数。
所有左子树和右子树自身必须也是二叉搜索树。

https://leetcode.cn/problems/validate-binary-search-tree

### 思路
```
1.中序遍历结果是增序，递归遍历生成list，判断list是否增序即可
2.可以用全局变量保存prev节点值，中序遍历时一旦小于等于prev，判别为false
```
### 代码
```java
class Solution {
    List<Integer> result=new ArrayList<>();
    public boolean isValidBST(TreeNode root) {
        order(root);
        for(int i=1;i<this.result.size();i++){
            if((long)this.result.get(i)-this.result.get(i-1)<=0){
                return false;
            }
        }
        return true;
    }
    public void order(TreeNode root){
        if(root.left!=null) order(root.left);
        result.add(root.val);
        if(root.right!=null) order(root.right);
    }
}
class Solution {
    private Integer prev = null;

    public boolean isValidBST(TreeNode root) {
        if (root == null) {
            return true;
        }

        if (!isValidBST(root.left)) {
            return false;
        }

        if (prev != null && prev >= root.val) {
            return false;
        }

        prev = root.val;

        return isValidBST(root.right);
    }
}
```
## 二叉搜索树的最小绝对差
给你一个二叉搜索树的根节点 root ，返回 树中任意两不同节点值之间的最小差值 。

差值是一个正数，其数值等于两值之差的绝对值。

https://leetcode.cn/problems/minimum-absolute-difference-in-bst/
### 思路
```
1.维护一个prev和min值，中序遍历即可
```
### 代码
```java
class Solution {
    private Integer prev=null;//要初始化为null，要用Integer
    private int min=Integer.MAX_VALUE;
    public int getMinimumDifference(TreeNode root) {
        order(root);
        return min;
    }
    public void order(TreeNode root){
        if(root.left!=null) order(root.left);
        if(prev!=null && root.val-prev<min) min=root.val-prev;
        prev=root.val;
        if(root.right!=null) order(root.right);
    }
}
```
## 二叉搜索树中的众数
给你一个含重复值的二叉搜索树（BST）的根节点 root ，找出并返回 BST 中的所有 众数（即，出现频率最高的元素）。

如果树中有不止一个众数，可以按 任意顺序 返回。

假定 BST 满足如下定义：

结点左子树中所含节点的值 小于等于 当前节点的值
结点右子树中所含节点的值 大于等于 当前节点的值
左子树和右子树都是二叉搜索树

https://leetcode.cn/problems/find-mode-in-binary-search-tree

### 思路
```
1.使用中序遍历的方式遍历二叉搜索树；
2.维护四个变量：prevVal，count，maxCount 和 modes。
* prevVal 表示上一个访问的节点的值；
* count 表示当前节点值出现的次数；
* maxCount 表示出现次数最多的节点值的出现次数；
* modes 表示出现次数最多的节点值组成的 List。
3.在遍历过程中，如果当前节点的值等于上一个访问的节点的值，则将 count 加 1；否则，将 count 设为 1，prevVal 设为当前节点的值。
4.如果 count 大于 maxCount，则将 maxCount 更新为 count，将 modes 清空，并将当前节点的值添加到 modes 中；如果 count 等于 maxCount，则将当前节点的值添加到 modes 中。
5.维护了所有出现次数最多的节点值，最后将这些节点值转换为数组返回即可。
```
### 代码
```java
class Solution {
    private int prevVal = -1;    // 上一个访问的节点的值
    private int count = 0;       // 当前节点值出现的次数
    private int maxCount = 0;    // 出现次数最多的节点值的出现次数
    private List<Integer> modes = new ArrayList<>();    // 出现次数最多的节点值组成的 List

    public int[] findMode(TreeNode root) {
        inorder(root);    // 中序遍历二叉搜索树
        int[] result = new int[modes.size()];
        for (int i = 0; i < modes.size(); i++) {
            result[i] = modes.get(i);
        }
        return result;    // 将出现次数最多的节点值转换为数组返回
    }

    private void inorder(TreeNode node) {
        if (node == null) {
            return;
        }

        inorder(node.left);    // 中序遍历左子树

        if (node.val == prevVal) {    // 如果当前节点的值等于上一个访问的节点的值
            count++;    // 将 count 加 1
        } else {
            count = 1;    // 将 count 设为 1
            prevVal = node.val;    // 将 prevVal 设为当前节点的值
        }

        if (count > maxCount) {    // 如果 count 大于 maxCount
            maxCount = count;    // 将 maxCount 更新为 count
            modes.clear();    // 将 modes 清空
            modes.add(node.val);    // 将当前节点的值添加到 modes 中
        } else if (count == maxCount) {    // 如果 count 等于 maxCount
            modes.add(node.val);    // 将当前节点的值添加到 modes 中
        }

        inorder(node.right);    // 中序遍历右子树
    }
}
```
## 二叉树的最近公共祖先
给定一个二叉树, 找到该树中两个指定节点的最近公共祖先。

百度百科中最近公共祖先的定义为：“对于有根树 T 的两个节点 p、q，最近公共祖先表示为一个节点 x，满足 x 是 p、q 的祖先且 x 的深度尽可能大（一个节点也可以是它自己的祖先）。”
**示例**
```
输入：root = [3,5,1,6,2,0,8,null,null,7,4], p = 5, q = 1
输出：3
解释：节点 5 和节点 1 的最近公共祖先是节点 3 。
```
https://leetcode.cn/problems/lowest-common-ancestor-of-a-binary-tree

### 思路
```
1.如果p,q为根节点，则公共祖先为根节点
2.如果p,q在左子树，则公共祖先在左子树查找
3.如果p,q在右子树，则公共祖先在右子树查找
4.如果p,q分属两侧，则公共祖先为根节点
```
### 代码
```java
class Solution {
    public TreeNode lowestCommonAncestor(TreeNode root, TreeNode p, TreeNode q) {
        if (root == null) return null;
        // 如果p,q为根节点，则公共祖先为根节点
        if (root.val == p.val || root.val == q.val) return root;
        // 如果p,q在左子树，则公共祖先在左子树查找
        if (find(root.left, p) && find(root.left, q)) {
            return lowestCommonAncestor(root.left, p, q);
        }
        // 如果p,q在右子树，则公共祖先在右子树查找
        if (find(root.right, p) && find(root.right, q)) {
            return lowestCommonAncestor(root.right, p, q);
        }
        // 如果p,q分属两侧，则公共祖先为根节点
        return root;
    }
    
    private boolean find(TreeNode root, TreeNode c) {
        if (root == null) return false;
        if (root.val == c.val) {
            return true;
        }
        
        return find(root.left, c) || find(root.right, c);
    }
}
```
## 二叉搜索树的最近公共祖先
给定一个二叉搜索树, 找到该树中两个指定节点的最近公共祖先。

百度百科中最近公共祖先的定义为：“对于有根树 T 的两个结点 p、q，最近公共祖先表示为一个结点 x，满足 x 是 p、q 的祖先且 x 的深度尽可能大（一个节点也可以是它自己的祖先）。”

例如，给定如下二叉搜索树:  root = [6,2,8,0,4,7,9,null,null,3,5]

https://leetcode.cn/problems/lowest-common-ancestor-of-a-binary-search-tree

### 思路
```
1.与上一题相比，树是有序的
2.替换find方法为val的比较
```
### 代码
```java
class Solution {
    public TreeNode lowestCommonAncestor(TreeNode root, TreeNode p, TreeNode q) {
        if (root == null) return null;
        // 如果p,q为根节点，则公共祖先为根节点
        if (root.val == p.val || root.val == q.val) return root;
        // 如果p,q在左子树，则公共祖先在左子树查找
        if (p.val<root.val && q.val<root.val) {
            return lowestCommonAncestor(root.left, p, q);
        }
        // 如果p,q在右子树，则公共祖先在右子树查找
        if (p.val>root.val && q.val>root.val) {
            return lowestCommonAncestor(root.right, p, q);
        }
        // 如果p,q分属两侧，则公共祖先为根节点
        return root;
    }
}
```
## 二叉搜索树中的插入操作
给定二叉搜索树（BST）的根节点 root 和要插入树中的值 value ，将值插入二叉搜索树。 返回插入后二叉搜索树的根节点。 输入数据 保证 ，新值和原始二叉搜索树中的任意节点值都不同。

注意，可能存在多种有效的插入方式，只要树在插入后仍保持为二叉搜索树即可。 你可以返回 任意有效的结果 。

https://leetcode.cn/problems/insert-into-a-binary-search-tree

### 思路
```
1.从根节点开始遍历树。
2.如果插入值大于当前节点的值，则往右子树遍历；否则，往左子树遍历。
3.如果遍历到一个空节点，则插入新节点并跳出循环。
4.返回根节点。
```
### 代码
```java
class Solution {
    public TreeNode insertIntoBST(TreeNode root, int val) {
        // 如果根节点为空，则新建一个节点并返回
        if(root==null) return new TreeNode(val);
        
        // 否则，从根节点开始遍历树
        TreeNode p=root;
        while(true){
            // 如果插入值大于当前节点的值，则往右子树遍历
            if(val>p.val){
                // 如果右子树为空，则插入新节点并跳出循环
                if(p.right==null) {
                    p.right=new TreeNode(val);
                    break;
                }
                // 否则，继续往右子树遍历
                else p=p.right;
            }
            // 如果插入值小于等于当前节点的值，则往左子树遍历
            else{
                // 如果左子树为空，则插入新节点并跳出循环
                if(p.left==null){
                    p.left=new TreeNode(val);
                    break;
                }
                // 否则，继续往左子树遍历
                else p=p.left;
            }
        }
        // 返回根节点
        return root;
    }
}
```
## 删除二叉搜索树中的节点
给定一个二叉搜索树的根节点 root 和一个值 key，删除二叉搜索树中的 key 对应的节点，并保证二叉搜索树的性质不变。返回二叉搜索树（有可能被更新）的根节点的引用。

一般来说，删除节点可分为两个步骤：

首先找到需要删除的节点；
如果找到了，删除它。

https://leetcode.cn/problems/delete-node-in-a-bst

### 思路
```
1.如果根节点为空，则返回 null。
2.如果待删除节点的值小于当前节点的值，则从左子树中删除；如果待删除节点的值大于当前节点的值，则从右子树中删除。
if(key < root.val) root.left = deleteNode(root.left, key);
3.如果找到了待删除节点，则：
    如果该节点的左子树为空，则返回右子树。
    如果该节点的右子树为空，则返回左子树。
    如果该节点的左右子树均不为空，则：
        找到右子树的最左节点。
        将左子树接到右子树的最左节点的左子树上。
        返回右子树。
4.返回根节点。
```
### 代码
```java
class Solution {
    public TreeNode deleteNode(TreeNode root, int key) {
        if(root == null) return null;
        if(key < root.val) root.left = deleteNode(root.left, key);//从左子树中删除
        else if(key > root.val) root.right = deleteNode(root.right, key);//从右子树中删除
        else{//找到待删除节点
            if(root.left == null) return root.right;//左子树为空，返回右子树
            else if(root.right == null) return root.left;//右子树为空，返回左子树
            //左右子树均不为空
            TreeNode p = root.right;
            while(p.left != null) p = p.left;//找到右子树的最左节点
            p.left = root.left;//将左子树接到右子树的最左节点的左子树上
            return root.right;//返回右子树
        }
        return root;
    }
}
```
## 修剪二叉搜索树
给你二叉搜索树的根节点 root ，同时给定最小边界low 和最大边界 high。通过修剪二叉搜索树，使得所有节点的值在[low, high]中。修剪树 不应该 改变保留在树中的元素的相对结构 (即，如果没有被移除，原有的父代子代关系都应当保留)。 可以证明，存在 唯一的答案 。

所以结果应当返回修剪好的二叉搜索树的新的根节点。注意，根节点可能会根据给定的边界发生改变。

https://leetcode.cn/problems/trim-a-binary-search-tree

### 思路
```
1.如果根节点为空，则返回 null。
2.如果根节点的值大于 high，则修剪左子树。
3.如果根节点的值小于 low，则修剪右子树。
4.否则，修剪左右子树。
5.回根节点。
```
### 代码
```java
class Solution {
    public TreeNode trimBST(TreeNode root, int low, int high) {
        // 如果根节点为空，则返回 null。
        if(root==null) return null;
        // 如果根节点的值大于 high，则修剪左子树。
        if(root.val>high) return trimBST(root.left,low,high);//这一步即是修剪过程
        // 如果根节点的值小于 low，则修剪右子树。
        else if(root.val<low) return trimBST(root.right,low,high);
        else{
            // 否则，修剪左右子树。
            root.left=trimBST(root.left,low,high);
            root.right=trimBST(root.right,low,high);
            return root;
        }
    }
}
```
## 将有序数组转换为二叉搜索树
给你一个整数数组 nums ，其中元素已经按 升序 排列，请你将其转换为一棵 高度平衡 二叉搜索树。

高度平衡 二叉树是一棵满足「每个节点的左右两个子树的高度差的绝对值不超过 1 」的二叉树。

https://leetcode.cn/problems/convert-sorted-array-to-binary-search-tree

### 思路
```
1.在类中定义一个成员变量 int[] nums，用于存储输入的有序数组。
2.在 sortedArrayToBST 方法中，将 nums 赋值为输入的有序数组，调用 build 方法构建平衡二叉搜索树并返回根节点。
3.在 build 方法中，如果起始下标大于结束下标，则返回 null。
4.找到中间位置的下标，将该位置的值作为当前节点的值。
5.递归构建左子树，起始下标为 start，结束下标为 (start+end)/2-1。
6.递归构建右子树，起始下标为 (start+end)/2+1，结束下标为 end。
7.返回当前节点。
```
### 代码
```java
class Solution {
    int[] nums;
    public TreeNode sortedArrayToBST(int[] nums) {
        this.nums=nums;
        // 调用 build 方法构建平衡二叉搜索树并返回根节点
        return build(0,nums.length-1);
    }
    public TreeNode build(int start,int end){
        // 如果起始下标大于结束下标，则返回 null
        if(start>end) return null;
        // 找到中间位置的下标，将该位置的值作为当前节点的值
        TreeNode node= new TreeNode(nums[(start+end)/2]);
        // 递归构建左子树，起始下标为 start，结束下标为 (start+end)/2-1
        node.left=build(start,(start+end)/2-1);
        // 递归构建右子树，起始下标为 (start+end)/2+1，结束下标为 end
        node.right=build((start+end)/2+1,end);
        // 返回当前节点
        return node;
    }
}
```
## 把二叉搜索树转换为累加树
给出二叉 搜索 树的根节点，该树的节点值各不相同，请你将其转换为累加树（Greater Sum Tree），使每个节点 node 的新值等于原树中大于或等于 node.val 的值之和。

提醒一下，二叉搜索树满足下列约束条件：

节点的左子树仅包含键 小于 节点键的节点。
节点的右子树仅包含键 大于 节点键的节点。
左右子树也必须是二叉搜索树。

https://leetcode.cn/problems/convert-bst-to-greater-tree/

### 思路
```
1.从大到小遍历，大的节点累加即可，使用递归，右中左
```
### 代码
```java
class Solution {
    private int sum=0;
    public TreeNode convertBST(TreeNode root) {
        if(root==null) return null;
        convertBST(root.right);
        sum+=root.val;
        root.val=sum;
        convertBST(root.left);
        return root;
    }
}
