---
layout: post
title: Mysql - Back to basic - ACID
categories: [discussion, database, transaction, mysql]
tags: [discussion, database, transaction, mysql]
date: 2024-07-16 20:22 +0700
---

![Back to basic ACID or Acid]({{ site.baseurl }}/assets/img/acid-or-acid.png)

Chắc hẳn anh em đã từng nghe 4 tính chất quan trọng cần đảm bảo trong xử lý transaction của database bao gồm tính nguyên tử, tính nhất quán, tính cô lập và tính bền vững (**ACID**).\
Các bài viết về chủ đề này cũng không ít nhưng tôi vẫn muốn cùng anh em ôn lại một lần nữa và tìm hiểu xem bằng cách nào mà **Mysql** có thể vỗ ngực là mình đạt đủ 4 tính chất trên.

# 1. Tính Nguyên tử (Atomicity)
Tại sao lại là nguyên tử? Ở đây chúng ta đã mượn khái niệm của vật lý là nguyên tử để nói về tính chất đầu tiên này, vì bản chất của nguyên tử cũng là điều mà mọi transaction cần phải có.
> Nguyên tử là **đơn vị cơ bản** của vật chất không thể chia nhỏ thành các phần tử khác.

Tóm lại, tính nguyên tử đảm bảo **toàn bộ** thay đổi về mặt dữ liệu trong một transaction phải được thực hiện trọn vẹn hoặc không có thay đổi nào được thực hiện. Database được đưa về trạng thái trước khi `transaction` được thực thi.

**Ví dụ**\
A thực hiện chuyển cho B $100 để làm từ thiện\
Đơn giản hoá quá trình trên ta có 3 action chính
1. Start transaction
2. Read from DB, kiểm tra số dư của A lớn hơn $100: Balance[A] > $100
3. Update record, trừ tiền tài khoản của A: Balance[A] = Balance[A] - $100
4. Update record, cộng thêm vào tài khoản của B: Balance[B] = Balance[B] + $100
5. Commit transaction

Happy case, tài khoản của A bị -$100 và tài khoản của B được +$100.\
Trong trường hợp xảy ra sự cố ở bước 4, lúc này tài khoản của A phải được trả lại $100 đã trừ ở bước 3. Hoặc tất cả các bước được thực hiện hoặc rollback toàn bộ.\
Sẽ không có trường hợp anh A bị trừ $100 nhưng anh B chưa nhận được đồng nào.

**Key takeaway**
> All or nothing

### Transaction
Mysql xem transaction là một khối thống nhất thông qua các lệnh

1. START TRANSACTION: Bắt đầu một transaction.
2. COMMIT: Xác nhận và áp dụng tất cả các thay đổi được thực hiện trong transaction.
3. ROLLBACK: Hủy bỏ tất cả các thay đổi được thực hiện trong transaction, đưa DB trở về trạng thái trước khi transaction bắt đầu.

### Redo log & Undo log
Mysql cũng duy trì 2 loại log `Redo log` và `Undo log`
1. **Redo log**: ghi thay đổi sẽ được apply vào DB. Đảm bảo trong trường hợp xảy ra sự cố, Mysql vẫn có thể hoàn thành các transaction đã commit nhưng chưa được apply vào DB.
2. **Undo log**: song song với redo log, undo log ghi lại các thay đổi ngược lại với redo log. Trong trường hợp transaction rollback, Mysql sử dụng undo log để khôi phục lại trạng thái ban đầu của DB.

```sql
-- Initial balance = 500
-- Redo log
UPDATE accounts SET balance = balance - 100 WHERE account_id = 1;

-- Undo log
UPDATE accounts SET balance = 500 WHERE account_id = 1;
```


# 2. Tính nhất quán (Consistency)

Đảm bảo rằng database phải được chuyển từ trạng thái hợp lệ này sang trang thái hợp lệ khác sau khi một transaction được hoàn tất. Nếu một trong số các lệnh vi phạm quy tắc hoặc ràng buộc của tính nhất quán, toàn bộ transaction sẽ bị huỷ bỏ.

**Ví dụ**\
Cửa hàng quy định số lượng tồn kho của một sản phẩm không được âm (stock >= 0).\
Nếu cửa hàng còn 10 sản phẩm (stock = 10), bán 2 sản phẩm, hệ thống đảm bảo stock = 8.\
Nếu phát sinh thêm yêu cầu mua 10 sản phẩm nữa, hệ thống phải reject transaction này và giữ nguyên ràng buộc tồn kho phải không âm (stock >= 0).

Ngoài trách nhiệm duy trì trạng thái hợp lệ của toàn bộ database, tính nhất quán còn thể hiện sự cam kết đảm bảo toàn vẹn dữ liệu (Data integrity) cho toàn bộ records.

