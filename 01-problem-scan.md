# 01 — Problem Scan (Phase 1 & 2)

**Nhóm:** ViMinhHien
**Thành Viên** Ngô Quang Dũng
**Mảng khảo sát chính:** Xanh SM (GSM) — Vận hành xe taxi điện thông minh

> Ghi chú: File này tổng hợp Phase 1 (SCAN) và Phase 2 (QUICK-ASSESS) của cả nhóm. Mỗi thành viên bổ sung dòng SCAN + card riêng của mình theo đúng mẫu bên dưới trước khi merge vào `main`.


## 🔍 Phase 1 — SCAN: Bảng quét cơ hội

| # | Subsidiary | Lens | Mô tả ngắn bài toán |
|---|------------|------|---------------------|
| 1 | Xanh SM | Lặp lại | Phân bổ lại cuốc xe khi khách đổi điểm đến giữa chừng, điều phối viên phải thao tác lại thủ công trên hệ thống điều vận. |
| 2 | Xanh SM | Tốn thời gian | Điều phối viên xử lý thủ công báo cáo sự cố sạc pin/hết pin của tài xế giữa đường, phải tra cứu trạm sạc và soạn hướng dẫn bằng tay. |
| 3 | VinFast | Lặp lại | Đối chiếu hóa đơn sạc điện hằng tuần từ hàng nghìn trụ sạc đối tác với dữ liệu tài chính nội bộ. |
| 4 | Vinhomes | AI-upgrade | Phân loại và điều hướng thủ công các phản ánh của cư dân (mất nước, hỏng đèn, ồn ào...) gửi qua App Vinhomes Resident đến đúng ban quản lý tòa nhà. |
| 5 | Vinmec | Pain từ người khác | Bác sĩ mất 20–30 phút/bệnh nhân để soạn thảo tóm tắt hồ sơ xuất viện thủ công từ dữ liệu bệnh án điện tử. |
| 6 | Vinpearl | Pain từ người khác | Quản lý khách sạn phải tự đọc thủ công hàng trăm review trên Booking.com/Agoda/Google Map mỗi tuần để lọc ra phàn nàn khẩn cấp. |

---

## 🃏 Phase 2 — QUICK-ASSESS: 3 Quick Problem Cards

Top 3 bài toán được chọn từ bảng SCAN: **#2 (Xanh SM — sự cố sạc pin), #4 (Vinhomes — khiếu nại cư dân), #5 (Vinmec — tóm tắt xuất viện)**.

### Quick Problem Card #1 — Xanh SM: Xử lý sự cố sạc pin thực địa

```text
┌─────────────────────────────────────────────────────────────┐
│ QUICK PROBLEM CARD #1                                        │
│                                                               │
│ Bài toán: Tài xế Xanh SM báo hết pin/sắp hết pin giữa đường, │
│ cần điều phối viên tìm trạm sạc phù hợp và hướng dẫn di chuyển│
│ Công ty thành viên: [x] Xanh SM (GSM)                        │
│                                                               │
│ Ai đang đau (Actor)? Tài xế (chờ đợi, lo lắng); Điều phối    │
│ viên (quá tải giờ cao điểm)                                  │
│                                                               │
│ Workflow thủ công hiện tại (5 bước):                         │
│  1. Nhận cuộc gọi sự cố ──> 2. Tra định vị GPS xe ──>        │
│  3. Tra trạm sạc trống phù hợp loại cổng sạc ──>             │
│  4. Soạn tin hướng dẫn gửi tài xế ──> 5. Gọi cứu hộ nếu cần  │
│                                                               │
│ Bước nào tốn thời gian/lỗi nhất? Bước 3–4 (⏱ 12 phút/lượt)   │
│ AI có thể nhảy vào hỗ trợ ở bước nào? Bước 3–4 (tự động lấy  │
│ vị trí, lọc trạm sạc trống theo loại xe, soạn nháp tin nhắn) │
│                                                               │
│ Đo thành công bằng gì (Metric có số)?                         │
│ Giảm thời gian xử lý sự cố từ 17 phút ──> dưới 4 phút.       │
│                                                               │
│ Quick Architecture: [x] LLM Feature  [ ] Rule  [ ] Agent     │
└─────────────────────────────────────────────────────────────┘
```

