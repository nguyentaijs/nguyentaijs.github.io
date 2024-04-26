---
layout: post
title: Leetcode 26. Remove Duplicates from Sorted Array
categories: [Leetcode, Challenge]
tags: [leetcode, challenge, easy]
date: 2024-04-25 18:08 +0700
---
[_Leet code problem 26_](https://leetcode.com/problems/remove-duplicates-from-sorted-array/description/)\
_Difficulty: Easy_
---
## Solution
- Use 2 variables `curIndex` & `uniqueCount`
- Iterate the whole `nums` array using `curIndex`. If there are any different between current and previous -> add it to the `uniqueCount` index

**Implementation**\
Code from [_github_](https://github.com/nguyentaijs/Leetcode/blob/main/src/L1047_RemoveDuplicated.java)
```java
public class L26_RemoveDuplicatesFromSortedArray {
    public int removeDuplicates(int[] nums) {
        int uniqueCount = 1;
        for (int curIndex = 1; curIndex < nums.length; curIndex++) {
            if (nums[curIndex] != nums[curIndex - 1]) {
                nums[uniqueCount] = nums[curIndex];
                uniqueCount++;
            }
        }
        return uniqueCount;
    }
}
```




