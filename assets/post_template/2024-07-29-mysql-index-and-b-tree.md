---
layout: post
title: Learn Mysql index the right way
categories: [discussion, database, optimization, mysql]
tags: [discussion, database, transaction]
date: 2024-07-16 20:22 +0700
---

![Back to basic ACID or Acid]({{ site.baseurl }}/assets/img/acid-or-acid.png)

"Query chậm à? Set index thôi chứ còn chờ gì nữa bạn ei"\
"Tôi set index JOIN bạn set index WHERE. Cứ thế mà làm"\
Nắm bí kíp trong tay, đôi bạn hí hửng chia nhau set hơn chục cái index cho tất cả các column điều kiện. Tốc độ query giảm từ 5s về 0.5s, hơn cả mong đợi, từ giờ task optimize cứ ném cho 2 thằng em.\
Bẵng đi một thời gian, đôi bạn thân được sếp tin tưởng giao thêm một task optimize nữa xử lý việc người dùng complain hiện tại tạo order phải chờ hơn chục giây.\
Đôi bạn bàng hoàng nhận ra cái ngày performance bắt đầu lao dốc cũng chính là ngày mà đôi bạn submit task optimize index đầu tiên của đời mình.

Index có phải là lời giải cho tất cả các yêu cầu optimize query?\
Bài viết này sẽ giúp anh em hiểu và tự tin set index chuẩn như quân đội.

# I. Hiểu về index

## 1. Vì sao cần có index

Cách dữ liệu được tổ chức trên ổ cứng

### Bài toán

Anh em cùng tôi thử lên ý tưởng xây dựng một database clone của Mysql siêu cơ bản cho phép `INSERT` và `SELECT` dữ liệu thí sinh bao gồm `họ và tên` và `số thứ tự`.

[Table of records](image)

**1. Khai báo kiểu dữ liệu**

Số báo danh\
Tôi đặt kiểu `INT` với kích thước 4 bytes, như vậy tôi có thể lưu được các giá trị từ 0 - 4,294,967,295.\
Họ và tên\
Họ và tên phải là một chuỗi ký tự kích thước không cố định, nên tôi sẽ tạo 1 kiểu `VARCHAR(50)`: một chuỗi cho phép lưu tối đa 50 ký tự có 1 byte đầu tiên xác định kích thước của chuỗi, phần còn lại tôi lưu giá trị thực của chuỗi. Như vậy kích thước tối đa cho cột này là `1 + 50 * 4 = 201 bytes`

**2. Tổ chức dữ liệu trên ổ cứng**

Xong phần khai báo kiểu dữ liệu, bây giờ lưu dữ liệu xuống ổ cứng như thế nào?\
Đơn giản nhất là tôi lưu theo dạng mảng tuần tự, tôi khoanh vùng 1GB trên ổ cứng dành riêng cho việc lưu dữ liệu không cho phép các ứng dụng khác truy cập vào vùng nhớ này.
Nếu dữ liệu mới được thêm vào, tôi chỉ đơn giản append record mới nối tiếp vào record trước đó. Tuyệt vời! Độ phức tạp của lệnh `INSERT` là **O(1)**.\
Dữ liệu của bảng trên sẽ được sắp xếp tuần tự như hình dưới.

[Data section](image)

**3. Tìm kiếm dữ liệu**

Insert thì ngon rồi đấy, nhưng mà việc tìm kiếm dữ liệu trên mảng này yêu cầu phải duyệt từ đầu section đến cuối để tìm ra giá trị hợp lệ. Độ phức tạp của phương pháp này là **O(n)** với n là tổng số byte của các records.
Ở ví dụ trên là 269 bytes, 1 con số quá lớn cho 5 records. Có phương pháp nào giảm số bước nhảy `n` xuống ngang bằng với số record không?

[Search without index](image)

**4. Sự ra đời của index**

Nhận thấy trường `so_bao_danh` là duy nhất và có thể đại diện cho 1 record. Tôi tạo thêm một vùng nhớ nhỏ chỉ để lưu `pointer` đến trường `so_bao_danh` của mỗi record - gọi nó là `Index section`.
Lúc này, thay vì tìm kiếm trên toàn bộ `Data section`, tôi thực hiện tìm kiếm trên `Index section`, nếu tìm thấy giá trị mong muốn, thực hiện lấy dữ liệu record từ `Data section` với `pointer` tìm được. Độ phức tạp vẫn là O(n) nhưng `n` đã giảm từ 269 xuống vỏn vẹn còn 5 bước.

[Search with index](image)

**5. B-tree typed index**

Nếu theo thời gian dữ liệu nở rộng đến 1 triêu, 2 triệu record thì việc truy vấn với độ phức tạp O(n) vẫn phát sinh các vấn đề về performance.
Tôi học Mysql sử dụng cấu trúc `B+ Tree` để tổ chức index, với cách tiếp cận này, độ phức tạp giảm mạnh từ O(n) xuống **O(log(n))**. Chỉ với vài bước nhảy, tôi đã có thể tìm được record mong muốn trong hơn triệu records. Cùng tham khảo hình dưới
