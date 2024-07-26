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

# Bài toán thực tế
Khi muốn ghi nhớ một khái niệm nào đó ta cần 2 yếu tố. Yếu tố tiên quyết là anh em phải tìm được ý nghĩa của việc tiếp thu kiến thức này. Yếu tố còn lại quan trọng không kém để ghi nhớ lâu hơn là hiểu được bản chất và tìm cách liên hệ với một bối cảnh thực tế, một câu chuyện quen thuộc hơn.\
Lấy ví dụ tình huống sau.

A mới được bổ nhiệm làm quản lý 100 giường bệnh của một bệnh viện, yêu cầu mỗi bệnh nhân nằm riêng 1 phòng.
Các bệnh nhân có thể trùng tên nên A đã in lên áo cho từng bệnh nhân 2 thông số *mã bệnh nhân* và *họ tên*.
Hãy cùng tôi khám phá hành trình của anh quản lý và đôi chân sưng tấy.

[Data section](image)

**Tìm kiếm bệnh nhân - Cuộc sống không có index**\
Ngoài việc quản lý vệ sinh trật tự trong dãy giường bệnh, A còn được phân công hỗ trợ bác sĩ khám cho các bệnh nhân của mình.
Trong ngày, các bác sĩ sẽ đến khám riêng cho từng bênh nhân hoặc một nhóm bệnh nhân.
Bác sĩ cho A một danh sách các mã bệnh nhân và yêu cầu A cho họ biết các bệnh nhân này ở phòng nào.
A đi hết 100 phòng, hỏi từng bệnh nhân, đối chiếu với mã bệnh nhân của bác sĩ để lấy được danh sách hoàn chỉnh.
A chăm chỉ nên A vẫn thấy hài lòng với công việc hiện tại.

**Index và cuốn sổ cái**\
Sau 1 thời gian làm việc ở đây, bắp chân A to lên, rắn chắc như một vận động viên điền kinh. A thầm cảm ơn những ngày làm việc vất vả chạy qua chạy lại 100 phòng đến hơn chục lượt.
Nhưng một ngày A bệnh, A lết mãi mới được nửa vòng, A quyết định lần này là lần cuối, A đi hết 100 phòng, mỗi phòng A dừng lại và mapping thông tin bệnh nhân và số phòng vào một **cuốn sổ cái**.
Những lần tiếp theo, khi có yêu cầu, A chỉ cần giở cuốn sổ ra và trích ra danh sách phòng của từng bệnh nhân theo yêu cầu của bác sĩ. Chân của A không còn đau và A đã nhàn hơn rất nhiều.

Giám đốc bệnh viện thấy dạo này A rảnh, ông cất nhắc A lên tuyến trên và yêu cầu anh A quản lý 10.000 giường bệnh.
Rút kinh nghiệm, a chấp nhận đau một lần rồi thôi, A cũng lại đi thống kê toàn bộ vào cuốn sổ cái của mình, nhưng cuốn sổ của A chằng chịt toàn chữ, dày lên trông thấy.
Mỗi lần bác sĩ đến khám, A dò 10.000 dòng trong cuốn sổ cái của mình. Chân A không còn đau nhưng mắt A bắt đầu nhoè dần sau 1 tuần làm việc.

**Chia để trị**\
A thử chia cuốn sổ cái của mình thành 1000 cuốn sổ nhỏ, mỗi cuốn 100 dòng, rồi chia theo dạng cây cân bằng vào giá sách có nhiều ngăn.\
Ngăn lớn ngoài cùng dán nhãn mã từ 1 -> 10.000, rồi tiếp tục từ 10.001 -> 20.000, tổng cộng có 10 ngăn.
Trong mỗi ngăn lớn lại chia thành 10 ngăn nhỏ, mã từ 1 -> 1.000, 1.001 -> 2.000, như vậy một ngăn chỉ còn có 10 cuốn sổ nhỏ.
Giả sử cần tìm bệnh nhân mã số = 1.890

