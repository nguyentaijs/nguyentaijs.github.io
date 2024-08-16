# I. Khái niệm
Một service được xem là một micro service khi nó chỉ làm và làm tốt đúng một vai trò duy nhất.\
Micro services là tập hợp của nhiều micro service, số lượng service con không giới hạn, có thể lên đến hàng trăm hoặc hàng nghìn services nhỏ.\
Nhờ có các dịch vụ như container và orchestration như Docker, Kubernetes mà việc theo dõi và quản lý các dịch vụ trong hệ thống cũng trở nên đơn giản và khả thi hơn.

Thiết kế kiến trúc này cần cân nhắc kỹ lưỡng để đảm bảo tính độc lập của từng service.
Trường hợp lý tưởng nhất, mỗi service nằm gọn trong **Bounded context** của mình, có nghĩa là service có một vùng dữ liệu riêng chỉ được access bởi service đó.
Các service khác nếu muốn lấy dữ liệu liên quan phải gọi thông qua một contract tới service này (VD: API calls), chứ không được access trực tiếp.
Nhờ có tính chất này mà việc phản ứng với thay đổi trở nên đơn giản hơn khi nó chỉ ảnh hưởng đến một hoặc một vài services liên quan.
Mặc dù vậy, trên thực tế việc set bounded context cho từng service đôi khi không thể thực hiện, một trọng những lý do là vì vấn đề performance, 2 services có thể access chung một database để giảm thiểu các giao tiếp không cần thiết.
Nhưng cũng chỉ nên giới hạn tối đa 6 services share chung môt context, nếu không có sự kiểm soát trong việc này có thể khiến cho việc maintain hệ thống ngày càng trở nên phức tạp.

# 2. Kiến trúc cơ bản
TODO: API Gateway
Như đã nói ở trên không có quy định rõ rành về số lượng tầng và chức năng của mỗi tầng nhưng về cơ bản kiến trúc sẽ có 4 tầng

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

# 3. Open and closed layer
**Closed layer**\
Bắt buộc một request từ tầng trên phải gọi trực tiếp xuống tầng kế tiếp nó. Giả sử một request để GET thông tin người dùng sẽ bắt buộc phải đi qua đủ 4 tầng để lấy được dữ liệu

> Presentation -> Business -> Persistence -> Database

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

Open layer mang lại sự linh hoạt cho hệ thống, tăng hiệu suất do có thể bỏ qua các layer trung gian thực tế chỉ làm vai trò forward yêu cầu xuống lớp bên dưới (Layer bloat/ pass-through layer).\
Nhược điểm của phương pháp này là làm giảm tính mô-dun, khó bảo trì và kiểm thử, cần nhiều công sức để tạo và cập nhật tài liệu giải thích.\
Phù hợp với hệ thống yêu cầu hiệu suất cao và linh hoạt.

# 4. Tính chất
Layered architecture là kiến trúc phổ biến nhất, được sử dụng rộng rãi vì sự đơn giản, dễ hiểu, dễ áp dụng.
Lựa chọn kiến trúc này khi anh em muốn khởi động một dự án mà chưa biết kiến trúc nào sẽ phù hợp. Tuy vậy, cái gì đơn giản mà muốn sử dụng hiệu quả thì anh em thực sự hiểu và liên tục review, maintain kiến trúc.
Một sai lầm thường mắc phải là rơi vào bẫy của **architecture sinkhole anti-pattern**

**Architecture sinkhole anti-pattern**\
Khi các request được truyền từ tầng trên xuống nhưng mỗi layer chỉ chuyển tiếp hoặc add thêm một lượng logic không đáng kể, làm giảm hiệu suất của toàn hệ thống và tăng độ phức tạp không cần thiết.
Nếu 80% số lượng request xảy ra tình trạng này thì cần phải cân nhắc tái cấu trúc lại kiến trúc thông qua các cách sau

- Thêm hoặc xóa bỏ layer khi cần thiết
- Chuyển layer từ closed sang open và ngược lại
- Gộp 2 layer thành một layer mới có chức năng của cả hai

# 5. Nên

**Nhanh gọn lẹ**\
Các hệ thống nhỏ, chi phí thấp, yêu cầu khắt khe về thời gian hoàn thành dự án.
Với những dự án kiểu này, việc xây dựng nguyên một hệ thống lớn với kiến trúc phức tạp là không cần thiết. Thay vì dành thời gian cho việc cân nhắc, phân tích và chia nhỏ theo domain như một vài pattern khác;
anh em hãy bắt tay vào xây dựng ngay một kiến trúc dạng layered đáp ứng được yêu cầu của dự án. Tất nhiên việc đánh giá scope phải khách quan và chính xác để đưa ra nhận định về độ phức tạp và size của dự án.
Việc chia team theo technical cũng đơn giản, anh nào làm FE thì có trách nhiệm xử lý tầng presentation, anh nào làm BE thì xử lý business và persistence, anh nào làm database thì tập trung vào database.
Rất nhanh chóng anh em đã có thể form một team chiến mọi dự án theo kiến trúc phân tầng.

**Technical oriented**\
Các dự án lớn không phải là không thể áp dụng kiến trúc phân tầng, đặc biệt là các dự án có thiên hướng thay đổi về mặt technical.
Phù hợp với các dự án mà yêu cầu nghiệp vụ của dự án đã tương đối đầy đủ và các thay đổi có thiên hướng tập trung về phía technical như thay đổi tầng database, hỗ trợ thêm các thiết bị mới mà nghiệp vụ không thay đổi.
Ví dụ như dự án yêu cầu xây dựng hệ thống quản lý tập trung có thể access được trên mọi thiết bị, phase đầu tiên triển khai trên nền web, sau đó mở rộng ra các thiết bị di động khác. Có thể thấy logic xử lý là như nhau cho tầng `business`, anh em có thể giới hạn chỉ cần mở rộng tầng `presentation` là có thể đáp ứng yêu cầu của bài toán.

# 6. Không nên

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

# 7. Kết luận

Tóm lại, Layered architecture là một lựa chọn lý tưởng cho những dự án cần sự đơn giản, dễ hiểu, và triển khai nhanh chóng.
Tuy nhiên, kiến trúc này cũng có những hạn chế rõ ràng, đặc biệt khi đối mặt với những yêu cầu về khả năng mở rộng, chịu lỗi, hoặc thay đổi phức tạp trong domain.
Với những hệ thống nhỏ và có thiên hướng technical, Layered architecture thực sự là một giải pháp nhanh gọn và hiệu quả.
Nhưng nếu dự án đòi hỏi sự linh hoạt cao hoặc có khả năng phát sinh những thay đổi lớn về domain, thì cần cân nhắc kỹ lưỡng hơn về cách phân tầng và các mô hình kiến trúc khác phù hợp.
