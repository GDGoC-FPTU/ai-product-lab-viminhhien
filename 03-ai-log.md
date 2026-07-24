# File 03 - AI Log & Reflection

## Bối cảnh sử dụng AI

Trong buổi lab này, tôi sử dụng AI như một thought-partner để hỗ trợ đọc yêu cầu bài nộp, tách phần cá nhân/nhóm, brainstorm các bài toán vận hành trong hệ sinh thái Vingroup và biến các ý tưởng thành Quick Problem Cards có metric cụ thể.

Tôi không dùng AI để quyết định thay hoàn toàn. Vai trò của AI là gợi ý, phản biện, sắp xếp cấu trúc và giúp tôi nhìn ra điểm yếu trong bài toán. Phần quyết định cuối cùng vẫn cần con người kiểm tra xem quy trình có sát thực tế, metric có hợp lý và ranh giới vận hành có an toàn hay không.

---

## 1. AI đã giúp gì?

AI giúp tôi ở ba việc chính.

Thứ nhất, AI giúp đọc và tóm tắt README để xác định file nào là cá nhân, file nào là nhóm. Từ đó tôi biết mình cần tập trung vào `01-problem-scan.md` cho Phase 1-2 và `03-ai-log.md` cho Phase 6.

Thứ hai, AI giúp brainstorm các pain point vận hành của Vingroup theo 4 lenses: Lặp lại, Tốn thời gian, AI-upgrade và Stakeholder Pain. Nếu tự nghĩ một mình, tôi dễ bị mắc ở các ý tưởng chung chung như "làm chatbot CSKH". Khi hỏi AI theo ngữ cảnh Vin Smart Future, tôi có danh sách cụ thể hơn như phân loại phản ánh cư dân Vinhomes, xử lý email booking đoàn Vinpearl, phân loại mô tả lỗi xe VinFast và tóm tắt hồ sơ xuất viện Vinmec.

Thứ ba, AI giúp chuyển ý tưởng thành Quick Problem Card có cấu trúc. Đặc biệt, AI nhắc tôi phải có actor rõ ràng, workflow 3-5 bước, bottleneck có thời gian ước tính, metric có số và quick architecture. Nhờ vậy bài làm không chỉ nói "dùng AI để nhanh hơn" mà có mục tiêu cụ thể như giảm thời gian phân loại ticket từ 7 phút xuống dưới 1 phút, hoặc giảm thời gian xử lý email booking từ 20 phút xuống dưới 5 phút.

---

## 2. AI đã sai hoặc thiếu gì?

Điểm sai/thiếu đầu tiên là AI ban đầu có thể dễ nhầm giữa "file 4" trong danh sách chi tiết của README với tên file `04-workflow-diagram`. Trong README, file số 4 của phần hướng dẫn chi tiết là `03-ai-log.md`, nhưng trong cấu trúc repo lại có file bắt đầu bằng số 04 là `04-workflow-diagram.png/.pdf`. Nếu không đọc kỹ, rất dễ làm nhầm sang file workflow của nhóm.

Điểm thiếu thứ hai là AI hay đưa ra ý tưởng quá rộng. Ví dụ, nếu chỉ hỏi "gợi ý bài toán AI cho VinFast", AI có xu hướng đề xuất "trợ lý bảo trì thông minh" hoặc "hệ thống chăm sóc khách hàng AI" rất chung chung. Nhưng rubric yêu cầu một bottleneck cụ thể, có actor, workflow hiện tại, bước tốn thời gian và metric có số.

Điểm sai/thiếu thứ ba là AI thỉnh thoảng đề xuất giải pháp Agent quá sớm. Với những bài toán như phân loại ticket Vinhomes hay trích xuất email booking Vinpearl, một LLM Feature có Human-in-the-loop là đủ phù hợp hơn. Nếu dùng Agent tự trị ngay từ đầu, rủi ro sai bộ phận, gửi nhầm email hoặc đưa thông tin chưa được phê duyệt sẽ cao hơn lợi ích.

---

## 3. Tôi đã sửa prompt và cách làm ra sao?

Tôi điều chỉnh cách hỏi AI theo hướng rõ ràng hơn:

```text
Hãy bám sát README và rubric của Lab 02.
Chỉ làm phần cá nhân: Phase 1, Phase 2 và Phase 6.
Với mỗi bài toán, bắt buộc có actor, workflow hiện tại, bottleneck,
thời gian ước tính, AI step, metric có số và quick architecture.
Không đề xuất Agent nếu LLM Feature hoặc Rule-based đã đủ.
Nếu có rủi ro an toàn/pháp lý, phải nêu Human-in-the-loop.
```

Sau khi AI đưa ra ý tưởng, tôi lọc lại bằng 3 câu hỏi:

1. Bài toán này có phải là một quy trình vận hành cụ thể không?
2. Nếu AI sai, hậu quả có nằm trong tầm kiểm soát của con người không?
3. Metric thành công có đo được bằng số không?

Nhờ cách làm này, tôi giữ được scope nhỏ và rõ hơn. Ví dụ, thay vì viết "AI quản lý cư dân Vinhomes", tôi thu hẹp thành "AI tóm tắt, phân loại và đề xuất bộ phận xử lý cho phản ánh cư dân". AI không tự động đóng ticket, không tự trả lời các tranh chấp nhạy cảm, và nhân viên vẫn là người duyệt cuối cùng.

---

## Kết luận cá nhân

AI hữu ích nhất khi được dùng như người phản biện và hỗ trợ cấu trúc hóa suy nghĩ. Nếu hỏi mơ hồ, AI dễ tạo ra câu trả lời nghe hay nhưng khó chấm điểm vì thiếu workflow và metric. Khi tôi ép prompt bám vào rubric, yêu cầu con số cụ thể và ranh giới vận hành, kết quả trở nên thực dụng hơn nhiều.

Bài học lớn nhất của tôi là: trong AI Product Scoping, không nên bắt đầu bằng câu hỏi "dùng AI để làm gì", mà nên bắt đầu bằng "ai đang mất thời gian ở bước nào, mất bao lâu, và nếu giảm được thì đo bằng metric nào".

