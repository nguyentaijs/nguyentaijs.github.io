---
layout: post
title: Better prompt enginerring - Prompt techniques
categories: [ discussion, prompt_engineering, AI, LLM ]
tags: [ discussion, prompt_engineering, AI, LLM ]
date: 2025-04-28 19:00 +0700
---

![cover]({{ site.baseurl }}/assets/img/prompting-techniques.png)

# Zero shot / General prompting
Zero-shot prompting là sử dụng prompt để giao cho LLM 1 task yêu cầu nó thực hiện và không cung cấp thêm thông tin gì.

Ví dụ:

**User**\
Explain the origin of the name 'Python' in the context of the Python programming language

**AI**\
The Python programming language was named after the British comedy group Monty Python.
 Guido van Rossum, the creator of Python, was a big fan of Monty Python's Flying Circus, and he wanted a name for his new language that was short, unique, and a little bit mysterious.\
 He didn't want the name to be too serious or technical. He was looking for a successor name of the ABC programming language, of which the working title was Advanced B. Then the language was getting ready to release. He thought that "Python" was a suitable name that stood out. Ultimately, the naming of Python was simply a nod to a comedic influence in its creator's life, showing a bit of whimsy in the world of programming.

# One-shot / few-shot
## One-shot
One-shot là kiểu prompting mà anh em cung cấp cho LLM 1 task kèm theo 1 ví dụ để thực hiện.

Ví dụ:

**User**\
Example: Function to calculate the area of a rectangle in Python\
Input: width = 5, height = 10\
 Output: 50

Function to calculate the area of a triangle in Python\
Input: base = 4, height = 6\
Output:

## Few-shot
Few-shot sử dụng nhiều example hơn để làm rõ mối quan hệ của input-output và pattern output kỳ vọng cho task. Few-shot phù hợp với các yêu cầu phức tạp, cần đưa ra các case cho nhiều trường hợp và các giá trị biên dùng làm tham chiếu cho LLM.

Ví dụ:
**User**\
Example:

Input = 2, 2 Expecting output 4\
Input = 4, 2 Expecting output 6\
write a python function comfort above example

**AI**\
```
def comfort(a, b):
"""
This function takes two numbers as input and returns their sum.

Args:
a: The first number.
b: The second number.

Returns:
The sum of a and b.
"""
return a + b
```

Mặc dù chỉ đưa ra yêu cầu chung chung là "function comfort above example", nhưng với đủ ví dụ, LLM có thể hiểu được pattern chung của 2 ví dụ trên là phép tính tổng 2 input đầu vào.\
Phương pháp này hữu ích khi anh em muốn LLM tự tìm ra quy luật dựa vào các ví dụ mà anh em cung cấp. Số lượng ví dụ và chất lượng của ví dụ sẽ ảnh hưởng đến độ chính xác đầu ra của LLM.

# System, contextual and role prompting
**System prompting** là quá trình cung cấp hướng dẫn, thêm thông tin và các chỉ dẫn cần thiết để LLM hiểu và định hướng kết quả kỳ vọng. Định nghĩa big picture task mà LLM phải thực hiện như dịch thuật, phân loại, etc.\
**Contextual prompting** là quá trình cung cấp ngữ cảnh cụ thể liên quan đến task được LLM yêu cầu thực hiện. Giúp LLM hiểu rõ hơn nội dung đang được đề cập.\
**Role prompting** là quá trình gán cho LLM 1 vai trò cụ thể để định hướng phong cách, giọng điệu và kiến thức cơ sở của LLM.

3 techniques này không triệt tiêu lẫn nhau mà bổ trợ cho nhau, giúp LLM hiểu rõ về yêu cầu và kỳ vọng output mong muốn của anh em.

Ví dụ:

**User**
You are a Python code reviewer specializing in data analysis. Provide a *bulleted* list (max 5 points) of actionable improvements for the code. Focus on error handling, robustness, and clarity. Avoid generic suggestions. (System prompting)

This code calculates the average product price from a daily-generated CSV (`product_prices.csv`). The CSV is mostly clean, but may contain missing prices, currency symbols, or empty lines. Target user: junior data analysts. (Contextual prompting)

