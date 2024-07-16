# I. xxx

## 1. xxx
xxx

## 2. xxx
xxx

# II. xxx

# III. xxx



### Tính cô lập (Isolation)
Đảm bảo rằng các transaction nếu diễn ra đồng thời không ảnh hưởng đến nhau.
Nhắc đến insolation ta phải đề cập đến khái niệm **Isolation level**. Mysql chia thành 4 cấp độ như sau

1. Read Uncommitted
2. Read Committed
3. Repeatable read
4. Serializable

#### Read uncommitted
Cho phép đọc dữ liệu chưa được commit.

**Ví dụ**\
Thời điểm ban đầu trường `balance` = 500
Có 2 transaction đọc và ghi xảy ra đồng thời\
T1 ghi `update balance = 600`\
T2 đọc `select balance`\

Happy case
1. T1 update balance = 600
2. T2 đọc giá trị của balance -> 600, vì cho phép đọc giá trị chưa được commit
3. T1 commit

Trong trường hợp này, không có sai số về mặt dữ liệu.

