---
layout: post
title: Layered architecture - kiến trúc quốc dân
categories: [discussion, system_design]
tags: [discussion, system_design]
date: 2024-08-11 22:22 +0700
---

# 0. Architecture partitioning
Trước khi đi vào chi tiết, anh em cần nắm rõ hai cách thực hiện architecture partitioning thường được sử dụng trong thiết kế hệ thống\
Architecture partitioning được hiểu là tiêu chí cơ bản để mô hình hóa kiến trúc hệ thống. Đơn giản ví dụ để hệ thống hóa cấp học trung học phổ thông ta có ít nhất 2 cách; đầu tiên có thể chia theo lớp (10, 11, 12), trong mỗi lớp có các môn Toán, Lí, Hóa, Văn, Sử, Địa; hoặc có thể chia theo môn học, Toán có Toán lớp 10, Toán lớp 11, Toán lớp 12, nhưng đều gọi chung là Toán THPT.\
Trong kiến trúc phần mềm, anh em cũng có ít nhất hai góc nhìn cơ bản là **Technical partitioning** và **Domain partitioning**.

**Technical Partitioning**\
Chia kiến trúc thành các **nhóm chức năng**, các component trong nhóm chỉ thực hiện một chức năng đã được định nghĩa cho nhóm đó.\
Nhóm Persistence chỉ quản lý transaction và query xuống db,  Application(Business) xử lý logic nghiệp vụ, Presentation xử lý UI, etc. Rất nhiều kiến trúc follow cách làm này, có thể kể đến như layered architecture, pipeline architecture, microkenel architecture, event-driven architecture.
Package trong `Java` sẽ có dạng như sau
```
  app.presentation.customer
  app.business.customer
  app.persistence.customer

  app.presentation.payment
  app.business.payment
  app.persistence.payment
```

**Domain partitioning**\
Chia kiến trúc thành các **nhóm lĩnh vực** cụ thể.\
Nhóm order có thể bao gồm việc xử lý logic và query database nhưng chỉ gói gọn các xử lý liên quan đến order, tương tự như vậy với inventory management, statistic, etc.
Được ứng dụng trong kiến trúc Domain driven design (DDD)
Package trong `Java` sẽ có dạng như sau
 ```
  app.customer.presentation
  app.customer.business
  app.customer.persistence

  app.payment.presentation
  app.payment.business
  app.payment.persistence
```

Bài viết này sẽ tập trung vào kiến trúc cơ bản nhất, đơn giản nhất, phổ biến nhất hiện nay, đời lập trình viên chưa ai chưa kinh qua kiến trúc này. **Layered architecture - kiến trúc quốc dân**

# I. Khái niệm
Layered architecture là kiểu kiến trúc được tổ chức theo chiều dọc bao gồm nhiều tầng xếp chồng lên nhau.
Tùy vào độ phức tạp của dự án mà kiến trúc có số lượng tầng và chức năng của mỗi tầng khác nhau.
Tuy vậy, các tầng phải tuân theo các tiêu chuẩn sau

**Technical partitioning**\
Kiến trúc này sử dụng technical partitioning để phân chia thành các nhóm chức năng.

**Separation of concern**\
Mỗi tầng có một vai trò riêng biệt, các tầng độc lập với nhau, đảm bảo logic không bị chồng chéo lên nhau. Các component con của tầng `presentation` chỉ có chức năng xử lý giao diện và tương tác với người dùng cuối, không được thực hiện query dữ liệu từ database, vai trò này thuộc về tầng `persistence`.
Một thay đổi về mặt technical không làm ảnh hưởng đến các tầng khác. Ví dụ hệ thống web application được mở rộng trên mobile application, lúc này chỉ cần mở rộng riêng tầng `Presentation` vốn chuyên xử lý giao diện và tương tác người dùng; các xử lý liên quan đến nghiệp vụ hoặc database của các tầng khác không bị ảnh hưởng.

**Giao tiếp giữa các tầng**\
Tầng trên có thể sử dụng các chức năng mà các tầng dưới cung cấp thông qua các interface mà tầng dưới đã định nghĩa sẵn.
Tầng dưới có trách nhiệm thực thi yêu cầu được gửi từ các tầng trên, và không có phụ thuộc ngược lại tầng trên.

# II. Kiến trúc cơ bản
Như đã nói ở trên không có quy định rõ rành về số lượng tầng và chức năng của mỗi tầng nhưng về cơ bản kiến trúc sẽ có 4 tầng

![layered_architecture.png]({{ site.baseurl }}/assets/img/layered_architecture/layered_architecture.png)
**Presentation**\
Xử lý tương tác với người dùng cuối, thường là xử lý UI, validate user input, xây dựng UI dựa trên dữ liệu được trả về từ tầng `application`; đồng thời cũng là trung chuyển yêu cầu của người dùng cuối sang tầng `application` để xử lý yêu cầu của người dùng.