As a senior Python developer, review the code for reliability and ease-of-use by junior analysts, considering the potential CSV issues. (Role prompting)

```python
def calculate_average_price(filename):
    """Calculates the average price from a CSV file using pandas."""
    df = pd.read_csv(filename)
    average_price = df['Price'].mean()
    return average_price
```

# Step-back prompting
Step-back prompting là kỹ thuật giúp LLM cải thiện chất lượng, độ chính xác và khả năng suy luận.\
Thay vì yêu cầu LLM trả lời trực tiếp một câu hỏi phức tạp, chúng ta đưa ra yêu cầu bài toán và yêu cầu LLM "step-back" (lùi lại) để suy nghĩ về khái niệm, nguyên tắc, đưa ra chiến lược, phương pháp giải quyết bài toán ban đầu. Sau khi hài lòng với chiến lược của LLM, chúng ta mới đưa ra yêu cầu cho LLM để áp dụng chiến lược đó giải quyết bài toán ban đầu.


Ví dụ:

**User**\
Câu hỏi gốc:\
Viết một hàm kiểm tra xem chuỗi có phải là hoán vị của một palindrome hay không (ví dụ: “civic”, “ivicc”).

Step-back Prompt:\
“Trước tiên, hãy mô tả cách suy nghĩ để giải quyết bài toán này.”

**AI (step-back)**\
Một chuỗi là hoán vị của palindrome nếu tối đa chỉ có một ký tự xuất hiện lẻ số lần. Tôi cần đếm tần suất từng ký tự và kiểm tra số lượng ký tự lẻ.

**User**\
Bây giờ, hãy áp dụng chiến lược trên để viết code bằng Python.

**AI**\
```python
def is_permutation_of_palindrome(s):
    freq = Counter(s)
    odd_count = sum(1 for count in freq.values() if count % 2 != 0)
    return odd_count <= 1
```

# Chain of thought (CoT)
Chain of Thought (CoT) là kỹ thuật prompting trong đó LLM được yêu cầu suy luận từng bước một để giải quyết vấn đề, thay vì trả lời ngay lập tức. CoT phù hợp cho các tác vụ cần suy luận nhiều bước hoặc giải bài toán logic/phép toán phức tạp. Ý tưởng chính là buộc mô hình "nói ra suy nghĩ" và qua đó mô hình tạo được hướng tư duy rõ ràng, giống như cách con người giải toán: phân tích đề, liệt kê giả thiết, thực hiện từng bước, rồi kết luận.

Bản chất LLM không phải là "suy luận logic" như con người, mà là dự đoán từ tiếp theo có xác suất cao nhất dựa trên tập dữ liệu huấn luyện. Do đó, với các bài toán logic hoặc tính toán, LLM thường dễ mắc lỗi nếu không được hướng dẫn từng bước cụ thể. CoT giúp mô hình tái hiện cách tư duy hợp lý hơn thông qua việc mô phỏng chuỗi suy luận.

Ví dụ:

**User**\
Tính tổng các số lẻ từ 1 đến 9. Hãy suy luận từng bước trước khi đưa ra kết quả.

**AI**\
Các số lẻ từ 1 đến 9 là: 1, 3, 5, 7, 9
Tổng các số đó là: 1 + 3 + 5 + 7 + 9 = 25

# Self-consistency
Self-consistency là một chiến lược giúp cải thiện chất lượng đầu ra của LLM bằng cách chạy nhiều lần một prompt theo dạng Chain of Thought, sau đó chọn câu trả lời xuất hiện nhiều nhất (most frequent answer).

Kỹ thuật này phù hợp khi tác vụ có tính không chắc chắn cao hoặc liên quan đến suy luận – ví dụ như giải toán, suy diễn logic, hoặc phân tích ngữ cảnh phức tạp. Thay vì chỉ lấy 1 câu trả lời, anh em để mô hình suy nghĩ theo nhiều hướng khác nhau, rồi chọn ra kết quả cuối cùng dựa trên xác suất.

Ví dụ:

