---
layout: post
title: Distinguish structural patterns
categories: [discussion, design_pattern]
tags: [discussion, design_pattern]
date: 2024-04-26 11:03 +0700
hidden: true
---

# Đặt vấn đề
Trong cuốn Head First Design Pattern, chúng ta đã biết rằng các loại design pattern có thể được nhóm thành 3 loại chính:

- Creation design pattern
- Behavior design pattern
- Structural design pattern

Mỗi loại pattern cung cấp các giải pháp để xử lý các vấn đề cụ thể.\
Trong đó, đặc biệt là structural design pattern có cách thức triển khai và định nghĩa có nhiều điểm tương đồng.\
Tôi sẽ đi 1 vòng để làm rõ các điểm khác biệt của các pattern trong nhóm này


# Các structural patterns cơ bản
- Composite pattern
- Decorator pattern
- Adapter pattern
- Proxy pattern
- Bridge pattern
- Facade
- Fly weight

## Composite pattern
![Composite pattern](https://refactoring.guru/images/patterns/content/composite/composite.png)
_Image from refactoring.guru_
### Mục đích sử dụng
Xử lý các bài toán liên quan đến cấu trúc dạng cây mà ở đó các `node` hoặc `leaf` đều có các phương thức xử lý chung
### Ví dụ điển hình
**Folder structure**\
`Parent folder` có thể add/remove/get `child folder` và `leaf node` có thể là `folder` hoặc `file` bất kỳ\
Trên mỗi folder/file, người dùng có thể thao tác `open/edit/delete` và tính size của `node` hiện tại
- Nếu node hiện tại là `leaf` thì chỉ cần return thuộc tính size của `leaf` hiện tại
- Nếu node hiện tại là `root` thì phải thực hiện tính tổng size của các `node con` cho đến các `leaf node`

**Giao diện danh mục phim**\
Danh mục có cấu trúc từ `root` đến các `leaf`, `leaf` có thể là một tập phim, một series, một category con chứa các deal cho phim mới ra mắt
```
Phim hành động > Châu Âu > Peaky blinders phần 1 > Peaky binliders tập 1
Phim hành động > Các phim mới nhất của Cillian Murphy
```

### Structure
![Image]()
*Image from refactoring.guru*

![Composite structure](https://refactoring.guru/images/patterns/diagrams/composite/structure-en.png)
_Image from refactoring.guru_
