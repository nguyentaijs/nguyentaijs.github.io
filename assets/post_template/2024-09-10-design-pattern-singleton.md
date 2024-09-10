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
>
>
TODO giải thích bằng tiếng Việt
Pattern này có 2 mục tiêu chính
1. Một class chỉ c


**Nhóm**: Creational pattern

**Structure tổng quát**

TODO
![structure](Structure from doc)

TODO giải thích các thành phần
**Prototype**: định nghĩa interface để clone\
**ConcretePrototype**: implement phương thức `clone()` để clone chính nó\
**Client**: tạo instance mới bằng cách gọi phương thức `clone()`

# II. TODO nội dung

# III. Thực hành implement trong Java

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
