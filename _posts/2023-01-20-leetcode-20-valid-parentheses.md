---
layout: post
title: Leetcode 20. Valid Parentheses (Tìm cặp dấu ngoặc hợp lệ)
categories: [Leetcode, Challenge]
tags: [leetcode, challenge, easy]
date: 2023-01-20 21:02 +0700
---
[_Leet code problem 20_](https://leetcode.com/problems/valid-parentheses/)\
_Difficulty: Easy_
---
## Given
Một chuỗi chứa toàn bộ các ký tự
```
'[', ']', '(', ')', '{', '}'
```

## Expectation
Kiểm tra tính hợp lệ của chuỗi được test, chuỗi được xét là hợp lệ khi
* Mở ngoặc phải được đóng bởi dấu đóng ngoặc tương ứng
* Dấu ngoặc phải được đóng theo đúng thứ tự chúng được mở
* Mỗi dấu ngoặc đóng phải có một dấu mở ngoặc tương ứng

## Solution

Yêu cầu có thể giải quyết được bằng cách sử dụng một `stack` chứa toàn bộ các dấu đóng ngoặc.\
Kiểm tra tính hợp lệ bằng cách so sánh ký tự trên cùng của `stack` với ký tự tiếp theo được đưa vào `stack`\
Cụ thể như sau

* Duyệt toàn bộ các ký tự của chuỗi
* Nếu ký tự được duyệt là dấu mở ngoặc ```{ [ (```, `push` ký tự đóng ngoặc tương ứng vào `stack`
* Nếu ký tự được duyệt là dấu đóng ngoặc ```) ] }``` và `stack` không `empty` thì thực hiện so sánh ký tự này với ký tự cuối cùng được thêm vào `stack`
  * Nếu trùng thì `pop` ký tự này ra khỏi `stack`
  * Nếu không trùng, đồng nghĩa với việc chuỗi hiện tại `không hợp lệ`
* Nếu ký tự được duyệt là dấu đóng ngoặc ```) ] }``` và `stack` `empty` thì chuỗi hiện tại cũng `không hợp lệ` vì lúc này không có ký tự nào mở ngoặc tương ứng được thêm vào trước ký tự đóng ngoặc này
* Nếu duyệt hết toàn bộ ký tự của chuỗi và stack empty, xác nhận `chuỗi hợp lệ`

**Implementation**\
Code from [_github_](https://github.com/nguyentaijs/Leetcode/blob/main/src/L20_ValidParentheses.java)
```java
public class L20_ValidParentheses {
    public boolean isValid(String s) {
        Stack<Character> stack = new Stack<Character>();
        for (int i = 0; i < s.length(); i++) {
            char c = s.charAt(i);
            if (c == '{') {
                stack.push('}');
            } else if (c == '[') {
                stack.push(']');
            } else if (c == '(') {
                stack.push(')');
            } else if (stack.isEmpty() || stack.pop() != c) {
                return false;
            }
        }
        return stack.isEmpty();
    }
}
```




