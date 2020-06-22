---
title: "1481. Least Number of Unique Integers after K Removals-K清除后的不同元素的最小个数"
date: 2020-06-12T18:41:18+08:00
draft: false
tags: ["LeetCode", "HashMap"]
categories: ["LeetCode", "HashMap"]

autoCollapseToc: true
---

## 问题描述
>Given an array of integers `arr` and an integer `k`. Find the least number of unique integers after removing **exactly** `k` elements.
>
>>**Example:**
>>
>>**Input:** arr = [5,5,4], k = 1
>>
>>**Output:** 1

## 解题思路
这道题目求解在数组中去除`k`个元素后所剩余的**不同元素**的最小值。解题思路非常明显，将数组中的元素与出现的频数形成一个map，然后将频数从小到大进行排序，遍历频数的列表，当`k = 0`时停止，这里使用一个`remove`的变量来存储当前数组中已经被去除的元素的个数。

## 代码实现
```java
class Solution {
    public int findLeastNumOfUniqueInts(int[] arr, int k) {
        Map<Integer, Integer> hMap = new HashMap<Integer, Integer>();
        for (int i : arr) {
            hMap.put(i, hMap.getOrDefault(i, 0)+1);
        }
        List<Integer> list = new ArrayList<Integer>(hMap.values());
        Collections.sort(list);

        int remove = 0;
        for (int i = 0; i < list.size(); i++) {
            if (k == 0) {
                break;
            }
            if (k >= list.get(i)) {
                k -= list.get(i);
                remove++;
            } else {
                k = 0;
            }
        }
        return (list.size()-remove);
    }
}
```

首先，`getOrDefault(index, defaultValue)`让我简化了代码；然后，使用`ArrayList`来存储map中的value，这样排序就很简单。最后，借鉴了讨论中的方法，通过remove变量来记录数组中被清除的元素（本来用`set()`方法对列表中的元素进行了归零，重新遍历列表得到非0的元素个数，好蠢 :( ）。
