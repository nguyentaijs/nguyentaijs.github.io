---
layout: post
title: Leetcode 21. Merge 2 sorted lists (Merge 2 linked list đã được sắp xếp)
categories: [Leetcode, Challenge]
tags: [leetcode, challenge, easy]
date: 2023-01-18 08:07 +0700
---
[_Leet code problem 21_](https://leetcode.com/problems/merge-two-sorted-lists/)\
_Difficulty: Easy_
---
## Given
2 linked list đã được sắp xếp `list1` & `list2`

## Expectation
Merge thành 1 list mới được sắp xếp từ `list1` & `list2`\
Return Head của merged list

![Image](https://assets.leetcode.com/uploads/2020/10/03/merge_ex1.jpg)

## Solution

**Chú thích**\
`resultList`: Linked list kết quả\
`p1`: node hiện tại của `list1`\
`p2`: node hiện tại của `list2`

### Iterative approach

1. So sánh giá trị của `p1` và `p2`
    - Thêm vào `resultList` phần tử có giá trị nhỏ hơn
    - Dịch chuyển `pointer` của list có giá trị nhỏ hơn 1 đơn vị
2. Nếu `p1 != null` hoặc `p2 != null`, lặp lại bước 1. Ngược lại, tiếp tục với bước 3
3. Thêm phần tử còn lại vào `resultList`
4. Trả về `node` đầu tiên của `resultList`

**Implementation**\
Code from [_github_](https://github.com/nguyentaijs/Leetcode/blob/main/src/L21_MergeTwoSortedList.java)

```java
public class L21_MergeTwoSortedList {
    public ListNode mergeTwoLists(ListNode l1, ListNode l2) {
        ListNode preHead = new ListNode(-1);
        ListNode curList = preHead;
        while (l1 != null && l2 != null) {
            if (l1.val < l2.val) {
                curList.next = l1;
                l1 = l1.next;
            } else {
                curList.next = l2;
                l2 = l2.next;
            }
            curList = curList.next;
        }
        curList.next = l1 == null ? l2 : l1;
        return preHead.next;
    }
}
```



