---
layout: post
title: Factors influencing LLM Output
categories: [ discussion, prompt_engineering, AI, LLM ]
tags: [ discussion, prompt_engineering, AI, LLM ]
date: 2025-04-24 23:00 +0700
---

![cover]({{ site.baseurl }}/assets/img/factors_infuencing_LLM_outptut.png)

### Model Used
Model được sử dụng ảnh hưởng lớn đến chất lượng của kết quả trả về. Các mô hình khác nhau có kiến trúc, khả năng và phương pháp huấn luyện khác nhau, dẫn đến các phản hồi khác nhau ngay cả khi sử dụng cùng 1 prompt.

Việc đầu tiên anh em cần làm để bắt đầu prompting là lựa chọn model, không có một chuẩn chung để optimize cho tất cả các model, mỗi model yêu cầu cách optimize khác nhau để có được kết quả tối ưu nhất.

### Model training data
Tập dữ liệu dùng để huấn luyện mô hình là yếu tố quan trọng quyết định output của LLM. Mức độ đa dạng, chất lượng của dữ liệu ảnh hưởng đến nền tảng kiến thức của mô hình và khả năng tạo ra phản hồi chính xác và phù hợp. Các mô hình được huấn luyện trên tập dữ liệu lớFactors influencing LLM Output - visual selection.pngn và đa dạng thường cho kết quả tốt hơn cho các câu hỏi dạng tổng quát.

### Model configuration
Các cấu hình như "temperature" hoặc "top-p" có thể thay đổi đáng kể kết quả đầu ra. Ví dụ, temperature cao có thể tạo ra những phản hồi sáng tạo và đa dạng hơn, trong khi temperature thấp sẽ cho phản hồi tập trung và có cơ sở hơn.
Anh em cần tìm ra cấu hình phù hợp bằng cách điều chỉnh và đánh giá để kiểm soát hành vi của mô hình đáp ứng với nhu cầu.
Dưới đây là một số config cho model cuả OpenAPI:

**1. Temperature**
- Điều chỉnh mức độ sáng tạo của câu trả lời.
- Config: 0 (cố định, ít sáng tạo) đến 2 (rất ngẫu nhiên). Default = 0

**2. Top_p**
- Lấy mẫu theo xác suất tích lũy (nucleus sampling), thay thế cho temperature.
- Config: 0 đến 1 - Giá trị càng nhỏ câu trả lời càng tập trung hơn.
- Không nên dùng temperature và top_p cao cùng lúc → dễ sinh ra văn bản kém ổn định.
- Dùng top_p thấp = kiểm soát tốt hơn, ít ngẫu nhiên hơn.

**3. Max_tokens**
- Giới hạn độ dài đầu ra (tính theo token).
- Tính tới thời điểm hiện tại, model mới nhất của OpenAI là `GPT-4.1` support đến 32,768 tokens

**4. Frequency_penalty**
- Phạt nếu lặp lại từ. Tức là: càng lặp lại nhiều → càng bị phạt mạnh hơn → càng ít khả năng xuất hiện lại.
- Config: -2.0 đến 2.0. Default = 0

**Ví dụ:**\
Giả sử model đang viết:\
"This is very very very good."\
Với `frequency_penalty` cao, model sẽ giảm xác suất chọn lại "very" lần thứ 3, và có thể thay bằng từ khác như "extremely".

**5. Presence_penalty**
- Điểm khác biệt với `frequency_penalty` là config này không quan tâm đến số lần xuất hiện, nó sẽ giảm xác suất chọn lại các từ đã từng xuất hiện, dù chỉ một lần.
- Do đó nó khuyến khích nội dung mới, đa dạng.
- Config: -2.0 đến 2.0.

**Ví dụ:**\
Nếu "happy" đã xuất hiện một lần, mô hình sẽ ưu tiên chọn từ khác, như "joyful" hay "cheerful".

### Prompt word choice
Từ ngữ được sử dụng trong prompt có thể định hướng phản hồi của mô hình theo nhiều hướng khác nhau. Việc dùng từ rõ ràng, chính xác giúp mô hình hiểu rõ yêu cầu, trong khi prompt mơ hồ có thể dẫn đến kết quả không nhất quán hoặc không phù hợp.

### Style and tone
Phong cách và giọng điệu của prompt cũng ảnh hưởng ít nhiều đến phản hồi. Prompt mang tính trang trọng hoặc thân mật sẽ khiến mô hình phản hồi theo phong cách tương tự. Điều quan trọng là cần giữ phong cách và giọng điệu nhất quán để phản hồi mạch lạc hơn.

### Structure
Cách tổ chức prompt -> ảnh hưởng đến cách mô hình hiểu và phản hồi. Prompt có cấu trúc càng rõ ràng sẽ càng làm rõ kỳ vọng và từ đó nâng cao chất lượng phản hồi.
- Đảm bảo cấu trúc câu hỏi và hướng dẫn trả lời rõ ràng.
- Giúp mô hình trả lời nhất quán, đúng định hướng.
- Tránh phản hồi lan man hoặc không đúng trọng tâm.
- Cấu trúc chuẩn thường gồm:
    - System message: Định nghĩa vai trò cho mô hình (VD: "Bạn là một junior developer...")
    - User message: Câu hỏi hoặc yêu cầu từ người dùng
    - Assistant message: Câu trả lời trước đó (giúp giữ ngữ cảnh nếu hội thoại nhiều lượt)
    - Instruction rõ ràng: Càng cụ thể càng tốt. Nên chỉ rõ đầu vào, đầu ra mong muốn.
    - Ví dụ: Gợi mẫu input → output để mô hình học theo cách trả lời

### Context
Cung cấp ngữ cảnh đặc biệt cần thiết để tạo ra phản hồi phù hợp, ngữ cảnh giúp mô hình hiểu rõ sắc thái và yêu cầu của người dùng. Càng cung cấp nhiều thông tin và chi tiết cuả context, mô hình càng dễ dàng tạo ra phản hồi chính xác, có ý nghĩa hơn.

**Ví dụ**
1. "Tôi là một junior Java developer, đang học Spring Boot."
2. "Tôi đang phát triển một ecommerce web, và cần thêm chức năng giỏ hàng."

Ngữ cảnh này cho mô hình biết được:
1. Người dùng là lập trình viên học lập trình.
2. Đang làm việc với Spring Boot và ecommerce web.
3. Việc cần làm tiếp theo là thêm chức năng giỏ hàng.

### Lời kết
Hiểu rõ cách các yếu tố như model, training data, configs, context và cách viết prompt ảnh hưởng đến đầu ra sẽ giúp anh em khai thác tối đa sức mạnh của các mô hình ngôn ngữ lớn. Bằng cách áp dụng những nguyên tắc này, anh em có thể tạo ra prompt chất lượng hơn, cho phản hồi chính xác và giá trị hơn.

**Prompt engineering không chỉ là kỹ thuật – đó là nghệ thuật giao tiếp hiệu quả với AI.**

