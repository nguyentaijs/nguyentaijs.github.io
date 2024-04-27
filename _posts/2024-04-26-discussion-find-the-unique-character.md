---
layout: post
title: [Discussion] Các giải thuật random cơ bản (TODO)
categories: [Discussion]
tags: [discussion]
date: 2024-04-26 11:03 +0700
published: false
---

## Đặt vấn đề
Yêu cầu tạo ra 1 chuỗi ký tự theo nguyên tắc
> 5 ký tự đầu là ký tự alphabet [a-z|A-Z]\
> 10 ký tự sau là chữ số [0-9]\
> Luôn phải đảm bảo tính **duy nhất** của chuỗi
> Không thể dự đoán giá trị một cách dễ dàng

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

