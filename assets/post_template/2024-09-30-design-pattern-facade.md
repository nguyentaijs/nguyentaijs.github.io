---
layout: post
title: Design pattern - Facade
categories: [ discussion, design_pattern, system_design ]
tags: [ discussion, design_pattern, system_design ]
date: 2024-10-04 22:00 +0700
---

TODO
COVER IMG

Chào mừng anh em trở lại với
series [Design Pattern thực chiến](https://viblo.asia/s/design-pattern-thuc-chien-0gdJzpPnVz5) trong lập trình hướng đối
tượng.
Trong lập trình hướng đối tượng, khi thiết kế các hệ thống phức tạp, một trong những thách thức lớn nhất là quản lý sự
phức tạp đó và giữ cho hệ thống dễ bảo trì, mở rộng. Facade Pattern (Mẫu Facade) là một trong những giải pháp thiết kế (
Design Patterns) giúp giải quyết vấn đề này. Nó giúp đơn giản hóa sự tương tác với hệ thống phức tạp bằng cách cung cấp
một giao diện đơn giản, từ đó làm cho mã dễ đọc và bảo trì hơn.

# I. Khái niệm và kiến trúc

## 1. Định nghĩa

Facade Pattern là một mẫu thiết kế thuộc nhóm Structural Patterns (các mẫu thiết kế cấu trúc). Nó cung cấp một giao
diện đơn giản và dễ sử dụng cho một hệ thống con phức tạp hoặc một nhóm các lớp có liên quan. Facade không xóa bỏ sự
phức tạp bên trong hệ thống, mà chỉ che giấu nó, làm cho người sử dụng chỉ cần tương tác với một lớp giao diện duy
nhất.

## 2. Vấn đề mà Facade giải quyết

Trong các hệ thống phần mềm lớn, một hệ thống con hoặc một tập hợp các lớp thường phức tạp với rất nhiều thành phần và
quy tắc nghiệp vụ. Điều này dẫn đến việc các lập trình viên phải tương tác với rất nhiều lớp, tạo ra sự rối loạn và khó
khăn trong việc bảo trì, nâng cấp. Facade Pattern giúp giảm thiểu vấn đề này bằng cách đưa ra một lớp giao diện chung để
tiếp xúc với hệ thống phức tạp đó. Nhờ vậy, các lập trình viên chỉ cần quan tâm đến một lớp duy nhất thay vì toàn bộ hệ
thống phức tạp bên trong.

## 3. Intent (Mục đích)

Trích từ cuốn **Design patterns Elements of Reusable Object-Oriented Software**
> TODO quote doc
>
>
> **GoF**

Mục đích chính của Facade Pattern là cung cấp một giao diện đơn giản cho một hệ thống phức tạp, giúp che giấu đi sự phức
tạp của các lớp bên trong hệ thống. Facade giúp cho việc sử dụng hệ thống trở nên dễ dàng hơn bằng cách:

Che giấu sự phức tạp của hệ thống con.
Giảm thiểu sự phụ thuộc giữa người dùng hệ thống và các thành phần phức tạp bên trong.
Tăng tính bảo trì và dễ mở rộng của hệ thống.

## 4. Structure (Cấu trúc)

TODO
STRUCTURE IMG

Cấu trúc của Facade Pattern thường gồm các thành phần sau:
Facade: Lớp này cung cấp giao diện đơn giản cho hệ thống con hoặc một nhóm các lớp có liên quan.
Subsystem Classes: Các lớp phức tạp bên trong hệ thống mà Facade tương tác. Các lớp này thực hiện các chức năng thực tế
nhưng người dùng không cần biết chi tiết của chúng.
TODO giải thích bằng tiếng Việt
TODO giải thích các thành phần

## 5. Ứng dụng

Facade Pattern thường được sử dụng trong các trường hợp sau:

Khi bạn có một hệ thống con phức tạp và muốn cung cấp một giao diện đơn giản hơn để người dùng sử dụng mà không cần quan
tâm đến chi tiết bên trong.
Trong các thư viện hoặc framework lớn, Facade được dùng để che giấu các phần phức tạp và chỉ cung cấp các API đơn giản
để sử dụng.
Khi cần làm giảm sự phụ thuộc giữa hệ thống con và các lớp khác trong ứng dụng.

## 6. Ưu điểm và nhược điểm

Ưu điểm:
Giảm sự phức tạp: Người dùng chỉ cần tương tác với Facade thay vì phải hiểu rõ cách hoạt động của từng lớp trong hệ
thống.
Tăng tính bảo trì: Khi có thay đổi trong hệ thống con, bạn chỉ cần thay đổi trong Facade mà không làm ảnh hưởng đến mã
sử dụng Facade.
Giảm sự phụ thuộc: Các lớp khác không cần biết chi tiết hệ thống bên trong, giúp giảm thiểu sự phụ thuộc chặt chẽ giữa
các thành phần.
Nhược điểm:
Che giấu quá nhiều: Trong một số trường hợp, Facade có thể che giấu quá nhiều chi tiết của hệ thống con, làm mất khả
năng kiểm soát của người dùng với hệ thống.
Thêm một lớp trừu tượng: Facade tạo thêm một lớp trừu tượng, điều này có thể làm tăng độ phức tạp tổng thể của hệ thống
trong một số tình huống.

## 7. Các pitfall (Cạm bẫy)

Quá phụ thuộc vào Facade: Việc sử dụng quá nhiều Facade có thể làm mất đi sự linh hoạt khi cần truy cập trực tiếp vào hệ
thống con.
Thiếu tính mở rộng: Nếu không thiết kế cẩn thận, Facade có thể làm giảm khả năng mở rộng của hệ thống vì mỗi khi cần
thêm chức năng mới, bạn có thể phải thay đổi giao diện của Facade.

# II. Các ứng dụng trong các thư viện lớn của Java

Trong Java, Facade Pattern được ứng dụng rộng rãi trong nhiều thư viện và framework. Một số ví dụ tiêu biểu bao gồm:

Java Database Connectivity (JDBC): JDBC cung cấp một giao diện đơn giản để làm việc với cơ sở dữ liệu, che giấu đi sự
phức tạp của các API liên quan đến kết nối, truy vấn và quản lý dữ liệu.
JavaServer Faces (JSF): JSF cung cấp một giao diện dễ sử dụng để xây dựng các ứng dụng web, che giấu đi sự phức tạp của
các chi tiết quản lý trạng thái, điều hướng, và hiển thị UI.
Spring Framework: Spring sử dụng Facade Pattern để cung cấp các API dễ sử dụng cho các chức năng phức tạp như AOP (
Aspect-Oriented Programming), Dependency Injection, và quản lý giao dịch (transaction management).

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

Full code anh em có thể tham khảo ở
đây: [design-pattern-made-easy/prototype](https://github.com/nguyentaijs/design-pattern-made-easy/tree/main/prototype)

# III. Lời kết

Giới thiệu
Trong lập trình hướng đối tượng, khi thiết kế các hệ thống phức tạp, một trong những thách thức lớn nhất là quản lý sự
phức tạp đó và giữ cho hệ thống dễ bảo trì, mở rộng. Facade Pattern (Mẫu Facade) là một trong những giải pháp thiết kế (
Design Patterns) giúp giải quyết vấn đề này. Nó giúp đơn giản hóa sự tương tác với hệ thống phức tạp bằng cách cung cấp
một giao diện đơn giản, từ đó làm cho mã dễ đọc và bảo trì hơn.

Định nghĩa
Facade Pattern là một mẫu thiết kế thuộc nhóm Structural Patterns (các mẫu thiết kế cấu trúc). Nó cung cấp một giao diện
đơn giản và dễ sử dụng cho một hệ thống con phức tạp hoặc một nhóm các lớp có liên quan. Facade không xóa bỏ sự phức tạp
bên trong hệ thống, mà chỉ che giấu nó, làm cho người sử dụng chỉ cần tương tác với một lớp giao diện duy nhất.

Vấn đề mà Facade giải quyết
Trong các hệ thống phần mềm lớn, một hệ thống con hoặc một tập hợp các lớp thường phức tạp với rất nhiều thành phần và
quy tắc nghiệp vụ. Điều này dẫn đến việc các lập trình viên phải tương tác với rất nhiều lớp, tạo ra sự rối loạn và khó
khăn trong việc bảo trì, nâng cấp. Facade Pattern giúp giảm thiểu vấn đề này bằng cách đưa ra một lớp giao diện chung để
tiếp xúc với hệ thống phức tạp đó. Nhờ vậy, các lập trình viên chỉ cần quan tâm đến một lớp duy nhất thay vì toàn bộ hệ
thống phức tạp bên trong.

Intent (Mục đích)
Mục đích chính của Facade Pattern là cung cấp một giao diện đơn giản cho một hệ thống phức tạp, giúp che giấu đi sự phức
tạp của các lớp bên trong hệ thống. Facade giúp cho việc sử dụng hệ thống trở nên dễ dàng hơn bằng cách:

Che giấu sự phức tạp của hệ thống con.
Giảm thiểu sự phụ thuộc giữa người dùng hệ thống và các thành phần phức tạp bên trong.
Tăng tính bảo trì và dễ mở rộng của hệ thống.
Structure (Cấu trúc)
Cấu trúc của Facade Pattern thường gồm các thành phần sau:

Facade: Lớp này cung cấp giao diện đơn giản cho hệ thống con hoặc một nhóm các lớp có liên quan.
Subsystem Classes: Các lớp phức tạp bên trong hệ thống mà Facade tương tác. Các lớp này thực hiện các chức năng thực tế
nhưng người dùng không cần biết chi tiết của chúng.
Dưới đây là một ví dụ đơn giản về cấu trúc của Facade Pattern:

java
Copy code
class SubsystemA {
public void operationA() {
System.out.println("Operation A");
}
}

class SubsystemB {
public void operationB() {
System.out.println("Operation B");
}
}

class Facade {
private SubsystemA subsystemA;
private SubsystemB subsystemB;

    public Facade() {
        subsystemA = new SubsystemA();
        subsystemB = new SubsystemB();
    }

    public void operation() {
        subsystemA.operationA();
        subsystemB.operationB();
    }

}
Ứng dụng
Facade Pattern thường được sử dụng trong các trường hợp sau:

Khi bạn có một hệ thống con phức tạp và muốn cung cấp một giao diện đơn giản hơn để người dùng sử dụng mà không cần quan
tâm đến chi tiết bên trong.
Trong các thư viện hoặc framework lớn, Facade được dùng để che giấu các phần phức tạp và chỉ cung cấp các API đơn giản
để sử dụng.
Khi cần làm giảm sự phụ thuộc giữa hệ thống con và các lớp khác trong ứng dụng.
Ưu điểm và nhược điểm
Ưu điểm:
Giảm sự phức tạp: Người dùng chỉ cần tương tác với Facade thay vì phải hiểu rõ cách hoạt động của từng lớp trong hệ
thống.
Tăng tính bảo trì: Khi có thay đổi trong hệ thống con, bạn chỉ cần thay đổi trong Facade mà không làm ảnh hưởng đến mã
sử dụng Facade.
Giảm sự phụ thuộc: Các lớp khác không cần biết chi tiết hệ thống bên trong, giúp giảm thiểu sự phụ thuộc chặt chẽ giữa
các thành phần.
Nhược điểm:
Che giấu quá nhiều: Trong một số trường hợp, Facade có thể che giấu quá nhiều chi tiết của hệ thống con, làm mất khả
năng kiểm soát của người dùng với hệ thống.
Thêm một lớp trừu tượng: Facade tạo thêm một lớp trừu tượng, điều này có thể làm tăng độ phức tạp tổng thể của hệ thống
trong một số tình huống.
Các pitfall (Cạm bẫy)
Quá phụ thuộc vào Facade: Việc sử dụng quá nhiều Facade có thể làm mất đi sự linh hoạt khi cần truy cập trực tiếp vào hệ
thống con.
Thiếu tính mở rộng: Nếu không thiết kế cẩn thận, Facade có thể làm giảm khả năng mở rộng của hệ thống vì mỗi khi cần
thêm chức năng mới, bạn có thể phải thay đổi giao diện của Facade.
Các ứng dụng trong các thư viện lớn của Java
Trong Java, Facade Pattern được ứng dụng rộng rãi trong nhiều thư viện và framework. Một số ví dụ tiêu biểu bao gồm:

Java Database Connectivity (JDBC): JDBC cung cấp một giao diện đơn giản để làm việc với cơ sở dữ liệu, che giấu đi sự
phức tạp của các API liên quan đến kết nối, truy vấn và quản lý dữ liệu.
JavaServer Faces (JSF): JSF cung cấp một giao diện dễ sử dụng để xây dựng các ứng dụng web, che giấu đi sự phức tạp của
các chi tiết quản lý trạng thái, điều hướng, và hiển thị UI.
Spring Framework: Spring sử dụng Facade Pattern để cung cấp các API dễ sử dụng cho các chức năng phức tạp như AOP (
Aspect-Oriented Programming), Dependency Injection, và quản lý giao dịch (transaction management).
Kết bài
Facade Pattern là một giải pháp thiết kế hữu ích để giảm sự phức tạp của các hệ thống phần mềm lớn. Bằng cách cung cấp
một giao diện đơn giản để tương tác với hệ thống con phức tạp, Facade giúp cải thiện khả năng bảo trì và mở rộng của hệ
thống. Tuy nhiên, cần sử dụng nó một cách cẩn thận để tránh che giấu quá nhiều chi tiết hoặc tạo thêm sự phức tạp không
cần thiết. Với việc ứng dụng rộng rãi trong các thư viện lớn như JDBC, JSF và Spring, Facade Pattern đã chứng minh giá
trị của mình trong việc giúp các lập trình viên làm việc với các hệ thống phức tạp một cách dễ dàng hơn.
Hẹn anh em ở các bài viết tiếp theo~

# IV. Tài liệu tham khảo

1. [Refactoring.guru](https://refactoring.guru/design-patterns)
2. Head first design pattern - O'Reilly
3. Design patterns Elements of Reusable Object-Oriented Software - GoF

-----

Nếu anh em cảm thấy bài viết hữu ích đừng ngần ngại click upvote cho bài viết, hoặc phát hiện ý nào chưa hợp lý hoặc cần
giải thích thêm hãy comment cho tôi biết để cùng trao đổi nhé.
Anh em có thể tham khảo các bài viết khác của tôi tại [Blog cái nhân](https://nguyentaijs.github.io/) hoặc kết nối với
tôi qua [Linkedin](https://www.linkedin.com/in/nguyentaijs)
