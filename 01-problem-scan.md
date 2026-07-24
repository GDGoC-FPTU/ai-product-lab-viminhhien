# File 01 - Problem Scan

## Thông tin bài làm

- Môn/Lab: Lab 02 - AI Product Scoping
- Vai trò giả định: AI Product Engineer tại Vin Smart Future
- Phạm vi: Tìm kiếm bài toán vận hành trong các công ty thành viên Vingroup

---

# Phase 1 - SCAN: Tìm kiếm cơ hội

Dùng 4 lenses để quét các quy trình vận hành có khả năng tối ưu bằng AI: Lặp lại, Tốn thời gian, AI-upgrade và Stakeholder Pain.

| # | Subsidiary | Lens | Mô tả ngắn bài toán |
|---|---|---|---|
| 1 | Vinhomes | Lặp lại | Phân loại và điều hướng phản ánh của cư dân trên app Vinhomes Resident đến đúng bộ phận xử lý như bảo trì điện nước, vệ sinh, an ninh, phí dịch vụ. |
| 2 | Vinpearl | Tốn thời gian | Nhân viên đặt phòng phải đọc email booking đoàn dài, trích xuất ngày đến, số phòng, loại phòng, yêu cầu ăn uống và tự tạo yêu cầu đặt phòng trong hệ thống. |
| 3 | VinFast | AI-upgrade | Tổng đài viên/kỹ thuật viên phải đọc mô tả lỗi bằng tiếng Việt từ khách hàng để phân loại nhóm lỗi xe ban đầu trước khi hẹn lịch dịch vụ. |
| 4 | Vinmec | Tốn thời gian | Bác sĩ mất nhiều thời gian soạn tóm tắt hồ sơ xuất viện từ bệnh án điện tử, kết quả xét nghiệm và ghi chú điều trị. |
| 5 | Xanh SM | Stakeholder Pain | Bộ phận vận hành chưa tổng hợp nhanh được lý do hủy chuyến từ ghi chú tài xế và nội dung khách hàng phản ánh, làm chậm việc phát hiện điểm đón sai hoặc tài xế đến muộn. |

---

# Phase 2 - QUICK-ASSESS: 3 Quick Problem Cards

Tôi chọn 3 bài toán tiềm năng nhất để đánh giá nhanh:

1. Vinhomes - Phân loại và điều hướng phản ánh cư dân
2. Vinpearl - Xử lý email booking đoàn
3. VinFast - Phân loại mô tả lỗi xe ban đầu

## Quick Problem Card #1 - Vinhomes phân loại phản ánh cư dân

```text
QUICK PROBLEM CARD #1

Bài toán:
Cư dân gửi phản ánh qua app Vinhomes Resident nhưng nội dung tự do, thiếu cấu trúc,
khiến nhân viên CSKH phải đọc tay và chuyển ticket đến đúng bộ phận.

Công ty thành viên:
[x] Vinhomes

Ai đang đau (Actor)?
- Nhân viên CSKH/ban quản lý tòa nhà: phải đọc và route nhiều ticket lặp lại.
- Cư dân: chờ lâu vì ticket bị chuyển sai bộ phận hoặc thiếu thông tin.

Workflow thủ công hiện tại:
1. Cư dân nhập phản ánh trên app
   -> 2. CSKH đọc nội dung tự do và ảnh đính kèm
   -> 3. CSKH phân loại bằng cảm tính: điện nước / vệ sinh / an ninh / phí dịch vụ
   -> 4. CSKH chuyển ticket sang bộ phận phụ trách
   -> 5. Bộ phận tiếp nhận hỏi lại nếu thiếu thông tin

Bước tốn thời gian/lỗi nhất:
- Bước 2-4: đọc, phân loại và chuyển ticket thủ công.
- Ước tính: 6-8 phút/ticket; ticket bị chuyển sai có thể mất thêm 1-2 giờ chờ.

AI có thể hỗ trợ ở bước nào?
- Bước 2-4: đọc nội dung phản ánh, tóm tắt vấn đề, gắn nhãn category,
  đề xuất bộ phận xử lý và nêu các thông tin còn thiếu.

Metric đo thành công:
- Giảm thời gian phân loại ticket từ 7 phút xuống dưới 1 phút/ticket.
- Đạt ít nhất 90% ticket được route đúng bộ phận ngay từ lần đầu.
- Giảm 30% số ticket phải hỏi lại vì thiếu thông tin.

Quick Architecture:
[ ] No AI  [ ] Rule  [x] LLM  [ ] Agent
```

### Lý do chọn

Bài toán này có tần suất cao, nội dung đầu vào là ngôn ngữ tự nhiên nên rule-based khó bao phủ hết các cách diễn đạt của cư dân. LLM phù hợp để tóm tắt và phân loại, nhưng vẫn nên có người duyệt với các ticket nhạy cảm như tranh chấp phí, an ninh hoặc khiếu nại nghiêm trọng.

---

## Quick Problem Card #2 - Vinpearl xử lý email booking đoàn

