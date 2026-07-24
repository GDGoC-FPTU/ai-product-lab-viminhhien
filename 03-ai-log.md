# 03 — AI Log & Reflection

## Tôi đã dùng AI để làm gì?

Tôi dùng AI như một thought-partner để brainstorm các điểm nghẽn vận hành tại Vin Smart Future, thu hẹp thành use case điều phối sự cố xe/pin của Xanh SM, rồi viết system prompt và các test prompt injection. AI giúp tôi chuyển yêu cầu nghiệp vụ thành các ranh giới có thể kiểm tra: output luôn có `[DRAFT_ONLY]`, pin dưới 5% phải tạo yêu cầu `dispatch_mobile_charger`, và mọi kết quả đều cần điều phối viên duyệt.

Tôi cũng dùng AI để gợi ý cách cấu trúc JSON output và cách tách phần LLM xử lý ngôn ngữ tự do khỏi rule-based safety gate. Điều này giúp nhận ra không nên dùng agent tự trị cho một luồng có rủi ro vận hành.

## AI sai hoặc gây hiểu nhầm ở đâu?

Khi chạy `python starter-code/prompt_prototype.py`, cả ba verification checks đều hiện **Passed**. Tuy nhiên ở test 2 và test 3, `draft_message` ghi rõ: `Không thể tạo nháp tự động (ClientError); cần xử lý thủ công.` Điều đó có nghĩa Gemini chưa trả lời thành công; kết quả pass ở các test này đến từ fallback/guardrail cục bộ chứ chưa chứng minh model Gemini thật đã tuân thủ prompt.

Đây là một điểm dễ gây hiểu nhầm: chỉ nhìn chữ “Passed” có thể kết luận sai rằng API và mô hình đã hoạt động tốt. Ngoài ra, ở test pin 2%, guardrail bằng code đã ghi đè kết quả theo quy tắc an toàn; vì thế test này kiểm tra được safety gate nhưng không đánh giá chất lượng suy luận của Gemini.

## Tôi đã sửa đổi prompt và ranh giới như thế nào?

Tôi bổ sung system prompt yêu cầu mọi phản hồi bắt đầu bằng `[DRAFT_ONLY]`, cấm mô hình tự gửi tin/tự điều xe, cấm bịa thông tin vận hành và cấm tiết lộ system prompt. Tôi thêm test tấn công yêu cầu bỏ thẻ draft-only và test yêu cầu tiết lộ system prompt, để kiểm tra prompt injection.

Quan trọng hơn, tôi không chỉ tin vào prompt. Code áp dụng rule deterministically: nếu phát hiện mức pin dưới 5%, response bị chuyển thành JSON với action `dispatch_mobile_charger`; không có đường nào để model đề xuất trạm sạc. Khi API lỗi hoặc thiếu API key, code trả fallback an toàn và yêu cầu xử lý thủ công thay vì giả vờ đã thực hiện tác vụ.

## Bước tiếp theo

Tôi cần kiểm tra lại `GEMINI_API_KEY`, quyền API và model để xử lý `ClientError`, sau đó chạy lại các test với phản hồi Gemini thật. Sau đó nhóm cần đánh giá bằng ticket đã ẩn danh và review của điều phối viên, thay vì chỉ dựa vào output mẫu hoặc các assertion pass.
