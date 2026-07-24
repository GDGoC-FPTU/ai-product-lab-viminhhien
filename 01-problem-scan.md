# 🔍 Phase 1 — SCAN & Phase 2 — QUICK-ASSESS (Bài cá nhân)

**Họ và tên:** *(Điền tên của bạn)*  
**MSSV:** *(Điền MSSV)*  

---

## 🔍 Phase 1 — SCAN: Tìm kiếm cơ hội AI tại Vin Smart Future

Sử dụng **4 Lenses** quét qua hoạt động vận hành của các công ty thành viên Vingroup. Dưới đây là **5 bài toán/bottleneck** thực tế:

| # | Subsidiary | Lens | Mô tả ngắn bài toán |
|---|------------|------|---------------------|
| 1 | **VinFast** | Tốn thời gian | **Chẩn đoán lỗi xe từ mô tả tiếng Việt của khách hàng:** Khách gọi hotline mô tả triệu chứng bằng ngôn ngữ tự nhiên (ví dụ: *"xe đi qua gờ giảm tốc kêu cụp cụp ở bánh trước"*). Kỹ thuật viên tổng đài phải tra cứu thủ công cơ sở dữ liệu mã lỗi kỹ thuật (DTC — Diagnostic Trouble Code) để phân loại, mất trung bình 12-15 phút/cuộc gọi. |
| 2 | **VinFast** | Lặp lại | **Đối chiếu hóa đơn sạc điện từ trạm đối tác:** Mỗi tuần, bộ phận tài chính VinFast phải so khớp thủ công hàng nghìn bản ghi sạc điện từ 500+ trụ sạc liên kết ngoài (PetroVietnam, EVN) với hóa đơn thực tế. Tỉ lệ sai lệch khoảng 8%, mất 2 ngày công/tuần. |
| 3 | **VinFast** | AI-upgrade | **Trợ lý hướng dẫn lịch trình sạc thông minh cho chủ xe:** Hiện tại app VinFast chỉ hiển thị danh sách trạm sạc trống tĩnh. Chủ xe VF8/VF9 không biết khi nào nên sạc, sạc ở đâu tối ưu chi phí và thời gian nhất dựa trên lịch trình di chuyển hàng ngày. |
| 4 | **Vinhomes** | Pain từ người khác | **Phân loại & điều hướng khiếu nại cư dân trên App Vinhomes Resident:** Cư dân gửi hàng trăm phản ánh mỗi ngày (mất nước, hỏng thang máy, ồn ào). Nhân viên BQL đọc và phân loại thủ công, thời gian phản hồi trung bình 12 tiếng — cư dân phàn nàn nhiều trên các group Facebook cộng đồng. |
| 5 | **Vinmec** | Tốn thời gian | **Soạn thảo tóm tắt hồ sơ xuất viện (Discharge Summary):** Bác sĩ Vinmec mất 20-30 phút/bệnh nhân để trích xuất thông tin từ bệnh án điện tử, xét nghiệm, ghi chú lâm sàng rồi viết bản tóm tắt xuất viện bằng ngôn ngữ dễ hiểu. Áp lực đặc biệt lớn khi khoa có 30+ bệnh nhân xuất viện/ngày. |

---

## 🃏 Phase 2 — QUICK-ASSESS: 3 Quick Problem Cards

**Chọn Top 3:** #1 (VinFast Chẩn đoán lỗi xe), #3 (VinFast Trợ lý sạc thông minh), #4 (Vinhomes Phân loại khiếu nại)

---

### Quick Problem Card #1 — VinFast: Chẩn đoán lỗi xe từ mô tả tiếng Việt

