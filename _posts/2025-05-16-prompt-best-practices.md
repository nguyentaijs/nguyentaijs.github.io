---
layout: post
title: Better prompt engineering - Best practices từ kỹ sư của Google
categories: [ discussion, prompt_engineering, AI, LLM ]
tags: [ discussion, prompt_engineering, AI, LLM ]
date: 2025-05-16 22:00 +0700
---

![cover]({{ site.baseurl }}/assets/img/better-prompt-engineering-best-practices.png)

Trong chuỗi bài viết về Prompt Engineering được tôi tổng hợp lại từ tài liệu của bác [Lee Boonstra](https://www.linkedin.com/in/leeboonstra) (Software Engineer Tech Lead tại Google, Office of the CTO), tôi đã chia sẻ về những kiến thức nền tảng và kỹ thuật xây dựng prompt hiệu quả. Nếu anh em chưa đọc thì có thể tham khảo lại hai bài viết trước đó để nắm được bức tranh tổng thể trước khi đi sâu vào nội dung của bài viết này.

- [Better prompt engineering - Factors influencing LLM Output](https://viblo.asia/p/lam-sao-de-viet-prompt-hieu-qua-hon-p1-factors-influencing-llm-output-Rk74aX5kVeO)
- [Better prompt engineering - Prompt techniques](https://viblo.asia/p/better-prompt-engineering-prompt-techniques-018J2bo0JYK)

Bài viết này sẽ tập trung vào phần **best practices** được các kỹ sư của Google áp dụng khi viết prompt. Tuy là các nguyên tắc đơn giản, nhưng nếu áp dụng đúng cách, anh em sẽ thấy chất lượng output cải thiện rõ rệt – từ độ chính xác cho đến tính nhất quán. Hy vọng sau bài viết này, anh em sẽ có thêm một “bộ công cụ” vững chắc để làm việc hiệu quả hơn với LLM.


**Bài viết sẽ cover các techniques sau**
- Sử dụng example
- Sử dụng các keyword dễ hiểu
- Instructions over Constraints
- Kiểm soát max token length
- Sử dụng variables
- Sử dụng schemas

# I. Sử dụng example
Một trong những lựa chọn đơn giản nhất mà hiệu quả nhất để cải thiện prompt là cung cấp example. Dựa vào example, model sẽ có hiểu biết rõ hơn, chính xác và đầy đủ hơn về yêu cầu và format của kết quả mong muốn.

Vì LLM là mô hình dự đoán dựa vào xác suất nên example được xem như một khung cấu trúc để model dựa vào từ đó tự generate ra kết quả đáp ứng yêu cầu của prompt. Thông qua ví dụ, model còn có thể cải thiện được độ chính xác, phong cách, ngữ điệu (nếu có) sao cho sát nhất với ví dụ của prompt đưa ra.

**DONT**\
Viết một hàm Python tên là `group_users_by_city` để chuyển đổi danh sách người dùng thành một JSON mới, trong đó các người dùng được nhóm theo thành phố.

**DO**\
Viết một hàm Python tên là `group_users_by_city` để chuyển đổi danh sách người dùng thành một JSON mới, trong đó các người dùng được nhóm theo thành phố.\
Input là một list các dictionary, mỗi dictionary đại diện cho một dòng dữ liệu.\
Output là một valid JSON.

Input example
```JSON
[
    {"name": "Alice", "age": 30, "city": "Hanoi"},
    {"name": "Bob", "age": 25, "city": "Saigon"},
    {"name": "Charlie", "age": 28, "city": "Hanoi"}
]
```
Output example
```JSON
{
  "Hanoi": [
    {"name": "Alice", "age": 30},
    {"name": "Charlie", "age": 28}
  ],
  "Saigon": [
    {"name": "Bob", "age": 25}
  ]
}
```
# II. Sử dụng các keyword dễ hiểu 
Nếu anh em tự mình đọc prompt của chính mình mà không thể hiểu nó đang cung cấp thông tin gì, cho mục đích gì thì điều đó cũng có nghĩa là model cũng khó có thể hiểu được ý của anh em.
Để anh em có thể giải thích mong muốn của mình cho người khác thì trước tiên anh em phải tự phát biểu được mong muốn đấy cho chính mình.\
Anh em cố gắng không dùng những từ ngữ quá phức tạp, hàn lâm và cũng không cung cấp thừa thông tin làm nhiễu suy luận của model.

Một số từ được recommend để mô tả hành động cho model anh em có thể tham khảo cho prompt của mình

**Understanding & interpretation** - Hiểu biết và giải thích
- Describe (Mô tả)
- Define (Định nghĩa)
- Compare (So sánh)
- Contrast (Phân biệt)
- Summarize (Tóm tắt)
- Show (Hiển thị / Trình bày)
- Translate (Dịch)

**Analysis & Reasoning** - Phân tích và Lý luận
- Analyze (Phân tích)
- Classify (Phân loại)
- Categorize (Xếp loại)
- Identify (Nhận diện)
- Evaluate (Đánh giá)
- Measure (Đo lường)
- Parse (Phân tích cú pháp)
- Find (Tìm kiếm)
- Extract (Trích xuất)
- Retrieve (Truy xuất)

**Decision & Judgment** - Ra quyết định và Phán đoán
- Pick (Chọn)
- Select (Lựa chọn)
- Rank (Xếp hạng)
- Recommend (Đề xuất)
- Predict (Dự đoán)

**Creation & Generation** - Tạo nội dung
- Create (Tạo ra)
- Generate (Sinh ra)
- Write (Viết)
- Rewrite (Viết lại)
- Provide (Cung cấp)
- Return (Trả về)
- Act (Thực hiện hành động)

**Organization & Structuring** - Tổ chức và Sắp xếp
- Organize (Sắp xếp)
- Sort (Phân loại / Sắp xếp)
- List (Liệt kê)

# III. Instructions over Constraints
Instruction cung cấp chỉ dẫn cho model hoàn thành task được giao, mang hơi hướng tích cực, tạo không gian khuyến khích AI sáng tạo\
Constraint đưa ra các ràng buộc yêu cầu model không được vi phạm các ràng buộc này, mang hơi hướng tiêu cực, giới hạn khả năng của AI\

**Vì sao nên dùng Instructions?**
- Giúp AI hiểu rõ mục tiêu và cách làm.
- Kết quả chính xác và đúng ý hơn.
- Giảm rủi ro AI hiểu sai yêu cầu.

Nếu có thể, anh em hãy viết prompt cung cấp instruction một cách tích cực cho model biết cần phải làm gì, thay vì đưa ra các nguyên tắc yêu cầu model không được vi phạp.\
Cùng xem ví dụ dưới đây

**DONT**\
Giải thích khái niệm LLM\
Đừng giải thích dài dòng, đừng dùng thuật ngữ chuyên ngành.

**DO**\
Giải thích khái niệm LLM\
Giải thích ngắn gọn, dưới 100 từ, dùng ngôn ngữ đơn giản mà người không chuyên có thể hiểu được.

Anh em có thể ứng dụng best practice này cho đa phần các prompt để giúp AI hiểu rõ mong muốn và cách thức xử lý task. Tuy vậy, với những model có tính sáng tạo cao (Temperature) nhưng mong muốn output nhất quán, đúng khuôn mẫu; sử dụng constraint sẽ giúp anh em kiểm soát điều đó.\
Ví dụ yêu cầu model chỉ ouptut theo định đạng cho trước như JSON, yêu cầu viết code không sử dụng thư viện X, Y, Z, etc. Do đó, anh em cần ứng dụng linh hoạt tùy vào use case của mình.

# IV. Kiểm soát max token length
Kiểm soát độ dài token tối đa (max_tokens) khi viết prompt là rất quan trọng với túi tiền của anh em.
Đặt max_tokens hợp lý sẽ giúp anh em tối ưu hóa kết quả và chi phí:

- Nếu anh em set max_tokens quá thấp: sẽ khiến câu trả lời bị cắt cụt, thiếu ý, hoặc không hoàn chỉnh.
- Nếu anh em set max_tokens quá cao: lãng phí tài nguyên hoặc khiến AI "cố" viết dài dòng không cần thiết, dù anh em đã yêu cầu ngắn gọn.

Do đó, anh em nên ước lượng độ dài cần thiết cho câu trả lời và đặt max_tokens cao hơn một chút so với dự kiến để đảm bảo tính trọn vẹn.\
Config này chủ yếu được set trong code lúc gửi request sang LLM, ngoài ra ChatGPT playground, Google AI studio cũng cung cấp giao diện trực quan để anh em tinh chỉnh max_tokens.\
Các ứng dụng chat bot phổ thông như ChatGPT không cung cấp option cho anh em config max_tokens, tuy vậy anh em có thể workaround bằng cách đưa ra các yêu cầu rõ ràng như
- Tóm tắt ABC trong 1 câu
- Tổng hợp DEF bằng 3 gạch đầu dòng
- Giải thích XYZ ngắn gọn dưới 50 từ

# V. Sử dụng variables
Sử dụng variables giúp anh em tái sử dụng prompt. Thay vì viết nhiều prompt tương tự nhau, anh em chỉ cần viết một prompt với các variables có thể thay đổi, rồi truyền giá trị sau.
Một số điểm cần lưu ý khi sử dụng variables trong prompt
- Đặt tên biến rõ ràng - task khó nhất với ae dev :))
- Có thể kết hợp nhiều biến trong một prompt.
- Tránh lồng biến phức tạp gây khó hiểu, khó maintain.

**Ví dụ**\
Cung cấp prompt: Viết mô tả cho sản phẩm: {product_name}. Đối tượng khách hàng là {customer_type}. Hãy viết với giọng văn {tone}, tập trung vào lợi ích chính và lý do nên mua.\
Sau đó, anh em upload `.csv` file chứa danh sách sản phẩm với format như sau
```
product_name,customer_type,tone
Máy lọc không khí,gia đình,thân thiện
Laptop chơi game,game thủ,nhiệt huyết
Sách giáo dục trẻ em,phụ huynh,bình tĩnh
```

Sử dụng variables giúp anh em tự động hoá, tiết kiệm thời gian và xử lý các yêu cầu dạng batch dễ dàng hơn.

# VI. Sử dụng schemas
Sử dụng JSON không chỉ hữu ích để định dạng output mà nó còn rất hiệu quả trong việc định dạng đầu vào của model. Khi được cung cấp schema, model sẽ hiểu rõ cấu trúc và kiểu dữ liệu của input, do đó, có thể giảm nguy cơ model hiểu sai ý nghĩa của input.

Một ví dụ nho nhỏ là việc format thời gian, nếu không có schema định nghĩa rõ ràng trường `creation_date` có dạng `YYYY-MM-DD`, thì với input là `2025-02-05`, model có thể hiểu đây là ngày 2 tháng 5, và nó cũng có thể hiểu anh em đang truyền vào ngày 5 tháng 2 năm 2025.

**Ví dụ**\
Anh em định nghĩa input schema như sau
```
{
  "type": "object",
  "properties": {
    "name": {
      "type": "string",
      "description": "Product name"
    },
    "creation_date": {
      "type": "string",
      "format": "YYYY-MM-DD",
      "description": "Date the product was created"
    }
  }
}

```
Sau đó, anh em có thể feed JSON input cho model như sau
```
{
 "name": "Apple",
 "creation_date": "2025-02-05"
}
```
Lúc này, nếu anh em hỏi model trong tháng 2 có sản phẩm Apple nào được tạo không, thì đáp án chắc chắn là có, cho dù anh em có re-run bao nhiêu lần đi nữa, nhưng nếu không cung cấp schema, câu trả lời sẽ là hên xui, lúc có lúc không :v

# VII. Lời kết
Trong phạm vi bài viết này, tôi tập trung chọn lọc những best practices có tính ứng dụng cao, dễ áp dụng vào công việc thực tế của anh em khi làm việc với LLM, đặc biệt là các tác vụ tạo nội dung, phân tích hoặc xây dựng ứng dụng AI mức cơ bản.

Iài liệu gốc của bác Lee Boonstra còn đề cập đến nhiều kỹ thuật nâng cao khác như sử dụng JSON repair, các best practices khi viết CoT prompt. Cá nhân tôi nhận thấy những phần này không thực sự cần thiết với phần lớn use case phổ thông cuả tôi, nên đã chủ động lược bỏ để tránh làm loãng nội dung.

Nếu anh em quan tâm và muốn mở rộng thêm kiến thức, anh em hãy dành thời gian review lại tài liệu gốc, để hiểu thêm về kỹ thuật và các phương pháp nâng cao cho anh em chuyên prompt engineering tại [đây](https://www.kaggle.com/whitepaper-prompt-engineering)

**Prompt engineering không chỉ là kỹ thuật – đó là nghệ thuật giao tiếp hiệu quả với AI.**

-----

Nếu anh em cảm thấy bài viết hữu ích đừng ngần ngại click upvote cho bài viết, hoặc phát hiện ý nào chưa hợp lý hoặc cần giải thích thêm hãy comment cho tôi biết để cùng trao đổi nhé.
Anh em có thể tham khảo các bài viết khác của tôi tại [Blog cái nhân](https://nguyentaijs.github.io/) hoặc kết nối với tôi qua [Linkedin](https://www.linkedin.com/in/nguyentaijs)

