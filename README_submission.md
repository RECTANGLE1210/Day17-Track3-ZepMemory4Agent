# Lab 17 Reflection

## Phân tích benchmark

1. **Layer có hit rate thấp nhất:** Không có layer nào thấp hơn layer khác trong full practice run: hệ thống đạt 11/11 case, tức 100% trên short-term, long-term, episodic, semantic và mixed.

2. **Query retrieve nhiều token nhất:** E02 – “Với demo cá nhân của Minh, ngôn ngữ ưu tiên là gì?” với **808 retrieved tokens**.

3. **E07:** Case này cần kết hợp **long-term + semantic memory**. Hai evidence bắt buộc là **Python** (preference của user) và **Idempotency-Key** (quy tắc domain).

4. **Token reduction:** Memory-enabled giảm trung bình **20.8%** so với full source context. No-memory giảm **81.8%** nhưng hit rate chỉ **18.2% (2/11)**, vì lấy rất ít hoặc không lấy context. Vì vậy giảm token chỉ có ý nghĩa khi vẫn retrieve đúng evidence.

## Reflection bắt buộc

1. **Layer quan trọng nhất trong bộ test này:** Tôi chọn **long-term memory** vì nó bao phủ 4 case E02, E03, E08, E09, kiểm tra preference, open-loop task, recency và user isolation.

2. **Zep Context Block vs Redis + Qdrant:** Zep tiện hơn vì đã tổ chức và lấy context liên quan cho từng user, giảm lượng logic phải tự xây. Redis + Qdrant cho nhiều quyền kiểm soát hơn về lưu trữ và tìm kiếm, nhưng cần tự thiết kế retrieval, cập nhật, phân tách user và bảo trì.

3. **Guardrail chống memory poisoning/background write tự cấp quyền:** Chỉ ghi durable memory khi đúng user scope, có consent và nguồn/provenance rõ ràng. Nội dung được retrieve không được tự biến thành quyền hoặc instruction mới. Heartbeat chỉ nên dọn trùng, đánh dấu task cũ và tạo recap an toàn, không tự thêm authority.

## E08 Recency và E10 Compaction

E08 cho thấy thông tin mới phải thắng thông tin cũ khi có xung đột: trạng thái mới `BLUEBIRD-42 / TypeScript / NestJS` được ưu tiên, còn thông tin cũ chỉ giữ để truy vết lịch sử. E10 cho thấy compaction có thể bỏ raw turns cũ nhưng vẫn phải giữ constraint bền vững `REVIEW-DEADLINE-1600`, Friday 16:00. Nói ngắn gọn: có thể quên lời chat cũ, nhưng không được quên cam kết quan trọng.