```
┌─────────────────────────────────────────────────────────────┐
│ QUICK PROBLEM CARD #1                                       │
│                                                             │
│ Bài toán (1 câu): Tự động phân loại mã lỗi kỹ thuật (DTC) │
│ từ mô tả triệu chứng bằng tiếng Việt của khách hàng VinFast│
│ khi gọi hotline, giúp kỹ thuật viên tổng đài xử lý nhanh. │
│ Công ty thành viên: [x] VinFast                            │
│                                                             │
│ Ai đang đau (Actor)?                                        │
│   - Kỹ thuật viên tổng đài VinFast (tra cứu thủ công)      │
│   - Khách hàng (chờ đợi lâu trên điện thoại)               │
│                                                             │
│ Workflow thủ công hiện tại (5 bước):                        │
│   1. Khách gọi hotline mô tả triệu chứng bằng lời nói     │
│   → 2. KTV ghi chú lại mô tả thủ công vào hệ thống CRM    │
│   → 3. KTV tra cứu cơ sở dữ liệu mã lỗi DTC (~800 mã)    │
│   → 4. KTV đối chiếu triệu chứng với danh sách mã lỗi     │
│        phù hợp, chọn 1-3 mã khả nghi nhất                  │
│   → 5. KTV tư vấn khách mang xe đến đại lý phù hợp        │
│        hoặc lên lịch hẹn kỹ thuật viên thực địa             │
│                                                             │
│ Bước nào tốn thời gian/lỗi nhất?                           │
│   Bước 3-4 (⏱ 10 phút/lượt). KTV phải đọc qua hàng trăm   │
│   mã DTC, dễ phân loại sai vì mô tả tiếng Việt đa nghĩa    │
│   (ví dụ: "kêu cụp cụp" có thể là lỗi giảm xóc, lỗi phanh│
│   hoặc lỗi thanh cân bằng — 3 mã DTC khác nhau hoàn toàn). │
│                                                             │
│ AI có thể nhảy vào hỗ trợ ở bước nào?                      │
│   Bước 3-4: LLM phân tích mô tả tiếng Việt tự nhiên →      │
│   mapping sang top-3 mã DTC khả nghi nhất kèm confidence    │
│   score → KTV chỉ cần xác nhận thay vì tra cứu thủ công.   │
│                                                             │
│ Đo thành công bằng gì (Metric có số)?                       │
│   - Giảm thời gian xử lý từ 12 phút → dưới 3 phút/cuộc    │
│   - Tỉ lệ phân loại đúng mã DTC đạt ≥ 90% (top-3 match)   │
│                                                             │
│ Quick Architecture: [x] LLM Feature                         │
│   (NLU tiếng Việt → mapping DTC, có HITL xác nhận)          │
└─────────────────────────────────────────────────────────────┘
```

---

### Quick Problem Card #2 — VinFast: Trợ lý lịch trình sạc thông minh

```
┌─────────────────────────────────────────────────────────────┐
│ QUICK PROBLEM CARD #2                                       │
│                                                             │
│ Bài toán (1 câu): Xây dựng trợ lý AI đề xuất lịch trình   │
│ sạc tối ưu cho chủ xe VinFast dựa trên dung lượng pin,     │
│ lịch di chuyển cá nhân, và tình trạng trạm sạc real-time.  │
│ Công ty thành viên: [x] VinFast                            │
│                                                             │
│ Ai đang đau (Actor)?                                        │
│   - Chủ xe VinFast VF5/VF8/VF9 (lo lắng hết pin, "range   │
│     anxiety", không biết sạc khi nào/ở đâu tối ưu)         │
│                                                             │
│ Workflow thủ công hiện tại (4 bước):                        │
│   1. Chủ xe nhận cảnh báo pin thấp trên dashboard xe       │
│   → 2. Mở app VinFast, xem danh sách trạm sạc trống        │
│        (hiển thị tĩnh, không tính toán thời gian di chuyển) │
│   → 3. Tự ước lượng xem pin có đủ đi đến trạm không        │
│   → 4. Lái xe đến trạm → phát hiện trạm hết chỗ hoặc      │
│        cổng sạc không phù hợp dòng xe → phải tìm trạm khác │
│                                                             │
│ Bước nào tốn thời gian/lỗi nhất?                           │
│   Bước 2-4 (⏱ 15-25 phút/lượt). Thông tin tĩnh trên app   │
│   không phản ánh thực tế → 23% chủ xe đến trạm sạc nhưng   │
│   phải quay đi vì trạm đầy hoặc sai loại cổng sạc.        │
│                                                             │
│ AI có thể nhảy vào hỗ trợ ở bước nào?                      │
│   Bước 2-3: AI tổng hợp dữ liệu pin + GPS + lịch Google   │
│   Calendar + API trạm sạc real-time → đề xuất "sạc lúc nào,│
│   ở đâu, mất bao lâu" cá nhân hóa cho từng chủ xe.        │
│                                                             │
│ Đo thành công bằng gì (Metric có số)?                       │
│   - Giảm tỉ lệ "đến trạm sạc rồi phải quay đi" từ 23%    │
│     xuống dưới 5%                                           │
│   - Tăng mức độ hài lòng (NPS) về trải nghiệm sạc ≥ 8/10  │
│                                                             │
│ Quick Architecture: [x] Agent                                │
│   (Cần truy cập nhiều API real-time: pin, GPS, trạm sạc,   │
│    lịch trình → orchestration phức tạp hơn LLM đơn giản)   │
└─────────────────────────────────────────────────────────────┘
```

