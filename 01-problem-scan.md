# 01 — Problem Scan & Quick Assess

> **Lưu ý:** Các mốc thời gian và metric dưới đây là ước tính ban đầu phục vụ scoping lab; cần đối chiếu log vận hành trước khi triển khai.

## Phase 1 — SCAN: Bảng quét cơ hội

| # | Công ty thành viên | Lens | Mô tả ngắn bài toán |
|---|---|---|---|
| 1 | Xanh SM | Lặp lại | Điều phối viên đọc ghi chú sự cố từ tài xế, phân loại mức độ khẩn và chuyển đúng nhóm hỗ trợ. |
| 2 | Vinhomes | Tốn thời gian | Nhân viên CSKH đọc phản ánh tự do của cư dân, tra chính sách rồi soạn phản hồi nháp. |
| 3 | VinFast | Lặp lại | Nhân viên hậu mãi tổng hợp nội dung cuộc gọi và ghi chú sửa chữa thành tóm tắt phiếu dịch vụ. |
| 4 | Vinpearl / VinWonders | AI-upgrade | Chatbot trả lời câu hỏi về vé, giờ mở cửa và quy định nhưng chưa hiểu ngữ cảnh đơn đặt chỗ. |
| 5 | Vinmec | Stakeholder Pain | Điều dưỡng phải gọi lại và sắp xếp lịch tái khám khi bệnh nhân gửi yêu cầu bằng ngôn ngữ tự do. |
| 6 | Xanh SM | Stakeholder Pain | Tài xế báo sự cố pin/xe qua điện thoại; điều phối viên tra cứu thông tin và phản hồi thủ công, khiến tài xế chờ lâu. |

## Phase 2 — QUICK-ASSESS: Ba Quick Problem Cards

### Card #1 — Phân loại và chuyển xử lý sự cố tài xế Xanh SM

- **Bài toán:** Rút ngắn thời gian điều phối viên đọc, phân loại và chuyển ticket sự cố do tài xế báo về.
- **Công ty thành viên:** Xanh SM (GSM)
- **Actor/Operator:** Điều phối viên trung tâm vận hành; tài xế là người chịu ảnh hưởng trực tiếp.
- **Workflow thủ công hiện tại:** (1) Tài xế gọi điện hoặc gửi ghi chú trong ứng dụng → (2) điều phối viên ghi nhận ticket → (3) đọc ticket, xác định loại sự cố/độ khẩn → (4) chuyển cho đội cứu hộ, sạc, CSKH hoặc an toàn → (5) đội nhận ticket phản hồi tài xế.
- **Bước tốn thời gian/lỗi nhất:** Bước 2–4 phải diễn giải ghi chú tự do và chọn đúng nhóm xử lý, **ước tính 6 phút/lượt**; dễ chuyển nhầm khi cao điểm.
- **AI có thể hỗ trợ:** LLM tóm tắt, gán nhãn loại sự cố/độ khẩn và đề xuất nhóm nhận ticket; điều phối viên xác nhận trước khi chuyển.
- **Metric thành công:** ≥**85%** ticket được tạo nháp phân loại dưới **30 giây**; giảm xử lý ban đầu từ **6 phút xuống dưới 2 phút/ticket**; tỷ lệ chuyển đúng nhóm ≥**95%**.
- **Quick Architecture:** **LLM Feature + Rule**. Rule bắt các tình huống khẩn cấp; LLM chỉ xử lý mô tả tự do và tạo đề xuất.

### Card #2 — Soạn phản hồi nháp cho phản ánh cư dân Vinhomes

- **Bài toán:** Hỗ trợ nhân viên CSKH Vinhomes soạn phản hồi nhất quán cho phản ánh cư dân, không tự cam kết chính sách hay chi phí.
- **Công ty thành viên:** Vinhomes
- **Actor/Operator:** Nhân viên CSKH ban quản lý; cư dân là người chờ phản hồi.
- **Workflow thủ công hiện tại:** (1) Cư dân gửi phản ánh qua ứng dụng/call center → (2) nhân viên đọc và xác định chủ đề/ưu tiên → (3) tìm chính sách hoặc hỏi vận hành → (4) viết phản hồi, tạo yêu cầu xử lý → (5) gửi sau khi kiểm tra.
- **Bước tốn thời gian/lỗi nhất:** Bước 2–4 phải tìm đúng thông tin rồi viết lại lịch sự, **ước tính 10 phút/yêu cầu**.
- **AI có thể hỗ trợ:** Sau khi nhân viên chọn chính sách đã phê duyệt, LLM phân loại chủ đề, tóm tắt và soạn phản hồi nháp có trích nguồn nội bộ.
- **Metric thành công:** Giảm từ **10 phút xuống dưới 3 phút/yêu cầu**; **90%** bản nháp được chấp nhận sau tối đa một lần sửa; không có nháp tự nêu phí hoặc cam kết SLA.
- **Quick Architecture:** **LLM Feature (RAG có kiểm soát) + Human-in-the-loop**; AI chỉ tạo draft, nhân viên duyệt trước khi gửi.

### Card #3 — Tóm tắt phiếu dịch vụ hậu mãi VinFast

- **Bài toán:** Giảm thời gian kỹ thuật viên tổng hợp cuộc gọi và ghi chú sửa chữa thành tóm tắt chuẩn cho phiếu dịch vụ VinFast.
- **Công ty thành viên:** VinFast
- **Actor/Operator:** Cố vấn dịch vụ/kỹ thuật viên tại xưởng; khách hàng chờ cập nhật trạng thái xe.
- **Workflow thủ công hiện tại:** (1) Cố vấn tiếp nhận mô tả lỗi → (2) kỹ thuật viên kiểm tra và ghi chú → (3) cố vấn đọc các ghi chú/cuộc trao đổi → (4) soạn tóm tắt, hạng mục chờ xác nhận → (5) kiểm tra lại trước khi lưu hoặc cập nhật khách hàng.
- **Bước tốn thời gian/lỗi nhất:** Bước 3–4 tổng hợp nhiều ghi chú kỹ thuật không đồng nhất, **ước tính 12 phút/phiếu**; dễ bỏ sót hạng mục cần xác nhận.
- **AI có thể hỗ trợ:** LLM tạo tóm tắt có cấu trúc: triệu chứng, chẩn đoán, việc đã làm, việc chờ xác nhận và câu hỏi cần hỏi khách hàng.
- **Metric thành công:** Giảm từ **12 phút xuống dưới 4 phút/phiếu**; **95%** tóm tắt có đủ năm trường bắt buộc; **100%** được cố vấn duyệt trước khi lưu/gửi.
- **Quick Architecture:** **LLM Feature**. Dữ liệu chẩn đoán lấy bằng rule từ hệ thống; LLM không được tự kết luận nguyên nhân, báo giá, thay thế linh kiện hoặc gửi tin.

## Nhận định sơ bộ

Card #1 được chọn để deep-dive vì đầu vào ngắn, nhãn đầu ra rõ và giữ được điều phối viên trong vòng phê duyệt. Card #2 và #3 cần xác nhận chất lượng kho chính sách/dữ liệu trước khi prototype.
