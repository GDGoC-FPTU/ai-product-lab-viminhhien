# 🏗️ Phase 3 — DEEP-DIVE & Phase 5 — EVALUATE (Báo cáo nhóm)

**Tên nhóm:** *(Điền tên nhóm)*  
**Thành viên:**
| STT | Họ và tên | MSSV |
|-----|-----------|------|
| 1 | Vi Minh Hiển | 2A202601743 |
| 2 | *(Điền tên)* | *(Điền MSSV)* |
| 3 | *(Điền tên)* | *(Điền MSSV)* |
| 4 | *(Điền tên)* | *(Điền MSSV)* |

**Bài toán được chọn:** VinFast — Chẩn đoán lỗi xe từ mô tả tiếng Việt của khách hàng

---

## 🗳️ Lý do lựa chọn bài toán

Nhóm quyết định chọn bài toán **"Chẩn đoán lỗi xe từ mô tả tiếng Việt của khách hàng VinFast"** vì:
- **Dữ liệu sẵn có:** VinFast đã có cơ sở dữ liệu mã lỗi DTC (Diagnostic Trouble Code) chuẩn hóa cho tất cả dòng xe.
- **Pain point rõ ràng:** Kỹ thuật viên tổng đài quá tải (~12 phút/cuộc gọi), khách hàng chờ lâu — ảnh hưởng trực tiếp đến trải nghiệm khách hàng (NPS).
- **Ranh giới an toàn dễ kiểm soát:** AI chỉ đề xuất mã lỗi (advisory), không tự ra quyết định sửa chữa. Luôn có HITL (kỹ thuật viên xác nhận).
- **Dễ prototype:** Dùng LLM + structured JSON output để demo ngay.

### Lý do loại bỏ các thẻ khác:
- **Card #2 (VinFast Trợ lý sạc thông minh):** Cần kiến trúc Agent phức tạp (truy cập nhiều API real-time), chưa phù hợp với scope buổi lab. Nên triển khai sau khi đã có baseline từ bài toán đơn giản hơn.
- **Card #3 (Vinhomes Phân loại khiếu nại):** Rủi ro pháp lý cao — phân loại sai khiếu nại liên quan phí quản lý, tranh chấp căn hộ có thể dẫn đến khiếu kiện cho Vinhomes. Cần gom thêm dữ liệu và xây dựng rule-based router trước.

---

## 3.1. Current-State Workflow Mapping

Quy trình xử lý cuộc gọi chẩn đoán lỗi xe hiện tại của kỹ thuật viên tổng đài VinFast:

```text
┌──────────────┐     ┌──────────────┐     ┌──────────────┐     ┌──────────────┐     ┌──────────────┐
│ Bước 1       │     │ Bước 2       │     │ Bước 3       │     │ Bước 4       │     │ Bước 5       │
│ Nhận cuộc    │     │ Ghi chú mô   │     │ Tra cứu CSDL │     │ Đối chiếu &  │     │ Tư vấn khách │
│ gọi từ KH    │ ──→ │ tả triệu     │ ──→ │ mã lỗi DTC   │ ──→ │ chọn top-3   │ ──→ │ mang xe đến  │
│              │     │ chứng vào CRM │     │ (~800 mã)    │     │ mã khả nghi  │     │ đại lý/xưởng │
│ Ai: KTV      │     │ Ai: KTV      │     │ Ai: KTV      │     │ Ai: KTV      │     │ Ai: KTV      │
│ ⏱ 1 phút     │     │ ⏱ 1 phút     │     │ ⏱ 5 phút 🔴  │     │ ⏱ 5 phút 🔴  │     │ ⏱ 2 phút     │
│ In: Cuộc gọi │     │ In: Lời KH   │     │ In: Từ khóa  │     │ In: Danh sách│     │ In: Mã DTC   │
│ Out: Tiếp nhận│    │ Out: Ghi chú │     │ Out: Mã DTC  │     │ Out: Top-3   │     │ Out: Lịch hẹn│
└──────────────┘     └──────────────┘     └──────────────┘     └──────────────┘     └──────────────┘

🔴 = Bottlenecks (Bước 3 & 4)
🔄 Handoff: Bước 1→2 (chuyển từ hệ thống điện thoại sang CRM)
             Bước 4→5 (chuyển từ CRM sang hệ thống đặt lịch đại lý)
⏱ Tổng thời gian xử lý thủ công: 14 phút/cuộc gọi
```

### Phân tích Bottleneck:
- **Bước 3 (5 phút):** KTV phải tìm kiếm thủ công trong ~800 mã DTC. Hệ thống CRM hiện tại chỉ hỗ trợ tìm theo keyword tiếng Anh, trong khi khách mô tả bằng tiếng Việt → KTV phải tự dịch/diễn giải.
- **Bước 4 (5 phút):** Mô tả tiếng Việt đa nghĩa gây nhầm lẫn. Ví dụ: *"xe kêu cụp cụp ở bánh trước"* có thể là lỗi giảm xóc (DTC C0034), lỗi phanh (DTC C0035), hoặc lỗi thanh cân bằng (DTC C0056) — 3 mã hoàn toàn khác nhau. Tỉ lệ phân loại sai lần đầu: **~22%**.

