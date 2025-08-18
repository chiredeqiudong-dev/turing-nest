---
title: Java 集合常见面试题总结
slug: java-collection-questions
categories: Java八股
tags:
  - Java集合
  - 面试题
date: 2025-08-18
---
>背景：OpenJDK 21.0.4
# Java 集合概念

Java 集合，也叫作容器，主要是由两大接口派生而来：一个是 `Collection`接口，主要用于存放单一元素；另一个是 `Map` 接口，主要用于存放键值对。对于`Collection` 接口，又有三个主要的子接口：`List`、`Set`、`Queue`。

![](https://cdn.img.turingzy.cn/2025/20250818.webp)

注：图中只列举了主要的继承派生关系，并没有列举所有关系。比方省略了`AbstractList`, `NavigableSet`等抽象类。
### 数组和集合的区别？

- 数组是固定长度的数据结构，一旦创建长度就无法改变，而集合是动态长度的数据结构，可以根据需要动态增加或减少元素。
- 数组可以包含基本数据类型和对象，而集合只能包含对象。
- 数组可以直接访问元素，而集合需要通过迭代器或其他方法访问元素。
### 使用过的 Java 集合特点？

### Java 中线程安全的集合是什么？

### Collections 和 Collection 的区别？

### 集合的遍历方式有哪些？

# List

# Queue

# Map
# Set