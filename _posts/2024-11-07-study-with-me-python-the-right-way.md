---
layout: post
title: Python the right way - Prefer multiple assignment unpacking over indexing
categories: [ discussion, design_pattern, system_design ]
tags: [ discussion, design_pattern, system_design ]
date: 2024-11-07 23:00 +0700
---

Chào mừng anh em trở lại với
series [Design Pattern thực chiến](https://viblo.asia/s/design-pattern-thuc-chien-0gdJzpPnVz5) trong lập trình hướng đối
tượng.
Khi thiết kế các hệ thống phức tạp, một trong những thách thức lớn nhất là độ phức tạp và giữ cho hệ thống dễ bảo trì, mở rộng.
**Facade Pattern** là một trong những giải pháp giúp giải quyết vấn đề trên.
Nó giúp đơn giản hóa tương tác với hệ thống phức tạp bằng cách cung cấp một interface đơn giản,
từ đó làm cho code dễ đọc và bảo trì hơn. Tuy nhiên, việc lạm dụng **Facade pattern** cũng nảy sinh các vấn đề mới.\
Vậy sử dụng Facade sao cho đúng và đủ, anh em hãy cùng tôi tìm hiểu qua bài viết này

# I. Rule #5 - Prefer multiple assigment unpacking over indexing
## 1. Terminologies
**Tuple**: được định nghĩa là một chuỗi các phần tử không thay đổi (immutable) được bọc trong dấu ngoặc đơn. Có một số tính chất như sau
- Immutable: sau khi khởi tạo, không thể thêm, xóa, sửa phần tử trong tuple.
- Có thể chứa nhiều kiểu dữ liệu khác nhau.
- Truy suất nhanh hơn thông qua index nhờ tính immutable
- Hỗ trợ cơ chế unpacking

**Unpacking**: là quá trình gán giá trị của một iterable (tuple, list, string, dictionary, etc) vào nhiều biến cùng lúc.
## 2. Examples
::: columns
::: column
**Column 1**
```python
data = ("HaNoi", "DaNang", "SG")
hanoi = data[0]
danang = data[1]
sg = data[2]
```
:::
::: column
**Column 2**
```python
data = ("HaNoi", "DaNang", "SG")
hanoi, danang, sg = data
```
:::
:::

# IV. Lời kết
**Facade Pattern** là một giải pháp hữu ích để giảm sự phức tạp của các hệ thống lớn. Cung cấp
một giao diện đơn giản để tương tác với hệ thống con phức tạp, Facade giúp cải thiện khả năng bảo trì và mở rộng của hệ
thống. Tuy nhiên, cần sử dụng nó một cách cẩn thận để tránh tạo thêm sự phức tạp không cần thiết.

Được ứng dụng rộng rãi trong các thư viện lớn như JDBC, JSF và Spring, Facade Pattern đã chứng minh giá
trị của mình trong việc giúp dev làm việc với các hệ thống phức tạp một cách dễ dàng hơn.

Hẹn anh em ở các bài viết tiếp theo~

# V. Tài liệu tham khảo

1. [Refactoring.guru](https://refactoring.guru/design-patterns)
2. Head first design pattern - O'Reilly
3. Design patterns Elements of Reusable Object-Oriented Software - GoF

-----

Nếu anh em cảm thấy bài viết hữu ích đừng ngần ngại click upvote cho bài viết, hoặc phát hiện ý nào chưa hợp lý hoặc cần
giải thích thêm hãy comment cho tôi biết để cùng trao đổi nhé.
Anh em có thể tham khảo các bài viết khác của tôi tại [Blog cái nhân](https://nguyentaijs.github.io/) hoặc kết nối với
tôi qua [Linkedin](https://www.linkedin.com/in/nguyentaijs)
