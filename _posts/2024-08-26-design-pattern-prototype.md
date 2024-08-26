---
layout: post
title: Design pattern - Công nghệ nhân bản nhờ prototype
categories: [discussion, design_pattern, system_design]
tags: [discussion, design_pattern, system_design]
date: 2024-08-26 23:45 +0700
---

![prototype-cover-assets/img.png]({{ site.baseurl }}/assets/img/design-pattern-prototype/prototype-cover-img.png)

Chào mừng anh em trở lại với series [Design Pattern thực chiến](https://viblo.asia/s/design-pattern-thuc-chien-0gdJzpPnVz5) trong lập trình hướng đối tượng.
Trong bài viết lần này, chúng ta sẽ tiếp tục khám phá một trong những pattern cơ bản khác – **Prototype Pattern**.
Đây là một công cụ hữu ích giúp anh em tối ưu việc tạo ra các object mới mà không cần phải thiết lập lại toàn bộ từ đầu.
Hãy cùng nhau đi sâu vào khái niệm, cơ chế hoạt động, cũng như cách thức implement của Prototype Pattern trong Java, với những ví dụ cụ thể về Shallow Copy và Deep Copy nhé!

# I. Khái niệm và kiến trúc
Trích từ cuốn **Design patterns Elements of Reusable Object-Oriented Software**
> Specify the kinds of objects to create using a prototypical instance, and create new
objects by copying this prototype.
>
> **GoF**

Mục tiêu hướng tới của pattern này là tạo các đối tượng mới bằng cách `clone (copy)` một prototype đã được định nghĩa sẵn.

**Structure tổng quát**

![structure]({{ site.baseurl }}/assets/img/design-pattern-prototype/prototype-structure.png)

**Prototype**: định nghĩa interface để clone\
**ConcretePrototype**: implement phương thức `clone()` để clone chính nó\
**Client**: tạo instance mới bằng cách gọi phương thức `clone()`

# II. Shallow copy và deep copy
Shallow Copy và Deep Copy là hai phương pháp clone đối tượng thường được sử dụng trong Prototype pattern.

## 1. Shallow copy
**Khái niệm**\
Shallow copy tạo ra một đối tượng mới, nhưng không sao chép các đối tượng con mà chỉ sao chép các tham chiếu đến chúng.
Có nghĩa là instance mới và instance gốc sẽ cùng tham chiếu đến các object con.

**Cơ chế**\
Các thuộc tính có kiểu nguyên thủy (primitive types) được sao chép giá trị.
Các thuộc tính có kiểu tham chiếu (reference types) array, list, hoặc object thì chỉ sao chép tham chiếu, không sao chép dữ liệu thực sự của object con.

**Hệ quả**\
Thay đổi trong một object con sẽ được phản ánh ở cả clone instance và instance gốc vì cả hai đều tham chiếu đến cùng object con đó.
Điều này rất nguy hiểm vì các side effect này rất khó phát hiện, trong trường hợp set có chủ đích cũng sẽ tạo ra sự ràng buộc giữa instance mới được tạo và instance gốc.

## 2. Deep Copy

**Khái niệm**\
Deep copy tạo ra một instance mới và sao chép toàn bộ cấu trúc dữ liệu của instance gốc, bao gồm cả việc sao chép độc lập tất cả các object con.
Có nghĩa là clone instance hoàn toàn tách biệt khỏi instance gốc.

**Cơ chế**
Các thuộc tính có kiểu nguyên thủy được sao chép giá trị.
Các thuộc tính có kiểu tham chiếu được tạo ra một bản sao hoàn toàn mới dựa trên các thuộc tính của bản gốc.

**Hệ quả**
Phương pháp này tách biệt clone instance và instance gốc, tránh phát sinh side effect trong quá trình sử dụng. Tuy nhiên phương pháp này tiêu tốn tài nguyên hơn nhiều so với shallow copy.

## 3. So sánh
![compare]({{ site.baseurl }}/assets/img/design-pattern-prototype/compare-shallow-and-deep.png)

# III. Thực hành implement trong Java
## 1. Shallow copy

**Shape | Prototype**\
Chú ý rằng `Shape` có 2 method quan trọng\
- method `clone()` trả về một `Shape` instance
- constructor `Shape(Shape target)` với tham số đầu vào là một `Shape` instance khác, được sử dụng để copy các properties của `target`

```java
public abstract class Shape {
  int x;
  int y;

  public Shape() {
  }

  public Shape(Shape target) {
    if (Objects.nonNull(target)) {
      this.x = target.x;
      this.y = target.y;
    }
  }

  ...

  public abstract Shape clone();

}

```

**Circle | Concrete prototype**\
Chú ý `Circle` có property color (RGB color) là một mảng int 3 phần tử đại điện cho 3 màu sắc cơ bản. Property này được lưu dưới dạng tham chiếu, anh em cùng xem shallow copy sẽ handle `clone()` như nào nhé.
```java

public class Circle extends Shape {
  private int radius;
  private int[] color;

  public Circle() {
  }

  public Circle(Circle target) {
    super(target);
    if (Objects.nonNull(target)) {
      this.radius = target.radius;
      System.out.println("Shallow copy");
      this.color = target.color;
    }
  }

  @Override
  public Shape clone() {
    return new Circle(this);
  }

  @Override
  public boolean equals(Object o) {
    if (this == o) return true;
    if (o == null || getClass() != o.getClass()) return false;
    Circle circle = (Circle) o;
    return this.radius == circle.radius && Arrays.equals(this.color, circle.color);
  }
}
```

**App | Client**\
Ở ví dụ này, tôi tạo một `circle` instance gốc, sau đó nhân bản ra `cloneCircle`. Khi tôi thay đổi giá trị color `cloneCircle.color[0] = 200` giá trị `color` của `circle` gốc cũng bị thay đổi. Do đó `cloneCircle = circle`.\
Việc sử dụng shallow copy cho các class có properties dạng tham chiếu rất dễ gây ra bug trên production. Best practice để tận dụng tốc độ của shallow copy là sử dụng cho các class có properties dạng tham trị.

```java
public class App {
    public static void main(String[] args) {
        Circle circle = createCircle();
        Circle cloneCircle = (Circle) circle.clone();
        System.out.println(circle.equals(cloneCircle) ? "They are EQUAL" : "They are not EQUAL");

        // Change clone Circle color
        int[] color = cloneCircle.getColor();
        color[0] = 200;
        System.out.println("\nAfter update clone Circle's RGB color to [200,255,255] ...");
        System.out.printf("Original color = %s%n", Arrays.toString(circle.getColor()));
        System.out.printf("Clone color = %s%n", Arrays.toString(cloneCircle.getColor()));
        System.out.println(circle.equals(cloneCircle) ? "They are EQUAL" : "They are not EQUAL");
    }

    private static Circle createCircle() {
        Circle circle = new Circle();
        circle.setX(2);
        circle.setY(2);
        circle.setRadius(5);
        circle.setColor(new int[]{255, 255, 255});
        return circle;
    }
}

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

## 2. Deep copy
Chỉ có một chút khác biệt nho nhỏ trong method `clone()` của `Circle`.
Thay vì sử dụng toán tử `=`, tôi sử dụng method `clone()` có sẵn của array để implement deep copy cho thuộc tính tham chiếu này.
Các phần tử tham trị khác có thể giữ nguyên, bằng cách này ta đã loại bỏ được việc chia sẻ các thuôc tính tham chiếu chung giữa 2 instance.

```java
public Circle(Circle target) {
    super(target);
    if (Objects.nonNull(target)) {
        this.radius = target.radius;
        System.out.println("Deep copy");
        this.color = target.color.clone();
    }
}
```

Trên thực tế, việc implement deep copy phức tạp hơn nhiều vì một class có thể có nhiều thuộc tính tham chiếu, mỗi thuộc tính tham chiếu lại có thể có nhiều thuộc tính con khác.

**Output**
```
Deep copy
They are EQUAL

After update clone Circle's RGB color to [200,255,255] ...
Original color = [255, 255, 255]
Clone color = [200, 255, 255]
They are not EQUAL
```
Full code anh em có thể tham khảo ở đây: [design-pattern-made-easy/prototype-deep-copy](https://github.com/nguyentaijs/design-pattern-made-easy/tree/main/prototype-deep-copy)

# III. Lời kết
Vậy là chúng ta đã cùng nhau tìm hiểu về Prototype Pattern – một giải pháp đơn giản nhưng hiệu quả trong việc tạo các đối tượng mới từ một prototype sẵn có.
Qua việc phân tích kỹ thuật Shallow Copy và Deep Copy, hy vọng anh em đã nắm rõ hơn về cách thức hoạt động và những điểm cần lưu ý khi triển khai pattern này trong thực tế.
Mỗi phương pháp đều có ưu và nhược điểm riêng, vì vậy, việc chọn lựa tùy thuộc vào ngữ cảnh và yêu cầu cụ thể của dự án.
Hãy áp dụng một cách thông minh để tránh những lỗi tiềm ẩn và đảm bảo hiệu suất tối ưu cho ứng dụng của anh em.\
Hẹn anh em ở các bài viết tiếp theo~

# IV. Tài liệu tham khảo
1. [Refactoring.guru](https://refactoring.guru/design-patterns)
2. Head first design pattern - O'Reilly
3. Design patterns Elements of Reusable Object-Oriented Software - GoF

-----

Nếu anh em cảm thấy bài viết hữu ích đừng ngần ngại click upvote cho bài viết, hoặc phát hiện ý nào chưa hợp lý hoặc cần giải thích thêm hãy comment cho tôi biết để cùng trao đổi nhé.
Anh em có thể tham khảo các bài viết khác của tôi tại [Blog cái nhân](https://nguyentaijs.github.io/) hoặc kết nối với tôi qua [Linkedin](https://www.linkedin.com/in/nguyentaijs)