### Quick Problem Card #2 — Vinhomes: Phân loại khiếu nại cư dân

```text
┌─────────────────────────────────────────────────────────────┐
│ QUICK PROBLEM CARD #2                                        │
│                                                               │
│ Bài toán: Phân loại và điều hướng phản ánh của cư dân đến    │
│ đúng ban quản lý tòa nhà phụ trách                           │
│ Công ty thành viên: [x] Vinhomes                             │
│                                                               │
│ Ai đang đau (Actor)? Nhân viên CSKH tổng đài Vinhomes        │
│ Resident, cư dân chờ phản hồi                                │
│                                                               │
│ Workflow thủ công hiện tại (4 bước):                         │
│  1. Cư dân gửi phản ánh qua App ──> 2. Nhân viên CSKH đọc và │
│  phân loại thủ công ──> 3. Chuyển tiếp email/tin cho đúng ban│
│  quản lý ──> 4. Theo dõi phản hồi và cập nhật trạng thái     │
│                                                               │
│ Bước nào tốn thời gian/lỗi nhất? Bước 2 (⏱ trung bình        │
│ 12 tiếng để phân loại và chuyển đúng nơi trong giờ cao điểm) │
│ AI có thể nhảy vào hỗ trợ ở bước nào? Bước 2 (tự động phân   │
│ loại danh mục + mức độ khẩn + gợi ý ban quản lý phụ trách)   │
│                                                               │
│ Đo thành công bằng gì (Metric có số)?                         │
│ Giảm thời gian phân loại + chuyển tiếp từ 12 tiếng ──> dưới  │
│ 30 phút; độ chính xác phân loại đạt ≥ 90%.                   │
│                                                               │
│ Quick Architecture: [ ] LLM Feature  [x] Rule + LLM hybrid   │
└─────────────────────────────────────────────────────────────┘
```

### Quick Problem Card #3 — Vinmec: Tóm tắt hồ sơ xuất viện

```text
┌─────────────────────────────────────────────────────────────┐
│ QUICK PROBLEM CARD #3                                        │
│                                                               │
│ Bài toán: Soạn thảo tóm tắt hồ sơ xuất viện bằng ngôn ngữ dễ │
│ hiểu cho bệnh nhân từ dữ liệu bệnh án điện tử                │
│ Công ty thành viên: [x] Vinmec                               │
│                                                               │
│ Ai đang đau (Actor)? Bác sĩ điều trị (quá tải giấy tờ)       │
│                                                               │
│ Workflow thủ công hiện tại (4 bước):                         │
│  1. Trích xuất dữ liệu lâm sàng từ bệnh án điện tử ──>       │
│  2. Đọc lại kết quả xét nghiệm & ghi chú điều trị ──>        │
│  3. Soạn thảo bản tóm tắt xuất viện thủ công ──>             │
│  4. Bác sĩ rà soát và ký duyệt                               │
│                                                               │
│ Bước nào tốn thời gian/lỗi nhất? Bước 2–3 (⏱ 25 phút/bệnh   │
│ nhân, dễ bỏ sót chi tiết khi bác sĩ quá tải)                 │
│ AI có thể nhảy vào hỗ trợ ở bước nào? Bước 2–3 (tự động tổng │
│ hợp và draft bản tóm tắt, bác sĩ chỉ cần rà soát và ký)      │
│                                                               │
│ Đo thành công bằng gì (Metric có số)?                         │
│ Giảm thời gian soạn tóm tắt từ 25 phút ──> dưới 8 phút/bệnh  │
│ nhân, 100% vẫn qua bác sĩ ký duyệt trước khi phát hành.      │
│                                                               │
│ Quick Architecture: [x] LLM Feature (bắt buộc HITL)  [ ] Agent│
└─────────────────────────────────────────────────────────────┘
```