1. Ở ngăn lớn ngoài cùng, A so sánh 1 < 1890 < 10.000, do đó A biết cần tìm trong ngăn này
2. A tìm trong ngăn 1 -> 1.000, không thấy. Chuyển sang tìm trong ngăn có giá trị > 1.000, phát hiện ngăn con thoả mãn 1001 < 1890 < 2000.
3. Ngăn trong cùng này có 10 cuốn mỗi cuốn 1.000 dòng, cuốn sổ cái thứ 9 lưu mã từ 1.801 -> 1.900 sẽ là cuốn sổ mà A cần tìm.

Chỉ cần 3 bước tìm kiếm, A tìm ra được phòng bệnh nhân mong muốn. Cuốn sổ cái trong ví dụ trên tương tự với khái niệm index trong các hệ cơ sở dữ liệu. Sử dụng một vùng nhớ nhỏ để lưu giá trị của column có tính đại diên, từ đó lấy được record tương ứng.

# Index
> Indexes are used to find rows with specific column values quickly. Without an index, MySQL must begin with the first row and then read through the entire table to find the relevant rows.
> The larger the table, the more this costs. If the table has an index for the columns in question, MySQL can quickly determine the position to seek to in the middle of the data file without having to look at all the data.
> This is much faster than reading every row sequentially.

Index là một cấu trúc dữ liệu giúp tăng tốc độ truy vấn và thao tác dữ liệu trong các bảng. Chỉ mục được tạo trên một hoặc nhiều column và hoạt động như một cuốn sổ cái để tìm kiếm và truy xuất dữ liệu mà không cần quét toàn bộ bảng.\
Dữ liệu càng lớn thì vai trò của index càng trở nên rõ ràng hơn. Ngoài ra, Mysql còn hỗ trợ composite index (hay multi-column index) đánh chỉ mục trên nhiều cột đồng thời.

## Phân loại
Mysql hỗ trợ nhiều loại index đáp ứng các cách đánh chỉ mục khác nhau.

**PRIMARY KEY**\
Primary key đảm bảo có thể xác định một record duy nhất.
- Non-nullable: không cho phép giá trị NULL
- Uniqueness: không chấp nhận giá trị trùng lặp
- Column được khai báo làm PRIMARY KEY sẽ được tự động đánh index
- Không thể khai báo 2 PRIMARY KEY trong một table.

**UNIQUE**\
Tương tự như PRIMARY KEY, UNIQUE đảm bảo các giá trị trong index là duy nhất.
Điều thú vị là UNIQUE index cho phép NULL và còn cho phép nhiều NULL values cùng tồn tại.

**INDEX**\
Thoáng hơn cả 2 anh trên, loại index này cho phép null, cho phép giá trị trùng lặp. Không có ràng buộc gì đặt biệt.

**FULLTEXT**\
Chỉ hỗ trợ các kiểu dữ liệu CHAR, VARCHAR và TEXT, được sử dụng để thực hiện các truy vấn theo ngôn ngữ tự nhiên `WHERE MATCH ... AGAINST`. Duy trì các index dạng này tốn nhiều tài nguyên hơn, đặc biệt với các bảng lớn.

**SPATIAL**\
Được thiết kế để hỗ trợ truy vấn cho dữ liệu không gian. Thực tế tôi cũng chưa được kinh qua món này nên chỉ để đây và không nói gì.

**Multi-column index (Composite index)**\
Khác với các loại trên, index loại này cho phép đánh chỉ mục trên nhiều column. Giả sử tôi có 1 multi-column index (col1, col2, col3), index này cũng tương đương với index(col1), index(col1, col2), index(col1, col2, col3)

```sql
CREATE INDEX col123 ON my_table(col1, col2, col3);

SELECT * FROM my_table WHERE col1 = 1;
-- Sử dụng index của col123 tương đương index(col1)


SELECT * FROM my_table WHERE col1 = 1 and col2 = 'a';
-- Vẫn có thể sử dụng index của col123 tương đương index(col1, col2)


SELECT * FROM my_table WHERE col1 = 1 and col2 = 'a' col3 = 'b';
-- Sử dụng index của col123
```

## B-tree (Balanced Tree)

