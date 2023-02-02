---
layout: post
title: Leetcode 1047. Remove All Adjacent Duplicates In String (Loại bỏ tất cả các bản sao liền kề trong chuỗi)
categories: [Leetcode, Challenge]
tags: [leetcode, challenge]
date: 2023-02-02 08:07 +0700
---
[_Leet code problem 1047_](https://leetcode.com/problems/remove-all-adjacent-duplicates-in-string/)\
_Difficulty: Easy_
---
### Given
Chuỗi String chứa các ký tự tiếng Anh viết thường

### Expectation
Loại bỏ toàn bộ các cặp ký tự thoả mãn 2 yêu cầu sau
* Sắp xếp liền kề
* Giống nhau

Lặp lại cho đến khi không còn tồn tại cặp ký tự thoả mãn yêu cầu trên. Trả về chuỗi ký tự còn lại

### Solution

1. Đưa lần lượt các ký vào 1 `Stack`
2. Nếu ký tự cuối cùng của `Stack` trùng với ký tự sắp được đưa vào thì loại bỏ cặp ký tự này
3. Lặp lại cho đến hết chuỗi, các ký tự nằm trong `Stack` chính là chuỗi cần tìm

**Implementation**\
Code from [_github_](https://github.com/nguyentaijs/Leetcode/blob/main/src/L1047_RemoveDuplicated.java)
```java
public class L1047_RemoveDuplicated {
    public String removeDuplicates(String s) {
        Stack<Character> st = new Stack<>();
        for (int i = 0; i < s.length(); i++) {
            if (!st.isEmpty()) {
                if (st.peek() == s.charAt(i)) {
                    st.pop();
                } else {
                    st.push(s.charAt(i));
                }
            } else {
                st.push(s.charAt(i));
            }
        }
        StringBuilder ns = new StringBuilder("");
        while (!st.isEmpty()) {
            ns.append(st.pop());
        }
        return ns.reverse().toString();
    }
}
```




