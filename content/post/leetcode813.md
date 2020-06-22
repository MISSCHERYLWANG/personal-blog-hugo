---
title: "813. Largest Sum of Averages-最大的平均数之和"
date: 2020-06-10T17:41:18+08:00
draft: false
tags: ["LeetCode", "动态规划"]
categories: ["LeetCode"]

autoCollapseToc: true
---

## 问题描述
> We partition a row of numbers A into at most K adjacent (non-empty) groups, then our score is the sum of the average of each group. What is the largest score we can achieve?
>
> Note that our partition must use every number in A, and that scores are not necessarily integers.
>>**Example:**
>>
>>**Input:**
>>
>>A = [9,1,2,3,9]
>>
>>K = 3
>>
>>**Output:** 20

这道题目求解将一个数组最多分割为K个非空子数组后，求每个子数组的平均值之和的最大值。栗子中最佳选择是将数组分为`[9], [1,2,3], [9]`，结果为$9 / 1 + (1 + 2 + 3) / 3 + 9 / 1 = 20$。因此可以分析出将数组的分割次数从`0-(k-1)`所计算得到的最大的平均数之和，用`k`代表分割的次数，用`n`代表数组的大小。

## 解法
因为`K`的个数不确定，如果`K`的个数太大，我们就不能通过遍历`K`来确定`K`个子数组时的情况，所以不能采用暴力的方法去遍历数组。首先，设置一个数组`sum[i]`,其大小为`n+1`，来存储`A[0]-A[i]`的和。这样`i-j`的平均数为`(sum[j]-sum[i])/(j-i)`，便于得到平均数。

|index|  0  |  1  |  2  |  3  |  4  |  5  |
|:---:|:---:|:---:|:---:|:---:|:---:|:---:|
|  A  |  9  |  1  |  2  |  3  |  9  |
| sum |  0  |  9  |  10 |  12 | 15  | 24  |

参考官方题解，可以用动态规划解决。利用一个二维数组`dp[i][k]`，它代表分割`k`次下标范围为`i-n`的数组的最大的平均数之和。`dp[i][0]`为分割0次的最大值，如下所示。

|  index  |  0  |  1  |  2  |  3  |  4  |
|:---:|:---:|:---:|:---:|:---:|:---:|
|dp[i][0] | 4.8 |3.75 | 4.67| 6.0 | 9.0 |

### 举例分析
当`k=1`时，也就是将数组分为两部分，那么从0开始遍历数组来求`dp[i][1]`的值，然后内层循环利用`j`从`i+1`开始遍历数组得到最大值，即`dp[i][1]=max(dp[i][1], avg(dp[i,...,j])+dp[j+1][k-1])`。
从中可以发现，每次计算时只依赖于`k-1`次时的dp值，因此可以使用一个一维数组`dp[i]`，每个k值i从0开始遍历数组dp，从而更新。动态方程为`dp[i] = max(dp[i], avg(dp[i,...,j])+dp[j+1])`。

### 复杂度分析
每个k中，复杂度为$O(n^2)$，因此该算法的复杂度为$O(K*n^2)$。