B-tree còn được gọi là cây tự cân bằng. Cấu trúc dữ liệu này giải quyết giới hạn của Binary search tree (Cây tìm kiếm nhị phân), một node của B-tree có thể lưu nhiều giá trị cùng lúc rút ngắn chiều dài của cây.
Hầu hết các loại index của Mysql (PRIMARY KEY, UNIQUE, INDEX, FULLTEXT) đều sử dung B-tree.
Các đặc tính của B-tree bao gồm:

- Các node lá (leaf) luôn cùng cấp với nhau.
- Quy định giá trị minimum degree (T), là số giá trị tối thiểu của một node khi quyết định có sinh node mới hay không.
- Tất cả các node ngoại trừ root phải có ít nhất `T-1` giá trị.
- Tất cả các node bao gồm root node chỉ có thể có nhiều nhất `2T - 1` giá trị.
- Số node con của một node cha bất kỳ bằng số giá trị của node + 1.
- Tất cả các giá tị của node được sắp xếp theo thứ tự tăng dần. Con của k1 và k2 luôn có giá trị nằm trong khoảng k1 < child node < k2.
- Độ phức tạo của các thao tác cơ bản như INSERT, DELETE, SELECT đều bằng nhau và bằng O(log(n))

## Index optimization không phải là kiếm thánh

### Use the right tool for the right job
Trước khi lao đầu vào cuộc chơi anh em phải biết mình có đang đi đúng hướng hay không.
Hãy bắt đầu bằng việc phân tích dựa trên các sự thật, thu thập nhiều thông tin nhất có thể dựa vào các log hệ thống (CPU, memory, system log...), các log của Mysql (error log, slow query log,..), log của từng service tham gia vào luồng cần tối ưu.
Tìm cách tái hiện được lỗi trên môi trường test. Khi anh em đã có cái nhìn tổng quan và các số liệu cần thiết, anh em sẽ có thể chỉ ra cụ thể vấn đề nằm ở đâu, không phải lúc nào database optimization cũng là giải pháp.

### Các lưu ý khi quyết định set index

**Số lượng record lớn**\
Độ hiệu quả của index tỉ lệ thuận với kích thước của bảng. Bảng có càng nhiều record thì càng cần phải cân nhắc set index cho các column thường được sử dụng cho câu lệnh điều kiện.

**Tỉ lệ trùng lặp thấp**\
Index nên có tỉ lệ trùng lặp thấp. Lí tưởng nhất là các index type = UNIQUE, 1 index tương đương với 1 record.
Nếu anh em set index cho column kiểu boolean (TINYINT(1)) chỉ chấp nhận 2 giá trị TRUE/FALSE trên hàng triệu record, index sẽ không tạo ra nhiều khác biệt.

Quá trình optimize nên được chia thành 3 phase

- Phân tích vấn đề
- Tìm kiếm giải pháp
- Optimize
- Theo dõi và đánh giá

### Phân tích vấn đề

**Tìm bottle neck**\
Database đặc biệt là database được chia sẻ bởi nhiều components như trong hệ thống monolithic thường có nguy cơ gặp các vấn đề về performance nhiều hơn. Nhưng không phải lúc nào điểm nghẽn cũng nằm ở database, và không phải lúc nào set index cũng giải quyết được các vấn đề.
Tránh suy đoán sẽ dễ sa đà vào bẫy thiên kiến xác nhận. Hãy bắt đầu bằng việc phân tích dựa trên các sự thật, thu thập nhiều thông tin nhất có thể dựa vào các log hệ thống (CPU, memory, system log...), các log của Mysql (error log, slow query log,..), log của từng service tham gia vào luồng cần tối ưu.
Tìm cách có tái hiện được lỗi trên môi trường dev không? Sau khi đã có cái nhìn tổng quan hơn, tập trung vào tìm kiếm bottle neck,
đôi khi vấn đề nằm ở việc phản hồi chậm của các bên thứ 3 tích hợp với hệ thống, thì trọng tâm không phải là tối ưu ở mức database.

**Tìm kiếm giải pháp**\
Rồi, xác định được là vấn đề nằm ở database. Tôi sẽ tập trung làm rõ câu hỏi target là gì? Phải đáp ứng bao nhiêu TPS






