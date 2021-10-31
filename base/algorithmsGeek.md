- 空间换时间
- 升维：一维升二维
- 找最近重复子问题 if else for while recursion

# L2

## 时间和空间复杂度



![image-20200810161317399](asserts/algorithmsGeek/image-20200810161317399.png)



## 时间复杂度曲线

![image-20200810162028434](asserts/algorithmsGeek/image-20200810162028434.png)

- 写完程序要分析出它的时间和空间复杂度
- 使用最简洁的时间和空间复杂度完成程序

## 主定理 

Master Theorem

任何递归函数都可以算出时空复杂度

![image-20200810162922625](asserts/algorithmsGeek/image-20200810162922625.png)

### 1 二分查找

### 2 二叉树遍历

### 3 排好序的二维矩阵进行二分查找

### 4 归并排序



# L3

## 数组

### 数组的底层实现

通过内存管理器开辟一段连续的地址，每个地址可直接通过内存管理器访问

![image-20200810163950354](asserts/algorithmsGeek/image-20200810163950354.png)



### 数组的缺点

插入和删除 复杂度为On

- java中的插入

![image-20200810164827520](asserts/algorithmsGeek/image-20200810164827520.png)

- java数组扩容

![image-20200810165056821](asserts/algorithmsGeek/image-20200810165056821.png)

### 时间复杂度

![image-20200810170052367](asserts/algorithmsGeek/image-20200810170052367.png)



## 链表

### java实现



![image-20200810170120496](asserts/algorithmsGeek/image-20200810170120496.png)

### 时间复杂度

![image-20200810170140943](asserts/algorithmsGeek/image-20200810170140943.png)



## 跳表

- 理解原理为主
- 在redis中有应用
- 用于解决链表的访问时间慢

### 思想

升维、空间换时间

### 工程应用

- Redis
- LRUcache

![image-20200810172720990](asserts/algorithmsGeek/image-20200810172720990.png)

### 实现

![image-20200810172116064](asserts/algorithmsGeek/image-20200810172116064.png)



![image-20200810172140410](asserts/algorithmsGeek/image-20200810172140410.png)



### 时间复杂度分析

![image-20200810172529857](asserts/algorithmsGeek/image-20200810172529857.png)

![image-20200810172458520](asserts/algorithmsGeek/image-20200810172458520.png)

### 空间复杂度

![image-20200810172512123](asserts/algorithmsGeek/image-20200810172512123.png)



## 题目

Array 实战题目
https://leetcode-cn.com/problems/container-with-most-water/
https://leetcode-cn.com/problems/move-zeroes/
https://leetcode.com/problems/climbing-stairs/	
https://leetcode-cn.com/problems/3sum/ (高频老题）

两数之和题目：[ https://leetcode-cn.com/problems/two-sum/](https://leetcode-cn.com/problems/two-sum/)

**Array** **实战题目**

- https://leetcode-cn.com/problems/container-with-most-water/
- https://leetcode-cn.com/problems/move-zeroes/
- https://leetcode.com/problems/climbing-stairs/
- [https://leetcode-cn.com/problems/3sum/ ](https://leetcode-cn.com/problems/3sum/)(高频老题）

**Linked List** **实战题目**

- https://leetcode.com/problems/reverse-linked-list/
- https://leetcode.com/problems/swap-nodes-in-pairs
- https://leetcode.com/problems/linked-list-cycle
- https://leetcode.com/problems/linked-list-cycle-ii
- https://leetcode.com/problems/reverse-nodes-in-k-group/

**课后作业**

- https://leetcode-cn.com/problems/remove-duplicates-from-sorted-array/
- https://leetcode-cn.com/problems/rotate-array/
- https://leetcode-cn.com/problems/merge-two-sorted-lists/
- https://leetcode-cn.com/problems/merge-sorted-array/
- https://leetcode-cn.com/problems/two-sum/
- https://leetcode-cn.com/problems/move-zeroes/
- https://leetcode-cn.com/problems/plus-one/

[戳此了解每周课后作业要求及提交方式](http://u.geekbang.org/lesson/1?article=144228)



<img src="assets/algorithmsGeek/image-20210825003530677.png" alt="image-20210825003530677" style="zoom:50%;" /

![image-20210825004156508](assets/algorithmsGeek/image-20210825004156508.png)

![image-20210825005108909](assets/algorithmsGeek/image-20210825005108909.png)



# L4

## stack

底层是一个数组

![image-20210829020719860](assets/algorithmsGeek/image-20210829020719860.png)

## Dequeue

![image-20210829020552383](assets/algorithmsGeek/image-20210829020552383.png)

![image-20210829020628409](assets/algorithmsGeek/image-20210829020628409.png)

<img src="assets/algorithmsGeek/image-20210829015713379.png" alt="image-20210829015713379" style="zoom:50%;" />

![image-20210829020757689](assets/algorithmsGeek/image-20210829020757689.png)

## queue

![image-20210829020332078](assets/algorithmsGeek/image-20210829020332078.png)

## Priority Queue

![image-20210829021031147](assets/algorithmsGeek/image-20210829021031147.png)



## 时间复杂度

![image-20210829022440410](assets/algorithmsGeek/image-20210829022440410.png)

