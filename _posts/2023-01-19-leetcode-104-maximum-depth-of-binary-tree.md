---
layout: post
title: Leetcode 104. Maximum Depth of Binary Tree (Chiều cao tối đa của cây nhị phân)
categories: [Leetcode, Challenge]
tags: [leetcode, challenge, easy]
date: 2023-01-19 19:07 +0700
---
[_Leet code problem 104_](https://leetcode.com/problems/maximum-depth-of-binary-tree/)\
_Difficulty: Easy_
---
### Given
Cây nhị phân

### Expectation
Chiều cao tối đa của cây nhị phân này

### Solution

**Recursive**

Yêu cầu của bài toán có thể được hiểu là tìm số node tối đa đếm được trong một cây nhị phân bao gồm cả `root node`\
Đặc điểm của cây nhị phân là luôn có 2 node con tương ứng với mỗi node cha\
Quy luật này giúp ta thực hiện đếm số các node từ `root` đến `leaf` bằng phương pháp `Recursive`

1. Từ `root node` tìm node max giữa `node` bên trái và `node` bên phải
2. Lặp lại với `node` bên trái và `node` bên phải cho đến phần tử cuối cùng
3. Cộng thêm `1` vào kết quả trên để đếm cả `root node`

**Implementation**\
Code from [_github_](https://github.com/nguyentaijs/Leetcode/blob/main/src/L104_MaxDepthOfBinaryTree.java)
```java
public class L104_MaxDepthOfBinaryTree {
    public int maxDepth(TreeNode treeNode) {
        if (treeNode == null) {
            return 0;
        }
        return Math.max(maxDepth(treeNode.left), maxDepth(treeNode.right)) + 1;
    }
}
```




