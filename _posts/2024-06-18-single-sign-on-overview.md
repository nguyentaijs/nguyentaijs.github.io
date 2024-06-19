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
Theo tài liệu chính thức của OpenID Connect, có ba phương thức xác thực khác nhau, tuỳ thuộc vào nhu cầu cụ thể của từng hệ thống.

![OpenId Connect authentication flows]({{ site.baseurl }}/assets/img/openid_connect/open-id-connect-authentication-flows.png)

Server flow có thể request `refresh_token` để duy trì quyền truy cập, đồng thời, giao tiếp chủ yếu là server-to-server, do đó, `User Agent` không cần lưu trữ `token`, giúp tăng cường bảo mật\

Ngược lại, implicit flow không cần một `server` để tương tác với `token endpoint` mà có thể request `token` trực tiếp từ `authentication endpoint`.\
Phương pháp này không yêu cầu gửi nhiều request đến các `endpoint` khác nhau. Tuy nhiên, nhược điểm của phương pháp này là `token` có thể bị lộ trên User Agent (browser).

Hybrid flow sẽ được mô tả sau.
## 1. Terminologies
**Resource owner:** Người sử dụng access vào hệ thống.\
VD: người dùng cần đăng nhập vào hệ thống thông qua tính năng [Signin with Google]

**Client:** Dịch vụ mà người sử dụng muốn access, thông qua `OpenId Connect` để xác thực.\
VD: muốn support tính năng [Signin with Google], bạn phải đăng ký dịch vụ `Google Identity`. Config các thông tin: `app_id`, `app_secret`, `redirectUri` trỏ về `endpoint` của `client` để nhận `Authentication response`

**Authentication endpoint:** Endpoint của `OpenId Connect` để nhận `Authentication request` trả về `Authentication response` có thể chứa `code` hoặc `token` tương ứng với `request_type` của `Authentication request`.\
VD: Endpoint của Google có dạng: https://accounts.google.com/o/oauth2/v2/auth?...

**Token endpoint:** Endpoint của `OpenId Connect` để nhận `Token request` trả về `Token response` chứa `token` bao gồm cả `refresh_token`. `Token endpoint` chỉ được sử dụng trong `Server Flow` và `Hybrid Flow`.\
VD: Endpoint của Google có dạng: https://oauth2.googleapis.com/token

**Authentication request:** `GET` request được tạo ra bởi `Client` thông qua `User Agent` để xác thực
```http request
  GET /authorize?
    response_type=code
    &scope=openid%20profile%20email
    &client_id=s6BhdRkqt3
    &state=af0ifjsldkj
    &redirect_uri=https%3A%2F%2Fclient.example.org%2Fcb HTTP/1.1
  Host: server.example.com
```
**Authentication response:** Response được tạo ra bởi `Authentication endpoint`
`request_type` = `code`
```http request
  HTTP/1.1 302 Found
  Location: https://client.example.org/cb?
  code=SplxlOBeZQQYbYS6WxSbIA
  &state=af0ifjsldkj
```
`request_type` = `token id_token`
```http request
  HTTP/1.1 302 Found
  Location: https://client.example.org/cb#
  access_token=SlAV32hkKG
  &token_type=bearer
  &id_token=eyJ0 ... NiJ9.eyJ1c ... I6IjIifX0.DeWt4Qu ... ZXso
  &expires_in=3600
  &state=af0ifjsldkj
```
**Token request:** `POST` request được tạo ra bởi `Client` exchange `code` lấy `token`
```http request
POST /token HTTP/1.1
  Host: server.example.com
  Content-Type: application/x-www-form-urlencoded
  Authorization: Basic czZCaGRSa3F0MzpnWDFmQmF0M2JW

  grant_type=authorization_code&code=SplxlOBeZQQYbYS6WxSbIA
    &redirect_uri=https%3A%2F%2Fclient.example.org%2Fcb
```
**Token response:** Response được tạo ra bởi `Token endpoint`
```http request
  HTTP/1.1 200 OK
  Content-Type: application/json
  Cache-Control: no-store

  {
   "access_token": "SlAV32hkKG",
   "token_type": "Bearer",
   "refresh_token": "8xLOxBtZp8",
   "expires_in": 3600,
   "id_token": "eyJhbGciOiJSUzI1NiIsImtpZCI6IjFlOWdkazcifQ.ewogImlzc
     yI6ICJodHRwOi8vc2VydmVyLmV4YW1wbGUuY29tIiwKICJzdWIiOiAiMjQ4Mjg5
     NzYxMDAxIiwKICJhdWQiOiAiczZCaGRSa3F0MyIsCiAibm9uY2UiOiAibi0wUzZ
     fV3pBMk1qIiwKICJleHAiOiAxMzExMjgxOTcwLAogImlhdCI6IDEzMTEyODA5Nz
     AKfQ.ggW8hZ1EuVLuxNuuIJKX_V8a_OMXzR0EHR9R6jgdqrOOF4daGU96Sr_P6q
     Jp6IcmD3HP99Obi1PRs-cwh3LO-p146waJ8IhehcwL7F09JdijmBqkvPeB2T9CJ
     NqeGpe-gccMg4vfKjkM8FcGvnzZUN4_KSP0aAp1tOJ1zZwgjxqGByKHiOtX7Tpd
     QyHE5lcMiKPXfEIQILVq0pc_E2DzL7emopWoaoZTF_m0_N0YzFC6g6EJbOEoRoS
     K5hoDalrcvRYLSrQAZZKflyuVCyixEoV9GfNQC3_osjzw2PAithfubEEBLuVVk4
     XUVrWOLrLl0nx7RkKU8NXNHq-rvKMzqg"
  }
```
## 2. Server flow
Trong `Authorization Request` set `response_type = code`, `client` dùng `code` này để exchange các `token`\
![SSO OpenIdConnect]({{ site.baseurl }}/assets/img/openid_connect/SSO_OpenIdConnect_Server_Flow.png)

## 3. Implicit flow
Trong `response_type` của `Implicit flow` không có `code`, request trực tiếp `token`, `id_token` hoặc `token id_token` đến `authentication endpoint`
![SSO OpenIdConnect]({{ site.baseurl }}/assets/img/openid_connect/SSO_OpenIdConnect_Implicit_Flow.png)
