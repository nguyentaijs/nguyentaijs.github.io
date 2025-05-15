---
layout: post
title: Better prompt enginerring - Prompt techniques
categories: [ discussion, prompt_engineering, AI, LLM ]
tags: [ discussion, prompt_engineering, AI, LLM ]
date: 2025-04-28 19:00 +0700
---

![cover]({{ site.baseurl }}/assets/img/prompting-techniques.png)

# Zero shot / General prompting
## Description
**Zero-shot prompting** là sử dụng prompt để giao cho LLM 1 task yêu cầu nó thực hiện và không cung cấp thêm thông tin gì.

## Pros
- Ngắn gọn, dễ thực hiện.
- Thích hợp với các tác vụ phổ biến (dịch thuật, định nghĩa, giải thích…).

## Cons
- Dễ bị hiểu sai yêu cầu nếu task không rõ ràng.
- Output có thể không ổn định hoặc không đúng format mong muốn.

## Usecase
- Khi task đơn giản hoặc quen thuộc với mô hình.
- Khi muốn tiết kiệm dung lượng prompt.
- Đặt câu hỏi mở, yêu cầu viết đoạn văn, định nghĩa khái niệm, dịch thuật.

## Example
**User**\
Explain the origin of the name 'Python' in the context of the Python programming language

**AI**\
The Python programming language was named after the British comedy group Monty Python.
Guido van Rossum, the creator of Python, was a big fan of Monty Python's Flying Circus, and he wanted a name for his new language that was short, unique, and a little bit mysterious.\
He didn't want the name to be too serious or technical. He was looking for a successor name of the ABC programming language, of which the working title was Advanced B. Then the language was getting ready to release. He thought that "Python" was a suitable name that stood out. Ultimately, the naming of Python was simply a nod to a comedic influence in its creator's life, showing a bit of whimsy in the world of programming.

# One-shot / few-shot
## One-shot

### Description
**One-shot** là kiểu prompting mà anh em cung cấp cho LLM 1 task kèm theo 1 ví dụ để thực hiện. Model có thể dựa vào ví dụ được cung cấp để hiểu hơn về yêu cầu và format của kết quả mong muốn.

### Pros
- Định hình được đầu ra rõ hơn so với zero-shot.
- Giảm rủi ro hiểu sai task.

### Cons
- Nếu ví dụ không đủ rõ ràng, mô hình có thể  hiểu sai pattern.
- Một ví dụ có thể không đủ cho những task phức tạp.

### Usecase
- Sinh code, xử lý văn bản, phân loại đơn giản, định dạng output.

### Example
**User**\
Example: Function to calculate the area of a rectangle in Python\
Input: width = 5, height = 10\
 Output: 50

Function to calculate the area of a triangle in Python\
Input: base = 4, height = 6\
Output:

## Few-shot
### Description
Few-shot sử dụng nhiều example hơn để làm rõ mối quan hệ của input-output và pattern output kỳ vọng cho task. Few-shot phù hợp với các yêu cầu phức tạp, cần đưa ra các case cho nhiều trường hợp và các giá trị biên dùng làm tham chiếu cho LLM.

### Pros
- Học từ pattern trong các ví dụ.
- Giảm thiểu lỗi suy diễn sai của one-shot prompting.
- Có thể xử lý các edge cases nếu cung cấp đúng và đủ các ví dụ.

### Usecase
- Task phức tạp hoặc đòi hỏi LLM tự tìm ra quy luật dựa trên các ví dụ.
- Khi cần đảm bảo format đầu ra thống nhất.
- Xử lý dữ liệu phi cấu trúc, trích xuất thông tin có quy luật.

### Example
**User**\
Example:

Input = 2, 2 Expecting output 4\
Input = 4, 2 Expecting output 6\
write a python function comfort above example

**AI**
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

