---
layout: post
title: Leetcode 165. Compare version number
categories: [Leetcode, Challenge]
tags: [leetcode, challenge, medium]
date: 2024-05-03 09:00 +0700
---

## Problem
[_Leet code problem 165_](https://leetcode.com/problems/compare-version-numbers/description)\
_Difficulty: Medium_

## Solution
1. Split each version into arrays of revisions using `string.split(\\.)`
  ```
    version1: 1.0.01 -> ["1", "0", "01"]
    version2: 1.0    -> ["1", "0"]
  ```
2. Make sure the 2 arrays has the same size, the one has smaller size has to fill remaining with `0s` logically
  ```
    version1: 1.0.01 -> ["1", "0", "01"] -> ["1", "0", "01"]
    version2: 1.0    -> ["1", "0"]       -> ["1", "0", "0"]
    Logically means v2.length = 2                       ^
    then consider index from 3 onward as 0
  ```
3. For each item in both extracted arrays, compare and return accordingly
  ```
    version1 > version2 ->  1
    version1 = version2 ->  0
    version1 < version2 -> -1
  ```

## Implementation
Code from [_github_](https://github.com/nguyentaijs/Leetcode/blob/main/src/L165_CompareVersionNumbers.java)

```java
public class L165_CompareVersionNumbers {
  public int compareVersion(String version1, String version2) {
    String[] v1 = version1.split("\\.");
    String[] v2 = version2.split("\\.");

    int length = Math.max(v1.length, v2.length);
    for (int i = 0; i < length; i++) {
      int v1Revision = i >= v1.length ? 0 : Integer.valueOf(v1[i]);
      int v2Revision = i >= v2.length ? 0 : Integer.valueOf(v2[i]);

      if (v1Revision > v2Revision) {
        return 1;
      } else if (v1Revision < v2Revision) {
        return -1;
      }
    }
    return 0;

  }
}
```
