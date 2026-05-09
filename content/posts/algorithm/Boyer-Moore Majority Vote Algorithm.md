---
title: Boyer-Moore Majority Vote Algorithm
slug: boyer–moore-majority-vote-algorithm
categories: 算法
tags:
  - leetcode
date: 2026-05-08
---
## 引言

给定一个大小为 `n` 的数组 `nums` ，返回其中的多数元素。多数元素是指在数组中出现次数 **大于** `⌊ n/2 ⌋` 的元素（多数元素总是存在）。

- [LeetCode 169. 多数元素](https://leetcode-cn.com/problems/majority-element/)

遇到这类问题我们一般会如何求解呢？

于我而言，最直观的思路是先使用哈希表统计每个元素出现的频率，然后再遍历一遍找到最大值。这种方案虽然思路简单，但时间或空间复杂度往往难以同时达到最优，编码也相对繁琐。

在昨天写 [LeetCode 2780. 合法分割的最小下标](https://leetcode.cn/problems/minimum-index-of-a-valid-split/)时我遇到了同样的问题，写完后就在思考对于这种 “求多数” 有什么更好的算法吗？

经过 Web/AI Research 让我了解到[博耶-摩尔多数投票算法](https://en.wikipedia.org/wiki/Boyer%E2%80%93Moore_majority_vote_algorithm)，简称「多数投票法」，主要用于在 $O(n)$ 的时间复杂度和 $O(1)$ 的额外空间复杂度下，从一个序列中找到出现次数超过一半（即大于 $n/2$）的那个元素。

这通常是处理该类问题的最优解。

## 算法图解

```go
major, count := 0, 0
for _, v := range nums {
	if count == 0 {
		major = v
		count = 1
	} else if v == major {
		count++
	} else {
		count--
	}
}
```

以上是上述问题的实现，代码非常简洁，一个 for 循环加上两个变量即可。

该算法的核心思路是维护一个“候选人”然后进行“票数抵消”，如下图所示：

![](https://cdn.img.turingzy.cn/2026/20260509120947839.png)

多数投票法一定可以从一个序列中找到出现次数超过一半的那个元素。如果元素在序列中出现的次数最多等于 $n/2$，则结果不唯一。例如：

```go
nums = {1,1,2,2} // major = 1
```

所以需要强调该算法解决的是“绝对多数”（Majority）问题，而不是广义上的“众数”。

## 扩展

如果要求出一个序列中出现次数超过 $n/k$ 的元素，又该如何做呢？

这就涉及到了博耶-摩尔算法的一个非常著名的推广 —— [Misra-Gries Algorithm](https://en.wikipedia.org/wiki/Misra%E2%80%93Gries_summary)。

当想要找出所有出现次数超过 $n/k$ 的元素时，核心逻辑依然是“抵消”，只是此时需要**同时维护多个候选人**。以 `k = 3` 举例：

```go
func misraGriesK3(nums []int) []int {
	// 1. 初始化 k-1 个候选人和计数器
    cand1, count1 := 0, 0
    cand2, count2 := 0, 0

    // 第一阶段：寻找候选人（扫描一遍数组）
    for _, v := range nums {
        // 必须先判断是否匹配已有候选人，避免 cand1 和 cand2 重复
        if v == cand1 && count1 > 0 {
            count1++
        } else if v == cand2 && count2 > 0 {
            count2++
        } else if count1 == 0 {
            cand1, count1 = v, 1
        } else if count2 == 0 {
            cand2, count2 = v, 1
        } else {
            // 遇到与当前所有候选人都不符的第三种元素
            // 执行集体抵消：所有当前候选人的计数器同时减 1
            count1--
            count2--
        }
    }

    // 第二阶段：验证（Misra-Gries 产生的只是潜在候选项，必须验证）
    results := make([]int, 0)
    verifyCount1, verifyCount2 := 0, 0
    for _, v := range nums {
        if count1 > 0 && v == cand1 {
            verifyCount1++
        } else if count2 > 0 && v == cand2 {
            verifyCount2++
        }
    }

    n := len(nums)
    if verifyCount1 * 3 > n {
        results = append(results, cand1)
    }
    if verifyCount2 * 3 > n {
        results = append(results, cand2)
    }
    return results
}
```

- 维护 `k-1` 个候选人
- 验证候选项
- 集体抵消

在 Misra-Gries 算法中，**集体抵消**（所有计数器同时减 1）是关键。这意味着每进行一次抵消，实际上是从序列中“抹去”了 k 个**互不相同**的元素。由于总共只有 n 个元素，这种“k 项抵消”操作最多发生 $n/k$ 次，从而保证了出现次数大于 $n/k$ 的元素一定能留存到最后。