Nếu chỉ đưa một ví dụ như input = 2, 2 → output = 4, LLM có thể hiểu nhầm giữa phép cộng và phép nhân, dễ dẫn đến kết quả không đúng kỳ vọng. Đây cũng là nhược điểm của one-shot prompting đã nêu ở trên.\
Ngược lại, khi dùng few-shot prompting với đầy đủ ví dụ, mô hình có thể nhận ra quy luật. Dù yêu cầu chung chung như "function comfort above example", đủ ví dụ giúp LLM suy luận đúng logic.\
Do đó, số lượng và chất lượng ví dụ là yếu tố then chốt quyết định độ chính xác của mô hình.

# System, contextual and role prompting
**System prompting** là quá trình cung cấp hướng dẫn, thêm thông tin và các chỉ dẫn cần thiết để LLM hiểu và định hướng kết quả kỳ vọng. Định nghĩa big picture task mà LLM phải thực hiện như dịch thuật, phân loại, etc.\
**Contextual prompting** là quá trình cung cấp ngữ cảnh cụ thể liên quan đến task được LLM yêu cầu thực hiện. Giúp LLM hiểu rõ hơn nội dung đang được đề cập.\
**Role prompting** là quá trình gán cho LLM 1 vai trò cụ thể để định hướng phong cách, giọng điệu và kiến thức cơ sở của LLM.

3 techniques này không triệt tiêu lẫn nhau mà bổ trợ cho nhau, giúp LLM hiểu rõ về yêu cầu và kỳ vọng output mong muốn của anh em.

## Pros
- Cung cấp đủ thông tin và kỳ vọng đầu ra do đó kết quả có độ chính xác, nhất quán cao.
- Kiểm soát được giọng điệu và tính tự nhiên của output.

## Cons
- Prompt phức tạp

## Usecase
- Đóng vai nhân vật, chuyên gia, tạo chatbot theo vai trò cụ thể.
- Có thể ứng dụng để integrate thông qua API (có thể sử dụng system prompting để mô tả JSON output contract)

## Example

**User**\
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

## Pros
- Hiểu được cách model xử lý yêu cầu.
- Có thể can thiệp, tinh chỉnh giúp model tư duy, xử lý đúng kỳ vọng.

## Cons
- Phức tạp
- Không phù hợp với các yêu cầu đơn giản.

## Ứng dụng
- Task đòi hỏi tư duy chiến lược, output rõ ràng, có logic.
- Giải toán, lập kế hoạch, đánh giá rủi ro.

## Example

