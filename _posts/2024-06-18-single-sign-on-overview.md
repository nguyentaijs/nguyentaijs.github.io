---
layout: post
title: Single Sign On Overview (Tổng quan về xác thực SSO)
categories: [discussion, authentication, system_design]
tags: [discussion, authentication, system_design]
date: 2024-06-18 18:07 +0700
---

# I. Khái niệm
Là phương thức xác thực cho phép người dùng đăng nhập vào nhiều ứng dụng hoặc hệ thống trong 1 hệ sinh thái bằng cách đăng nhập 1 lần duy nhất trong hệ sinh thái đó.\
Đặc biệt phù hợp với mô hình SaaS, microservices nhằm đảm bảo tính bảo mật và tăng trải nghiệm người dùng trên hệ sinh thái.

# II. Phân loại
- SAML: xml based, ra đời trước OpenId Connect vẫn được sử dụng đến thời điểm hiện tại
- OpenId Connect: JSON based, những hệ thống hiện đại ưu tiên sử dụng phương pháp này

Cùng tìm hiểu chi tiết hơn 2 phương pháp này ở phần sau

# III. OpenId Connect
OpenId Connect là một tiêu chuẩn mở rộng của OAuth 2.0 nhằm đáp ứng nhu cầu sử dụng một Identity Provider cho mục đích `Authentication` & `Authorization`

Tài liệu chi tiết được mô tả ở đây [Tài liệu](https://openid.net/specs/openid-connect-core-1_0.html)\
Theo tài liệu chính thức của OpenID Connect, có ba phương thức xác thực khác nhau, tuỳ thuộc vào nhu cầu cụ thể của từng hệ thống.\

![OpenId Connect authentication flows]({{ site.baseurl }}/assets/img/openid_connect/open-id-connect-authentication-flows.png)

Server flow có thể request `refresh_token` để duy trì quyền truy cập, đồng thời, giao tiếp chủ yếu là server-to-server, do đó, `User Agent` không cần lưu trữ `token`, giúp tăng cường bảo mật\

Ngược lại, implicit flow không cần một `server` để tương tác với `token endpoint` mà có thể request `token` trực tiếp từ `authentication endpoint`.\
Phương pháp này không yêu cầu gửi nhiều request đến các `endpoint` khác nhau. Tuy nhiên, nhược điểm của phương pháp này là `token` có thể bị lộ trên User Agent (browser).

Hybrid flow sẽ được mô tả sau.
## 1. Server flow
Trong `Authorization Request` set `response_type = code`, `client` dùng `code` này để exchange các `token`\
![SSO OpenIdConnect]({{ site.baseurl }}/assets/img/openid_connect/SSO_OpenIdConnect_Server_Flow.png)

## 2. Implicit flow
Trong `response_type` của `Implicit flow` không có `code`, request trực tiếp `token`, `id_token` hoặc `token id_token` đến `authentication endpoint`
![SSO OpenIdConnect]({{ site.baseurl }}/assets/img/openid_connect/SSO_OpenIdConnect_Implicit_Flow.png)
