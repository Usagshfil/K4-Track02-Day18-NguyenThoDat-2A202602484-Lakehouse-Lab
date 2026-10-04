# Reflection: Lakehouse Anti-Patterns

Trong 5 anti-patterns của Data Lakehouse, hệ thống RAG và LLM Observability mà tôi quan tâm dễ vướng phải nhất là **External Vector Index Desynchronization (Bất đồng bộ vòng đời giữa Lakehouse và Vector DB ngoại vi)**.

Khi ứng dụng mở rộng, đội ngũ kỹ thuật thường coi Vector DB (Pinecone/Milvus) là một kho dữ liệu độc lập và chỉ thiết lập pipeline upsert định kỳ một chiều. Khi người dùng thực hiện quyền riêng tư (yêu cầu xóa dữ liệu cá nhân theo GDPR/CCPA) hoặc tài liệu bị thu hồi, lệnh DELETE được thực thi thành công trên Lakehouse (Single Source of Truth). Tuy nhiên, vì pipeline sao chép không lắng nghe sự kiện xóa (Change Data Feed - CDF delete events), Vector DB vẫn giữ nguyên các vector cũ. Hậu quả là mô hình RAG tiếp tục truy xuất thông tin đã bị xóa để trả lời người dùng, gây rò rỉ dữ liệu nghiêm trọng và vi phạm pháp lý.

Để phòng tránh, hệ thống bắt buộc phải tích hợp CDC/CDF hai chiều để truyền sự kiện xóa ngay lập tức sang index ngoại vi, hoặc ưu tiên lưu trữ vector trực tiếp trong bảng Lakehouse nhằm ràng buộc vòng đời vector với bản ghi gốc.

*(Sử dụng AI hỗ trợ đọc hiểu tài liệu lab và kiểm tra rubric)*