---

## 3.2. Problem Statement (6-field)

| Field | Nội dung chi tiết |
|---|---|
| **1. Actor / Operator** | Kỹ thuật viên tổng đài (Technical Support Agent) thuộc Trung tâm Hỗ trợ Khách hàng VinFast. |
| **2. Current Workflow** | Khi khách hàng gọi hotline mô tả triệu chứng xe bằng tiếng Việt, KTV ghi chú vào CRM, tra cứu thủ công cơ sở dữ liệu ~800 mã lỗi DTC (Diagnostic Trouble Code), đối chiếu triệu chứng để chọn top-3 mã khả nghi, rồi tư vấn khách mang xe đến đại lý hoặc lên lịch hẹn kỹ thuật viên thực địa. Quy trình 5 bước, hoàn toàn thủ công, mất **14 phút/cuộc gọi**. |
| **3. Bottleneck** | Bước 3 & 4 (chiếm 10 phút): Tra cứu thủ công mã DTC và đối chiếu triệu chứng. Khách hàng mô tả bằng tiếng Việt tự nhiên đa nghĩa (ví dụ: *"kêu cụp cụp"*, *"rung lắc khi phanh"*, *"mùi khét ở đầu xe"*), trong khi CSDL mã DTC dùng thuật ngữ kỹ thuật tiếng Anh. Tỉ lệ phân loại sai lần đầu: **22%**, dẫn đến khách phải gọi lại hoặc mang xe đến sai xưởng. |
| **4. Business Impact** | Trung tâm tiếp nhận trung bình **350 cuộc gọi/ngày** tại Hà Nội. Mỗi cuộc mất 14 phút → tổng **81.7 giờ-nhân/ngày**. Tỉ lệ phân loại sai 22% gây ra **~77 cuộc gọi lại/ngày**, lãng phí thêm **18 giờ-nhân/ngày**. Chi phí ẩn: khách hàng phàn nàn trên mạng xã hội làm giảm NPS của VinFast khoảng **3-5 điểm**. Ước tính tổn thất nhân sự: **~25 triệu VNĐ/tháng** (chi phí overtime + tuyển thêm KTV). |
| **5. Success Metric** | 1. Giảm thời gian xử lý cuộc gọi từ 14 phút xuống **dưới 5 phút** (Efficiency). <br>2. Tỉ lệ phân loại đúng mã DTC (top-3 match) đạt **≥ 90%** so với 78% hiện tại (Quality). <br>3. Giảm tỉ lệ khách gọi lại vì phân loại sai từ 22% xuống **dưới 8%** (Customer Satisfaction). |
| **6. Operational Boundary** | AI được phép: Đọc mô tả tiếng Việt của khách → phân tích NLU → trả về top-3 mã DTC khả nghi kèm confidence score → hiển thị cho KTV xem xét. **CẤM:** (1) AI không được tự động xác nhận mã lỗi cuối cùng mà không có KTV phê duyệt (Bắt buộc HITL). (2) AI không được đưa ra hướng dẫn sửa chữa cụ thể cho khách hàng (chỉ đề xuất mã lỗi). (3) AI không được truy cập thông tin cá nhân của khách hàng ngoài mô tả triệu chứng xe. |

---

## 3.3. Future-State Flow & AI Fit

### AI Fit: **LLM Feature**

**Lý do chọn LLM Feature thay vì các mức khác:**

| Lựa chọn | Đánh giá |
|-----------|----------|
| **Rule / State-Machine** | ❌ Không đủ — Mô tả tiếng Việt tự nhiên quá đa dạng, không thể enumerate tất cả biến thể bằng rule. Ví dụ: "kêu cụp cụp", "có tiếng động lạ phía trước", "nghe lộp cộp khi đi ổ gà" đều cùng 1 lỗi nhưng diễn đạt khác nhau hoàn toàn. |
| **LLM Feature** | ✅ Phù hợp — LLM hiểu ngôn ngữ tự nhiên tiếng Việt, mapping sang mã DTC kỹ thuật. Quy trình có cấu trúc cố định (input text → output top-3 codes), chỉ cần 1 API call. |
| **Agentic Loop** | ❌ Quá phức tạp — Không cần agent tự trị vì không có bước ra quyết định phức tạp hay truy cập nhiều tool. Rủi ro over-engineering. |

### Quy trình tương lai (Future-State Flow):

