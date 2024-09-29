---
layout: post
title: Design pattern - Top Gun - Maverick, thinking strategy và adapter pattern
categories: [discussion, design_pattern, system_design]
tags: [discussion, design_pattern, system_design]
date: 2024-09-26 23:45 +0700
---

![cover-img.png]({{ site.baseurl }}/assets/img/design-pattern-adapter/cover-img.png)

Top Gun: Maverick là một bộ phim rất thành công do Tom Cruise thủ vai chính (Maverick).
Có một sự thật mà ít người biết đến là bộ phim lấy bối cảnh của một sự kiện có thật và đặc biệt hơn còn liên quan mật thiết đến chiến tranh Việt Nam.

Năm 1969, sau khi đắc cử tổng thống không lâu, Nixon tuyên bố chiến dịch **Việt Nam hóa chiến tranh**, bước đầu là không kích vào các cứ điểm của quân ta nhưng bất thành.\
Mặc dù Việt Nam còn hạn chế về công nghệ và kỹ năng chiến đấu so với Mỹ, nhưng những chiến thuật cứng nhắc và thiếu tính thích ứng với đặc điểm của Việt Nam đã khiến họ dễ dàng bị quân ta khai thác.
Nhận thấy sự cần thiết phải thay đổi cách tiếp cận, Hải quân Mỹ đã thành lập Top Gun nhằm cải thiện kỹ năng chiến đấu không chiến, đặc biệt tập trung vào việc phát triển tư duy linh hoạt và chiến thuật ứng biến trong những tình huống phức tạp.

Chương trình Top Gun của Hải quân Hoa Kỳ, chính thức được biết đến với tên gọi The United States Navy Strike Fighter Tactics Instructor, là một chương trình huấn luyện đặc biệt dành cho các phi công và sĩ quan hàng không.\
Mục tiêu của chương trình là dạy cho họ các chiến thuật và kỹ thuật không chiến, giúp họ trở thành những người hướng dẫn chiến đấu cho các đơn vị hoạt động của mình.
Thay vì chỉ tuân thủ các quy tắc cứng nhắc, Top Gun khuyến khích áp dụng tư duy linh hoạt (adaptive thinking) trong chiến đấu để phản ứng nhanh nhạy và hiệu quả trước những tình huống bất ngờ.

Tư duy linh hoạt không chỉ có ý nghĩa trong quân sự, mà còn là một kỹ năng cần thiết trong đời sống hàng ngày, giúp anh em thích nghi với những thay đổi và thách thức một cách hiệu quả.\
Ví dụ, trong công việc, khi một dự án gặp sự cố hoặc bị thay đổi yêu cầu, tư duy linh hoạt giúp bạn nhanh chóng điều chỉnh chiến lược để hoàn thành nhiệm vụ.
Thay vì bám vào kế hoạch ban đầu, bạn sẽ tìm cách thích nghi, phân tích tình hình và đưa ra giải pháp mới phù hợp hơn. (Nghe quen quen, có phải là **Agile** không nhỉ)

Trong lĩnh vực lập trình, adaptive thinking cũng được ứng dụng để giải quyết các vấn đề phức tạp, bằng cách tìm ra các phương án thay đổi linh hoạt dựa trên các tình huống cụ thể.\
Điều này thể hiện rõ trong **Adapter pattern**, một mô hình giúp hệ thống thích nghi với nhiều tình huống khác nhau mà không cần phải thay đổi cấu trúc tổng thể.
Anh em hãy cùng tôi tìm hiểu các ứng dụng của pattern này và phương pháp implement trong Java nhé.

# II. Khái niệm và kiến trúc
## 1. Intent
Trích từ cuốn **Design patterns Elements of Reusable Object-Oriented Software**
> Convert the interface of a class into another interface clients expect. Adapter lets classes
work together that couldn't otherwise because of incompatible interfaces.\
> **GoF**

Đơn giản thôi, adapter biến interface của một class thành một interface khác tương thích với client. Nói cách khác, adapter là lớp trung gian giải quyết vấn đề bất tương thích giữa các interface.

**Nhóm**: Structural pattern

