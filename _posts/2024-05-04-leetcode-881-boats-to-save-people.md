---
layout: post
title: Leetcode 881. Boats to save people
categories: [Leetcode, Challenge]
tags: [leetcode, challenge, medium]
date: 2024-05-03 05:00 +0700
---

## Problem
[_Leet code problem 881_](https://leetcode.com/problems/boats-to-save-people/description/)\
_Difficulty: Medium_
---
## Solution


**Implementation**\
Code from [_github_](https://github.com/nguyentaijs/Leetcode/blob/main/src/L2441_LargestPositiveIntegerThatExistsWithItsNegative.java)

**First approach** (HashMap)
```java
public class L2441_LargestPositiveIntegerThatExistsWithItsNegative {

  public static final int FOUND_BOTH_NEGATIVE_POSITIVE = 0;
  public static final int FOUND_NEGATIVE = -1;
  public static final int FOUND_POSITIVE = 1;

  public int findMaxK(int[] nums) {
    Map<Integer, Integer> resultMap = new HashMap();
    for (int i = 0; i < nums.length; i++) {
      if (nums[i] > 0) {
        if (!resultMap.containsKey(nums[i])) {
          resultMap.put(nums[i], FOUND_POSITIVE);
        } else if (resultMap.get(nums[i]) == FOUND_NEGATIVE) {
          resultMap.put(nums[i], FOUND_BOTH_NEGATIVE_POSITIVE);
        }
      } else {
        if (!resultMap.containsKey(-nums[i])) {
          resultMap.put(-nums[i], FOUND_NEGATIVE);
        } else if (resultMap.get(-nums[i]) == FOUND_POSITIVE) {
          resultMap.put(-nums[i], FOUND_BOTH_NEGATIVE_POSITIVE);
        }
      }
    }
    int max = -1;
    for (Map.Entry<Integer, Integer> entry : resultMap.entrySet()) {
      if (entry.getValue() == FOUND_BOTH_NEGATIVE_POSITIVE && max < entry.getKey()) {
        max = entry.getKey();
      }
    }
    return max;
  }
}
```

**Second approach** (HashSet)
```java
public class L2441_LargestPositiveIntegerThatExistsWithItsNegative {
  public int findMaxK(int[] nums) {
    Set<Integer> resultSet = new HashSet<>();
    for (int i = 0; i < nums.length; i++) {
      resultSet.add(nums[i]);
    }
    int max = -1;
    for (Integer item : resultSet) {
      if (resultSet.contains(-item) && max < Math.abs(item)) {
        max = Math.abs(item);
      }
    }
    return max;
  }
}
```


