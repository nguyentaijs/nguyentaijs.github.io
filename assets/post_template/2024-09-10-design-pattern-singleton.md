---
layout: post
title: Design pattern - Singleton
categories: [discussion, design_pattern, system_design]
tags: [discussion, design_pattern, system_design]
date: 2024-09-10 23:45 +0700
---

TODO
![cover](Cover image)

Chào mừng anh em trở lại với series [Design Pattern thực chiến](https://viblo.asia/s/design-pattern-thuc-chien-0gdJzpPnVz5) trong lập trình hướng đối tượng.
TODO intro
# I. Khái niệm và kiến trúc
## 1. Intent
Trích từ cuốn **Design patterns Elements of Reusable Object-Oriented Software**
> Ensure a class only has one instance, and provide a global point of access to it.
>
> **GoF**
>

**Nhóm**: Creational pattern

Pattern này có 2 mục tiêu chính
1. Đảm bảo 1 class chỉ có 1 instance duy nhất: ngăn chặn việc khởi tạo nhiều instance của một class
2. Cung cấp một điểm truy cập toàn cục (global access point): sử dụng một static method cho phép các component khác tùy ý access instance duy nhất này.

**Structure tổng quát**
TODO
![structure](Structure from doc)

Structure của Singleton rất đơn giản, chỉ có một class có tên là singleton
**Singleton**: class chứa ít nhất 3 thành phần chính
1. Private static instance `private static instance`
2. Private constructor `private Singleton()`
3. Public static method `getInstance()` cung cấp access point đến instance cho các component khác

## 2. Ứng dụng
Sử dụng khi cần quản lý tài nguyên hệ thống một cách nhất quán. Một ứng dụng khác là khởi tạo những instance tiêu tốn nhiều tài nguyên hệ thống mặc dù instance này vẫn có khả năng được tái sử dụng.\
Các ví dụ thực tế có thể kể đến bao gồm database connection, loggers, hoặc các config hệ thống phức tạp nhưng lại yêu cầu sự nhất quán trên toàn bộ hệ thống.

# II. Thực hành implement trong Java


TODO
**Shape | Prototype**\
Gỉai thích

```java
CODE java
```

**Shape | Prototype**\
Gỉai thích

```java
CODE java
```

**Output**
```
Shallow copy
They are EQUAL

After update clone Circle's RGB color to [200,255,255] ...
Original color = [200, 255, 255]
Clone color = [200, 255, 255]
They are EQUAL
```
Full code anh em có thể tham khảo ở đây: [design-pattern-made-easy/prototype](https://github.com/nguyentaijs/design-pattern-made-easy/tree/main/prototype)

# III. Lời kết
TODO

Hẹn anh em ở các bài viết tiếp theo~

# IV. Tài liệu tham khảo
1. [Refactoring.guru](https://refactoring.guru/design-patterns)
2. Head first design pattern - O'Reilly
3. Design patterns Elements of Reusable Object-Oriented Software - GoF

-----

Nếu anh em cảm thấy bài viết hữu ích đừng ngần ngại click upvote cho bài viết, hoặc phát hiện ý nào chưa hợp lý hoặc cần giải thích thêm hãy comment cho tôi biết để cùng trao đổi nhé.
Anh em có thể tham khảo các bài viết khác của tôi tại [Blog cái nhân](https://nguyentaijs.github.io/) hoặc kết nối với tôi qua [Linkedin](https://www.linkedin.com/in/nguyentaijs)
