---
title: 剑指offer刷题
date: 2019-9-30 18:33:01
updated: 2020-10-06 20:53:01
tags:
    - 剑指offer
    - 刷题
categories: algorithm
---
# 缘起

这是我学习[《剑指offer》](https://book.douban.com/subject/6966465/)的学习记录，希望能够通过用java完成其中的题目来提高我对java的理解，并且复习以前数据结构的相关内容以提升面试中算法方面的应对能力。题目主要来自[心谭博客](https://xin-tan.com/passages/2019-06-23-algorithm-offer/#介绍)，对于其中按照字符串、查找、链表等分类的题目依次进行分析和实现。

希望也能够对大家的学习和面试有所帮助吧！
# 查找

> 主要掌握顺序查找和二分查找方法

## 1、旋转数组最小的数字

### 题目描述

把一个数组最开始的若干个元素搬到数组的末尾，我们称之为数组的旋转。

输入一个递增排序的数组的一个旋转，输出旋转数组的最小元素。例如数组{3,4,5,1,2}为{1,2,3,4,5}的一个旋转，该数组的最小值为 1。

### 解题思路

利用二分查找的思想，如果中间的元素比最左边的元素大，则显而易见最小值在右侧，反之则在左侧。

如果左右侧和中间的值相同无法判断，则转为顺序查找。

### 代码实现

```java
import java.util.Arrays;

public class 旋转数组最小的数字 {
    static int method1 (int[] a) {
        if (a.length == 0) {
            System.out.println("Empty array!");
            return 0; // 空数组
        }
        int left = 0;
        int right = a.length-1;
        if (right-left <= 1) return a[right]; // 数组元素少于2返回结果
        int mid = (left+right)/2;
        if (a[left] == a[mid] && a[right] == a[mid]) {
            return findMin(Arrays.copyOfRange(a, left, right + 1)); // 三处元素大小相同转为普通排序
        }
        if (a[mid] >= a[left]) return method1(Arrays.copyOfRange(a, mid, right + 1)); // 最小值在右边
        if (a[mid] <= a[right]) return method1(Arrays.copyOfRange(a, left, mid + 1)); // 最小值在左边
        System.out.println("Unknwon error!");
        return -1;
    }
    static int findMin (int[] a) {
        // 顺序查找最小值
        int min = a[0];
        for(int i = 1; i < a.length; i++) {
            if (a[i] < min) min = a[i];
        }
        return min;
    }
    public static void main (String[] args) {
        int[] sample = {3, 4, 5, 6, 1, 1, 2, 3}; // 输入数组
        System.out.println(method1(sample));
    }
}
```

## 2、数字在排序数组中出现的次数

### 题目描述

统计一个数字在排序数组中出现的次数。

### 解题思路

首先采用二分查找找到指定数字在数组中的下标。

然后分别向前向后遍历确定左右边界下标，差值即为出现次数。

### 代码实现

```java
public class 数字在排序数组中出现的次数 {
    static int method1 (int[] a, int b) {
        int index = findBoundary(a, b);
        if (index == -1) return -1; // a中无b
        int left = index;
        int right = index;
        while(a[left] == b) left--; // 寻找左边界
        while(a[right] == b) right++; // 寻找右边界
        return right - left - 1;
    }
    static int findBoundary (int[] a, int b) {
        // 二分查找b在a中的位置
        if (a.length == 0) return -1; // 空数组
        int left = 0;
        int right = a.length - 1;
        int mid = -1;
        while (left < right) {
            mid = (left + right) / 2;
            if (a[mid] == b) return mid; // bingo
            if (a[mid] < b) { // b在右边
                left = mid + 1;
            } else right = mid - 1; // b在左边
        }
        if (a[left] == b) return left; // left==right
        return -1; // a中无b
    }
    public static void main (String[] args) {
        int[] sample = {1, 2, 3, 3, 3, 4, 4, 5}; // 输入数组
        System.out.println(method1(sample, 2));
    }
}
```
# 字符串

>  关于字符串的题目，对于熟悉字符串相关api的使用有很大的帮助。

## 1、替换空格

###题目描述

请实现一个函数，把字符串中的每个空格替换成"%20"。

例如输入“We are happy.”，则输出“We%20are%20happy.”。

###解题思路

一种是利用String类的replaceAll()方法，可以将字符串中的指定子字符串替换为其他字符串(支持正则)；

另一种方法则是首先用split()函数将字符串根据空格分割成字符串数组，在用StringJoiner将字符串合并并在每个元素间加上"%20"。

###代码实现

```java
import java.util.StringJoiner;

public class 替换空格 {
    public static void main(String[] args) {
        String a = "We are happy.";
        System.out.println(method1(a));
        System.out.println(method2(a));
    }
    static String method1(String a) {
        return a.replaceAll(" ", "%20");
    } // 将空格换成%20
    static String method2(String a) {
        // 利用split将字符串按照空格分开
        String b[] = a.split(" ");
        var c = new StringJoiner("%20"); // 在每个字符串之间加上%20
        for (String bb : b) {
            c.add(bb);
        }
        return c.toString();
    }
}
```

## 2、字符串的全排列

### 题目描述

输入一个字符串，打印出该字符串中字符的所有排列。例如输入字符串 abc，则打印出由字符 a、b、c 所能排列出来的所有字符串 abc、acb、bac、bca、cab 和 cba。

### 解题思路

用递归的方法，对于每次传入递归方法中的字符串和下标，将他的对应下标字符和之后的所有字符依次进行交换并将下标+1继续递归，直到下标到达了字符串的末尾，便实现了所有可能的字符串的遍历。还利用了set数据结构，防止重复结果的出现。method1()中使用copy实现了基本类型的深拷贝，使得每次调用该函数的aa值不互相影响。

### 代码实现

```java
import java.util.HashSet;

public class 字符串的全排列 {
    public static void main(String[] args) {
        String test = "abbcccdddd"; // 输入字符串
        method1(test.toCharArray(), 0);
        System.out.println("result:"+result);
        System.out.println("length:"+result.size());
    }
    static HashSet<String> result = new HashSet<>();
    static char[] swap(char[] aa, int i, int j) {
        // 交换元素
        char[] a = aa.clone();
        char b = a[i];
        a[i] = a[j];
        a[j] = b;
        return a;
    }
    static void method1(char[] sample, int n) {
        // n表示当前交换元素下标
        int len = sample.length;
        if (n == len) {
            // 最后一个元素交换完记录结果
            result.add(new String(sample));
        }
        for (int i = n; i < len; i++) {
            // 将当前交换元素后的每个元素和当前元素进行交换
            method1(swap(sample, n, i), n+1);
        }
    }
}
```

## 3、翻转单词顺序

### 题目描述

输入一个英文句子，翻转句子中单词的顺序，但单词内字符的顺序不变。

为简单起见，标点符号和普通字母一样处理。例如输入字符串"I am a student."，则输出"student. a am I"。

### 解题思路

以空格为分割将字符串编程数组再利用StringJoiner逆序将他们合成新的字符串

### 代码实现

```java
import java.util.StringJoiner;

public class 翻转单词顺序 {
    public static void main(String[] args) {
        String sample = "I am a student."; // 输入字符串
        System.out.println(method1(sample));
    }
    static String method1(String a) {
        String[] aa = a.split(" "); // 将输入字符串根据空格分组
        var reversedA = new StringJoiner(" ");
        for (int i = aa.length-1; i>=0; i--) {
            reversedA.add(aa[i]);
        }
        return reversedA.toString();
    }
}
```

## 4、实现atoi

### 题目描述

请你来实现一个  atoi  函数，使其能将字符串转换成整数。

首先，该函数会根据需要丢弃无用的开头空格字符，直到寻找到第一个非空格的字符为止。

当我们寻找到的第一个非空字符为正或者负号时，则将该符号与之后面尽可能多的连续数字组合起来，作为该整数的正负号；假如第一个非空字符是数字，则直接将其与之后连续的数字字符组合起来，形成整数。

该字符串除了有效的整数部分之后也可能会存在多余的字符，这些字符可以被忽略，它们对于函数不应该造成影响。

注意：假如该字符串中的第一个非空格字符不是一个有效整数字符、字符串为空或字符串仅包含空白字符时，则你的函数不需要进行转换。

在任何情况下，若函数不能进行有效的转换时，请返回 0。

说明：

假设我们的环境只能存储 32 位大小的有符号整数，那么其数值范围为  [−2^31,  2^31 − 1]。如果数值超过这个范围，qing 返回  INT_MAX (2^31 − 1) 或  INT_MIN (−2^31) 。

题目来自 [LeetCode](https://leetcode-cn.com/problems/string-to-integer-atoi)，可以直接前往这个网址查看题目各种情况下要求的输出。

### 解题思路

- 利用split按照空格分割字符串，获得第一段非空字符串，如果没有则返回0
- 判断首位字符是否为正负或者数字，若是正负则记录正负，不是则返回零
- 一直到下个非数字之前，将所有数字字符转化为整数，用long类型防止越界，并且每次加/减完都要检查是否越界，是则直接返回 INT_MAX or INT_MIN

### 代码实现

```java
public class atoi {
    static final int INT_MAX = Integer.MAX_VALUE; // 最大值
    static final int INT_MIN = Integer.MIN_VALUE; // 最小值
    static boolean isPlus = true; // 是否为正数
    static boolean isSymble(char a) {
        // 判断是否为正负号
        return a=='+' || a=='-';
    }
    static boolean isNum(char a) {
        // 判断是否为数字
        return a>='0' && a<='9';
    }
    static int atoi(String a) {
        // 转换字符串为整数
        String[] aToArray = a.split(" ");
        if (aToArray.length==0) return 0;
        int i = 0;
        for (; i<aToArray.length; i++) {
            if(aToArray[i].length()>0) break;
        }
        if (i==aToArray.length) return 0; // 排除空输入的情况
        char[] aa = aToArray[i].toCharArray(); // 排除首尾空格和多余字符串
        i = 0;
        if (isSymble(aa[0])) {
            isPlus = aa[0]=='+';
            i += 1;
        }
        else if (isNum(aa[0])) {
            isPlus = true;
        }
        else return 0;
        long re = 0; // 返回的数字
        for(;i<aa.length; i++) {
            if (!isNum(aa[i])) break; //排除字符串中有非数字的异常情况
            if (isPlus) re = re*10+aa[i]-'0';
            else re = re*10-aa[i]+'0';
            if (re>INT_MAX) return INT_MAX;
            if (re<INT_MIN) return INT_MIN;
        }
        return (int)re;
    }
    public static void main(String[] args) {
        String a = "9223372036854775808"; // 输入字符串
        System.out.println(atoi(a));
    }
}
```
# 树

> 树相关数据结构题目

## 1. 重建二叉树

### 题目描述

输入某二叉树的前序遍历和中序遍历的结果，请重建该二叉树。假设输入的前序遍历和中序遍历的结果中都不含重复的数字。

例如，给出

前序遍历 preorder = [3,9,20,15,7]
中序遍历 inorder = [9,3,15,20,7]
返回如下的二叉树：

```
    3
   / \
  9  20
    /  \
   15   7
```

### 解题思路

题目中强调了**两个遍历中都不含重复数字**，因此可以直接用遍历序列中的数字值来定位根节点。

我们知道前序遍历序列的第一个数字是该序列对应二叉树的根节点，因此可以轻松的获取根节点的位置和大小。

然后我们可以通过确定根节点的大小在中序遍历序列中找到根节点的位置，从而将两个序列都分成左子树和右子树的子数列。

再在两个子树的子序列中找到他们的根节点，以此类推。

直到找到序列的长度为1，则递归完了整棵树。

### 代码实现

```java
import java.util.Arrays;

public class 重建二叉树 {
    static public class node {
        // 定义节点类
        public node left;
        public node right;
        public int value; // 节点值
        public node (node l, node r, int v) {
            this.left = l;
            this.right = r;
            this.value = v;
        }
    }
    public node getNode(int[] preorder, int[] inorder) {
        if (preorder.length <= 0)
            return null;
        if (preorder.length == 1)
            return new node(null, null, preorder[0]);
        int value = preorder[0]; // 获得节点值
        int midIndex = 0;
        for (int i = 0; i < inorder.length; i++) {
            if (inorder[i] == value) {
                // 获取根节点索引
                midIndex = i;
                break;
            }
        }
        // 递归整棵树
        return new node(getNode(Arrays.copyOfRange(preorder, 1, midIndex+1),
                Arrays.copyOfRange(inorder, 0, midIndex)),
                getNode(Arrays.copyOfRange(preorder, midIndex+1, preorder.length),
                Arrays.copyOfRange(inorder, midIndex+1, inorder.length)), value);
    }
}
```
# 链表

> 帮助你快速掌握链表这种数据结构

## 0、链表构造

在一切开始前，首先要用java构造出链表的基本数据结构(顺便实现了深拷贝)，以下是代码实现:

```java
public class Node <T> implements Cloneable {
    public Node <T> next = null; // next节点
    public T data; // 节点内容
    public Node (T data) {
        this.data = data;
    }
    @Override
    public Node <T> clone() {
        try {
            var re = (Node) super.clone();
            re.next = this.next;//深拷贝处理
            return re;
        } catch (CloneNotSupportedException e) {
            return null;
        }
    }
}
public class MyLink <T> implements Cloneable {
    public Node <T> head = null; // 头节点
    public void insert (T data) {
        // 插入新节点
        var newNode = new Node <T> (data); // 实例化新节点
        if (this.head == null) {
            this.head = newNode; // 空链表情况
            return;
        }
        var tmp = head;
        while (tmp.next != null) {
            tmp = tmp.next;
        }
        tmp.next = newNode; // 遍历到空节点，并将新节点连上
    }
    public void print () {
        // 顺序输出链表值
        var tmp = head;
        while(tmp != null) {
            System.out.print(tmp.data + " ");
            tmp = tmp.next;
        }
        System.out.println();
    }
    @Override
    public MyLink <T> clone() {
        try {
            var re = (MyLink) super.clone();
            re.head = this.head.clone();//深拷贝处理
            var tmp = re.head;
            while (tmp.next != null) {
                tmp.next = tmp.next.clone();//深拷贝处理
                tmp = tmp.next;//深拷贝处理
            }
            return re;
        } catch (CloneNotSupportedException e) {
            return null;
        }
    }
}
```

## 1、从尾到头打印链表

### 题目描述

输入一个链表，从尾到头打印链表每个节点的值。

### 解题思路

new一个节点，从链表头部开始遍历整个链表，将得到的值依次存入栈中。

利用栈的先入后出的特性，再依次从栈中pop出节点值。

### 代码实现

```java
import java.util.Stack;

public class 从尾到头打印链表 {
    static <T> void method1 (MyLink <T> a) {
        if (a.head == null) {
            System.out.println("链表为空");
            return;
        }
        var tmp = a.head;
        Stack <T> re = new Stack<>();
        while (tmp != null) {
            re.push(tmp.data);
            tmp = tmp.next;
        }
        while (!re.empty()) {
            System.out.print(re.pop());
            System.out.print(' ');
        }
        System.out.println();
    }
    public static void main (String[] args) {
        MyLink<Integer> a = new MyLink<>();
        a.insert(1);
        a.insert(2);
        a.insert(3);
        a.insert(3);
        a.insert(3);
        method1(a);

    }
}

```

## 2、快速删除链表节点

### 题目描述

给定单向链表的头指针和一个结点指针，定义一个函数在 O(1) 时间删除该结点。

### 解题思路

已办删除节点的思路是遍历找到要求节点的上一个节点，在将要求节点的下一个节点连到上一个的next后删除要求节点，但本体要求时间复杂度在 O(1) ，所以我们需要另谋出路。

仔细观察会发现题目给定的是节点类而非正常情况的节点内容，因此直接将要求节点的next的内容和next复制到要求节点本身再删除要求节点的next即可。

这样除了尾节点的情况需要单独遍历删除，其他n-1种情况时间复杂度都是 O(1) ，符合题目条件。

### 代码实现

```java
public class 快速删除链表节点 {
    static <T> void method1 (MyLink <T> a, Node <T> b) {
        if (a.head == null || b == null) return; // 排除异常情况
        if (a.head == b) { // 删除头节点情况
            a.head = a.head.next;
            return;
        }
        var nextNode = b.next; // b的下一个节点
        if (nextNode == null) { // b是尾节点
            nextNode = a.head;
            while (nextNode.next != b) nextNode = nextNode.next; // 寻找b节点的前一个节点
            nextNode.next = null; // 删除节点
            return;
        }
        b.data = nextNode.data;
        b.next = nextNode.next;
        nextNode = null; // 建议垃圾回收
    }
    public static void main (String[] args) {
        MyLink <Integer> sample = new MyLink<>();
        sample.insert(1);
        sample.insert(2);
        sample.insert(3);
        sample.print();
        var node = sample.head.next; // 要删除的节点
        method1(sample, node);
        sample.print();
    }
}

```

## 3、链表倒数第k节点

### 题目描述

输入一个单链表，输出该链表中倒数第 k 个节点。

### 解题思路

先遍历一遍链表获得链表长度；

通过长度获得倒数第k个节点的下标；

遍历第二遍链表，找到指定下标的节点并返回。

### 代码实现

```java
public class 链表倒数第k节点 {
    static <T> Node <T> method1 (MyLink <T> a, int k) {
        if (k <= 0) return null; // k非法
        if (a.head == null) return null; // 空链表
        int length = 1; // 链表长度
        var tmp = a.head;
        while (tmp.next != null) {
            tmp = tmp.next;
            length ++;
        }
        if (k > length) return null; // k非法
        int index = length - k; // 倒数k节点下标
        tmp = a.head;
        while (index > 0) {
            tmp = tmp.next;
            index --;
        }
        return tmp;
    }
    public static void main (String[] args) {
        MyLink<Integer> sample = new MyLink<>();
        sample.insert(0);
        sample.insert(1);
        sample.insert(1);
        sample.insert(2);
        sample.insert(2);
        sample.insert(2);
        sample.insert(3);
        sample.insert(5);
        sample.print();
        System.out.println(method1(sample, 6).data);
    }
}
```

## 4、反转链表

### 题目描述

定义一个函数，输入一个链表，反转该链表并输出反转后链表。

### 解题思路

第一种思路是使用头插法，将输入链表的节点依次放入输出链表的头指针所指节点，类似栈；

第二种思路同样需要遍历所有节点，对于每个节点，

依次保存他的前节点pre和下一节点next。

然后将当前节点的next指向pre。

最后通过之前保存的next，将pre和当前节点向后移一位。

重复上述步骤，直到当前节点指向了null，便可反转整个链表；

两种方法都需要遍历整个链表，时间复杂度为O(N)。

但如果第二种方法不创建新链表，则空间复杂度只有O(1)，相比第一种方法的O(N)​更加节省空间。

### 代码实现

```java
public class 反转链表 {
    static <T> MyLink <T> method1(MyLink <T> sample) {
        // 头插法
        if (sample.head == null) return null; // 排除异常
        var re = new MyLink<T>();
        Node<T> re_head = new Node<T>(null); // 定义头指针
        re_head.next = re.head; // 指向第一个节点
        var tmp = sample.head;
        while (tmp != null) {
            var next = re_head.next; // 记录当前头节点
            re_head.next = new Node <> (tmp.data); // 插入新节点
            re_head.next.next = next; // 将原头节点放入新头节点后面
            tmp = tmp.next;
        }
        re.head = re_head.next;
        return re; // 返回反转链表
    }
    static <T> MyLink <T> method2(MyLink <T> sample) {
        var tmp = sample.head; // tmp表示当前节点
        Node<T> pre = null; // pre表示当前节点的前节点
        Node<T> next; // next表示当前节点的下一个节点
        while (tmp != null) {
            // 将tmp.next指向pre
            next = tmp.next; // 存储下一个节点
            tmp.next = pre;
            // pre和tmp向后移一位
            pre = tmp;
            tmp = next;
        }
        sample.head = pre; // 重新定位头节点位置
        return sample;
    }
    public static void main(String[] args) {
        var sample = new MyLink<Integer>();
        sample.insert(1);
        sample.insert(2);
        sample.insert(3);
        sample.insert(4);
        sample.insert(5);
        sample.print();
        method1(sample).print();
        method2(sample.clone()).print();
    }
}
```