---
layout: post
title: Đánh đổi giữa tính nhất quán và tính sẵn sàng (Tradeoff between Consistency and Availability)
categories: [discussion, distributed_system, system_design]
tags: [discussion, distributed_system, system_design]
date: 2024-07-05 21:42 +0700
---

# I. Khái niệm

### Tính nhất quán (Consistency)
Trong hệ thống phân tán, tại cùng một thời điểm, dữ liệu mà các client nhìn thấy phải hoàn toàn giống nhau. Bất kể là dữ liệu được lấy từ node nào.\
Để đạt được điều này, phải thực hiện đồng bộ dữ liệu cho tất cả các node.

>Consistency means that all clients see the same data at the same time, no matter which node they connect to. For this to happen, whenever data is written to one node, it must be instantly forwarded or replicated to all the other nodes in the system before the write is deemed ‘successful.’

**Ví dụ:** Trong hệ thống ngân hàng giao dịch được xem là nhất quán nếu\
Balance[A] = $100, A thực hiện chuyển tiền $50\
**Trường hợp** hệ thống báo thành công và cập nhật Balance[A] = $50\
**Trường hợp** hệ thống báo thất bại và giữ nguyên Balance[A] = $100\
A kiểm tra trên tất cả các thiết bị và nhận được cùng 1 kết quả như kì vọng.

### Tính sẵn sàng (Availability)
Hệ thống phải đảm bảo mọi request từ client đều nhận được phản hồi. Bất kể là phản hồi đó có nhất quán hay không.

>Availability means that that any client making a request for data gets a response, even if one or more nodes are down.

**Ví dụ**: 2 người dùng nhấn like 1 video youtube, lượt like trên cả 2 thiết bị đều chỉ tăng thêm 1, cho người dùng biết họ đã like video.\
Trong trường hợp này tính sẵn sàng được ưu tiên vì số lượng like chính xác không quá quan trọng, điều quan trọng là người dùng biết mình đã like video.

### Khả năng chịu phân mảnh (Partition Tolerant)
Hệ thống phải tiếp tục hoạt động khi xảy ra sự cố gây mất kết nối giữa các node với nhau.

>Partition tolerance means that the cluster must continue to work despite any number of communication breakdowns between nodes in the system.

**Ví dụ**: Hệ thống cloud của Amazon hoàn toàn có thể hoạt động được ngay cả khi có sự cố phân chia các trung tâm dữ liệu ở các khu vực khác nhau.

### CAP Theorem
CAP Theorem, còn được gọi là `Brewer's theorem`, là một nguyên lý trong lĩnh vực hệ thống phân tán, được công bố bởi Eric Brewer vào năm 2000.\
Nó nêu rõ rằng một hệ thống phân tán không thể đồng thời đảm bảo cả ba thuộc tính trên.\
Thông thường hệ thống thường phải lựa chọn đánh đổi giữa C (Tính nhất quán) và A (Tính sẵn sàng) vì P (Khả năng chịu phân mảnh) rất khó kiểm soát một cách triệt để.

# II. Redis và CAP theorem

Trong kiến trúc `clustered Redis`, khi có một thao tác ghi trên `Redis master`, các `Redis replica` sẽ được cập nhật dữ liệu tương ứng.

1. Redis master thực hiện ghi trên node của mình
2. Nếu thành công trả về kết quả cho client
3. **Đồng thời** `Redis master` cũng gửi yêu cầu ghi sang các `replica`

Vì quá trình này diễn ra bất đồng bộ, nên nếu `Redis master` mất kết nối đến một trong số các `replica`, thao tác ghi trên `master` sẽ không được đồng bộ với replica đó.\
Tuy nhiên, `Redis master` vẫn xác nhận thao tác ghi thành công. Do đó, dữ liệu trả về từ các `Redis replica` sẽ là dữ liệu cũ.\
Điều này cho thấy rằng Redis (với cấu hình mặc định) ưu tiên hai tiêu chí **AP** (Availability và Partition Tolerance).

![Redis & cap theorem]({{ site.baseurl }}/assets/img/consistency_vs_availability/redis_ap_oriented.png)

# III. MongoDb và CAP theorem

Trong MongoDB, cũng có một cách cấu hình tương tự để đồng bộ dữ liệu từ primary shard sang các replica.\
Khi có một thao tác ghi trên primary shard, các hành động sau sẽ được thực hiện:
1. Validate `operation` đầu vào, reject nếu sai cấu trúc
2. Thực thi `operation` trên `primary shard`
3. Chuyển tiếp `operation` cho toàn bộ `replica` liên quan
4. Chỉ khi toàn bộ `replica` thực hiện thành công và phản hồi lại cho `primary shard`, lúc này `primary shard` mới xác nhận request của client là thành công.

Như vậy, khác với `Redis`, `MongoDb` hướng tới 2 tiêu chí **CP** (Consistency & Partition tolerant)

![MongoDB & cap theorem]({{ site.baseurl }}/assets/img/consistency_vs_availability/mongo_cp_oriented.png)

Bài viết lấy cảm hứng từ [Grokking Webinar - Sự đánh đổi giữa tính Consistency và Availability trong các database cluster](https://www.youtube.com/watch?v=P62lCiZKZgo)\
Cảm ơn Grokking đã có những buổi chia sẻ sâu và chất lượng.


