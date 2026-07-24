# 02 — Deep-Dive Report: Xanh SM Incident Triage Co-pilot

## Thông tin nhóm

| Trường thông tin | Nội dung |
|---|---|
| Tên nhóm | **ViMinhHien** |
| Thành viên 1 | **Nguyễn Thế Khôi - 2A202601439** |
| Thành viên 2 | **[ – MSSV]** |
| Thành viên 3 | **[CẦN BỔ SUNG HỌ TÊN – MSSV]** |
| Thành viên 4 | **[CẦN BỔ SUNG HỌ TÊN – MSSV]** |
| Thành viên 5 | **[CẦN BỔ SUNG HỌ TÊN – MSSV]** |
| Thành viên 6 | **[CẦN BỔ SUNG HỌ TÊN – MSSV]** |

> Các trường trong ngoặc vuông cần được nhóm thay bằng thông tin thật trước khi nộp.

## Quyết định lựa chọn

Nhóm chọn **Card #1 — Phân loại và chuyển xử lý sự cố tài xế Xanh SM** trong `01-problem-scan.md`. Scope pilot tập trung vào các ticket xe/pin được gửi qua ghi chú hoặc tổng đài. Trường hợp pin dưới 5% là nhánh rủi ro cao: AI không được đề xuất trạm sạc mà chỉ tạo yêu cầu xem xét xe sạc di động.

## 3.1. Current-State Workflow

Sơ đồ trực quan quy trình hiện tại: [04-workflow-diagram.png](04-workflow-diagram.png).

| Bước | Người/hệ thống thực hiện | Đầu vào → đầu ra | Thời gian ước tính | Điểm cần chú ý |
|---|---|---|---:|---|
| 1. Báo sự cố | Tài xế → tổng đài/ứng dụng | Cuộc gọi hoặc ghi chú → nội dung thô | 2 phút | 🔄 Handoff tài xế → điều phối viên |
| 2. Tạo ticket | Điều phối viên | Nội dung thô → ticket | 1 phút | 🔄 Handoff điện thoại/app → hệ thống ticket |
| 3. Đọc và phân loại | Điều phối viên | Ticket → loại sự cố, độ khẩn, nhóm nhận | 5 phút | 🔴 Bottleneck: ghi chú tự do, dễ chuyển sai |
| 4. Tra cứu/đề xuất hướng xử lý | Điều phối viên | Loại sự cố → hướng xử lý, tin nhắn | 3 phút | 🔴 Bottleneck: phải tra nhiều hệ thống; pin thấp cần xử lý cẩn trọng |
| 5. Duyệt và phản hồi | Điều phối viên/đội nhận ticket | Tin nhắn/ticket → phản hồi tài xế | 1 phút | 🔄 Handoff điều phối → đội cứu hộ/sạc/CSKH |

**Tổng thời gian baseline cần xác nhận từ log: khoảng 12 phút/ticket.** Các thời lượng là giả định để thiết kế pilot, không phải số liệu nội bộ đã được xác minh.

## 3.2. Problem Statement (6-field)

| Field | Nội dung |
|---|---|
| **1. Actor / Operator** | Điều phối viên tại trung tâm vận hành Xanh SM. Tài xế là người gửi thông tin và chịu ảnh hưởng bởi thời gian xử lý. |
| **2. Current Workflow** | Điều phối viên nhận cuộc gọi/ghi chú, tạo ticket, đọc nội dung tự do, tự phân loại mức độ khẩn, tra cứu hướng xử lý và chuyển ticket cho đội phù hợp. Quy trình gồm 5 bước, baseline giả định 12 phút/ticket. |
| **3. Bottleneck** | Phân loại và tra cứu (bước 3–4, khoảng 8 phút): một ticket có thể chứa vị trí, mức pin, triệu chứng xe và yêu cầu khẩn; nội dung thiếu cấu trúc dẫn đến chuyển sai nhóm hoặc bỏ sót dấu hiệu pin nguy cấp. |
| **4. Business Impact** | Ticket xử lý chậm làm tài xế chờ, kéo dài thời gian xe không sẵn sàng nhận cuốc và tăng tải cho điều phối viên. Tác động định lượng sẽ được đo bằng số ticket, thời gian xử lý và tỷ lệ chuyển sai trong pilot. |
| **5. Success Metric** | (1) ≥85% ticket được tạo bản nháp phân loại dưới 30 giây; (2) thời gian xử lý ban đầu trung vị giảm từ baseline 12 phút xuống ≤4 phút/ticket; (3) ≥95% ticket được chuyển đúng nhóm sau lần duyệt đầu; (4) 100% trường hợp pin <5% có `dispatch_mobile_charger` dạng nháp và có người duyệt. |
| **6. Operational Boundary** | AI chỉ đọc ticket đã được cấp quyền, tóm tắt, gán nhãn và tạo **nháp**. AI không được gửi tin, điều xe, xác nhận trạm sạc, tiết lộ dữ liệu nội bộ hoặc tự quyết tình huống khẩn. Mọi action đều cần điều phối viên duyệt; pin <5% không được đề xuất trạm sạc. |

## 3.3. Future-State Flow & AI Fit

### AI Fit

Giải pháp là **LLM Feature có Rule-based safety gate**, không phải Agentic Loop:

