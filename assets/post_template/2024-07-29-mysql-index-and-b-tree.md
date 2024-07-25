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

**B-tree index**\
A đọc được cách tổ chức Index của Mysql sử dụng **B tree**, thử áp dụng bằng cách chia cuốn sổ cái của mình thành 1000 cuốn sổ nhỏ, mỗi cuốn 100 dòng, rồi chia theo dạng cây cân bằng vào giá sách có nhiều ngăn.\
Ngăn lớn dán nhãn mã từ 1 -> 10.000, rồi tiếp tục từ 10.001 -> 20.000, tổng cộng có 10 ngăn.
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
Dữ liệu càng lớn thì vai trò của index càng trở nên rõ ràng. Ngoài ra, Mysql còn hỗ trợ composite index (hay multi-column index) đánh chỉ mục trên nhiều cột đồng thời.
Ví dụ trên chỉ lấy ý tưởng từ cách Mysql sử dụng B Tree để đánh chỉ mục. Bây giờ anh em cùng tôi tìm hiểu thế nào là một cấu trúc B tree chuẩn chỉ.

# B-tree (Balanced Tree)

B-tree còn được gọi là cây tự cân bằng. Cấu trúc dữ liệu này giải quyết giới hạn của Binary search tree (Cây tìm kiếm nhị phân), một node của B-tree có thể lưu nhiều giá trị cùng lúc rút ngắn chiều dài của cây.
Các đặc tính của B-tree bao gồm:

- Các node lá (leaf) luôn cùng cấp với nhau.
- Quy định giá trị minimum degree (T), là số giá trị tối thiểu của một node khi quyết định có sinh node mới hay không.
- Tất cả các node ngoại trừ root phải có ít nhất `T-1` giá trị.
- Tất cả các node bao gồm root node chỉ có thể có nhiều nhất `2T - 1` giá trị.
- Số node con của một node cha bất kỳ bằng số giá trị của node + 1.
- Tất cả các giá tị của node được sắp xếp theo thứ tự tăng dần. Con của k1 và k2 luôn có giá trị nằm trong khoảng k1 < child node < k2.
- Độ phức tạo của các thao tác cơ bản như INSERT, DELETE, SELECT đều bằng nhau và bằng O(log(n))

# Các câu hỏi phỏng vấn về index thường gặp






