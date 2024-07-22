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

# I. Hiểu về index

## 1. Vì sao cần có index

Cách dữ liệu được tổ chức
Giả sử bạn có một bảng thông tin xe như sau


