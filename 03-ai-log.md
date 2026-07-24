# 03 — AI Log & Reflection (Phase 6)

**Nhóm:** ViMinhHien
**Thành Viên** Ngô Quang Dũng

> Mỗi thành viên viết phản ánh trung thực của riêng mình (khoảng 150–250 chữ/người) theo 3 câu hỏi: AI giúp gì, AI sai gì, sửa đổi ra sao. Bên dưới là ví dụ mẫu đã hoàn thiện đầy đủ (Thành viên 1) — các thành viên còn lại điền phần của mình theo đúng cấu trúc này.

---
**1. AI giúp gì?**
Trong buổi lab, tôi dùng AI (Claude/Gemini) làm thought-partner ở ba việc chính: (1) brainstorm nhanh danh sách bài toán vận hành theo 4 lenses cho từng công ty thành viên Vingroup khi tôi chưa có đủ ý tưởng; (2) phản biện lại 3 Quick Problem Card của tôi dưới vai một CFO khó tính, giúp tôi nhận ra 2 trong 3 card ban đầu thiếu metric có số cụ thể; (3) hỗ trợ viết và rà soát `SYSTEM_PROMPT` cùng các adversarial test case trong `prompt_prototype.py`, đặc biệt là gợi ý các kiểu tấn công prompt injection để kiểm tra ranh giới an toàn.

**2. AI sai gì?**
Khi tôi yêu cầu AI ước tính "tổn thất doanh thu do rò rỉ hiệu suất điều xe", AI đưa ra ngay một con số phần trăm cụ thể (ví dụ "~15%") mà không có căn cứ dữ liệu thực tế nào — đây là một dạng hallucination về số liệu định lượng. Ngoài ra, ở lần đầu thiết kế Operational Boundary, AI đề xuất một cơ chế rule-engine khá phức tạp (nhiều tầng điều kiện lồng nhau) để xử lý ngưỡng pin, trong khi thực tế chỉ cần 1 điều kiện đơn giản (pin < 5% và khoảng cách > 5km) là đủ.

**3. Sửa đổi ra sao?**
Tôi yêu cầu AI trích rõ nguồn hoặc gắn nhãn "ước tính giả định, cần xác minh thực tế" cho mọi con số không có dữ liệu gốc, thay vì trình bày như số liệu chắc chắn. Với phần rule-engine phức tạp, tôi yêu cầu AI đơn giản hóa lại chỉ giữ đúng 1 điều kiện ngưỡng pin/khoảng cách theo đúng Operational Boundary đã xác định ở Phase 3.2, tránh over-engineering cho một bài toán có phạm vi hẹp.

---