**User**\
Câu hỏi gốc:\
Viết một hàm kiểm tra xem chuỗi có phải là hoán vị của một palindrome hay không (### Ví dụ: “civic”, “ivicc”).

Step-back Prompt:\
“Trước tiên, hãy mô tả cách suy nghĩ để giải quyết bài toán này.”

**AI (step-back)**\
Một chuỗi là hoán vị của palindrome nếu tối đa chỉ có một ký tự xuất hiện lẻ số lần. Tôi cần đếm tần suất từng ký tự và kiểm tra số lượng ký tự lẻ.

**User**\
Bây giờ, hãy áp dụng chiến lược trên để viết code bằng Python.

**AI**
```python
def is_permutation_of_palindrome(s):
    freq = Counter(s)
    odd_count = sum(1 for count in freq.values() if count % 2 != 0)
    return odd_count <= 1
```

# Chain of thought (CoT)
Chain of Thought (CoT) là kỹ thuật prompting trong đó LLM được yêu cầu suy luận từng bước một để giải quyết vấn đề, thay vì trả lời ngay lập tức. CoT phù hợp cho các tác vụ cần suy luận nhiều bước hoặc giải bài toán logic/phép toán phức tạp. Ý tưởng chính là buộc mô hình "nói ra suy nghĩ" và qua đó mô hình tạo được hướng tư duy rõ ràng, giống như cách con người giải toán: phân tích đề, liệt kê giả thiết, thực hiện từng bước, rồi kết luận.

Bản chất LLM không phải là "suy luận logic" như con người, mà là dự đoán từ tiếp theo có xác suất cao nhất dựa trên tập dữ liệu huấn luyện. Do đó, với các bài toán logic hoặc tính toán, LLM thường dễ mắc lỗi nếu không được hướng dẫn từng bước cụ thể. CoT giúp mô hình tái hiện cách tư duy hợp lý hơn thông qua việc mô phỏng chuỗi suy luận.

## Pros
- Giúp anh em hiểu các thực hiện.
- Giúp model suy luận rõ ràng, output có độ tin cậy cao.

## Cons
- Phức tạp, tốn chi phí token.
- Không phù hợp với các task đơn giản, không cần giải thích.

## Usecase
- Task cần nhiều bước logic (toán học, lập luận, phân tích).
- Khi muốn tăng độ chính xác của output.

## Example (Simple)

**User**\
Cho yêu cầu như sau:\
Một công ty tổ chức phỏng vấn với 5 ứng viên (A, B, C, D, E) theo thứ tự khác nhau trong ngày. Biết rằng:\
A được phỏng vấn trước C.\
B không phải là người đầu tiên.\
D được phỏng vấn sau E nhưng trước A.\
Hỏi thứ tự đúng của các ứng viên là gì?\

Suy luận từng bước trước khi đưa ra câu trả lời cuối cùng.

**AI**\
Có 5 người: A, B, C, D, E cần được sắp xếp theo thứ tự từ 1 đến 5.
Giả thiết 1: A < C → A phỏng vấn trước C.\
Giả thiết 2: B ≠ 1 → B không ở vị trí đầu tiên.\
Giả thiết 3: E < D < A → E phỏng vấn trước D, và D trước A.\
→ Suy ra: E < D < A < C (từ giả thiết 1 và 3).\

Có 5 người, nên các vị trí là 1 đến 5.\
Hãy thử gán các vị trí từ giả thiết:\
Giả sử E = 1 → D = 2 → A = 3 → C = 4 → Còn lại B = 5\
✅ Thỏa:\
- A < C (3 < 4) → OK
- B ≠ 1 → B = 5 → OK
- E < D < A (1 < 2 < 3) → OK

👉 Không có mâu thuẫn nào, đây có vẻ là đáp án duy nhất hợp lệ.

🎯 Kết quả cuối cùng:\
Thứ tự phỏng vấn là: E, D, A, C, B

# Self-consistency
Self-consistency là một chiến lược giúp cải thiện chất lượng đầu ra của LLM bằng cách chạy nhiều lần một prompt theo dạng Chain of Thought, sau đó chọn câu trả lời xuất hiện nhiều nhất (most frequent answer).

Kỹ thuật này phù hợp khi tác vụ có tính không chắc chắn cao hoặc liên quan đến suy luận – ví dụ như giải toán, suy diễn logic, hoặc phân tích ngữ cảnh phức tạp. Thay vì chỉ lấy 1 câu trả lời, anh em để mô hình suy nghĩ theo nhiều hướng khác nhau, rồi chọn ra kết quả cuối cùng dựa trên xác suất.

## Pros
- Tăng xác suất output chính xác.
- Giảm sai số do random hoặc bias.

## Cons
- Tốn tài nguyên (do chạy nhiều lần).
- Không phù hợp với các task đơn giản.

## Usecase
- Task có nhiều khả năng gây nhầm lẫn hoặc output không ổn định.
- Khi chấp nhận trade-off hiệu suất để tăng độ tin cậy.

## Example

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

## Pros
- Tăng khả năng tìm được giải pháp tối ưu.

## Cons
- Phức tạp, tốn thời gian + token.
- Cần có hiểu biết để hướng dẫn model phân nhánh.

## Use case
- Task sáng tạo, lập kế hoạch, tối ưu hóa.
- Khi cần tư duy sáng tạo và so sánh nhiều phương án.

## Example

**User**\
Viết kế hoạch kiểm tra đầu vào cho một hàm nhận chuỗi trong Python. Đưa ra 3 hướng tiếp cận, phân tích ưu nhược, chọn cách tốt nhất.

**AI**\

- Kiểm tra độ dài chuỗi (Ưu: nhanh; Nhược: không phát hiện lỗi ký tự)
- Kiểm tra ký tự hợp lệ (chỉ chứa chữ cái) (Ưu: đảm bảo an toàn; Nhược: tốn CPU)
- Kiểm tra bằng regex (Ưu: linh hoạt; Nhược: khó đọc)

→ Chọn cách 2 vì cân bằng tốt giữa bảo vệ và hiệu năng.

# ReAct (Reason and Act)
**ReAct** là kỹ thuật prompting kết hợp giữa “Reasoning” (suy luận) và “Acting” (hành động), cho phép mô hình vừa suy nghĩ, vừa thực hiện hành động (như gọi hàm, truy vấn thông tin, hoặc in kết quả trung gian). Kỹ thuật này phù hợp cho các task yêu cầu tư duy phức tạp và khả năng tương tác như lập trình, truy vấn cơ sở dữ liệu, hay giải quyết bài toán từng bước có hành động xen kẽ.

Với ReAct, prompt thường yêu cầu mô hình vừa nêu lý do (Reason) cho mỗi hành động, rồi thực hiện hành động tương ứng (Act). Điều này giúp cải thiện độ rõ ràng, minh bạch và khả năng debug các lời giải của mô hình.

ReAct hữu ích trong môi trường agent hoặc các hệ thống yêu cầu tác nhân suy nghĩ rồi quyết định bước tiếp theo.

## Pros
- Dễ kiểm tra từng bước reasoning.
- Có thể tương tác với các tool để thu thập thông tin

## Cons
- Phức tạp

## Usecase
- Task phức tạp, cần tương tác với các tool bên ngoài để thu thập thông tin.


## Example

**User**\
Cho tôi biết thời tiết hiện tại ở Hà Nội, và tôi cần biết có nên mang ô không.

**AI**\
Thought: Tôi cần kiểm tra thời tiết hiện tại ở Hà Nội để đưa ra lời khuyên.\
Action: CallWeatherAPI("Hanoi")

Observation: Thời tiết ở Hà Nội hiện tại: Mưa nhỏ, nhiệt độ 24°C.\
Thought: Trời đang mưa, nên người dùng nên mang ô.

Action: Respond("Thời tiết ở Hà Nội đang mưa nhẹ (24°C). Bạn nên mang theo ô khi ra ngoài.")

# Lời kết
Bài viết này tổng hợp những kỹ thuật prompting cơ bản, giúp anh em hiểu rõ hơn cách tương tác hiệu quả với model. Tuy nhiên, tôi vẫn đang trong quá trình tìm hiểu, nên nội dung có thể chưa đủ sâu và cũng chưa thể bao quát hết toàn bộ các phương pháp.

Một số kỹ thuật như `automatic prompt engineering`, `code prompting`, hay các phương pháp kết hợp với công cụ ngoài (`tool-augmented prompting`) vẫn chưa được đề cập xin nhường lại cho các chiên za master mảng prompting này.

Hy vọng bài viết này hữu ích với anh em dev đang bắt đầu khám phá thế giới prompt engineering. Nếu có góp ý, ví dụ hay tài liệu hay ho, rất mong được anh em chia sẻ!

**Prompt engineering không chỉ là kỹ thuật – đó là nghệ thuật giao tiếp hiệu quả với AI.**

-----

Nếu anh em cảm thấy bài viết hữu ích đừng ngần ngại click upvote cho bài viết, hoặc phát hiện ý nào chưa hợp lý hoặc cần giải thích thêm hãy comment cho tôi biết để cùng trao đổi nhé.
Anh em có thể tham khảo các bài viết khác của tôi tại [Blog cái nhân](https://nguyentaijs.github.io/) hoặc kết nối với tôi qua [Linkedin](https://www.linkedin.com/in/nguyentaijs)