- **Rule/State machine:** đọc mức pin có cấu trúc; nếu `<5%`, khóa đường đề xuất trạm sạc và tạo action `dispatch_mobile_charger` dạng nháp.
- **LLM Feature:** tóm tắt ticket tiếng Việt, đề xuất nhãn loại sự cố/độ khẩn và soạn tin nhắn nháp.
- **Không dùng Agent tự trị:** hệ thống không được tự gửi tin, tự điều xe hoặc gọi dịch vụ bên ngoài.

### Future-state flow

```text
Tài xế báo sự cố
       ↓
🔵 Rule lấy trường mức pin + kiểm tra dữ liệu bắt buộc
       ↓
Nếu pin <5% ──→ 🔵 Tạo nháp {action: dispatch_mobile_charger}
       │                         ↓
       │                    🟢 Điều phối viên xác minh vị trí, duyệt/điều xe
       ↓ (pin ≥5% hoặc chưa rõ)
🔵 LLM tóm tắt ticket + đề xuất nhãn, nhóm nhận và [DRAFT_ONLY]
       ↓
🟢 Điều phối viên kiểm tra dữ liệu, sửa/duyệt
       ↓
Gửi tin/chuyển ticket qua hệ thống hiện có
       ↓
↩️ Fallback: nếu thiếu dữ liệu, confidence thấp, Gemini lỗi hoặc prompt injection
   → không tự động hành động; điều phối viên dùng SOP và xử lý ticket thủ công.
```

### Human-in-the-loop và fallback

| Tình huống | Hệ thống được phép làm | Con người/fallback |
|---|---|---|
| Ticket đủ dữ liệu, không khẩn | Tạo nhãn và tin nhắn `[DRAFT_ONLY]` | Điều phối viên duyệt trước khi chuyển/gửi |
| Pin dưới 5% | Khóa đề xuất trạm sạc; tạo nháp `dispatch_mobile_charger` | Điều phối viên kiểm tra GPS, mức pin và quyết định điều xe |
| Thiếu mức pin/vị trí, confidence thấp | Yêu cầu bổ sung thông tin, không suy đoán | Điều phối viên gọi lại tài xế theo SOP |
| Gemini/API lỗi hoặc có prompt injection | Trả fallback an toàn, không thực hiện action | Xử lý thủ công và ghi log sự cố kỹ thuật |

## 5. Evaluate

### AI Readiness Checklist

| Câu hỏi | Trạng thái | Bằng chứng / việc cần làm |
|---|---|---|
| Có dữ liệu mẫu/logs sạch để test? | ⚠️ Chưa xác nhận | Cần xin ticket đã ẩn danh, nhãn lịch sử và tiêu chuẩn phân loại của vận hành. |
| Rủi ro khi AI sai có kiểm soát được? | ✅ Có điều kiện | Có rule pin `<5%`, output draft-only, HITL và fallback thủ công; cần kiểm thử với ticket thực tế. |
| Stakeholder sẵn sàng đổi quy trình? | ⚠️ Chưa xác nhận | Cần một trưởng ca đồng ý pilot và quy định SLA duyệt nháp. |
| Gemini endpoint chạy thành công? | ❌ Chưa | Lần chạy prototype nhận `ClientError`; output vẫn pass nhờ guardrail cục bộ, chưa chứng minh model thật hoạt động. |

### Quyết định: **NOT YET**

Chưa nên triển khai pilot có người dùng thật. Prototype đã chứng minh được ranh giới cục bộ: pin 2% bị chuyển thành `dispatch_mobile_charger` dạng nháp và các test bypass `[DRAFT_ONLY]` đều pass. Tuy nhiên `ClientError` ở các test Gemini cho thấy API/model chưa sẵn sàng, nên chưa có kết quả đánh giá chất lượng LLM trên dữ liệu thật. Đồng thời chưa có log ticket đã ẩn danh, baseline 12 phút và tỷ lệ chuyển sai để xác minh metric.

### Điều kiện để chuyển sang GO

1. Cấu hình API key hợp lệ, kiểm tra quyền dùng `gemini-2.5-flash`, và lưu nguyên nhân lỗi nếu gọi thất bại.
2. Chuẩn bị tập dữ liệu pilot đã ẩn danh: tối thiểu 100 ticket, có nhãn chuẩn do vận hành duyệt và bộ test chứa tình huống pin nguy cấp/prompt injection.
3. Chạy shadow mode 2 tuần: AI chỉ tạo draft, điều phối viên làm như cũ; đo latency, tỷ lệ duyệt, tỷ lệ chuyển đúng và false-negative ở ticket khẩn.
4. Phê duyệt SOP fallback và quyền truy cập dữ liệu tối thiểu.

### Ước lượng chi phí pilot (cần xác nhận nội bộ)

| Hạng mục | Giả định | Ước lượng |
|---|---|---:|
| Tích hợp và guardrails | 1 AI engineer × 6 tuần | 30 person-days |
| Gán nhãn/đánh giá | 2 điều phối viên × 2 giờ/ngày × 10 ngày | 40 person-hours |
| Hạ tầng LLM | Token và giá model chưa được xác nhận | Đo từ 100 ticket pilot trước, đặt hạn mức ngân sách |
| Vận hành | Shadow mode 2 tuần, không tự động gửi | Không ảnh hưởng luồng production ngoài thời gian review |

Pilot chỉ nên được phê duyệt khi giá xử lý/ticket và mức tiết kiệm thời gian thực tế được đo từ shadow mode; không dùng các giả định trên để cam kết ROI.
