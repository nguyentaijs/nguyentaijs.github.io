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

Tài liệu chi tiết được mô tả ở đây [Tài liệu](https://openid.net/specs/openid-connect-core-1_0.html)
## 1. Flow tổng quát
![Single Sign On OpenId Connect]("https://github.com/nguyentaijs/nguyentaijs.github.io/tree/main/assets/img/SSO_OpenIdConnect.png")

## 2.