## 2. Structure tổng quát
Adapter có 2 cách tiếp cận\
**Class adapter** ứng dụng đa kế thừa (multiple inheritance), `Adapter` kế thừa cả `client` và `Adaptee`\
**Object adapter** ứng dụng composition, biến `Adaptee` thành một thành phần của `Adapter`

### Object Adapter
![structure-object-adapter]({{ site.baseurl }}/assets/img/design-pattern-adapter/structure-object-adapter-pattern.png)

### Class Adapter
![structure-class-adapter]({{ site.baseurl }}/assets/img/design-pattern-adapter/structure-class-adapter-pattern.png)

# II. Ứng dụng
Sử dụng pattern này sẽ đảm bảo được nguyên tắc S&O trong SOLID

**Single Responsibility Principle**\
Tách biệt interface hoặc code convert data ra khỏi logic nghiệp vụ chính của hệ thống.

**Open/Closed Principle**\
Anh em có thể thêm các loại adapter mới vào hệ thống mà không phải sửa codebase hiện tại.
Điều này giúp giảm thiểu các bug tiềm ẩn vì code của hệ thống hiện tại không bị thay đổi mà được mở rộng.

Dưới đây là một vài ứng dụng

## 1. Tích hợp hệ thống cũ sang hệ thống mới
Hệ thống hiện tại sử dụng các API cũ và muốn tích hợp với một hệ thống mới mà không muốn hoặc không thể thay đổi nhiều code base,
Adapter Pattern giúp các class cũ tương thích với yêu cầu của hệ thống mới mà không cần phải thay đổi class đó.

## 2. Tái sử dụng các thành phần có sẵn
Adapter Pattern giúp tái sử dụng các thành phần hoặc class đã có sẵn mà không cần phải sửa code.
Điều này rất hữu ích trong các hệ thống lớn, phức tạp, khi việc sửa đổi có thể làm ảnh hưởng đến những phần khác của hệ thống.

## 3. Integrate với các 3rd party khác nhau
Adapter có thể tích hợp các hệ thống với input/output khác nhau bằng cách chuyển đổi input/output tương ứng với từng hệ thống.

Ví dụ: Tùy thuộc vào lựa chọn của người dùng, hệ thống cần gửi thông báo qua các nền tảng SMS, e-mail, Telegram, hoặc thông báo qua ứng dụng.
Sử dụng một Adapter, hệ thống có thể xử lý việc tạo request và xử lý response từ các dịch vụ thông báo tương ứng.

# III. Thực hành implement trong Java
Dưới dây là một ví dụ khá thú vị tôi tìm được để triển khai adapter pattern.\
Rất thực tế, điện lưới nước ta là 220V, mục tiêu của chúng ta là tạo ra một Adapter chuyển nguồn 220V sang các dòng điện khác: 110V cho đồ Nhật bãi, 20V cho đèn led, 5v cho sạc điện thoại.

**Volt**\
Định nghĩa hiệu điện thế của thiết bị

```java
public class Volt {
  private int volts;
  public Volt(int volts) {
    this.volts = volts;
  }
  public int getVolts() {
    return volts;
  }
  public void setVolts(int volts) {
    this.volts = volts;
  }
}
```

**Socket**\
Ổ cắm nguồn 220V

```java
public class Socket {
  private Volt volt;
  public Socket() {
    this.volt = new Volt(220);
  }

  public Volt getVolt() {
    return volt;
  }
}
```

**SocketAdapter**\
Interface khai báo danh sách hiệu điện thế Adapter có thể hỗ trợ

```java
public interface SocketAdapter {
  Volt get110Volts();
  Volt get20Volts();
  Volt get5Volts();
}
```

**ObjectSocketAdapterImpl | Class Adapter**\
Thực hiện chuyển dòng 220V sang các dòng điện có hiệu điện thế thấp hơn sử dụng **Object adapter**.\
Khai báo field `Socket`, sau đó biến đồi dòng điện dựa trên `Socket` này

```java
public class ObjectSocketAdapterImpl implements SocketAdapter {
  private Socket socket;

  public ObjectSocketAdapterImpl(Socket socket) {this.socket = socket;}

  @Override
  public Volt get110Volts() {
    return convertVolt(socket.getVolt(), 2);
  }

  @Override
  public Volt get20Volts() {
    return convertVolt(socket.getVolt(), 11);
  }

  @Override
  public Volt get5Volts() {
    return convertVolt(socket.getVolt(), 44);
  }

  private Volt convertVolt(Volt v, int divisor) {
    return new Volt(v.getVolts() / divisor);
  }
}
```