**User**\
Viết chương trình đếm số ký tự khác nhau trong chuỗi “banana”. Chạy 3 lần và chọn kết quả xuất hiện nhiều nhất.

**AI**\
Lần 1: Các ký tự khác nhau là b, a, n → có 3 ký tự khác nhau.\
Lần 2: Các ký tự duy nhất là b, a, n → tổng cộng 3 ký tự.\
Lần 3: Đếm số ký tự duy nhất trong “banana” là 3.\

Kết quả xuất hiện nhiều nhất là: 3

# Tree of thoughts (ToT)
Tree of Thoughts (ToT) lấy Chain of Thought làm nền tảng, trong đó thay vì suy luận theo một hướng duy nhất, LLM suy nghĩ theo dạng cây – mỗi nhánh là một hướng tiếp cận khác nhau. ToT cho phép mô hình thử nhiều ý tưởng, phân nhánh logic, đánh giá từng hướng rồi chọn giải pháp tốt nhất.
Điểm mạnh của ToT là giúp giải quyết các bài toán sáng tạo, lập kế hoạch, tối ưu hóa hoặc những bài toán cần "tầm nhìn rộng". Thay vì chỉ đi theo một lối mòn, ToT cho mô hình quyền thử nhiều phương án trước khi kết luận.

Để áp dụng ToT, prompt nên khuyến khích mô hình đề xuất nhiều phương án, đánh giá ưu-nhược điểm của từng phương án, sau đó chọn hướng giải quyết tối ưu nhất.

Ví dụ:

**User**\
Viết kế hoạch kiểm tra đầu vào cho một hàm nhận chuỗi trong Python. Đưa ra 3 hướng tiếp cận, phân tích ưu nhược, chọn cách tốt nhất.

**AI**\

- Kiểm tra độ dài chuỗi (Ưu: nhanh; Nhược: không phát hiện lỗi ký tự)
- Kiểm tra ký tự hợp lệ (chỉ chứa chữ cái) (Ưu: đảm bảo an toàn; Nhược: tốn CPU)
- Kiểm tra bằng regex (Ưu: linh hoạt; Nhược: khó đọc)

→ Chọn cách 2 vì cân bằng tốt giữa bảo vệ và hiệu năng.

# ReAct (Reason and Act)
eAct là kỹ thuật prompting kết hợp giữa “Reasoning” (suy luận) và “Acting” (hành động), cho phép mô hình vừa suy nghĩ, vừa thực hiện hành động (như gọi hàm, truy vấn thông tin, hoặc in kết quả trung gian). Kỹ thuật này phù hợp cho các task yêu cầu tư duy phức tạp và khả năng tương tác như lập trình, truy vấn cơ sở dữ liệu, hay giải quyết bài toán từng bước có hành động xen kẽ.

Với ReAct, prompt thường yêu cầu mô hình vừa nêu lý do (Reason) cho mỗi hành động, rồi thực hiện hành động tương ứng (Act). Điều này giúp cải thiện độ rõ ràng, minh bạch và khả năng debug các lời giải của mô hình.

ReAct hữu ích trong môi trường agent hoặc các hệ thống yêu cầu tác nhân suy nghĩ rồi quyết định bước tiếp theo.

Ví dụ:

**User**\
Tôi cần kiểm tra xem một số có chia hết cho 3 không. Hãy giải thích trước, rồi viết mã Python kiểm tra.

**AI**\
Reason: Để biết một số chia hết cho 3, ta kiểm tra nếu number % 3 == 0.
Act:

```python
def is_divisible_by_3(number):
    return number % 3 == 0
```


## Lời kết
Hiểu rõ cách các yếu tố như model, training data, configs, context và cách viết prompt ảnh hưởng đến đầu ra sẽ giúp anh em khai thác tối đa sức mạnh của các mô hình ngôn ngữ lớn. Bằng cách áp dụng những nguyên tắc này, anh em có thể tạo ra prompt chất lượng hơn, cho phản hồi chính xác và giá trị hơn.

**Prompt engineering không chỉ là kỹ thuật – đó là nghệ thuật giao tiếp hiệu quả với AI.**

