---
title: Java 集合常见面试题总结
slug: java-collection-questions
categories: Java八股
tags:
  - Java集合
  - 面试题
date: 2025-08-20
---
>背景：OpenJDK 21.0.4
# Java 集合概念

Java 集合，也叫作容器，主要是由两大接口派生而来：一个是 `Collection`接口，主要用于存放单一元素；另一个是 `Map` 接口，主要用于存放键值对。对于`Collection` 接口，又有三个主要的子接口：`List`、`Set`、`Queue`。

![](https://cdn.img.turingzy.cn/2025/20250818.webp)

注：图中只列举了主要的继承派生关系，并没有列举所有关系。比方省略了`AbstractList`, `NavigableSet`等抽象类。
### 数组和集合的区别？

- 数组是固定长度的数据结构，而集合是动态长度的数据结构，可以根据需要动态增加或减少元素。
- 数组可以包含基本数据类型和对象，而集合只能包含对象。
- 数组可以直接访问元素，而集合需要通过迭代器或其他方法访问元素。
### 你使用过的 Java 集合类的特点？

| 集合类               | 接口          | 底层实现            | 特点             | 时间复杂度（主要操作）            |
| :---------------- | :---------- | :-------------- | :------------- | :--------------------- |
| **ArrayList**     | List        | 动态数组            | 有序、可重复，随机访问快   | 访问 O(1)，插入/删除 O(n)     |
| **LinkedList**    | List, Deque | 双向链表            | 有序、可重复，插入/删除快  | 访问 O(n)，插入/删除 O(1)     |
| **HashMap**       | Map         | 哈希表（数组+链表/红黑树）  | 无序、key 唯一，快速查找 | 平均 O(1)，最坏 O(log n)    |
| **HashSet**       | Set         | 哈希表（基于 HashMap） | 无序、唯一，快速查询     | 平均 O(1)，最坏 O(log n)    |
| **PriorityQueue** | Queue       | 最小堆（数组）         | 按优先级排序，非线程安全   | 入队/出队 O(log n)，查看 O(1) |

>HashMap：JDK 1.8 之前 HashMap 由数组+链表组成的，数组是 HashMap 的主体，链表则是主要为了解决哈希冲突而存在的（“拉链法”），JDK 1.8 以后在解决哈希冲突时有了较大的变化，当链表长度大于阈值（默认为 8）时，将链表转化为红黑树，以减少搜索时间。
### Java 中线程安全的集合有哪些？

- Vector：List 接口的实现，对修改方法都进行了 **synchronized** 同步处理。
- Hashtable：Map 接口的实现，底层是数组+链表，也是通过 **synchronized** 进行同步处理。
- ConcurrentHashMap：Map 接口的实现，JDK1.7 底层数据结构是分段的数组+链表，采取的是 **Segment 分段锁**；JDK1.8 后和 HashMap 一致，使用 **synchronized** 和 **CAS** 来操作，锁粒度更小性能也更好。
- ......

java.util.concurrent 包下提供的都是线程安全的集合。

这里取 [Java集合常见面试题总结(下)](https://javaguide.cn/java/collection/java-collection-questions-02.html#%E2%AD%90%EF%B8%8Fconcurrenthashmap-%E5%92%8C-hashtable-%E7%9A%84%E5%8C%BA%E5%88%AB) 中 ConcurrentHashMap 不同时期的实现图作为对比。

JDK1.7 的 ConcurrentHashMap：

![](https://cdn.img.turingzy.cn/2025/20250820.webp)

JDK1.8后 的 ConcurrentHashMap：

![](https://cdn.img.turingzy.cn/2025/20250820-1.webp)
### Collections 和 Collection 的区别？

- Collections：这个是针对 Java 集合框架的工具类实现，主要用来对 Collection 实现类进行排序、查找、复制、反转等操作。
- Collection：Java 集合框架中的一个接口，定义了集合操作（增删改查）通用的方法，有着诸多实现类。
### 集合的遍历方式有哪些？

>参考 [HashMap 的 7 种遍历方式与性能分析！「修正篇」](https://mp.weixin.qq.com/s?__biz=MzkxOTcxNzIxOA==&mid=2247505580&idx=1&sn=1825ca5be126c2b650e201fb3fa8a3e6&source=41&poc_token=HAzMpWijgk6pP0lUaTjh0JnakPPZTGioCiDjELmL)

1. 普通 for 循环
2. For Each 方式遍历
3. forEach() 方法
4. 迭代器（Iterator），实现 Iterable 接口
5. Lambda 表达式遍历（JDK 1.8+）
6. Streams API 遍历（JDK 1.8+）
# List

# Queue

# Map
# Set