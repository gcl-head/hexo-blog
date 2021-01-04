---
title: LeetCode刷题
date: 2019-10-16 22:17:33
updated: 2019-10-16 22:17:33
tags:
    - leetcode
    - 刷题
---
## 1、公交站间的距离

### 题目描述

环形公交路线上有 n 个站，按次序从 0 到 n - 1 进行编号。我们已知每一对相邻公交站之间的距离，distance[i] 表示编号为 i 的车站和编号为 (i + 1) % n 的车站之间的距离。

环线上的公交车都可以按顺时针和逆时针的方向行驶。

返回乘客从出发点 start 到目的地 destination 之间的最短距离。

[题目链接](https://leetcode-cn.com/problems/distance-between-bus-stops/)

### 解题思路

最简单的思路是直接获得全部距离之和，然后求得其中一个方向的距离(选取经过车站数量少的以减少运算)，这样再通过求差获得另一个方向的距离然后进行比较，输出最小值；

我的方法是，两个方向同步前进。假如一个方向先到达了目标站点且距离较小则直接返回，否则继续沿另一个方向前进，直到另一个方向距离大于已走完的方向或者另一方向走完为止。

这样最好的情况是只计算了两段路程即可输出O(1)​，最坏情况也就O(n)​，与直接遍历一样。

### 实现代码

```java
class Solution {
    public int distanceBetweenBusStops(int[] distance, int start, int destination) {
        int n1 = start; // 顺时针的当前位置
        int n2 = start; // 逆时针的当前位置
        int len = distance.length; // 节点数量
        int re1 = 0; // 顺时针距离
        int re2 = 0; // 逆时针距离
        while (n1 != destination && n2 != destination) {
            re1 += distance[n1];
            n1 = (n1 + 1) % len;
            re2 += distance[(n2 - 1 + len) % len];
            n2 = (n2 - 1 + len) % len;
        }
        if (n1 == destination) {
            if (re1 <= re2) return re1; // 顺时针已走到且比逆时针短
            while (n2 != destination) { // 逆时针继续前进
                re2 += distance[(n2 - 1 + len) % len];
                if (re2 >= re1) return re1; // 顺时针短
                n2 = (n2 - 1 + len) % len;
            }
            return re2;
        }
        if (n2 == destination && re2 <= re1) return re2;
        while (n1 != destination) { // 顺时针继续前进
            re1 += distance[n1];
            if (re1 >= re2) return re2; // 逆时针短
            n1 = (n1 + 1) % len;
        }
        return re1;
    }
}
```

## 2、最小好进制

### 题目描述

对于给定的整数 n, 如果n的k（k>=2）进制数的所有数位全为1，则称 k（k>=2）是 n 的一个好进制。

以字符串的形式给出 n, 以字符串的形式返回 n 的最小好进制。

[题目链接](https://leetcode-cn.com/problems/smallest-good-base/)

### 解题思路

首先十进制转其他进制再判断是不是好进制肯定没有将各个长度的好进制直接与十进制进行大小比较效率高。因为后者由于是大小比较，可以使用中分查找大幅度减小计算资源的消耗。

其次，每个整数都必有好进制，最坏情况也必有n-1和n两个好进制。

我的思路是，从$⌊\log(n)/\log(2)⌋+1$长度开始，一直到3，对于每个长度的情况，都进行一次好进制的猜测。由于随着长度的减小，进制数是递增的，所以一旦检测到了好进制，便可以直接返回。如果没有遍历到，则返回长度为2的好进制n-1。

对于好进制的检测，由于有大小顺序，可以引入二分法来实现。

### 代码实现

```java
class Solution {
    public String smallestGoodBase(String n) {
        long num = Long.parseLong(n); // String to long
        for (int len = (int)Math.floor(Math.log(num)/Math.log(2)) + 1; len>2; len --) {
            // 从长到短遍历所有长度的可能性
            long radix = getRadix(num, len);
            if (radix != -1) return String.valueOf(radix);
        }
        return String.valueOf(num - 1); // 最坏情况，进制是自身大小减一
    }
    private long getRadix (long num, int len) {
        // 获得num对应的len长度的进制数
        long left = 2;
        long right = num - 1;
        while (left < right) {
            long mid = (long)Math.floor((left + right) / 2);
            // 二分查找
            if (getSum(mid, len) == num) return mid; // 得到进制
            if (getSum(mid, len) > num) {
                right = mid - 1;
            } else left = mid + 1;
        }
        if (getSum(left, len) == num) return left;
        return -1;
    }
    private long getSum (long radix, int len) {
        long p = 1;
        long sum = 0;
        for (int i = 0; i < len; ++i) {
            if (Long.MAX_VALUE - sum < p) {     // 防止溢出
                return Long.MAX_VALUE;
            }
            sum += p;
            if (Long.MAX_VALUE / p < radix) {   // 防止溢出
                p = Long.MAX_VALUE;
            } else {
                p *= radix;
            }
        }
        return sum;
    }
}
```

## 3、岛屿数量

### 题目描述

给定一个由 '1'（陆地）和 '0'（水）组成的的二维网格，计算岛屿的数量。一个岛被水包围，并且它是通过水平方向或垂直方向上相邻的陆地连接而成的。你可以假设网格的四个边均被水包围。

[题目链接](https://leetcode-cn.com/problems/number-of-islands/)

###  解题思路

利用感染函数，递归地将当前点和点周围的四个点感染。

具体来说就是判断是否为1，如果是说明是未感染的岛将他置为2，直到将当前点的所有关联点即一座岛都置为2，则一次感染结束，岛屿数量加一。

### 代码实现

```java
class Solution {
    public int numIslands(char[][] grid) {
        int num = 0; // 岛的数量
        for(int i = 0; i < grid.length; i++)
            for(int j = 0; j < grid[0].length; j++)
                if(grid[i][j] == '1') {
                    infect(grid, i, j);
                    num++;
                }
        return num;
    }
    public void infect(char[][] grid, int i, int j) {
        if(grid.length <= 0 || i < 0 || i >= grid.length || j < 0 || j >= grid[0].length || grid[i][j] != '1') return; // 感染函数结束条件
        grid[i][j] = '2'; // 感染该点
        // 感染周围
        infect(grid, i-1, j);
        infect(grid, i+1, j);
        infect(grid, i, j+1);
        infect(grid, i, j-1);
    }
}
```