### Mysql data integrity
* **Primary Key**: Đảm bảo rằng mỗi hàng trong bảng là duy nhất.
* **Foreign Key**: Đảm bảo rằng một giá trị trong một bảng tương ứng với một giá trị hợp lệ trong bảng khác.
* **Unique**: Đảm bảo rằng tất cả các giá trị trong một cột hoặc một tập hợp các cột là duy nhất.
* **Not Null**: Đảm bảo rằng một cột không chứa giá trị null.

# 3. Tính bền vững (Durability)
Đảm bảo rằng một khi một transaction đã hoàn tất, các thay đổi của nó đối với cơ sở dữ liệu sẽ được ghi lại vĩnh viễn, ngay cả khi hệ thống gặp sự cố như mất điện.\
Các thay đổi được lưu trữ trong bộ nhớ lâu dài, đảm bảo rằng dữ liệu sẽ không bị mất sau khi transaction được commit.

Có ba cơ chế chính được Mysql InnoDb sử dụng để đảo bảo tính bền vững
### Write ahead logging (WAL)
Trước khi bản ghi thực sự được lưu vào bộ nhớ, Mysql ghi trực tiếp dữ liệu vào redo log.\
Điều này đảm bảo rằng các thay đổi có thể khôi phục khi xảy ra sự cố.

### Doublewrite buffer và checkpointing
Khi một data set được ghi vào disk, nó đồng thời được ghi vào `doublewrite buffer`.\
Trong trường hợp xảy ra sự cố, dữ liệu cũng có thể khôi phục từ `buffer` này.
InnoDb định kì ghi dữ liệu từ `doublewrite buffer` vào disk và double check dữ liệu giữa `buffer` và disk đã được đồng bộ hay chưa.

### Binary log
MySQL sử dụng binary log để ghi lại tất cả các thay đổi đến DB.\
Binary log không chỉ hỗ trợ recovery trong trường hợp sự cố mà còn hỗ trợ nhân bản (replication) và point-in-time recovery.

# 4. Tính cô lập (Isolation)
Đảm bảo rằng các transaction nếu diễn ra đồng thời không ảnh hưởng đến nhau.\
Nếu chỉ có một transaction tại một thời điểm, thì transaction đấy mặc định đã được cô lập.
Nhưng có hai hay nhiều transaction xảy ra đồng thời, cần có các cơ chế để xử lý xong đột giữa các transaction để đảm bảo tính cô lập của từng transaction.\
Để xử lý vấn đề này, Mysql chia thành 4 cấp độ như sau

1. Read Uncommitted
2. Read Committed
3. Repeatable read
4. Serializable

### Read uncommitted
Cho phép đọc dữ liệu chưa được commit.

**Ví dụ**\
Thời điểm ban đầu trường `balance` = 500
Có 2 transaction đọc và ghi xảy ra đồng thời\
T1 có một thao tác ghi `update balance = 600`\
T2 có một thao tác đọc `select balance`

Happy case, theo trình tự thời gian
1. T1 update balance = 600
2. T2 đọc giá trị của balance -> 600, vì cho phép đọc giá trị chưa được commit
3. T1 commit

Trong trường hợp này, không có sai số về mặt dữ liệu. Chuyện gì xảy ra nếu ở step 3 T1 rollback

1. T1 update balance = 600
2. T2 đọc giá trị của balance -> 600
3. **T1 rollback balance = 500**

Balance mà T2 đọc được thực tế không phải là giá trị thực được lưu trong database.\
Ta gọi quá trình đọc này là **Dirty read**, dữ liệu chỉ clean khi dữ liệu đã được commit.

### Read committed
Ở level này, ta loại bỏ được **Dirty read** vì lúc này ta chỉ đọc dữ liệu đã được commit.

**Ví dụ**\
Cũng ví dụ trên, thời điểm ban đầu trường `balance` = 500
Có 2 transaction đọc và ghi xảy ra đồng thời\
T1 có một thao tác ghi `update balance = 600`\
T2 có một thao tác đọc `select balance`

Happy case, theo trình tự thời gian
1. T1 update balance = 600
2. T2 đọc giá trị của balance -> 500, lệnh update của T1 bị bỏ qua vì T1 chưa commit
3. T1 commit

Như vậy tại thời điểm T2 đọc dữ liệu, balance của T2 phản ánh đúng giá trị của T2 trong database.
Nhưng nếu T2 thực hiện thêm 1 lệnh đọc sau khi T1 commit, T2 sẽ nhận được giá trị không nhất quán với giá trị trước đó (balance=500), balance lúc này đã được cập nhật thành 600.


1. T1 update balance = 600
2. T2 đọc giá trị của balance -> **500**
3. T1 commit
4. **T2 đọc lại giá trị của balance -> 600**