**TV, JapaneseCooker, MobileCharger, LedLight**\
Các thiết bị với hiệu điện thế tương ứng là\
TV - 220V\
JapaneseCooker - 110V\
LedLight - 20V\
MobileCharger - 5V

```java
public class TV {
  private Volt volt;

  public TV(Volt volt) {
    this.volt = volt;
  }

  public boolean powerOn() {
    if (volt.getVolts() != 220) {
      System.err.println("Socket voltage must be 220V. TV is damaged!!!");
      return false;
    }
    System.out.println("Turning on TV with " + volt.getVolts() + " volts");
    return true;
  }
}

public class JapaneseCooker {
  private Volt volt;

  public JapaneseCooker(Volt volt) {
    this.volt = volt;
  }

  public boolean cook() {
    if (volt.getVolts() != 110) {
      System.err.println("Socket voltage must be 110V. Cooker is damaged!!!");
      return false;
    }
    System.out.println("Cooking with " + volt.getVolts() + " volts");
    return true;
  }
}

public class LedLight {
  private Volt volt;

  public LedLight(Volt volt) {
    this.volt = volt;
  }

  public boolean turnOn() {
    if (volt.getVolts() != 20) {
      System.err.println("Socket voltage must be 20V. Light is damaged!!!");
      return false;
    }
    System.out.println("Turning on Led Light with " + volt.getVolts() + " volts");
    return true;
  }
}

public class MobileCharger {
  private Volt volt;

  public MobileCharger(Volt volt) {
    this.volt = volt;
  }

  public boolean charge() {
    if (volt.getVolts() != 5) {
      System.err.println("Socket voltage must be 5V. Charger is damaged!!!");
      return false;
    }
    System.out.println("Charging with " + volt.getVolts() + " volts");
    return true;
  }
}
```

**Test | 200V cho toàn bộ thiết bị**\
Chuyện gì xảy ra nếu ta sử dụng chung 1 nguồn 220V cho toàn bộ các thiết bị
```java
public class App {
    public static void main( String[] args ) {
        Socket socket = new Socket();
        TV tv = new TV(socket);
        JapaneseCooker cooker = new JapaneseCooker(socket);
        LedLight ledLight = new LedLight(socket);
        MobileCharger charger = new MobileCharger(socket);
        // Sử dụng default Socket - 220V
        turnOnAllDevices(tv, cooker, ledLight, charger);
    }

    private static void turnOnAllDevices(TV tv, JapaneseCooker cooker, LedLight ledLight, MobileCharger charger) {
        System.out.println("Turning on all devices...");
        int damagedDevices = 0;
        damagedDevices += tv.powerOn() ? 0 : 1;
        damagedDevices += cooker.cook()? 0 : 1;
        damagedDevices += ledLight.turnOn()? 0 : 1;
        damagedDevices += charger.charge()? 0 : 1;

        System.out.println("---");
        System.out.println("Total damaged devices:" + damagedDevices);
    }
}
```

**Output**\
Vâng, 3 thiết bị khét lẹt!
```
Turning on all devices...
Turning on TV with Volt: 220V volts
---
Total damaged devices:3
Socket voltage must be 110V. Cooker is damaged!!!
Socket voltage must be 12V. Light is damaged!!!
Socket voltage must be 5V. Charger is damaged!!!
```

**Test again | Sử dụng đúng dòng điện thông qua adapter**
```java
public class App {
    public static void main( String[] args ) {
      Socket socket = new Socket();
      ObjectSocketAdapterImpl adapter = new ObjectSocketAdapterImpl(socket);

      TV tv = new TV(socket.getVolt()); // TV - 220V
      JapaneseCooker cooker = new JapaneseCooker(adapter.get110Volts()); // JapaneseCooker - 110V
      LedLight ledLight = new LedLight(adapter.get20Volts()); // LedLight - 20V
      MobileCharger charger = new MobileCharger(adapter.get5Volts()); // MobileCharger - 5V

      turnOnAllDevices(tv, cooker, ledLight, charger);
    }

    private static void turnOnAllDevices(TV tv, JapaneseCooker cooker, LedLight ledLight, MobileCharger charger) {
        System.out.println("Turning on all devices...");
        int damagedDevices = 0;
        damagedDevices += tv.powerOn() ? 0 : 1;
        damagedDevices += cooker.cook()? 0 : 1;
        damagedDevices += ledLight.turnOn()? 0 : 1;
        damagedDevices += charger.charge()? 0 : 1;

        System.out.println("---");
        System.out.println("Total damaged devices:" + damagedDevices);
    }
}
```

