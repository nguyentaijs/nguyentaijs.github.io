---
layout: post
title: Design pattern - Builder chợ lớn
categories: [discussion, design_pattern, system_design]
tags: [discussion, design_pattern, system_design]
date: 2024-09-17 22:18 +0700
---

![cover-img]({{ site.baseurl }}/assets/img/design-pattern-builder/cover-img.png)

Chào mừng anh em trở lại với series [Design Pattern thực chiến](https://viblo.asia/s/design-pattern-thuc-chien-0gdJzpPnVz5) trong lập trình hướng đối tượng.
Khi đối tượng có nhiều tham số hoặc cần nhiều bước khởi tạo, sử dụng các phương thức khởi tạo (constructor) truyền thống thường gây ra nhiều bất cập, có thể nêu ra như khó đọc, khó mở rộng đến dễ mắc lỗi.

Builder Pattern xuất hiện như một giải pháp hữu hiệu, giúp chúng ta tách biệt quá trình xây dựng đối tượng khỏi cách biểu diễn cụ thể của nó, đồng thời tăng cường tính linh hoạt và dễ mở rộng trong việc tạo ra các đối tượng khác nhau nhưng có cùng một cách thức khởi tạo.

Bài viết này sẽ giúp anh em hiểu rõ khái niệm, cấu trúc, và cách triển khai Builder Pattern.
Thông qua các ví dụ của tôi, hy vọng anh em sẽ nắm rõ hơn về cách áp dụng pattern này một cách hiệu quả trong các dự án thực tế.

# I. Khái niệm và kiến trúc
## 1. Intent
Trích từ cuốn **Design patterns Elements of Reusable Object-Oriented Software**
> Separate the construction of a complex object from its representation so that the same
construction process can create different representations.
>
> **GoF**

Có thể rút ra 2 ý chính:

1. **Tách biệt** quá trình xây dựng một đối tượng phức tạp khỏi cách nó được thể hiện (representation), nghĩa là không bị ràng buộc bởi một cách biểu diễn cụ thể.
2. Quá trình xây dựng có thể tạo ra **các biểu diễn khác nhau của đối tượng**, nhờ vậy cùng một cách xây dựng có thể dùng để tạo nhiều loại đối tượng với cấu trúc khác nhau.

Nói cách khác, Builder Pattern cho phép linh hoạt trong việc tạo đối tượng mà không làm thay đổi quy trình tạo ra chúng.

**Structure tổng quát**
![structure]({{ site.baseurl }}/assets/img/design-pattern-builder/structure.png)

**Product**: là đối tượng phức tạp mà builder cần phải tạo ra. Thường constructor sẽ được set về `private`, đẩy trách nhiệm khởi tạo cho builder\
**Builder**: khai báo danh sách các phương thức để khởi tạo Product\
**ConcreteBuilder**: implement các phương thức đã được định nghĩa của **Builder**, và phương thức `GetResult()/build()` để trả về Product đã được tạo\
**Director**: thành phần optional, được dùng để mô tả cách Builder xây dựng Product. Cân nhắc sử dụng thành phần này khi quán trình khởi tạo instance chia thành nhiều bước.

## 2. Ứng dụng
### 2.1. Xây dựng đối tượng phức tạp có cần thực hiện nhiều bước hoặc có nhiều tham số
Khi đối tượng có nhiều tham số, việc sử dụng constructor thông thường dễ gây nhầm lẫn và khó hiểu.
Không có một tiêu chuẩn nào cụ thể cho số lượng param truyền vào trong 1 method nói chung và một constructor nói riêng,
nhưng best practice là giữ số param cho một method **nhỏ hơn 7** (theo tác giả Steve McConnell trong cuốn Code Complete).

Nhưng constructor của tôi cần nhiều hơn 7 params thì phải làm sao?\
Một trong những cách để giảm số params là tạo ra một object mới chứa tất cả các param có chung domain, hoặc sử dụng **builder pattern** để đơn giản hóa quá trình khởi tạo.
Chắc anh em đã từng thấy những method/constructor tương tự như đoạn code dưới đây (code thực tế của một người bạn trùng tên tôi vài năm trước :v)

```java
public Offer(OfferComponents offerComponents, String fileType, List<MultipartFile> listPoster,
		List<MultipartFile> subtitleFile, boolean useDrm, boolean useDrmOtt, boolean useChromecastDrm,
		boolean freeOtt, boolean useEncrypted, boolean useEncoded) {
}
```

Đoạn code trên khó maintain, khó đọc, mà lại có nguy cơ cao tạo ra bug vì sai sót trong lúc set giá trị của 1 trong 6 anh `boolean params` xếp liền nhau.

### 2.2. Telescoping constructor
Đây là một phương pháp xử lý các tham số không bắt buộc. Theo đó, một class có nhiều constructor với số lượng tham số khác nhau.
Mỗi constructor sẽ gọi đến constructor khác với số lượng tham số ít hơn, và dần dần "bổ sung" các tham số cần thiết.
Đây là một trong các cách phổ biến để xử lý việc cung cấp các giá trị mặc định cho các tham số không bắt buộc.

Tuy nhiên nhược điểm của phương pháp này là khi số lượng các tham số tăng lên quá nhiều khiến cho việc maintain khó hiểu hơn, mở rộng khó khăn hơn, đồng thời vẫn không thể giải quyết được vấn đề về số lượng tham số truyền vào.

```java
// 2 params, others default
public Offer(OfferComponents offerComponents, String fileType) {
}

// 4 params, others default
public Offer(OfferComponents offerComponents, String fileType, List<MultipartFile> listPoster,
             List<MultipartFile> subtitleFile) {
}

// 7 params, others default
public Offer(OfferComponents offerComponents, String fileType, List<MultipartFile> listPoster,
             List<MultipartFile> subtitleFile, boolean useDrm, boolean useDrmOtt, boolean useChromecastDrm) {
}

// 10 params
public Offer(OfferComponents offerComponents, String fileType, List<MultipartFile> listPoster,
             List<MultipartFile> subtitleFile, boolean useDrm, boolean useDrmOtt, boolean useChromecastDrm,
             boolean freeOtt, boolean useEncrypted, boolean useEncoded) {
}
```
### 2.3. Tách biệt xử lý khởi tạo đối tượng với logic của đối tượng
Cũng ở ví dụ trên, ta có thể ước tính số dòng code dành riêng cho việc định nghĩa constructor đã chiếm kha khá không gian của code base.
Điều này ảnh hưởng đến quá trình maintain theo thời gian khi code base càng ngày càng to lên, đặc biệt gây khó khăn cho người sau khi phần lớn các dòng code chỉ dùng để khai báo constructor.

Việc Sử dụng builder pattern sẽ giúp ta tách biệt quá trình khởi tạo đối tượng khỏi logic của chương trình. Giúp code dễ dàng bảo trì và mở rộng hơn.

### 2.4. Đảm bảo tính bất biến (immutable)
Ứng dụng này đặc biệt hữu dụng trong việc xử lý multi thread, một khi instance được khởi tạo thành công, các thuộc tính hay trạng thái của instance sẽ không thể bị thay đổi.
Để đạt được mục tiêu này, thường các thuộc tính sẽ được định nghĩa với từ khóa `final`, các `constructor` và phương thức `setter` sẽ được giới hạn về `private`. Dưới đây là một ví dụ:

```java
public class Car {
    private final String model;
    private final String color; // final để đảm bảo tính bất biến

    private Car(CarBuilder builder) {
        this.color = builder.color;
        this.model = builder.model;
    }

    public String getColor() {
        return color;
    }

    public String getModel() {
        return model;
    }
}
```

# III. Thực hành implement trong Java
Lấy ví dụ ứng dụng Builder pattern để xây dựng đối tượng `Car`.
## 1. Builder thuần dựa trên structure tổng quát

**Car | Product**\
Một chiếc ôtô cần có các thông tin cơ bản như hãng sản xuất, model, màu và năm sản xuất.
```java
public class Car {
  private String manufacturer;
  private String model;
  private String color;
  private int year;

  public Car(String manufacturer, String model, String color, int year) {
    this.manufacturer = manufacturer;
    this.model = model;
    this.color = color;
    this.year = year;
  }

  // Getters
  public String getManufacturer() { return manufacturer; }
  public String getModel() { return model; }
  public String getColor() { return color; }
  public int getYear() { return year; }

  @Override
  public String toString() {
    return "Car [manufacturer=" + manufacturer + ", model=" + model + ", color=" + color + ", year=" + year + "]";
  }
}
```

**Builder | Builder**\
Định nghĩa các phương thức để cấu thành nên đối tượng `Car`
```java
public interface Builder {
  void setManufacturer(String make);
  void setModel(String model);
  void setColor(String color);
  void setYear(int year);
}
```

**CarBuilder | Concrete builder**\
Thực hành thiết lập các thông số đối tượng `Car` dựa trên các phương thức được định nghĩa bởi `Builder interface` phía trên.\
Ngoài ra, CarBuilder còn có thêm phương thức `build()` để trả về sản phẩm `Car` sau khi đã được thiết lập các thông số.
```java
public class CarBuilder implements Builder {
  private String manufacturer;
  private String model;
  private String color;
  protected int year;

  @Override
  public void setManufacturer(String manufacturer) {
    this.manufacturer = manufacturer;
  }

  @Override
  public void setModel(String model) {
    this.model = model;
  }

  @Override
  public void setColor(String color) {
    this.color = color;
  }

  @Override
  public void setYear(int year) {
    this.year = year;
  }

  public Car build() {
    return new Car(manufacturer, model, color, year);
  }
}
```

**Director | Director**\
Để tạo các instance của xe **xForce 2024 và SantaFe 2025**, chúng ta sử dụng CarBuilder.\
Trong quá trình này, `Director` sẽ định nghĩa hai phương thức: `buildXForce` và `buildSantafe`.\
Mỗi phương thức sẽ chỉ định chi tiết các thông số cần thiết cho từng dòng xe.\
```java

public class Director {
  public void buildXForce(Builder builder) {
    System.out.println("Building X-Force...");
    builder.setManufacturer("Mitsubishi");
    builder.setColor("White");
    builder.setModel("xForce");
    builder.setYear(2024);
  }

  public void buildSantafe(Builder builder) {
    System.out.println("Building SantaFe...");
    builder.setManufacturer("Hyundai");
    builder.setColor("Blue");
    builder.setModel("SantaFe");
    builder.setYear(2025);
  }
}
```
**Main test**
```java
public class Main {
    public static void main(String[] args) {
        Director director = new Director();

        CarBuilder santaFeBuilder = new CarBuilder();
        director.buildSantafe(santaFeBuilder);

        Car santaFe = santaFeBuilder.build();
        System.out.println(santaFe);

        CarBuilder xForceBuilder = new CarBuilder();
        director.buildXForce(xForceBuilder);

        Car xForce = xForceBuilder.build();
        System.out.println(xForce);
    }
}

```

**Output**
```
Car [manufacturer=Hyundai, model=SantaFe, color=Blue, year=2025]
Building X-Force...
Car [manufacturer=Mitsubishi, model=xForce, color=White, year=2024]
```

Full code anh em có thể tham khảo ở đây: [design-pattern-made-easy/builder](https://github.com/nguyentaijs/design-pattern-made-easy/tree/main/builder)

## 2. Builder với inheritance và method chaining
Một số anh em có thể thắc mắc: "Sao nhìn vẫn không giống **Builder Pattern** nhỉ? Tôi dùng **Lombok**, chỉ cần gọi `builder()` rồi chấm vài cái là xong mà!".\
Vâng, lý do là anh em chưa đọc đến phần này thôi. Ở phần này, chúng ta sẽ cùng nhau modify lại một chút để đáp ứng hai yêu cầu:

1. Dự án cần bổ sung thêm xe điện VF3 của **Vinfast**. Xe điện thì cần thêm thuộc tính Dung lượng Pin (batteryCapacity).
2. Đồng thời, `Director` phải áp dụng `method chaining` để khởi tạo đối tượng trông cho nó chuyên nghiệp.

Các bước cần thực hiện như sau\
**1. Builder | Builder**\
`Builder` interface sử dụng **generic type Builder<T>**\
Các phương thức của `Builder` return `T` thay vì return `void`
```java
public interface Builder<T extends Builder<T>> {
    T setManufacturer(String make);
    T setModel(String model);
    T setColor(String color);
    T setYear(int year);
    Car build();
}
```

**2. Car | Parent product**\
Đưa `CarBuilder class` thành nested class bên trong `Car`, đồng thời constructor của `Car` set về `protected` để tránh việc khởi tạo trực tiếp thông qua constructor
```java
public class Car {
    protected String manufacturer;
    protected String model;
    protected String color;
    protected int year;

    protected Car(String manufacturer, String model, String color, int year) {
        this.manufacturer = manufacturer;
        this.model = model;
        this.color = color;
        this.year = year;
    }

    ...

    public static class CarBuilder implements Builder<CarBuilder> {
        private String manufacturer;
        private String model;
        private String color;
        protected int year;

        @Override
        public CarBuilder setManufacturer(String manufacturer) {
            this.manufacturer = manufacturer;
            return this;
        }

        @Override
        public CarBuilder setModel(String model) {
            this.model = model;
            return this;
        }

        @Override
        public CarBuilder setColor(String color) {
            this.color = color;
            return this;
        }

        @Override
        public CarBuilder setYear(int year) {
            this.year = year;
            return this;
        }

        public Car build() {
            return new Car(manufacturer, model, color, year);
        }
    }
}
```
**3. ElectricCar | Child product**\
Định nghĩa class `ElectricCar extends Car`, thêm thuộc tính `batteryCapcity`.\
Để ý constructor của `ElectricCar` được set về `private`, chỉ có thể khởi tạo thông qua `buidler`.\
Định nghĩa thêm `ElectricCarBuilder` bên trong `ElectricCar` để support khởi tạo instance thông qua `Builder`
```java
public class ElectricCar extends Car {
    private int batteryCapacity;

    private ElectricCar(String manufacturer, String model, String color, int year, int batteryCapacity) {
        super(manufacturer, model, color, year);
        this.batteryCapacity = batteryCapacity;
    }

    ...

    public static class ElectricCarBuilder implements Builder<ElectricCarBuilder> {
        private String manufacturer;
        private String model;
        private String color;
        private int year;
        private int batteryCapacity;

        @Override
        public ElectricCarBuilder setManufacturer(String manufacturer) {
            this.manufacturer = manufacturer;
            return this;
        }

        @Override
        public ElectricCarBuilder setModel(String model) {
            this.model = model;
            return this;
        }

        @Override
        public ElectricCarBuilder setColor(String color) {
            this.color = color;
            return this;
        }

        @Override
        public ElectricCarBuilder setYear(int year) {
            this.year = year;
            return this;
        }

        public ElectricCarBuilder setBatteryCapacity(int batteryCapacity) {
            this.batteryCapacity = batteryCapacity;
            return this;
        }

        public ElectricCar build() {
            return new ElectricCar(manufacturer, model, color, year, batteryCapacity);
        }
    }
}
```
**4. Director | Director**\
Thêm phương thức build xe cho bác Vượng. Đã có thể sử dụng **method chaining** để build đồ
```java
public class Director {
  public Car buildXForce() {}
  public Car buildSantafe() {}

  public ElectricCar buildlVF3() {
    System.out.println("Building VF3...");
    return new ElectricCar.ElectricCarBuilder()
      .setManufacturer("Vinfast")
      .setColor("Red")
      .setModel("VF3")
      .setYear(2025)
      .setBatteryCapacity(9000)
      .build();
  }
}
```
**5. Main test**
```java
public class Main {
    public static void main(String[] args) {
        Director director = new Director();

        Car santaFe = director.buildSantafe();
        System.out.println(santaFe);

        Car xForce = director.buildXForce();
        System.out.println(xForce);

        ElectricCar vf3 = director.buildlVF3();
        System.out.println(vf3);
    }
}
```

**Output**
```
Building SantaFe...
Car [manufacturer=Hyundai, model=SantaFe, color=Blue, year=2025]
Building X-Force...
Car [manufacturer=Mitsubishi, model=xForce, color=White, year=2024]
Building VF3...
Car [manufacturer=Vinfast, model=VF3, color=Red, year=2025], batteryCapacity=9000
```

Full code anh em có thể tham khảo ở đây: [design-pattern-made-easy/builder-inheritance](https://github.com/nguyentaijs/design-pattern-made-easy/tree/main/builder-inheritance)



# III. Lời kết
Qua bài viết này, tôi đã cùng anh em khám phá **Builder Pattern**, từ khái niệm, cấu trúc, đến các ứng dụng thực tiễn của nó.
Cần nắm rõ, **Builder Pattern** không chỉ giúp đơn giản hóa quá trình khởi tạo các đối tượng phức tạp mà còn mang lại sự linh hoạt trong việc mở rộng và bảo trì codebase sau này.
Hy vọng anh em có thể áp dụng những kiến thức này vào dự án của mình một cách hiệu quả.

Bài viết này cũng là bài viết cuối cùng về **Creational pattern** được định nghĩa bởi **GOF**.
Ở bài viết tiếp theo, anh em sẽ cùng tôi tìm hiểu một nhóm pattern mới thiên về cấu trúc **Behavioral pattern**.

Hẹn anh em ở các bài viết tiếp theo~

# IV. Tài liệu tham khảo
1. [Refactoring.guru](https://refactoring.guru/design-patterns)
2. Head first design pattern - O'Reilly
3. Design patterns Elements of Reusable Object-Oriented Software - GoF

-----

Nếu anh em cảm thấy bài viết hữu ích đừng ngần ngại click upvote cho bài viết, hoặc phát hiện ý nào chưa hợp lý hoặc cần giải thích thêm hãy comment cho tôi biết để cùng trao đổi nhé.
Anh em có thể tham khảo các bài viết khác của tôi tại [Blog cái nhân](https://nguyentaijs.github.io/) hoặc kết nối với tôi qua [Linkedin](https://www.linkedin.com/in/nguyentaijs)
