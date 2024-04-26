---
layout: post
title: Leetcode 35. Search Insert position
categories: [Leetcode, Challenge]
tags: [leetcode, challenge, easy]
date: 2024-04-26 21:03 +0700
---
[_Leet code problem 35_](https://leetcode.com/problems/search-insert-position/description/)\
_Difficulty: Easy_
---
### Solution
- The question mentioned that the complexity must be `Olog(n)` and it's a search algorithm
- We can clearly see that one of the way to solve this is using `Binary search` with a little modification
- Instead of return `-1 (not found)` we return the `minIndex` as the position of the `target` if it exists in the `nums` array

**Implementation**\
Code from [_github_](https://github.com/nguyentaijs/Leetcode/blob/main/src/L35_SearchInsertPosition.java)
```java
public class L35_SearchInsertPosition {
    public int searchInsert(int[] nums, int target) {
        int min = 0;
        int max = nums.length - 1;
        while (max >= min) {
            int mid = min + (max - min) / 2;
            if (nums[mid] == target) {
                return mid;
            } else if (target < nums[mid]) {
                max = mid - 1;
            } else {
                min = mid + 1;
            }
        }
        return min;
    }
}
```




