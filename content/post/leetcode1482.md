---
title: "1482. Minimum Number of Days to Make m Bouquets-制造M束花束需要的最少天数"
date: 2020-06-20T01:00:45+08:00
draft: false
tags: ["LeetCode", "Binary Search"]
categories: ["LeetCode", "二分搜索"]

autoCollapseToc: true
---

## 问题说明

>Given an integer array `bloomDay`, an integer `m` and an integer `k`.
>
>We need to make `m` bouquets. To make a bouquet, you need to use `k` adjacent flowers from the garden.
>
>The garden consists of `n` flowers, the `ith` flower will bloom in the bloomDay[i] and then can be used in **exactly one** bouquet.
>
>Return *the minimum number of days* you need to wait to be able to make `m` bouquets from the garden. If it is impossible to make `m` bouquets return **-1**.
>>**Example:**
>>
>>**Input:**
>>A = [1,10,2,9,3,8,4,7,5,6], m = 4, k = 2
>>
>>**Output:** 9

题目给定输入的数组和两个量。数组表示第`i`朵花在第`bloomDay[i]`天开放，m表示花束的个数，k表示每个花束需要的相邻的花的朵数。拿给的例子来说明，要求4个花束，每个花束由相邻两朵花一起。如下图所示。

![image](/images/lc1482-1.png)

可以看出，第6天开始出现了相邻两朵花为一个花束，直到第9天满足要求。那么最少的天数就为9。

## 解法

根据提示用二分搜索法。我们可以注意到天数是这里最重要的一个概念，如果从`bloomDay`这个数组中找到开放的天数的左右边界，从这个范围内二分搜索，如果当前天未达到我们的`m`个花束的要求，那么就从当前天往右边界搜索，反之在左边界-当前天范围内搜索。

还是看上面这个栗子。具体搜索过程如下图所示。

![image](/images/lc1482-2.png)

判断花束个数等于几的具体过程为：设变量`flowers`和花束个数`bouquets`。遍历数组，如果`bloomDay[i]`大于等于`mid`，`flowers`加1，此时如果`flowers`等于`k`，`bouquets`加1；否则，如果`bloomDay[i]`小于`mid`，`flowers`置为0。


## 复杂度

**时间复杂度：** O(Nlog(max(bloomDay[i])))

**空间复杂度：** O(1)