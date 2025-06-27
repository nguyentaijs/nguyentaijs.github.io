---
layout: post
title: Better prompt engineering - Pair programming with Coding Agent
categories: [ discussion, prompt_engineering, AI, LLM ]
tags: [ discussion, prompt_engineering, AI, LLM ]
date: 2025-06-21 23:09 +0700
---

![cover]({{ site.baseurl }}/assets/img/best-practices-coding-agent-cover-image.png)

Chào anh em, lại là tôi với series [Better Prompt Engineering](https://viblo.asia/s/better-prompt-engineering-018J23AlLYK) đây.\
Hôm nay tôi và anh em cùng tìm hiểu một vài best practices để tương tác với Coding agent (Cursor, Windsurf, etc).

**Coding agent** về cơ bản là anh em tương tác với một bạn senior kiến thức rất sâu, nhiệt tình, chăm chỉ, nhưng điểm yếu là bạn này là **không hiểu requirement của project**, hay quên, thường xuyên **sáng tạo** và **over-engineering**. Ngược lại, anh em là người nắm rõ yêu cầu của project, nhiệm vụ của anh em là **pair** với anh bạn này, hướng dẫn bạn code sao cho đúng với kỳ vọng của anh em.
Một trong những vấn đề lớn nhất của Coding Agent mà anh em cần xử lý là kiểm soát sức mạnh của model. Anh em đã từng sử dụng các Coding Agent như Cursor chắc không lạ lẫm gì với **YOLO Mode**, chỉ cần đưa ra 1 yêu cầu không rõ ràng, anh em có thể biến code base hiện tại của mình thành 1 phiên bản khác mà chỉ anh bạn thần thánh kia có thể hiểu được chứ người thường thì bó tay.\
Như trong ví dụ dưới đây, **17** files được edit, thêm **1398** dòng và xoá 30 dòng code.

![messy code]({{ site.baseurl }}/assets/img/best-practices-coding-agent-mess-code.png)

Ở đây anh em có 2 lựa chọn:

- Một là anh em ngồi lại review từng dòng code và tìm cách hiểu bạn Agent đã làm gì, có đáp ứng yêu cầu của bài toán không, có ảnh hưởng tới các function hiện tại đang chạy ổn không, etc...
- Cách làm thứ hai, anh em đọc được 1 2 đọan code change rồi nhắm mắt nhắm mũi nhấn accept all và hô to **"in Cursor we trust"**

Dù anh em đi theo hướng nào đi nữa thì đây vẫn là 1 trong những dấu hiệu cho thấy anh em đang không làm việc vói Agent đúng cách và hiệu quả.
Trong bài viết này, tôi chia sẻ cho anh em 1 vài bí kíp để anh em tham khảo để làm chủ sức mạnh của agent, đáp ứng 2 tiêu chí đúng và đủ.\
Let's go~

---

# I. Plan.md - Lập kế hoạch rõ ràng
Một trong những cách đơn giản nhất để control model là yêu cầu agent trình bày và giải thích kế hoạch mà nó sắp thực hiện.
Lợi thế của phương pháp này là giúp anh em chậm lại 1 chút, review cách làm, map với requirement xem thực sự đã đáp ứng được yêu cầu chưa.
Ngoài ra, đây cũng là cơ hội để anh em giao tiếp với model, giúp anh em align với agent trước khi agent thực sự động thủ. Khi yêu cầu viết plan anh em nên follow những point sau

### 1. Rõ Ràng và Cụ Thể

*    Sử dụng ngôn ngữ đơn giản, rõ ràng để mô tả yêu cầu tính năng.
*    Nêu rõ mục tiêu và kết quả mong đợi của tính năng.
*    Định nghĩa các thuật ngữ kỹ thuật hoặc từ chuyên ngành để tránh hiểu sai.
*    Chỉ rõ phạm vi và các ràng buộc (ví dụ: giới hạn hiệu năng, khả năng tương thích).

### 2. Cung Cấp Đầy Đủ context
* Bao gồm tất cả thông tin liên quan mà agent cần để hiểu tính năng.
* Liệt kê mọi giả định, phụ thuộc, và chi tiết hệ thống hiện có vào prompt.
* Nếu có thể, đưa ví dụ về input, output hoặc user scenario.
* Nêu rõ các công cụ, thư viện, hoặc API mà agent có thể sử dụng hoặc cần tích hợp.

### 3. Xác Định Cấu Trúc và format mong muốn
* Trình bày kế hoạch theo định dạng có cấu trúc (ví dụ: Requirement, tech stacks, etc).
* Chỉ rõ định dạng mong muốn trong output của agent (markdown, bảng, danh sách).
* Bao gồm hướng dẫn về mức độ chi tiết cho từng bước.
* Nếu có yêu cầu chỉnh sửa lặp lại, hãy nêu rõ cách agent nên trình bày thay đổi.

### 4. Role prompting
* Chỉ định agent đóng vai trò gì (ví dụ: "You are a senior software engineer...").
* Việc này giúp điều chỉnh phong cách và độ chi tiết của plan.

### 5. Lặp lại quá trình giao tiếp cho đến khi hoàn thiện plan
* Bắt đầu với một prompt ban đầu rõ ràng và xem xét kỹ output từ agent.
* Đưa phản hồi về điểm không chính xác, thiếu chi tiết hoặc vấn đề về định dạng.
* Tinh chỉnh prompt bằng cách bổ sung ràng buộc, làm rõ hoặc thêm ví dụ.
* Lặp lại quy trình cho đến khi plan.md đạt yêu cầu.

### 6. Cung cấp ví dụ và ràng buộc
* Cung cấp ví dụ về bản plan.md tốt hoặc phần bạn thấy thích.
* Chỉ rõ những điều cần tránh (ví dụ: tránh mô tả mơ hồ, tránh dùng từ chuyên ngành quá mức).
* Đặt giới hạn về độ dài hoặc độ phức tạp nếu cần.

Anh em có thể tham khảo bài viết trước đó của tôi về [cách viết prompt](https://viblo.asia/p/better-prompt-engineering-prompt-techniques-018J2bo0JYK) và [best practices](https://viblo.asia/p/better-prompt-engineering-best-practices-tu-ky-su-cua-google-Rk74aYNE4eO).\
Dưới đây là 1 ví dụ anh em có thể tham khảo và tự generate ra prompt phù hợp cho project của mình:

```
    You are a senior software engineer tasked with creating a detailed implementation plan for a new feature in our [project name]. The feature is [brief description].

    Requirements:
    - Clearly outline all steps needed to implement the feature.
    - Include dependencies, tools, and technologies involved.
    - Use markdown format with headings and bullet points.
    - Avoid vague language; be precise and technical.
    - Present the plan as a numbered list of tasks.

    Context:
    - The project uses [tech stack].
    - Current relevant modules are [list].
    - The feature should integrate with [APIs/libraries].
    - Constraints: [list any constraints].

    Example:
    1. Analyze current module X for integration points.
    2. Design API endpoints for feature Y.
    3. Implement backend logic using [technology].
    4. Write unit and integration tests.
    5. Update documentation.

    Please generate the initial plan.md. After reviewing, I will provide feedback for refinement.
```

# II. Edit -> Test -> Commit Loop
**Edit-Test-Commit** loop là cách tiếp cận **TDD** phiên bản AI.\
Để làm chủ Edit-Test-Commit Loop với AI, anh em nên bắt đầu bằng cách tự định nghĩa một danh sách test case bao gồm cả các trường hợp thất bại, edge case và các điều kiện đặc biệt.\
Các test này nên được viết theo format **Given - When - Then**, với phần assertion cần được làm rõ ràng, chi tiết.\
Khi đã có plan bao gồm các test cases được định nghĩa, bước tiếp theo là yêu cầu model implement dựa trên **plan.md** anh em đã định nghĩa.

Sau mỗi cycle, anh em phải **review thật kỹ** danh sách test case đã định nghĩa, kiểm tra xem có bao nhiêu test case đã pass, code mà agent thay đổi là gì, có acceptable không.
Trong một số trường hợp, agent có thể tự ý thay đổi assertion để khớp với code hiện tại, dẫn đến việc test không còn phản ánh đúng yêu cầu gốc.
Do đó, anh em cần chủ động theo dõi cách agent xử lý từng cycle, thường xuyên kiểm tra lại các thay đổi do agent tạo ra, sẵn sàng can thiệp kịp thời nếu thấy agent đang đi lệch hướng.
Nếu kết quả chấp nhận được, anh em nên thực hiện thao tác commit, 1 là change đủ nhỏ để anh em có thể review lại được, 2 là trong TH agent "làm láo", anh có có 1 check point để làm lại.
Cursor có cung cấp tính năng "Restore checkpoint" có chức năng tương tự nhưng tôi vẫn khuyến khích anh em commit để tránh bị lost context khi làm việc với agent.

Ví dụ:
```
Implement the feature strictly following the test cases defined in plan.md, which use the Given-When-Then format.

- Do NOT modify or weaken any assertions in the test cases without explicit instruction.
- After implementation, run all test cases and provide a detailed report:
  + Which tests passed and which failed.
  + For any failed test, explain why and propose a fix.
- Provide the full code diff for the changes you made.
- Keep each implementation cycle focused and small enough for easy review and commit.
- If you encounter ambiguity or conflict between code and tests, highlight it instead of guessing.
- Wait for my review and feedback before proceeding to the next cycle.

Please start by implementing the first set of test cases from plan.md and provide your code changes along with the test results.
```


# Codebase summary
Vì sao việc tóm tắt codebase lại đóng vai trò then chốt khi pair programming với agent?\
Câu trả lời chỉ có 1 từ: **Context**

Khi anh em cung cấp cho agent một cái nhìn tổng quan về cấu trúc dự án, các dependency và kiến trúc tổng thể, agent sẽ hiểu rõ context của project. Nhờ vậy, những gợi ý mà agent đưa ra sẽ sát với thực tế hơn và ít phải chỉnh sửa nhiều lần.

Khi đã nắm được "big picture", agent có thể nhanh chóng xác định được file, module liên quan mà không cần phải đi lòng vòng trong đống code. Điều này giúp tiết kiệm rất nhiều thời gian, đặc biệt là với các dự án lớn có cấu trúc phức tạp và nhiều lớp abstraction.

Ngoài ra, **codebase summary** còn giúp agent học được các pattern, convention đang dùng trong dự án, từ đó đưa ra các gợi ý phù hợp với kiến trúc hiện tại mà không làm ảnh hưởng đến những phần đang hoạt động ổn định. Điều này góp phần duy trì chất lượng, tính nhất quán của codebase và giảm rủi ro khi phát triển thêm các tính năng mới.

Một lợi ích quan trọng khác là anh em có thể cung cấp thêm thông tin về lịch sử của project, từng có những tính năng gì được thêm vào, vì sao lại có những tính năng đó, cũng như các yêu cầu phi chức năng (non-functional requirements) mà chỉ đọc code thì agent sẽ không thể hiểu hết được.\
Khi đó, nó sẽ có khả năng đưa ra giải pháp gần đúng ngay từ lần đầu, giúp tiết kiệm công sức refactor và rút ngắn **Edit -> Test -> Commit** loop đã được nêu ở trên.



Dưới đây là cấu trúc recommended của 1 file codebase summary
```
- Project structure:
  + Cách tổ chức thư mục
  + Vị trí các file cấu hình (pom.xml, application.yaml)
  + Các thư mục trong project và vai trò của từng thư mục
- Tech stacks
  + Framework đang sử dụng
  + Database
  + Deppendencies
- Architecture components
  + Các service layers (controllers, services, repositories)
  + Design pattern đang được dùng
  + Các API được expose (API contract)
  + Cách handle event (SQS, Event Bridge, event contract)
- Business domain context
  + Scope của project
  + Các feature mà service cung cấp
  + Business rules và constraints
  + Lịch sử của các feature
- Environment
  + Profiles local/dev/sit/stg/prod
  + Các config cho từng env
  + Docker config
- Code conventions
  + Naming conventions
  + Cách tổ chức package
  + Cách handle exception
- Integration context
  + Các hệ thống được tích hợp (AWS services, 3rd party services)
  + Những microservices đang tương tác với service này, vai trò của các service đó
  + Pattern đang sử dụng để integrate
- Non-functional requirements
  + Performance requirements: ressponse time, TPS, chiến thuật caching
  + Scalability: load-balancing, auto-scalling
  + Security requirement: cách authentication & authorization, chuẩn encrypt data, rate limit, etc
- Curent state
  + Git branching (gitflow hay là trunk-based)
  + Know issues & limitations
- References
  + Readme
  + API documentations
  + Architecture diagrams
  + Link đến các tài liệu liên quan
```
Anh em có thể yêu cầu agent tóm tắt project theo bố cục trên, review và tiếp tục yêu cầu agent chỉnh sửa cho đến khi đạt được tiêu chuẩn đúng và đủ.

> Nếu anh em thích mì ăn liền thì anh em có thể sử dụng [gitingest](http://gitingest.com) để hỗ trợ anh em summary và sử dụng file đó làm reference mỗi lần tương tác với agent.

# III. Re-index after big changes
Việc re-index đảm bảo rằng agent luôn có được thông tin latest về trạng thái của project. Thông thường thì agent sẽ tự động re-index nhưng khi anh em thấy agent bắt đầu đưa ra những câu trả lời dựa trên codebase cũ thì anh em nên force re-index.

Ngoài ra, những file như codebase summary sẽ không được update tự động, do đó, anh em cần chủ động yêu cầu agent update những tài liệu liên quan để match với codebase mới nhất.
Vấn đề này thường xảy ra khi anh em yêu cầu agent refactor cấu trúc project, hoặc anh em pull code được merge vào main branch.

# IV. Kết luận
AI Agent là một trợ thủ đắc lực của anh em, nhưng để khai thác hiệu quả, anh em **phải hiểu cách nó hoạt động**, biết rõ nó cần gì, giới hạn ở đâu, và làm sao để dẫn dắt nó trở thành một đồng đội thật sự.
Hi vọng bài viết này giúp anh em tiết kiệm thời gian, bớt đau đầu, và tận dụng AI agent để tăng lương giảm giờ làm.

**Prompt engineering không chỉ là kỹ thuật – đó là nghệ thuật giao tiếp hiệu quả với AI.**

-----

Nếu anh em cảm thấy bài viết hữu ích đừng ngần ngại click upvote cho bài viết, hoặc phát hiện ý nào chưa hợp lý hoặc cần giải thích thêm hãy comment cho tôi biết để cùng trao đổi nhé.
Anh em có thể tham khảo các bài viết khác của tôi tại [Blog cái nhân](https://nguyentaijs.github.io/) hoặc kết nối với tôi qua [Linkedin](https://www.linkedin.com/in/nguyentaijs)