**Output**\
Toàn bộ các thiết bị đã được sử dụng đúng hiệu điện thế
```
Turning on all devices...
Turning on TV with 220 volts
Cooking with 110 volts
Turning on Led Light with 20 volts
Charging with 5 volts
---
Total damaged devices:0
```

Ở ví dụ trên, `Object adapter` được sử dụng để chuyển đổi dòng điện 220V. Ngoài ra, ta cũng có thể sử dụng `Class adapter` để có được kết quả tương tự

**ClassSocketAdapterImpl | Class Adapter**\
Thực hiện chuyển dòng 220V sang các dòng điện có hiệu điện thế thấp hơn sử dụng **Class adapter**.\
`ClassSocketAdapterImpl` extends `Socket` và implements `SocketAdapter`

```java
public class ClassSocketAdapterImpl extends Socket implements SocketAdapter {

  @Override
  public Volt get120Volt() {
    return getVolt();
  }

  @Override
  public Volt get12Volt() {
    return convertVolt(getVolt(), 10);
  }

  @Override
  public Volt get3Volt() {
    return convertVolt(getVolt(), 40);
  }

  private Volt convertVolt(Volt v, int divisor) {
    return new Volt(v.getVolts() / divisor);
  }
}
```
Full code anh em có thể tham khảo ở đây: [design-pattern-made-easy/adapter](https://github.com/nguyentaijs/design-pattern-made-easy/tree/main/adapter/src/main/java/io/github/nguyentaijs)

# III. Lời kết
Adapter Pattern là một trong những design pattern cơ bản trong lập trình hướng đối tượng, giúp giải quyết vấn đề tương thích giữa các interface mà không làm thay đổi cấu trúc tổng thể của hệ thống.
Như đã thấy trong ví dụ về việc chuyển đổi dòng điện, Adapter giúp đảm bảo tính linh hoạt và mở rộng trong việc sử dụng lại các thành phần có sẵn, tích hợp với các hệ thống khác, hoặc làm việc với các nền tảng có yêu cầu khác nhau mà không cần phải thay đổi trực tiếp code hiện tại.

Với cách tiếp cận linh hoạt như này, anh em không chỉ tiết kiệm thời gian phát triển mà còn đảm bảo tính ổn định và khả năng bảo trì của hệ thống trong tương lai.\
Adapter Pattern không chỉ ứng dụng trong lập trình mà còn mang lại những bài học hữu ích về tư duy linh hoạt trong mọi lĩnh vực của cuộc sống, từ công việc, quản lý dự án đến việc đối mặt với những thách thức và thay đổi hàng ngày.

Hãy nhớ rằng, đôi khi không phải lúc nào cũng cần phải phá bỏ và xây dựng lại từ đầu, đôi khi chỉ cần một adapter nhỏ là đã có thể giải quyết vấn đề hiệu quả!
Hẹn anh em ở các bài viết tiếp theo~

# IV. Tài liệu tham khảo
1. [Refactoring.guru](https://refactoring.guru/design-patterns)
2. Head first design pattern - O'Reilly
3. Design patterns Elements of Reusable Object-Oriented Software - GoF

-----

Nếu anh em cảm thấy bài viết hữu ích đừng ngần ngại click upvote cho bài viết, hoặc phát hiện ý nào chưa hợp lý hoặc cần giải thích thêm hãy comment cho tôi biết để cùng trao đổi nhé.
Anh em có thể tham khảo các bài viết khác của tôi tại [Blog cái nhân](https://nguyentaijs.github.io/) hoặc kết nối với tôi qua [Linkedin](https://www.linkedin.com/in/nguyentaijs)
