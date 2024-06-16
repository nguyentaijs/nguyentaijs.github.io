---
layout: post
title: Authentication overview (Tổng quan về các phương thức xác thực)
categories: [discussion, authentication, system_design]
tags: [discussion, authentication, system_design]
date: 2024-06-15 16:29 +0700
---

# Đặt vấn đề
`Authentication` là quá trình xác nhận danh tính của 1 đối tượng, thường là một cá nhân hoặc một hệ thống,
đảm bảo rằng đối tượng này chính là đối tượng được xác thực bởi hệ thống.
Trong một số trường hợp, `authentication` còn bao hàm cả `authorization`, là bước tiếp theo của authentication,
xác nhận đối tượng này có quyền gì trên các tài nguyên nào.

Ví dụ, anh Nguyễn Văn A đến ngân hàng để xin sao kê tài khoản. Anh cần cung cấp căn cước và chữ ký để xác định danh tính.\
Là một người dùng dịch vụ ngân hàng, anh có quyền kiểm tra và thay đổi thông tin tài khoản của mình. Tuy nhiên, anh không thể thay đổi thông tin tài khoản của người khác hoặc tự ý thay đổi lãi suất cho khoản vay 1 tỉ của mình.

Trong hệ thống phần mềm, việc xác thực được phân thành ba kiểu phương thức chính:
- What you remember: tên tài khoản mật khẩu, tên trường tiểu học bạn từng học, v.v.
- What you have: Mã OTP gửi về điện thoại, yêu cầu xác thực qua ứng dụng trên điện thoại, USB chứa thông tin xác thực.
- What you are: Sinh trắc học như vân tay, nhận diện khuôn mặt.

Dựa vào số phương thức xác thực
- Single authentication
- Two factors authentication (2FA)
- Multi factors authentication

> Cần tránh việc nhầm lẫn 2FA là việc sử dụng 2 phương thức bảo mật.\
> Ví dụ, xác thực bằng cách cung cấp username/password sau đó lại nhập thêm ngày sinh để xác thực
>
> 2FA nên hiểu là việc sử dụng 2 phương thức bảo mật thuộc 2 kiểu phương thức bảo mật khác nhau\
> Ví dụ, sau khi xác thực bằng username/password (what you remember), người dùng nhập OTP gửi về điện thoại (what you have) để hoàn tất xác thực

# Các phương thức xác thực cơ bản
- Password authentication
- Passwordless authentication
- CAPTCHA
- Single sign on (SSO)
- Biometric
- Secure Assertion Markup Language (SAML)
- Adaptive authentication
- Token Authentication
- Out-of-band Authentication (OOBA)