```text
QUICK PROBLEM CARD #2

Bài toán:
Nhân viên Vinpearl phải đọc email đặt phòng theo đoàn từ công ty lữ hành,
trích xuất yêu cầu và nhập lại vào hệ thống booking.

Công ty thành viên:
[x] Vinpearl

Ai đang đau (Actor)?
- Nhân viên reservations/sales operations: mất thời gian đọc email dài và nhập liệu.
- Khách đoàn/công ty lữ hành: chờ xác nhận phòng lâu, dễ phát sinh sai thông tin.

Workflow thủ công hiện tại:
1. Công ty lữ hành gửi email booking đoàn
   -> 2. Nhân viên đọc email và file đính kèm
   -> 3. Trích xuất ngày check-in/check-out, số phòng, loại phòng, số khách
   -> 4. Kiểm tra phòng trống trên hệ thống
   -> 5. Soạn email phản hồi báo giá/xác nhận tạm thời

Bước tốn thời gian/lỗi nhất:
- Bước 2-3: đọc email và trích xuất yêu cầu không có format cố định.
- Ước tính: 15-20 phút/email booking đoàn, dễ sai ngày hoặc loại phòng.

AI có thể hỗ trợ ở bước nào?
- Bước 2-3 và một phần bước 5: trích xuất thành bảng có cấu trúc,
  phát hiện thông tin thiếu, draft email hỏi lại khách.

Metric đo thành công:
- Giảm thời gian xử lý booking đoàn từ 20 phút xuống dưới 5 phút/email.
- Giảm lỗi nhập sai ngày/số phòng xuống dưới 2% booking.
- 95% email được tạo bản nháp phản hồi trong vòng 5 phút.

Quick Architecture:
[ ] No AI  [ ] Rule  [x] LLM  [ ] Agent
```

### Lý do chọn

Bài toán có đầu vào là email tự do, nhiều cách diễn đạt và file đính kèm. LLM có giá trị ở việc trích xuất thông tin và tạo draft, còn việc xác nhận phòng/báo giá vẫn cần kết nối hệ thống booking và nhân viên phê duyệt trước khi gửi.

---

## Quick Problem Card #3 - VinFast phân loại mô tả lỗi xe ban đầu

```text
QUICK PROBLEM CARD #3

Bài toán:
Khách hàng VinFast mô tả lỗi xe bằng ngôn ngữ đời thường, khiến tổng đài viên
hoặc cố vấn dịch vụ mất thời gian hiểu hiện tượng và phân loại nhóm lỗi ban đầu.

Công ty thành viên:
[x] VinFast

Ai đang đau (Actor)?
- Tổng đài viên/chăm sóc khách hàng: phải hỏi lại nhiều câu để hiểu lỗi.
- Cố vấn dịch vụ: nhận thông tin ban đầu thiếu cấu trúc.
- Khách hàng: phải chờ lâu để được hướng dẫn hoặc hẹn lịch đúng nhóm dịch vụ.

Workflow thủ công hiện tại:
1. Khách hàng gọi tổng đài hoặc gửi mô tả lỗi
   -> 2. Tổng đài viên đọc/nghe và hỏi lại thông tin
   -> 3. Phân loại nhóm lỗi bằng kinh nghiệm cá nhân
   -> 4. Chuyển đến cố vấn dịch vụ hoặc đặt lịch xưởng dịch vụ
   -> 5. Kỹ thuật viên kiểm tra lại khi xe đến xưởng

Bước tốn thời gian/lỗi nhất:
- Bước 2-3: hiểu mô tả lỗi bằng tiếng Việt đời thường và gắn nhóm lỗi.
- Ước tính: 8-12 phút/yêu cầu; nếu phân loại sai có thể đặt sai slot dịch vụ.

AI có thể hỗ trợ ở bước nào?
- Bước 2-3: tóm tắt triệu chứng, đề xuất nhóm lỗi ban đầu,
  gợi ý câu hỏi bổ sung và mức độ ưu tiên.

Metric đo thành công:
- Giảm thời gian ghi nhận và phân loại yêu cầu từ 10 phút xuống dưới 3 phút.
- 85% yêu cầu có đầy đủ thông tin tối thiểu trước khi chuyển cho cố vấn dịch vụ.
- Giảm 25% trường hợp phải gọi lại khách để hỏi thông tin cơ bản.

Quick Architecture:
[ ] No AI  [ ] Rule  [x] LLM  [ ] Agent
```

### Lý do chọn

Bài toán phù hợp với LLM vì cần hiểu ngôn ngữ tự nhiên, nhưng rủi ro liên quan đến an toàn xe nên AI chỉ nên đóng vai trò hỗ trợ phân loại ban đầu. AI không được kết luận lỗi kỹ thuật cuối cùng, không được đưa hướng dẫn sửa chữa nguy hiểm, và cần có cố vấn dịch vụ/kỹ thuật viên xác nhận.

---

# Kết luận cá nhân

Trong 3 bài toán, tôi đánh giá **Vinhomes - phân loại và điều hướng phản ánh cư dân** là ứng viên tốt nhất để đưa vào thảo luận nhóm, vì:

- Tần suất ticket cao và lặp lại hằng ngày.
- Đầu vào là ngôn ngữ tự nhiên, LLM có lợi thế hơn rule-based đơn thuần.
- Rủi ro có thể kiểm soát bằng Human-in-the-loop: AI chỉ đề xuất category, tóm tắt và bộ phận xử lý; nhân viên vẫn duyệt trước khi chuyển ticket.
- Metric thành công rõ: thời gian phân loại, tỉ lệ route đúng, tỉ lệ ticket thiếu thông tin.
