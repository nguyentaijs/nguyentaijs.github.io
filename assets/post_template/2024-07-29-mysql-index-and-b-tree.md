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

# I. Các khái niệm cơ bản

Cách dữ liệu được tổ chức trên ổ cứng

## Bài toán thực tế
Bài toán rất đơn giản, A mới được bổ nhiệm làm quản lý 100 giường bệnh của một bệnh viện, yêu cầu mỗi bệnh nhân nằm riêng 1 phòng.
Các bệnh nhân có thể trùng tên nên tôi in lên áo cho từng bệnh nhân 2 thông số mã bệnh nhân và họ tên.

[Data section](image)

**Tìm kiếm bệnh nhân - Cuộc sống không có index**\
Tại một thời điểm bất kỳ trong ngày, bác sĩ sẽ đến khám riêng cho từng bênh nhân hoặc một nhóm bệnh nhân.
Bác sĩ cung cấp một danh sách các mã bệnh nhân và yêu cầu A cho họ biết các bệnh nhân này ở phòng nào.
A phải đi hết 100 phòng, hỏi từng bệnh nhân, đối chiếu với mã bệnh nhân của bác sĩ để lấy được danh sách hoàn chỉnh.

**Index và cuốn sổ cái**\
Sau 1 thời gian làm việc ở đây, chân A to lên hẳn vì ngày nào anh cũng chạy qua chạy lại 100 phòng đến hơn chục lượt.
Cái cần to thì không to, A quyết định lần này là lần cuối, A đi hết 100 phòng, mỗi phòng A dừng lại và mapping thông tin bệnh nhân và số phòng vào một **cuốn sổ cái**.
Từ các lần tiếp theo, khi có yêu cầu, A chỉ cần giở cuốn sổ ra và thống kê lại danh sách phòng cho bác sĩ. Chân của A từ đó lại bắt đầu teo tóp dần.

Giám đốc bệnh viện thấy A dạo này rảnh quá, yêu cầu anh A quản lý 10.000 phòng khám.
Đau một lần rồi thôi, A cũng lại đi thống kê toàn bộ vào cuốn sổ cái của mình, nhưng cuốn sổ của A dày lên trông thấy.
Mỗi lần bác sĩ đến khám, A dò trong cuốn sổ cái của mình 10.000 dòng. Giờ chân A không to nhưng mắt A bắt đầu nhoè dần sau 1 tuần làm việc.

**B-tree index**\
A đọc được cách tổ chức Index của Mysql sử dụng **B+ tree**, A thử áp dụng bằng cách chia cuốn sổ cái của mình thành 1000 cuốn sổ nhỏ, mỗi cuốn có 100 dòng, rồi chia theo dạng cây cân bằng vào các ngăn.\
Ngăn lớn dán nhãn mã từ 1 -> 10.000, rồi tiếp tục từ 10.001 -> 20.000, tổng cộng có 10 ngăn.
Trong mỗi ngăn lớn lại chia thành 10 ngăn nhỏ, mã từ 1 -> 1.000, 1.001 -> 2.000, như vậy một ngăn chỉ còn có 10 cuốn sổ nhỏ.
Giả sử cần tìm bệnh nhân mã số = 1890

1. Ở ngăn ngoài cùng, A so sánh 1 < 1890 < 10.000, do đó A biết cần tìm trong ngăn này
2. A tìm trong ngăn 1 -> 10.000, trong ngăn này tìm ngăn con 1001 < 1890 < 2000.
3. Ngăn cuối cùng này có 10 cuốn mỗi cuốn 1000 dòng, cuốn sổ cái thứ 9 lưu mã từ 1801 -> 1900 sẽ cuốn sổ mà A cần tìm.

Index là một cấu trúc dữ liệu giúp tăng tốc độ truy vấn và thao tác dữ liệu trong các bảng. Chỉ mục được tạo trên một hoặc nhiều column và hoạt động như một cuốn sổ cái để tìm kiếm và truy xuất dữ liệu mà không cần quét toàn bộ bảng.
Ví dụ trên chỉ lấy ý tưởng từ cách Mysql sử dụng B Tree để đánh chỉ mục. Bây giờ anh em cùng tôi tìm hiểu thế nào là một cấu trúc B tree chuẩn chỉ.

## B-tree (Balanced Tree)

B-tree còn được gọi là cây tự cân bằng. Cấu trúc dữ liệu này giải quyết giới hạn của Binary tree (Cây nhị phân), một node của B-tree có thể lưu nhiều giá trị cùng lúc rút ngắn chiều dài của cây.
Các đặc tính của B-tree bao gồm:

- Các node lá (leaf) luôn cùng cấp với nhau.
- Quy định giá trị minimum degree (T), là số giá trị tối thiểu của một node khi quyết định có sinh node mới hay không.
- Tất cả các node ngoại trừ root phải có ít nhất `T-1` giá trị.
- Tất cả các node bao gồm root node chỉ có thể có nhiều nhất `2T - 1` giá trị.
- Số node con của một node cha bất kỳ bằng số giá trị của node + 1.
- Tất cả các giá tị của node được sắp xếp theo thứ tự tăng dần. Con của k1 và k2 luôn có giá trị nằm trong khoảng k1 < child node < k2.
- Độ phức tạo của các thao tác cơ bản như INSERT, DELETE, SELECT đều bằng nhau và bằng O(log(n))

## Multi-column index
Mysql hỗ trợ index gộp (composite index) sử dụng nhiều column để đánh chỉ mục cho dữ liệu, số lượng column không vượt quá 16 colunm.



