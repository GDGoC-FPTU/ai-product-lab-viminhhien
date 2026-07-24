AI Reflection Log
AI giúp gì?

Trong buổi lab, tôi sử dụng ChatGPT để brainstorm các bài toán AI phù hợp với Vin Smart Future. AI giúp tôi xác định các pain point trong VinFast, Xanh SM và Vinhomes, đồng thời hỗ trợ xây dựng workflow, xác định bottleneck và đề xuất metric đánh giá hiệu quả.

Ngoài ra, AI còn hỗ trợ viết System Prompt cho Gemini API và giải thích cách thiết lập môi trường Python, Virtual Environment và API Key.

AI sai gì?

Một số gợi ý ban đầu của AI thiên về sử dụng Agent cho những bài toán chỉ cần Rule-based hoặc LLM Feature. Ngoài ra, AI cũng đề xuất một số workflow chưa phù hợp với phạm vi của bài lab.

Trong quá trình chạy thử Gemini API, AI không thể xử lý được lỗi quota vì đây là vấn đề từ phía Google API chứ không phải lỗi của chương trình.

Tôi đã sửa như thế nào?

Tôi điều chỉnh lại System Prompt để bổ sung các Operational Boundary như:

Luôn thêm tiền tố [DRAFT_ONLY] vào phản hồi.
Không được đề xuất trạm sạc cách quá 5 km khi pin xe dưới 5%.
Khi pin dưới 5%, AI phải trả về hành động điều xe sạc pin di động.
Thêm Human-in-the-loop để nhân viên kiểm tra trước khi gửi phản hồi cho khách hàng.

Ngoài ra, tôi đổi model Gemini phù hợp hơn với SDK hiện tại và kiểm tra lại cấu hình API Key cũng như môi trường Python.
