---
layout: post
title: Design pattern - Singleton
categories: [discussion, design_pattern, system_design]
tags: [discussion, design_pattern, system_design]
date: 2024-09-10 23:45 +0700
---

![cover.png]({{ site.baseurl }}/assets/img/design-pattern-singleton/cover.png)

Chào mừng anh em trở lại với series [Design Pattern thực chiến](https://viblo.asia/s/design-pattern-thuc-chien-0gdJzpPnVz5) trong lập trình hướng đối tượng.
**Singleton** là một trong những design pattern cơ bản và quan trọng nhất trong nhóm **Creational Pattern**.\
Mục tiêu chính của nó là đảm bảo một class chỉ có duy nhất một instance trong suốt vòng đời của chương trình và cung cấp một điểm truy cập toàn cục tới instance này.
Điều này rất hữu ích trong các tình huống đòi hỏi sự nhất quán về trạng thái của đối tượng hoặc tài nguyên, chẳng hạn như khi làm việc với database connection hay loggers.
Thông qua bài viết này, chúng ta sẽ cùng tìm hiểu khái niệm, kiến trúc và cách triển khai Singleton trong ngôn ngữ lập trình Java.

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
![structure]({{ site.baseurl }}/assets/img/design-pattern-singleton/structure.png)

Structure của Singleton rất đơn giản, chỉ có một class\
**Singleton**: class chứa ít nhất 3 thành phần chính
1. Private static instance `private static instance`
2. Private constructor `private Singleton()`
3. Public static method `getInstance()` cung cấp access point đến instance cho các component khác

## 2. Ứng dụng
Sử dụng khi cần quản lý tài nguyên hệ thống một cách nhất quán.
Các ví dụ thực tế có thể kể đến bao gồm database connection, loggers hoặc các config hệ thống phức tạp nhưng lại yêu cầu sự nhất quán trên toàn bộ hệ thống.

# II. Thực hành implement trong Java

## 1. Naive implementation
Đúng như định nghĩa trên
1. private static instance
2. private constructor
3. public static method `getInstance()`

**Singleton**
```java
public class NaiveSingleton {
  private static NaiveSingleton instance;
  private NaiveSingleton() {}

  public static NaiveSingleton getInstance() {
    if(Objects.isNull(instance)) {
      instance = new NaiveSingleton();
    }
    return instance;
  }
}
```

## 2. Thread-safe implementation
Vấn đề của implementation #1 là nó sẽ phát sinh vấn đề khi có nhiều thread cùng tham gia vào quá trình tạo instance.\
Hãy tưởng tượng trường hợp có 2 thread cùng gọi phương thức `getInstance()` và instance chưa được khởi tạo.\
Lúc này cả 2 sẽ cùng tạo ra 2 instance, phá vỡ quy tắc chỉ cho phép tạo 1 instance duy nhất.

Để giải quyết vấn đề trên có các cách sau

**Static initialization** - Khởi tạo instance ngay tại câu lệnh định nghĩa instance\
Điểm yếu của phương pháp này là không hỗ trợ lazy load
```java
private static Singleton instance = new Singleton();

```
**Synchronized method** - Khởi tạo instance ngay tại câu lệnh định nghĩa instance\
Phương pháp này gặp vấn đề về performance vì các thread sẽ phải chờ khóa được released mỗi khi gọi phương thức `getInstance()`
```java
public static synchronized Singleton getInstance() {
  if(Objects.isNull(instance)) {
    instance = new Singleton();
  }
  return instance;
}
```

**Double-checked locking**\
Phương pháp này vừa hỗ trợ lazy load và cũng cải thiện đáng kể hiệu suất
```java
public class ThreadSafeSingleton {
    private static volatile ThreadSafeSingleton instance;
    private ThreadSafeSingleton() {}

    /**
     * Get instance
     * Using double-checked locking mechanism
     * @return instance
     */
    public static ThreadSafeSingleton getInstance() {
        ThreadSafeSingleton result = instance;
        if(Objects.nonNull(result)) {
            return result;
        }
        synchronized (ThreadSafeSingleton.class) {
            if (Objects.isNull(instance)) {
                instance = new ThreadSafeSingleton();
            }
            return instance;
        }
    }
}
```

Full code anh em có thể tham khảo ở đây: [design-pattern-made-easy/singleton](https://github.com/nguyentaijs/design-pattern-made-easy/tree/main/singleton)

# III. Lời kết
Singleton là một pattern đơn giản nhưng mạnh mẽ, giúp tối ưu việc quản lý tài nguyên và đảm bảo tính nhất quán trong hệ thống.
Từ cách tiếp cận naive cho đến các giải pháp tối ưu hơn như Thread-safe Singleton và Double-checked locking, chúng ta có thể lựa chọn phương pháp phù hợp với yêu cầu cụ thể của từng ứng dụng.
Qua đó, việc sử dụng Singleton không chỉ cải thiện hiệu suất mà còn giúp chúng ta duy trì một kiến trúc rõ ràng và hiệu quả.
Hẹn anh em ở các bài viết tiếp theo~

# IV. Tài liệu tham khảo
1. [Refactoring.guru](https://refactoring.guru/design-patterns)
2. Head first design pattern - O'Reilly
3. Design patterns Elements of Reusable Object-Oriented Software - GoF

-----

Nếu anh em cảm thấy bài viết hữu ích đừng ngần ngại click upvote cho bài viết, hoặc phát hiện ý nào chưa hợp lý hoặc cần giải thích thêm hãy comment cho tôi biết để cùng trao đổi nhé.
Anh em có thể tham khảo các bài viết khác của tôi tại [Blog cái nhân](https://nguyentaijs.github.io/) hoặc kết nối với tôi qua [Linkedin](https://www.linkedin.com/in/nguyentaijs)
