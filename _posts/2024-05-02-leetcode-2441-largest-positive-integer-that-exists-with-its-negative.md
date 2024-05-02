---
layout: post
title: Leetcode 2114. Maximum Number of Words Found in Sentences
categories: [Leetcode, Challenge]
tags: [leetcode, challenge, easy]
date: 2024-05-03 05:00 +0700
---
[_Leet code problem 2441_](https://leetcode.com/problems/largest-positive-integer-that-exists-with-its-negative/description/)\
_Difficulty: Easy_
---
## Solution
**First approach**\
Utilize `HashMap`, iterate the whole array, for each item add it to the map. Each item should have one of
the following value
- NEGATIVE ONLY
- POSITIVE ONLY
- FOUND BOTH NEGATIVE AND POSITIVE

This approach is cumbersome with lost of `if else` blocks


**Second approach**\
Use `HashSet` instead\
The concept is to put every item in the array to a set of unique items\
After that find the max value among items that contains its counterpart


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