**Application**\
Một số tài liệu cũng gọi là `business layer`, mục đích nhằm xử lý toàn bộ các logic nghiệp vụ của hệ thống.
Định nghĩa và cung cấp các interface cho tầng trên là `presentation`

**Persistence**\
Chịu trách nhiệm tương tác với tầng database, có thể kể đến như quản lý connection pool và thực hiện các cầu truy vấn được yêu cầu bởi tầng trên.\
Persistence layer về mặt tổng quan không chỉ tương tác với database mà mục đích chính là duy trì trạng thái của các entity trong suốt vòng đời của application. Entity có thể lưu trên DB, file hoặc bất kỳ một hệ thống lưu trữ khác.

**Database**\
Là các dịch vụ lưu trữ như Mysql, MongoDb cung cấp các phương thức để thêm, xóa, sửa, truy vấn dữ liệu.

# III. Open and closed layer
**Closed layer**\
Bắt buộc một request từ tầng trên phải gọi trực tiếp xuống tầng kế tiếp nó. Giả sử một request để GET thông tin người dùng sẽ bắt buộc phải đi qua đủ 4 tầng để lấy được dữ liệu

> Presentation -> Business -> Persistence -> Database

![closed]({{ site.baseurl }}/assets/img/layered_architecture/closed.png)

Cách làm này ứng dụng khái niệm **Layers of isolation**, đảm bảo rằng một thay đổi ở một tầng không (hoặc ít) ảnh hưởng các tầng khác phụ thuộc vào nó.\
Việc này giúp cho hệ thống trở nên tường minh, dễ hiểu, dễ bảo trì hơn, giảm thiểu đa phụ thuộc (interdependencies) giữa các tầng, tầng ở trên chỉ phụ thuộc vào tầng liền dưới nó.\
Phù hợp với hệ thống yêu cầu tính ổn định, bảo mật và dễ bảo trì.

**Open layer**\
Ngược lại với closed layer, cho phép một tầng có thể gọi trực tiếp đến các tầng con bên dưới nó (tầng trên nó vẫn không được gọi nha mấy ní).
Giả sử kiến trúc có thêm 1 layer nữa

> Presentation -> Business -> Services -> Persistence -> DB
>
Một request có thể đi qua cả 5 tầng như `closed layer`, nhưng cũng có thể bỏ qua lớp trung gian `services layer` nếu cần thiết

> Presentation -> Business -> Persistence -> DB


![open]({{ site.baseurl }}/assets/img/layered_architecture/open.png)

Open layer mang lại sự linh hoạt cho hệ thống, tăng hiệu suất do có thể bỏ qua các layer trung gian thực tế chỉ làm vai trò forward yêu cầu xuống lớp bên dưới (Layer bloat/ pass-through layer).\
Nhược điểm của phương pháp này là làm giảm tính mô-dun, khó bảo trì và kiểm thử, cần nhiều công sức để tạo và cập nhật tài liệu giải thích.\
Phù hợp với hệ thống yêu cầu hiệu suất cao và linh hoạt.

# IV. Tính chất
Layered architecture là kiến trúc phổ biến nhất, được sử dụng rộng rãi vì sự đơn giản, dễ hiểu, dễ áp dụng.
Lựa chọn kiến trúc này khi anh em muốn khởi động một dự án mà chưa biết kiến trúc nào sẽ phù hợp. Tuy vậy, cái gì đơn giản mà muốn sử dụng hiệu quả thì anh em thực sự hiểu và liên tục review, maintain kiến trúc.
Một sai lầm thường mắc phải là rơi vào bẫy của **architecture sinkhole anti-pattern**

**Architecture sinkhole anti-pattern**\
Khi các request được truyền từ tầng trên xuống nhưng mỗi layer chỉ chuyển tiếp hoặc add thêm một lượng logic không đáng kể, làm giảm hiệu suất của toàn hệ thống và tăng độ phức tạp không cần thiết.
Nếu 80% số lượng request xảy ra tình trạng này thì cần phải cân nhắc tái cấu trúc lại kiến trúc thông qua các cách sau

- Thêm hoặc xóa bỏ layer khi cần thiết
- Chuyển layer từ closed sang open và ngược lại
- Gộp 2 layer thành một layer mới có chức năng của cả hai

# V. Nên

