---
layout: post
title: Authentication overview (Tổng quan về các phương thức xác thực)
categories: [discussion, authentication, system_design]
tags: [discussion, authentication, system_design]
date: 2024-06-15 16:29 +0700
---

# I. Đặt vấn đề
`Authentication` là quá trình xác nhận danh tính của 1 đối tượng, thường là một cá nhân hoặc một hệ thống,
đảm bảo rằng đối tượng này chính là đối tượng được xác thực bởi hệ thống.
Thông thường, `authentication` sẽ đi cùng với `authorization`, là bước tiếp theo của authentication,
xác nhận đối tượng này có quyền gì trên các tài nguyên nào.

Ví dụ, anh Nguyễn Văn A đến ngân hàng để xin sao kê tài khoản. Anh cần cung cấp căn cước và chữ ký để xác định danh tính.\
Là một người dùng dịch vụ ngân hàng, anh có quyền kiểm tra và thay đổi thông tin tài khoản của mình. Tuy nhiên, anh không thể thay đổi thông tin tài khoản của người khác hoặc tự ý thay đổi lãi suất cho khoản vay 1 tỉ của mình.

# II. Phân loại
## 1. Single-factor authentication (SFA)
Single-factor authentication sử dụng đúng một phương pháp duy nhất để xác thực người dùng.\
Ưu điểm của SFA là sự đơn giản, giảm sự khó chịu cho người dùng. Ngược lại, phương pháp này được xem là chưa đủ bảo mật khi ứng dụng cho các tổ chức yêu cầu mức bảo mật cao.\
Phù hợp với các mô hình chỉ yêu cầu mức xác thực cơ bản.
Các phương pháp có thể gộp lại thành 3 nhóm chính sau
- What you remember: mật khẩu, câu hỏi mật khẩu, mã pin
- What you have: Smartcard, USB xác thực, mã OTP gửi về điện thoại, ứng dụng được cài đặt trên điện thoai
- What you are: Sinh trắc học như vân tay, nhận diện khuôn mặt, phân tích mống mắt.
- Where you access: địa chỉ IP, GPS
- What you do: thói quên gõ phím, pattern di chuột

## 2. Two-factor authentication (2FA)
Two-factor authentication sử dụng 2 phương pháp xác thực khác nhau để gia tăng độ tin cậy của hệ thống.
So sánh với SFA, 2FA vượt trội hơn về mức độ bảo mật, giảm thiểu nguy cơ bị đánh cắp, lừa đảo hoặc các truy cập trái phép.\
Mặc dù vậy, 2FA có thể gây bất tiện cho người dùng và gặp các vấn đề tương thích với các thiết bị cũ

> Cần tránh việc nhầm lẫn 2FA là việc sử dụng 2 phương thức bảo mật.\
> Ví dụ, xác thực bằng cách cung cấp username/password sau đó lại nhập thêm ngày sinh để xác thực
>
> 2FA nên hiểu là việc sử dụng 2 phương thức bảo mật thuộc 2 kiểu phương thức bảo mật khác nhau\
> Ví dụ, sau khi xác thực bằng username/password (what you remember), người dùng nhập OTP gửi về điện thoại (what you have) để hoàn tất xác thực

## 3. Multi-factor authentication (MFA)
MFA đôi khi còn được hiểu là 2FA, vì bản chất 2FA cũng sử dụng nhiều hơn một phương pháp xác thực.\
MFA mang ý nghĩa rộng hơn 2FA, MFA có thể kết hợp nhiều hơn 2 phương thức bảo mật để đạt được mục tiêu bảo mật của hệ thống.\
Mặc dù có tính an toàn cao nhất trong 3 phương pháp nhưng kĩ sư thiết kế cần cân đối giữa tính bảo mật và độ thân thiện với người dùng.\
Chỉ nên sử dụng khi 2FA vẫn chưa đủ đáp ứng yêu cầu về bảo mật của hệ thống vì nhược điểm của MFA thấy rõ là sự bất tiện cho người dùng để có được quyền truy cập.