Ta gọi vấn đề này là **Non-repeatable read**, dữ liệu không đồng nhất giữa hai lần đọc trong một transaction.

### Repeatable read
Để giải quyết vấn đề mà read committed để lại, cách xử lý này đảm bảo giá trị đọc được ở các lần gọi khác nhau của một transaction luôn nhất quán.
Quay lại với ví dụ trên

1. T1 update balance = 600
2. T2 đọc giá trị của balance -> **500**
3. T1 commit
4. T2 đọc lại giá trị của balance -> **500**

Giá trị của balance ở step 2 và step 4 không thay đổi đối với transaction T2 ngay cả khi T1 đã commit ở step 3.\
Nhưng các bạn đoán xem, vấn đề của cách xử lý này là gì? **Phantom read**\
Phantom read hiểu nôm na là **đang đọc thì gặp ma**, cụ thể là kết quả của truy vấn bị thay đổi do việc thêm hoặc xoá dữ liệu của các transaction khác. Cùng xem ví dụ dưới đây

1. T1 đọc: `SELECT * FROM employees WHERE department = 'Sales'` -> **10 kết quả**
2. T2 insert vào bảng employees một nhân viên thuộc phòng ban 'Sales'
3. T2 commit
3. T1 đọc lại: `SELECT * FROM employees WHERE department = 'Sales'` -> **11 kết quả**

T1 lần đầu thực hiện truy vấn ra 10 kết quả, lần thứ 2 cùng truy vấn này ra 11 kết quả. T1 kiểu: "What? Ma làm à?".
Với T1, record mới được thêm vào được xem là một phantom record, nó không hề tồn tại ở truy vấn trước.

Mysql InnoDB set `Repeatable read` là default isolation level có lẽ vì sự cân bằng giữa data consistency và performance.

> Có một số lập luận cho rằng Mysql InnoDb REPEATABLE_READ level có thể ngăn chặn phantom read thông qua Snapshot và Next-Key Lock.\
> Nhưng tài liệu chính thức của Mysql đã làm rõ InnoDB không đảm bảo loại bỏ được phantom read ở level này\
> \
> REPEATABLE READ\
> The default isolation level for InnoDB. It prevents any rows that are queried from being changed by other transactions, thus blocking non-repeatable reads but not phantom reads. It uses a moderately strict locking strategy so that all queries within a transaction see data from the same snapshot, that is, the data as it was at the time the transaction started.

### Serializable - The ultimate raid boss
Anh này giải quyết hết các vấn đề của ba anh kia đẻ ra. Luôn đảm bảo tính tuần tự của giao dịch, ngăn chặn mọi sung đột đọc/ghi.\
Anh trai 3 không - không dirty read, không non-repeatable read, không phantom read. Tật gì anh cũng tránh xa thì anh này chắc chắn 10 điểm nhưng bị cái anh chậm.

1. T1 start transaction
2. T2 đọc nhưng T1 không cho
3. T1 làm n thứ khác
4. T2 đọc nhưng T1 không cho
5. T1 làm n thứ khác nữa
6. T1 commit
7. T2 mới được đọc sau khi T1 đã commit

T2 đến sau nên ngay cả việc đọc dữ liệu cũng phải chờ T1 hoàn thành transaction của mình.

Có thể thấy từ bốn Insolation level trên, nếu muốn gia tăng Consistency thì phải giảm Availability xuống và ngược lại.\
Khi có xung đột đọc/ghi giữa các transaction, `read uncommitted` cho throughput cao nhất đồng thời tiềm ẩn rủi ro sai sót dữ liệu, ngược lại, `serializable` đảm bảo tính nhất quán cho dữ liệu nhưng đánh đổi lại tốc độ rất chậm.

Tuỳ theo yêu cầu dự án, ta có thể lựa chọn `Isolation level` sao cho phù hợp, có thể sử dụng `read uncommitted` để tăng tốc độ với những project thiên chấp nhận sai số hoặc set về `serializable` để đảm bảo tính nhất quán của dữ liệu.
Các bạn quan tâm đến vấn đề trade-off giữa C và A có thể tham khảo bài viết về [**CAP Theorem**](https://nguyentaijs.github.io/posts/consistency-or-availability/) này của tôi.

# Kết luận
Như vậy anh em đã cùng tôi đi hết 4 tính chất của database, tôi hi vọng đã giúp anh em ôn lại một lần nữa về các khái niệm này.\
Happy learning~\
Khoan! Dừng khoảng chừng là 2s. Vậy còn xử lý xung đột ghi/ghi giữa các transaction thì sao? Mysql xử lý như thế nào xin hẹn lại anh em ở một bài viết khác về các cơ chế locking của Mysql.