**Nhanh - gọn - lẹ**\
Các hệ thống nhỏ, chi phí thấp, yêu cầu khắt khe về thời gian hoàn thành dự án.
Với những dự án kiểu này, việc xây dựng nguyên một hệ thống lớn với kiến trúc phức tạp là không cần thiết. Thay vì dành thời gian cho việc cân nhắc, phân tích và chia nhỏ theo domain như một vài pattern khác;
anh em hãy bắt tay vào xây dựng ngay một kiến trúc dạng layered đáp ứng được yêu cầu của dự án. Tất nhiên việc đánh giá scope phải khách quan và chính xác để đưa ra nhận định về độ phức tạp và size của dự án.
Việc chia team theo technical cũng đơn giản, anh nào làm FE thì có trách nhiệm xử lý tầng presentation, anh nào làm BE thì xử lý business và persistence, anh nào làm database thì tập trung vào database.
Rất nhanh chóng anh em đã có thể form một team chiến mọi dự án theo kiến trúc phân tầng.

**Technical oriented**\
Các dự án lớn không phải là không thể áp dụng kiến trúc phân tầng, đặc biệt là các dự án có thiên hướng thay đổi về mặt technical.
Phù hợp với các dự án mà yêu cầu nghiệp vụ của dự án đã tương đối đầy đủ và các thay đổi có thiên hướng tập trung về phía technical như thay đổi tầng database, hỗ trợ thêm các thiết bị mới mà nghiệp vụ không thay đổi.
Ví dụ như dự án yêu cầu xây dựng hệ thống quản lý tập trung có thể access được trên mọi thiết bị, phase đầu tiên triển khai trên nền web, sau đó mở rộng ra các thiết bị di động khác. Có thể thấy logic xử lý là như nhau cho tầng `business`, anh em có thể giới hạn chỉ cần mở rộng tầng `presentation` là có thể đáp ứng yêu cầu của bài toán.

# VI. Không nên

**Operation concerns**\
Layered architecture lộ rõ khuyết điểm khi ứng dụng cho các hệ thống yêu cầu khả năng mở rộng cao (scalable), khả năng chịu lỗi (fault tolerance).

*Scale issue*\
Layered architecture thường đi cùng kiến trúc monolithic, tất cả xử lý của các tầng nằm trong một khối lớn, lợi ích của kiến trúc này có thể kể đến như giảm thiểu overhead giao tiếp giữa các component, đơn giản hoá việc xử lý transaction, etc..
nhưng ngược lại, nếu muốn scale một layer nào đó, ta phải scale toàn bộ khối monolithic này, mặc dù các layer khác hoàn toàn đáp ứng được capacity của hệ thống.
Do đó scaling trở nên khó khăn, thường không hiệu quả và không tận dụng tối đa tài nguyên.\
*Fault tolerance*\
Fault tolerance đại diện cho khả năng chịu lỗi của hệ thống, ngăn chặn sự gián đoạn phát sinh từ một điểm thất bại duy nhất (Single point of failure).
Nếu sử dụng kiến trúc nguyên khối phân tầng, tỉ lệ cao là hệ thống cũng sẽ down khi có sự cố xảy ra.
Một trong những giải pháp cho phương án này là run service theo mô hình Active-standby, nhưng điều này lại raise lên vấn đề đã đề cập trước đấy là scaling issue.

**Domain oriented**\
Vì đây là một kiến trúc dạng technical, nên các dự án có nhiều thay đổi mang hơi hướng domain sẽ ảnh hưởng tới toàn bộ các layer.
Ví dụ, yêu cầu thêm một trường expire date cho sản phẩm, thay đổi bắt đầu từ tầng database tất nhiên phải lưu trữ thông tin này, persistence, business và presentation layer đều phải thay đổi để cho phép operator chỉnh sửa expire date và hiển thị thông tin này cho người dùng cuối.\
Càng nhiều layer, thì công việc càng trở nên phức tạp cho đến khi việc maintain trở nên cực kì khó khăn.

# VII. Kết luận

Tóm lại, Layered architecture là một lựa chọn lý tưởng cho những dự án cần sự đơn giản, dễ hiểu, và triển khai nhanh chóng.
Tuy nhiên, kiến trúc này cũng có những hạn chế rõ ràng, đặc biệt khi đối mặt với những yêu cầu về khả năng mở rộng, chịu lỗi, hoặc thay đổi phức tạp trong domain.
Với những hệ thống nhỏ và có thiên hướng technical, Layered architecture thực sự là một giải pháp nhanh gọn và hiệu quả.
Nhưng nếu dự án đòi hỏi sự linh hoạt cao hoặc có khả năng phát sinh những thay đổi lớn về domain, thì cần cân nhắc kỹ lưỡng hơn về cách phân tầng và các mô hình kiến trúc khác phù hợp.\
Dưới đây là hình ảnh đánh giá layered architecture trong cuốn **Software Architecture patterns**

![characteristic]({{ site.baseurl }}/assets/img/layered_architecture/characteristic.png)
