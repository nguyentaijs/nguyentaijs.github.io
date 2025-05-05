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
User
> Explain the origin of the name 'Python' in the context of the Python programming language

AI
> The Python programming language was named after the British comedy group Monty Python.
 Guido van Rossum, the creator of Python, was a big fan of Monty Python's Flying Circus, and he wanted a name for his new language that was short, unique, and a little bit mysterious.\
 He didn't want the name to be too serious or technical. He was looking for a successor name of the ABC programming language, of which the working title was Advanced B. Then the language was getting ready to release. He thought that "Python" was a suitable name that stood out. Ultimately, the naming of Python was simply a nod to a comedic influence in its creator's life, showing a bit of whimsy in the world of programming.

# One-shot / few-shot
One-shot là kiểu prompting mà anh em cung cấp cho LLM 1 task kèm theo 1 ví dụ để thực hiện.

Ví dụ:
User
> Example: Function to calculate the area of a rectangle in Python\
> Input: width = 5, height = 10\
>  Output: 50
>
> Function to calculate the area of a triangle in Python\
> Input: base = 4, height = 6\
> Output:

Few-shot sử dụng nhiều example hơn để làm rõ mối quan hệ của input-output và pattern output kỳ vọng cho task. Few-shot phù hợp với các yêu cầu phức tạp, cần đưa ra các case cho nhiều trường hợp và các giá trị biên dùng làm tham chiếu cho LLM.\

User
>Example:
>
>Input = 2, 2 Expecting output 4
>
> Input = 4, 2 Expecting output 6
> write a python function comfort above example

AI
> def comfort(a, b):
> """
> This function takes two numbers as input and returns their sum.
>
> Args:
>   a: The first number.
>   b: The second number.
>
> Returns:
>   The sum of a and b.
> """
> return a + b

Mặc dù chỉ đưa ra yêu cầu chung chung là "function comfort above example", nhưng với đủ ví dụ, LLM có thể hiểu được pattern chung của 2 ví dụ trên là phép tính tổng 2 input đầu vào.\
Phương pháp này hữu ích khi anh em muốn LLM tự tìm ra quy luật dựa vào các ví dụ mà anh em cung cấp. Số lượng ví dụ và chất lượng của ví dụ sẽ ảnh hưởng đến độ chính xác đầu ra của LLM.

# System, contextual and role prompting
System prompting là quá trình cung cấp hướng dẫn, thêm thông tin và các chỉ dẫn cần thiết để LLM hiểu và định hướng kết quả kỳ vọng. Định nghĩa big picture task mà LLM phải thực hiện như dịch thuật, phân loại, etc.\
Contextual prompting là quá trình cung cấp ngữ cảnh cụ thể liên quan đến task được LLM yêu cầu thực hiện. Giúp LLM hiểu rõ hơn nội dung đang được đề cập.\
Role prompting là quá trình gán cho LLM 1 vai trò cụ thể để định hướng phong cách, giọng điệu và kiến thức cơ sở của LLM.

3 techniques này không triệt tiêu lẫn nhau mà bổ trợ cho nhau, giúp LLM hiểu rõ về yêu cầu và kỳ vọng output mong muốn của anh em.

Ví dụ:
>You are a Python code reviewer specializing in data analysis. Provide a *bulleted* list (max 5 points) of actionable improvements for the code. Focus on error handling, robustness, and clarity. Avoid generic suggestions. (System prompting)
>
>This code calculates the average product price from a daily-generated CSV (`product_prices.csv`). The CSV is mostly clean, but may contain missing prices, currency symbols, or empty lines. Target user: junior data analysts. (Contextual prompting)
>
>As a senior Python developer, review the code for reliability and ease-of-use by junior analysts, considering the potential CSV issues. (Role prompting)
>
>```python
>def calculate_average_price(filename):
>    """Calculates the average price from a CSV file using pandas."""
>    df = pd.read_csv(filename)
>    average_price = df['Price'].mean()
>    return average_price

# Step-back prompting

# Chain of thought (CoT)

# Self-consistency

# Tree of thoughts (ToT)

# ReAct (Reason and Act)

# Automatic prompt Engineering

# Code prompting





## Lời kết
Hiểu rõ cách các yếu tố như model, training data, configs, context và cách viết prompt ảnh hưởng đến đầu ra sẽ giúp anh em khai thác tối đa sức mạnh của các mô hình ngôn ngữ lớn. Bằng cách áp dụng những nguyên tắc này, anh em có thể tạo ra prompt chất lượng hơn, cho phản hồi chính xác và giá trị hơn.

**Prompt engineering không chỉ là kỹ thuật – đó là nghệ thuật giao tiếp hiệu quả với AI.**

