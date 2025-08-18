---
title: Floyd Cycle Detection Algorithm
slug: floyd-cycle-detection-algorithm
categories: 算法
tags:
  - leetcode
  - Floyd判圈算法
date: 2025-08-18
---
> 原题链接 [环形链表Ⅱ](https://leetcode.cn/problems/linked-list-cycle-ii/)
> Floyd判圈算法（Floyd Cycle Detection Algorithm）也称龟兔赛跑算法  
# Floyd Cycle Detection Algorithm

理解该算法需要知道理解以下问题：  

1. 为什么 fast 指针的速度是 slow 指针的两倍？  
2. 为什么 fast 指针一定会在环内与 slow 指针相遇？  
3. 为什么在 fast 指针同 slow 指针相遇后，将 slow 指针从头节点重新开始遍历，并且`fast`和`slow`会在入环的第一个节点相遇？  
## 为什么`fast`指针同`slow`的移动速度为`2:1`,而不是`3:1`或其他比例？  

1. 首先排除 $1.5:1$ 的这种情况，在移动速度比小于 $2:1$ 的情况下，slow 永远无法追上 fast 指针
2. 当移动速度比为 $3:1$ 时，对于某些特殊情况会导致 slow 与 fast 永远不会相遇（如下图）

![](https://cdn.img.turingzy.cn/2025/LinkedListCycle%20II.png)

$2:1$ 的比例是经过验证的最优选择，它确保了算法的可靠性、简洁性和效率。（查了，但没有明确的说法）
## 为什么 `fast` 指针一定会在环内与 `slow` 指针相遇？   

> 参考力扣 [Shawxing精讲算法](https://leetcode.cn/u/shawxing-kwok/)

重画链表如下所示，线上有若干个节点。记蓝色慢指针为 slow，红色快指针为 fast。初始时 slow 和 fast 均在头节点处。  

![](https://pic.leetcode.cn/1715514553-RxQrzr-0208_2.png)  

使 slow 和 fast 同时开始遍历，fast 的速度是 slow 的两倍。当 slow 抵达环的入口处时，fast 一定在环上，如下所示。  

![](https://pic.leetcode.cn/1715514558-mCJsmw-0208_3.png)  

- 这不难理解，fast 的速度是 slow 的两倍，当 slow 到达环的入口节点时，fast 肯定在环上。  
  

其中：  

- head 和 A 的距离为 z  
- 弧 AB (沿箭头方向) 的距离为 x  
- 弧 BA 的距离为 y
  

可知：  

- slow 移动的距离为 z
- 设 fast 已经走过了 k 个环 $(k≥0)$  
- fast 对应的移动的距离为 $z+k(x+y)+x$  

因为 fast 的 移动速度是 slow 的两倍，所以 $2z=z+k(x+y)+x$ 化简可得 $z=x+k(x+y)$，替换后如下图所示。  

![](https://pic.leetcode.cn/1715514562-KmTrNr-0208_4.png)  

此时，fast 离 slow 只有 y 个单位长度，所以当 slow 再走 y 步, 相应地 fast 走 2y 步，slow 就会与 fast 相遇。  

- 这里很好的回答了为什么 slow 一定会在环内与 fast 相遇 

设相遇在 C 点，位置如下所示，可得弧 AC 长度为 y  

![](https://pic.leetcode.cn/1715514566-cEsEBC-0208_5.png)  
## 为什么`fast`指针同 `slow`指针相遇后，将`slow`指针从头节点重新开始遍历，`fast` 和 `slow` 会在入环的第一个节点相遇？  

因为此前 x+y 为环长，所以弧 CA 的长度为 x。此时我们另用一橙色指针 ptr (pointer) 指向 head，如下所示。  并使 ptr 和 slow 以 1 个单位的速度前进，在经过 $z=x+k(x+y)$ 步后，可在 A 处相遇。  

![](https://pic.leetcode.cn/1715514569-ATwmZT-0208_6.png)  

这里简单解释为什么 ptr 会与 slow 在 A 节点处相遇。

- 可以知道 ptr 到 A 节点地距离为 $z=x+k(x+y)$，其中 $k(x+y)$ 表示绕着环移动 k 次
- 当 ptr 在离 A 节点距离 x 时，slow 绕着环移动了 k 次并回到 C，最终双方移动 x 后相遇在 A  
  

注：链表无环的情况下，fast 在追到 slow 之前就会指向空节点。