---

### Quick Problem Card #3 — Vinhomes: Phân loại & Điều hướng khiếu nại cư dân

```
┌─────────────────────────────────────────────────────────────┐
│ QUICK PROBLEM CARD #3                                       │
│                                                             │
│ Bài toán (1 câu): Tự động phân loại phản ánh/khiếu nại     │
│ của cư dân trên App Vinhomes Resident và điều hướng đến     │
│ đúng ban quản lý tòa nhà phụ trách trong vòng 30 giây.     │
│ Công ty thành viên: [x] Vinhomes                            │
│                                                             │
│ Ai đang đau (Actor)?                                        │
│   - Nhân viên Ban Quản Lý tòa nhà (đọc & phân loại thủ    │
│     công hàng trăm phản ánh/ngày)                           │
│   - Cư dân (chờ phản hồi trung bình 12 tiếng, bực bội)     │
│                                                             │
│ Workflow thủ công hiện tại (5 bước):                        │
│   1. Cư dân gửi phản ánh qua App (text tự do + ảnh)        │
│   → 2. Nhân viên CSKH đọc nội dung phản ánh                │
│   → 3. Phân loại thủ công vào 1 trong 15 danh mục          │
│        (điện, nước, thang máy, an ninh, tiếng ồn...)       │
│   → 4. Chuyển tiếp (forward) đến đúng bộ phận kỹ thuật    │
│   → 5. Bộ phận kỹ thuật xử lý và phản hồi lại cư dân      │
│                                                             │
│ Bước nào tốn thời gian/lỗi nhất?                           │
│   Bước 2-4 (⏱ 8 phút/phản ánh). Phân loại sai chiếm 18%,  │
│   dẫn đến chuyển nhầm bộ phận → phải chuyển lại → kéo dài  │
│   thời gian xử lý thêm 4-6 tiếng. Đặc biệt khó khi cư    │
│   dân viết mô tả mơ hồ: "phòng tôi có vấn đề" (vấn đề gì?)│
│                                                             │
│ AI có thể nhảy vào hỗ trợ ở bước nào?                      │
│   Bước 2-4: LLM đọc text + ảnh → auto-classify danh mục    │
│   + mức độ ưu tiên (khẩn cấp/bình thường) → auto-route     │
│   đến đúng bộ phận. HITL: nhân viên duyệt nhanh trước khi  │
│   chuyển tiếp chính thức.                                   │
│                                                             │
│ Đo thành công bằng gì (Metric có số)?                       │
│   - Giảm thời gian phân loại từ 8 phút → dưới 30 giây     │
│   - Tỉ lệ phân loại đúng danh mục đạt ≥ 92%               │
│   - Giảm thời gian phản hồi cư dân từ 12h → dưới 2h       │
│                                                             │
│ Quick Architecture: [x] LLM Feature                         │
│   (Classification + routing, có HITL duyệt kết quả)         │
└─────────────────────────────────────────────────────────────┘
```

---

## 🗳️ Đề xuất bài toán cho nhóm Deep-Dive

**Đề xuất chọn Card #1 — VinFast Chẩn đoán lỗi xe từ mô tả tiếng Việt** vì:
- **Dữ liệu sẵn có:** VinFast đã có cơ sở dữ liệu mã DTC chuẩn hóa cho tất cả dòng xe.
- **Pain point rõ ràng:** Kỹ thuật viên tổng đài quá tải, khách hàng chờ lâu — ảnh hưởng trực tiếp đến NPS.
- **Ranh giới an toàn dễ kiểm soát:** AI chỉ đề xuất mã lỗi (advisory), không tự động đưa ra quyết định sửa chữa. Luôn có HITL (kỹ thuật viên xác nhận).
- **Dễ prototype:** Có thể dùng LLM + structured output JSON để demo ngay trong buổi lab.
