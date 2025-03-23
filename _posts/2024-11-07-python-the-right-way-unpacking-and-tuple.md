---
layout: post
title: Python the right way - Unpacking and tuple
categories: [python, best_practice]
tags: [python, best_practice]
date: 2024-11-07 23:00 +0700
---

![cover]({{ site.baseurl }}/assets/img/python_the_right_way_no1.png)

Trong series này, anh em sẽ cùng tôi đi qua các quy tắc được recommend trong cuốn **Effective Python – 125 Specific Ways to Write Better Python** của tác giả **Brett Slatkin**.\
Đây là một cuốn sách hữu ích giúp anh em viết code Python tốt hơn, hiệu quả hơn, và tuân theo những best practice.

Tôi sẽ không chỉ đơn thuần tóm tắt lại nội dung cuốn sách, mà còn chia sẻ những trải nghiệm cá nhân, những bài học rút ra trong quá trình học Python từ góc nhìn của một người đã có kinh nghiệm với Java.

Let's get it.

# I. Rule #5 - Prefer multiple assigment unpacking over indexing
## 1. Explanation
Thay vì sử dụng chỉ mục (index) để truy xuất phần tử trong iterable như tuple hay list.\
Unpacking giúp code trở nên rõ ràng hơn, giảm thiểu lỗi, và tăng hiệu suất xử lý.

## 2. Terminologies
**Tuple**: được định nghĩa là một chuỗi các phần tử không thay đổi (immutable) được bọc trong dấu ngoặc đơn. Có một số tính chất như sau
- Immutable: sau khi khởi tạo, không thể thêm, xóa, sửa phần tử trong tuple.
- Có thể chứa nhiều kiểu dữ liệu khác nhau.
- Truy suất nhanh hơn thông qua index nhờ tính immutable
- Hỗ trợ cơ chế unpacking

**Unpacking**: là quá trình gán giá trị của một iterable (tuple, list, string, dictionary, etc) vào nhiều biến cùng lúc.\
Sử dụng unpacking giúp code dễ đọc hơn, tránh việc phải sử dụng index để trích suất ra các phần từ trong iterable

## 3. Examples
### Tuple unpacking
```python
data = ("Ha Noi", "Da Nang", "SG")
hanoi, danang, sg = data

print(hanoi) # Ha Noi
print(danang) # Da Nang
print(sg) # SG
```

### List unpacking
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
print(name, age) # Bob 31
```

### Unpacking in the loop
```python
employees = [("Alice", 21), ("Bob", 28), ("Carlo", 31)]
for index, (name, age) in enumerate(employees):
    print(f'No#{index + 1} - Name: {name}, age: {age}')
# No#1 - Name: Alice, age: 21
# No#2 - Name: Bob, age: 28
# No#3 - Name: Carlo, age: 31
```


## 4. Things to remember
- Khác với Java, Python cung cấp cơ chế unpacking để gán giá trị cho nhiều biến trong cùng một statement.
- Sử dụng unpacking khi có thể để code trở nên dễ đọc, dễ hiểu hơn

# II. Rule #6 - Always surround single-element tuples with parentheses
## 1. Explanation
Để khai báo một tuple, ta thường sử dụng 1 trong các cách sau
```python
# Single tuple
single_tuple = 1,
assert(isinstance(single_tuple, tuple))
single_tuple = (1,)
assert(isinstance(single_tuple, tuple))
#multiple tuple
multiple_tuple = (1,2)
assert(isinstance(multiple_tuple, tuple))
multiple_tuple = (1,2,)
assert(isinstance(multiple_tuple, tuple))
multiple_tuple = 1,2,
assert(isinstance(multiple_tuple, tuple))
multiple_tuple = 1,2
assert(isinstance(multiple_tuple, tuple))
```
**Bỏ qua dấu ngoặc () khi khai báo tuple**

Trong Python, anh em có thể bỏ qua dấu ngoặc () khi khai báo tuple. Ngoài ra, với tuple có nhiều phần tử, dấu phẩy cuối cùng cũng không bắt buộc.\
Tuy nhiên, trong một số trường hợp, việc không sử dụng dấu ngoặc () có thể gây nhầm lẫn khi đọc code.

Hãy xem ví dụ sau:
```python
to_refund = calculate_refund(
get_order_value(user, order.id),
get_tax(user.address, order.dest),
adjust_discount(user) + 0.1),

print(type(to_refund)) # <class 'tuple'>
```
Trong trường hợp này, có thể anh em kỳ vọng `to_refund` là một giá trị numeric, nhưng thực tế nó lại là một tuple có một phần tử.

**Best Practice: Luôn dùng dấu ngoặc () cho single tuple**

Để tránh nhầm lẫn, anh em nên sử dụng dấu ngoặc () khi khai báo tuple có một phần tử.\
Điều này giúp anh em và người đọc code dễ nhận biết rằng biến đang chứa một tuple.

Cách viết đúng:
```python
to_refund = (calculate_refund(
  get_order_value(user, order.id),
  get_tax(user.address, order.dest),
  adjust_discount(user) + 0.1),)

print(type(to_refund)) # <class 'tuple'>
```
-----

## 2. Things to remember
- Việc khai báo tuple trong Python có nhiều cách khác nhau bằng cách kết hợp () và "," -> dễ gây nhầm lẫn cho người mới.
- Tuple thông thường được khai báo bằng cách sử dụng dấu () và phân tách các phần tử bằng dấu ","
- Với tuple có một phần tử luôn phải đặt dấu phẩy "," ở cuối, dấu ngoặc () optional.
- Best practice: luôn sử dụng dấu ngoặc () cho tuple một phần tử để tránh hiểu nhầm về ý nghĩa của biến.

Anh em có thể tham khảo các bài viết khác của tôi tại [Blog cái nhân](https://nguyentaijs.github.io/) hoặc kết nối với
tôi qua [Linkedin](https://www.linkedin.com/in/nguyentaijs)