# III. Các phương thức xác thực cơ bản

## 1. Password authentication
Xác thực bằng mật khẩu là phương pháp xác thực cơ bản và lâu đời nhất.
Phương pháp này yêu cầu người dùng cung cấp một chuỗi ký tự mà chỉ họ biết (what you remember).
Bạn có thể thấy phương pháp này trong hầu hết các hệ thống, như mật khẩu đăng nhập máy tính, điện thoại, hoặc wifi.

Best practice cho một mật khẩu mạnh:
- Tối thiểu 12 ký tự
- Bao gồm tối thiểu một chữ hoa, chữ thường, sô và ký tự đặc biệt

Ngoài ra, người dùng cũng nên thay đổi mật khẩu thường xuyên và sử dụng các công cụ tạo mật khẩu để tăng cường bảo mật.

## 2. Single sign on (SSO)
Là phương thức xác thực cho phép người dùng đăng nhập vào nhiều ứng dụng hoặc hệ thống trong 1 hệ sinh thái bằng cách đăng nhập 1 lần duy nhất trong hệ sinh thái đó.\
Đặc biệt phù hợp với mô hình SaaS, microservices nhằm đảm bảo tính bảo mật và tăng trải nghiệm người dùng trên hệ sinh thái.\
Ví dụ đăng nhập vào Gmail, bạn có thể access tất cả các dịch vụ khác trong hệ sinh thái của Google.\
Các công nghệ và giao thức thường được sử dụng
- SAML (Security Assertion Markup Language)
- OAuth
- OpenId Connect xây dựng trên nền OAuth 2.0

## 3. Biometric
Xác thực sinh trắc học là phương pháp xác thực dựa trên các đặc điểm sinh học hoặc hành vi của con người.
Phương pháp này đang được áp dụng rộng rãi trên các thiết bị di động với các tính năng như nhận diện khuôn mặt, vân tay, mống mắt hoặc giọng nói.

**Ưu điểm**
- Tính bảo mật cao và khó giả mạo
- Nhanh chóng và tiện lợi, không cần nhớ mật khẩu

**Nhược điểm**
- Chi phí cao
- Tính chính xác: Đôi khi không chính xác do tình trạng cơ thể hoặc tổn thương vật lý
- Quyền riêng tư: Quan ngại về quyền riêng tư khi các thông tin này là thông tin nhạy cảm của cá nhân.

## 4. CAPTCHA & reCAPTCHA
Completely Automated Public Turing Test to tell Computers and Humans Apart, là phương thức xác thực nhằm phân biệt người dùng là con người hay chương trình tự động bởi máy tính.\
Các loại CAPTCHA cơ bản
- Hình ảnh
- Âm thanh
- Thử thách giải đố
- reCAPTCHA sử dụng các phương pháp phân tích hành vi và đánh giá điểm số tương đồng với 1 người dùng bình thường.\
Năm 2018, Google đã tung ra phiên bản reCAPTCHA v3, sử dụng trí tuệ nhân tạo để phân tích hành vi người dùng nhằm đưa ra yêu cầu xác thực khi thực sự cần thiết, tăng trải nghiệm người sử dụng dịch vụ.

## 5. Adaptive authentication
Adaptive authentication sử dụng nhiều yếu tố khác nhau để đánh giá và đưa ra yêu cầu xác thực tương ứng nếu cần thiết.\
Các yếu tố thường được xem xét bao gồm
- Vị trí địa lí
- Thiết bị
- Địa chỉ IP thực hiện yêu cầu
- Số lần request trong 1 khoảng thời gian
- Hành vi bất thường của người dùng
Từ đó đưa ra các yêu cầu xác thực bổ sung ví dụ như mã OTP, xác thực sinh trắc, etc.

Ngoài ra còn có các phương thức xác thực khác chưa được nhắc đến ở đây, tôi sẽ quay lại và bổ sung thêm sau khi đã nắm rõ các phương thức xác thực cơ bản kể trên.
