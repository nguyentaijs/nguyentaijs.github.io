---
layout: post
title: Leetcode 14. Longest Common Prefix (Tìm tiền tố dài nhất)
categories: [Leetcode, Challenge]
tags: [leetcode, challenge, easy]
date: 2023-01-17 20:30 +0700
---
[_Leet code problem 14_](https://leetcode.com/problems/longest-common-prefix/)\
_Difficulty: Easy_
---
### Given
Một mảng các chuỗi string. VD
```
["abc", "abcde", "abwtf"]
```

### Expectation
Tìm tiền tố dài nhất có thể có của các chuỗi trong mảng. Ở ví dụ trên, kế quả sẽ là `ab`

### Solution

Yêu cầu rất cơ bản đơn giản. Nếu sử dụng function `startWith()` & `substring()` được cung cấp bởi Java, cách giải quyết sẽ là

1. Lấy phần tử đầu tiên làm `result`
2. So sánh `result` này với phần tử tiếp theo
   * Nếu `result` không phải là tiền tố, cắt bớt 1 phần từ cuối cùng của `result`
   * Nếu `result` bị gọt hết thì kết thúc chương trình và trả về giá trị rỗng `""`
   * Nếu `result` là tièn tố của phần tử hiện tại, tiếp tục kiểm tra với phần tử tiếp theo trong mảng cho đến phần tử cuối cùng

**Implementation**\
Code from [_github_](https://github.com/nguyentaijs/Leetcode/blob/main/src/L14_LongestCommonPrefix.java)
```java
public class L14_LongestCommonPrefix {
    public String longestCommonPrefix(String[] strs) {
        if (strs.length == 0) {
            return "";
        }
        String result = strs[0];
        for (int i = 0; i < strs.length; i++) {
            while (!strs[i].startsWith(result)) {
                result = result.substring(0, result.length() - 1);
                if (result == ""){
                    return "";
                }
            }
        }
        return result;
    }
}
```