```text
┌──────────────┐     ┌──────────────┐     ┌──────────────┐     ┌──────────────┐
│ Bước 1       │     │ Bước 2       │     │ Bước 3       │     │ Bước 4       │
│ Nhận cuộc    │     │ 🔵 AI phân   │     │ 🟢 KTV xác   │     │ KTV tư vấn   │
│ gọi từ KH    │ ──→ │ tích mô tả   │ ──→ │ nhận/sửa mã  │ ──→ │ & lên lịch   │
│              │     │ → top-3 DTC  │     │ DTC đề xuất  │     │ hẹn cho KH   │
│ Ai: KTV      │     │ Ai: LLM      │     │ Ai: KTV (HITL)│    │ Ai: KTV      │
│ ⏱ 1 phút     │     │ ⏱ 5 giây     │     │ ⏱ 1 phút     │     │ ⏱ 2 phút     │
└──────────────┘     └──────────────┘     └──────────────┘     └──────────────┘
                                                │
                                                ▼
                                         ↩️ Fallback:
                                         Nếu confidence score
                                         của cả 3 mã DTC < 60%,
                                         hệ thống cảnh báo KTV
                                         và KTV tra cứu thủ công
                                         như quy trình cũ.

🔵 = AI Step (LLM xử lý)
🟢 = Human-in-the-loop (KTV phê duyệt)
↩️ = Fallback (kế hoạch dự phòng)

⏱ Tổng thời gian xử lý mới: ~4 phút/cuộc gọi (giảm 71% so với 14 phút)
```

### Chi tiết AI Step (Bước 2):
- **Input:** Mô tả triệu chứng bằng tiếng Việt tự nhiên từ khách hàng + dòng xe (VF5/VFe34/VF8/VF9)
- **Processing:** LLM (Gemini 2.5 Flash) phân tích ngữ nghĩa → mapping sang top-3 mã DTC
- **Output (Structured JSON):**
```json
{
  "vehicle_model": "VF8",
  "symptom_summary": "Tiếng động lạ phía bánh trước khi đi qua gờ giảm tốc",
  "top_3_dtc": [
    {"code": "C0034", "description": "Front Suspension Strut Fault", "confidence": 0.85},
    {"code": "C0056", "description": "Stabilizer Bar Link Worn", "confidence": 0.72},
    {"code": "C0035", "description": "Front Brake Caliper Issue", "confidence": 0.45}
  ],
  "recommended_action": "Đề xuất khách mang xe đến đại lý VinFast để kiểm tra hệ thống treo trước",
  "urgency": "medium"
}
```

---

# 🏁 Phase 5 — EVALUATE

## AI Readiness Checklist:

| # | Câu hỏi | Trả lời |
|---|---------|---------|
| 1 | Chúng tôi có sẵn dữ liệu mẫu/logs sạch để test? | ✅ **Có.** VinFast có CSDL ~800 mã DTC chuẩn hóa theo tiêu chuẩn OBD-II quốc tế. Ngoài ra, hệ thống CRM lưu trữ lịch sử ~150,000 cuộc gọi/năm kèm ghi chú triệu chứng bằng tiếng Việt — đủ để fine-tune và evaluation. |
| 2 | Rủi ro khi AI sai có nằm trong tầm kiểm soát? | ✅ **Có.** AI chỉ đề xuất top-3 mã DTC (advisory), KTV bắt buộc phê duyệt trước khi tư vấn khách (HITL). Nếu confidence < 60%, fallback về quy trình thủ công. Rủi ro sai sót không ảnh hưởng trực tiếp đến an toàn xe (chỉ ảnh hưởng đến tốc độ phân loại). |
| 3 | Stakeholders sẵn sàng thay đổi quy trình? | ✅ **Có.** Trưởng phòng Tổng đài VinFast đã phàn nàn về tình trạng quá tải KTV (turnover rate 30%/năm). Đội ngũ KTV sẵn sàng chấp nhận công cụ hỗ trợ vì giảm áp lực tra cứu thủ công. |

## Quyết định cuối cùng:

### ✅ **GO (Bắt đầu xây dựng Prototype)**

**Justification:**

> **Lý giải kỹ thuật:** Bài toán có đầy đủ điều kiện để triển khai MVP:
> 1. **Dữ liệu sẵn có** — CSDL mã DTC chuẩn hóa + 150K cuộc gọi lịch sử làm training/eval data.
> 2. **Kiến trúc đơn giản** — Chỉ cần 1 LLM API call (Gemini 2.5 Flash), không cần multi-agent hay infrastructure phức tạp.
> 3. **Rủi ro thấp** — AI chỉ advisory, luôn có HITL, có fallback khi confidence thấp. Sai sót không ảnh hưởng an toàn xe.
> 4. **ROI rõ ràng** — Giảm 71% thời gian xử lý (14 phút → 4 phút), tiết kiệm ~25 triệu VNĐ/tháng chi phí nhân sự overtime.
> 5. **Chi phí triển khai thấp** — Gemini 2.5 Flash: ~$0.15/1M input tokens. Với 350 cuộc gọi/ngày × ~200 tokens/cuộc = 70K tokens/ngày ≈ **$0.01/ngày** (~300 VNĐ/ngày). Gần như miễn phí so với giá trị tiết kiệm.
>
> **Scope MVP đề xuất:** Triển khai pilot 2 tuần tại Trung tâm Hỗ trợ KH VinFast Hà Nội, chỉ áp dụng cho dòng xe VF8 (dòng xe có nhiều cuộc gọi nhất), đánh giá accuracy trên 500 cuộc gọi đầu tiên trước khi mở rộng.
