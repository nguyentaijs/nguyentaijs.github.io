---
layout: post
title: [Design pattern] Distinguish structural patterns
categories: [Discussion, Design_pattern]
tags: [discussion, design_pattern]
date: 2024-04-26 11:03 +0700
published: false
---

## Đặt vấn đề
Trong cuốn Head First Design Pattern, chúng ta đã biết rằng các loại design pattern có thể được nhóm thành 3 loại chính:

- Creation design pattern
- Behavior design pattern
- Structural design pattern

Mỗi loại pattern cung cấp các giải pháp để xử lý các vấn đề cụ thể.
Trong đó, đặc biệt là structural design pattern có cách thức triển khai và định nghĩa có nhiều điểm tương đồng.


## Các giải pháp

### Time based random
Random dựa trên giá trị của `System.curentTimeMillis()` cho chuỗi số [stackoverflow](https://stackoverflow.com/a/18228151/7081611)/

Cách này cho phép tạo dữ liệu unique nhưng không đảm bảo tính bảo mật vì giá trị của key có thể dự đoán dễ dàng

```java
  private static final long LIMIT = 10000000000L;
  private static long last = 0;

  public static long getID() {
    // 10 digits.
    long id = System.currentTimeMillis() % LIMIT;
    if ( id <= last ) {
      id = (last + 1) % LIMIT;
    }
    return last = id;
  }
```

### Random theo giải thuật random của Java

