---
title: Java 集合常见面试题总结
slug: java-collection-questions
categories: Java八股
tags:
  - Java集合
  - 面试题
date: 2025-08-21
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

注：java.util.concurrent 包下提供的都是线程安全的集合。

这里取 [Java集合常见面试题总结(下)](https://javaguide.cn/java/collection/java-collection-questions-02.html#%E2%AD%90%EF%B8%8Fconcurrenthashmap-%E5%92%8C-hashtable-%E7%9A%84%E5%8C%BA%E5%88%AB) 中 ConcurrentHashMap 不同时期的实现图作为对比。

JDK1.7 的 ConcurrentHashMap：

![](https://cdn.img.turingzy.cn/2025/20250820.webp)

JDK1.8后 的 ConcurrentHashMap：

![](https://cdn.img.turingzy.cn/2025/20250820-1.webp)
### Collections 和 Collection 的区别？

- Collections：这个是针对 Java 集合框架的工具类实现，主要用来对 Collection 实现类进行排序、查找、复制、反转等操作。
- Collection：Java 集合框架中的一个接口，定义了集合操作（增删改查）通用的方法，有着诸多实现类。
### 集合的遍历方式有哪些？

1. 普通 for 循环
2. For Each 方式遍历
3. forEach() 方法
4. 迭代器（Iterator），实现 Iterable 接口
5. Lambda 表达式遍历（JDK 1.8+）
6. Streams API 遍历（JDK 1.8+）
# List 
### 讲一下 Java 里面 List 的几种实现，几种实现有什么不同？

| **特性**     | ArrayList                         | Vector                             | LinkedList        |
| ---------- | --------------------------------- | ---------------------------------- | ----------------- |
| **底层数据结构** | `Object[]` (动态数组)                 | `Object[]` (动态数组)                  | `Node` 节点 (双向链表)  |
| **线程安全**   | 线程不安全，无同步操作                       | 线程安全，方法使用 `synchronized` 同步        | 线程不安全，无同步操作       |
| **性能**     | 高性能，无锁开销                          | 性能较低，因同步锁粒度大                       | 性能因操作类型而异         |
| **扩容机制**   | 动态扩容，默认增加 50%（`oldCapacity >> 1`） | 动态扩容，默认翻倍（`oldCapacity`），支持自定义增量   | 无容量限制，无需扩容        |
| **适合场景**   | 随机访问、顺序存储                         | 线程安全环境，随机访问、顺序存储                   | 频繁插入和删除           |
| **内存占用**   | 可能因扩容浪费内存，支持 `trimToSize()` 缩容    | 可能因翻倍扩容浪费更多内存，支持 `trimToSize()` 缩容 | 每个节点额外存储指针，内存开销较大 |

### List 可以一边遍历一边修改元素吗？

可以，但要看分情况。以 ArrayList 实现为例：

```java
List<String> list = new ArrayList<>();  
list.add("A");  
list.add("B");  
list.add("C");  

// 1、普通 for 循环
for (int i = 0; i < list.size(); i++) {  
    if("A".equals(list.get(i))) {  
        list.remove(i);  
        --i;  
    }else {  
        System.out.println(list.get(i));  
    }  
}

// 2、for-Each/迭代器
for (String s : list) {  
    if ("A".equals(s)) {  
        list.remove(s);  
    }else {  
        System.out.println(s);  
    }  
}

// 3、使用 iterator.remove() 方法
Iterator<String> iterator = list.iterator(); 
while (iterator.hasNext()) { 
	String s = iterator.next(); 
	if ("A".equals(s)) { 
		iterator.remove(); 
	} 
}
```

对于普通 for 循环，可以一边遍历一边修改元素。

第二种情况则会抛出 `ConcurrentModificationException` 异常。通过查看第二种方式的字节码可知 for Each 底层采用迭代器方式进行遍历。

![](https://cdn.img.turingzy.cn/2025/20250821-2.webp)

为什么会抛出并发修改异常呢？看 ArrayList 中迭代器的实现就知道了。

![](https://cdn.img.turingzy.cn/2025/20250821.webp)

![](https://cdn.img.turingzy.cn/2025/20250821-1.webp)

![](https://cdn.img.turingzy.cn/2025/20250821-3.webp)

在调用 list 本身的 `remove()` 方法时会 `modCount++`，但没有 `expectedModCount = modCount`，导致在调用 `next()` 的过程中二者不一致抛出并发修改异常。

第三种情况的 `iterator.remove()` 源码如下：

![](https://cdn.img.turingzy.cn/2025/20250821-4.webp)

可以看到有 `expectedModCount = modCount` 语句。
### List 如何快速删除某个指定下标的元素？

- ArrayList：主要在删除后元素的移动，末尾元素删除最快。
- LinkedList：主要删除元素前的遍历，头尾元素删除最快
- copyOnWriteArrayList：主要取决于数组的复制速度，但在并发环境下，它的删除操作不会影响读操作，具有较好的并发性能。
### 把 ArrayList 变成线程安全有哪些方法？

- 使用 Collections 类的 synchronizedList 方法

```java
List<String> synchronizedList = Collections.synchronizedList(list);
```

- 使用 Vector 或者 CopyOnWriteArrayList 代替 ArrayList。
### ArrayList 的扩容机制？

1. 添加新元素时判断当前 `Obejct[] elementData` 的长度是否等于当前元素数量，
2. 一旦等于，就调用 `grow()` 方法扩容，反之则直接赋值。

`grow()` 方法源码如下：

```java
private Object[] grow() {  
    return grow(size + 1);  
}

private Object[] grow(int minCapacity) {  
	int oldCapacity = elementData.length;  
    if (oldCapacity > 0 || elementData != DEFAULTCAPACITY_EMPTY_ELEMENTDATA) {  
        int newCapacity = ArraysSupport.newLength(oldCapacity,  
                minCapacity - oldCapacity, /* minimum growth */  
                oldCapacity >> 1           /* preferred growth */);  
        return elementData = Arrays.copyOf(elementData, newCapacity);  
    } else {  
        return elementData = new Object[Math.max(DEFAULT_CAPACITY, minCapacity)];  
    }  
}
```

- 将当前容量扩张至 1.5 倍，然后将元素复制到新长度的数组中，
- 因为扩容操作涉及到数组内存的重新分配、复制等操作，所以当大量添加元素时性能也会因为**扩容**而降低，所以在初始化时应该设定一个合适的 capacity（默认 10）。
# Map

### HashMap 实现原理介绍一下？

JDK 1.7 版本之前，底层数据结构是数组+链表，通过哈希算法将 key 映射对应的 bucket，如果存在哈希冲突，则通过拉链法避免，但是因为链表的查询为 $O(n)$，所以一旦哈希冲突严重性能会下降很多。

![](https://cdn.img.turingzy.cn/2025/20250821-5.webp)

JDK 1.8 及以上版本中，HashMap 的底层数据结构是数组 + 链表/红黑树。当某个桶（bucket）中的链表长度达到树化阈值 `TREEIFY_THRESHOLD = 8` 且数组容量达到最小树化容量 `MIN_TREEIFY_CAPACITY = 64` 时，链表会转换为红黑树（一种自平衡二叉查找树），将查找时间复杂度从链表的 $O(n)$ 优化为 $O(log n)$，提升查询效率。

如果数组容量未达到 64，即使链表长度达到 8，也会优先触发扩容 `resize()` 而非树化，以减少哈希冲突的发生概率。当红黑树节点数减少到去树化阈值 `UNTREEIFY_THRESHOLD = 6` 或以下时，红黑树会转换回链表。此时，由于节点数较少，链表和红黑树的查询性能差距不大，而链表的维护开销更低。

![](https://cdn.img.turingzy.cn/2025/20250821-6.webp)

### 介绍一下 HashMap 的扩容机制

HashMap 默认的负载因子是0.75，即如果hashmap中的元素个数超过了总容量75%，则会触发扩容，扩容分为两个步骤：

1. 对哈希表长度的扩大 2 倍（原因如下）
2. 将旧哈希表中的数据放到新的哈希表中
### HashMap 的长度为什么是 2 的幂次方？

>取余操作中如果除数是 2 的幂次则等价于与其除数减一的与操作。

1. 对于 Key 在数组中存储位置的计算，相比于 %，二进制位操作 & 能够提高运算效率。
2. 可以让 HashMap 在扩容的时候 Key 分布更均匀，最好的情况是一半在新数组的前半部分，一半在新数组后半部分。
3. 扩容机制变得简单和高效。

举个例子：

```java
假设有一个元素的哈希值为 10101100

旧数组元素位置计算：
hash        = 10101100
length - 1  = 00000111
& --------------------
index       = 00000100  (4)

新数组元素位置计算：
hash        = 10101100
length - 1  = 00001111
& --------------------
index       = 00001100  (12)

看第四位（从右数）：
1.高位为 0：位置不变。
2.高位为 1：移动到新位置（原索引位置+原容量）。
```
# 参考

- [小林coding Java集合面试题](https://www.xiaolincoding.com/interview/collections.html)
- [JavaGuide Java集合常见面试题总结](https://javaguide.cn/java/collection/java-collection-questions-01.html)
- [HashMap 的 7 种遍历方式与性能分析！「修正篇」](https://mp.weixin.qq.com/s?__biz=MzkxOTcxNzIxOA==&mid=2247505580&idx=1&sn=1825ca5be126c2b650e201fb3fa8a3e6&source=41&poc_token=HAzMpWijgk6pP0lUaTjh0JnakPPZTGioCiDjELmL)
- [面试官问：List如何一边遍历，一边删除？](https://cloud.tencent.com/developer/article/1649315)