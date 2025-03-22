---
layout: post
title: Python the right way - Rule No.5 - Prefer multiple assignment unpacking over indexing
categories: [python, best_practice]
tags: [python, best_practice]
date: 2024-11-07 23:00 +0700
---

Trong series này, anh em sẽ cùng tôi đi qua các quy tắc được recommend trong cuốn Effective Python – 125 Specific Ways to Write Better Python của tác giả Brett Slatkin.\
Đây là một cuốn sách hữu ích giúp anh em viết code Python tốt hơn, hiệu quả hơn, và tuân theo những best practice.\
Tôi sẽ không chỉ đơn thuần tóm tắt lại nội dung cuốn sách, mà còn chia sẻ những trải nghiệm cá nhân, những bài học rút ra trong quá trình học Python từ góc nhìn của một người đã có kinh nghiệm với Java.\
Let's get it.

# I. Rule #5 - Prefer multiple assigment unpacking over indexing
Thay vì sử dụng chỉ mục (index) để truy xuất phần tử trong iterable như tuple hay list.\
Unpacking giúp code trở nên rõ ràng hơn, giảm thiểu lỗi, và tăng hiệu suất xử lý.

## 1. Terminologies
**Tuple**: được định nghĩa là một chuỗi các phần tử không thay đổi (immutable) được bọc trong dấu ngoặc đơn. Có một số tính chất như sau
- Immutable: sau khi khởi tạo, không thể thêm, xóa, sửa phần tử trong tuple.
- Có thể chứa nhiều kiểu dữ liệu khác nhau.
- Truy suất nhanh hơn thông qua index nhờ tính immutable
- Hỗ trợ cơ chế unpacking

**Unpacking**: là quá trình gán giá trị của một iterable (tuple, list, string, dictionary, etc) vào nhiều biến cùng lúc.\
Sử dụng unpacking giúp code dễ đọc hơn, tránh việc phải sử dụng index để trích suất ra các phần từ trong iterable

## 2. Examples
### Tuple unpacking
```python
data = ("Ha Noi", "Da Nang", "SG")
hanoi = data[0]
danang = data[1]
sg = data[2]
```
Recommended
```python
data = ("Ha Noi", "Da Nang", "SG")
hanoi, danang, sg = data

print(hanoi) # Ha Noi
print(danang) # Da Nang
print(sg) # SG
```

### List unpacking
Recommended
```python
data = ["Ha Noi", "Da Nang", "SG"]
hanoi, danang, sg = data

print(hanoi) # Ha Noi
print(danang) # Da Nang
print(sg) # SG
```

### Unpacking first and last element
```python
data = ("Ha Noi", "Hue", "Da Nang", "Phu Yen", "Can Tho", "SG")
hanoi, *ignores, sg = data
print(hanoi) # Ha Noi
print(sg) # SG
print(ignores) # ['Hue', 'Da Nang', 'Phu Yen', 'Can Tho']

```

### Unpacking tuple returned from a function
```python
def get_user():
  return "Bob", 31

name, age = get_user()
print(name, age)
```



# IV. Lời kết
Unpacking là một kỹ thuật mạnh mẽ và hữu ích trong Python, giúp đơn giản hóa việc truy xuất dữ liệu từ các iterable như tuple hay list.\
Việc ưu tiên sử dụng unpacking thay vì truy xuất bằng index không chỉ giúp code dễ đọc hơn mà còn giúp hạn chế lỗi tiềm ẩn.\
Hy vọng qua bài viết này, anh em đã hiểu rõ hơn về unpacking và có thể áp dụng nó vào các dự án của mình! 🚀

Hẹn anh em ở các bài viết tiếp theo~

# V. Tài liệu tham khảo
1. Effective python - Brett Slatkin

-----

Anh em có thể tham khảo các bài viết khác của tôi tại [Blog cái nhân](https://nguyentaijs.github.io/) hoặc kết nối với
tôi qua [Linkedin](https://www.linkedin.com/in/nguyentaijs)
