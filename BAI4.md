# BÁO CÁO PHÂN TÍCH KỸ THUẬT & ĐÁNH ĐỔI GIẢI PHÁP (TRADE-OFF) - RIKKEICARE MOBILE

## PHẦN 1: TÓM TẮT NGUYÊN NHÂN THẤT BẠI CỐT LÕI (ROOT CAUSES)

* **Sự cố 1 (Hiệu năng):** Ứng dụng truyền tải trực tiếp toàn bộ tệp ảnh y tế gốc dung lượng lớn (DICOM/MRI) từ máy chủ cơ sở dữ liệu mà không qua xử lý nén lũy tiến, phân mảnh lát cắt, lưu bộ nhớ đệm (CDN) và thiếu cơ chế tự động co giãn tải (Auto-scaling), dẫn đến nghẽn băng thông I/O và cạn kiệt Connection Pool khi vượt quá 150 kết nối đồng thời.
* **Sự cố 2 (Bảo mật):** Kênh truyền tải API và kho lưu trữ tệp tin y tế không kích hoạt giao thức bảo mật lớp truyền tải (TLS 1.3/HTTPS) và mã hóa dữ liệu nhạy cảm ở cấp độ trường (Field-level Encryption/AES-256), khiến gói tin lưu chuyển ở dạng văn bản thuần (Plaintext) dễ bị tấn công nghe lén (Man-in-the-Middle) và vi phạm tiêu chuẩn bảo mật y tế HIPAA.
* **Sự cố 3 (Khả dụng):** Giao diện người dùng vi phạm tiêu chuẩn thiết kế tiếp cận người cao tuổi (WCAG 2.1 AA) do cố định kích thước phông chữ (dưới 16sp), diện tích vùng chạm cảm ứng (Touch Target) nhỏ hơn chuẩn công thái học 48x48dp và thiếu bước xác thực hai bước (Two-step Confirmation) cho các thao tác hủy bỏ dịch vụ.

---

## PHẦN 2: ĐỀ XUẤT ĐA PHƯƠNG ÁN KỸ THUẬT & BẢNG PHÂN TÍCH ĐÁNH ĐỔI (TRADE-OFF)

### 1. Đề xuất 2 phương án kỹ thuật giải quyết đồng thời Tải ảnh & Bảo mật

* **Phương án 1: Scale-up Monolith & TLS Truyền thống (Tập trung & Nâng phần cứng)**
  * *Mô tả:* Tăng cấu hình CPU/RAM máy chủ hiện tại lên gấp 4 lần (Vertical Scaling), lưu ảnh trực tiếp trên Database/File Server nội bộ, truyền tải file ảnh DICOM/MRI nguyên bản qua giao thức HTTPS chuẩn (TLS 1.2/1.3) và mã hóa ổ cứng máy chủ (TDE cơ bản).
* **Phương án 2: Cloud Hybrid Storage, Micro-caching CDN & Client-side Selective Decryption (Phân tán & Tối ưu luồng)**
  * *Mô tả:* Chuyển kho lưu trữ ảnh y tế sang Cloud Object Storage (S3/GCS); áp dụng kỹ thuật nén ảnh lũy tiến (Progressive DICOM WADO-RS) và phân phối qua mạng biên CDN riêng tư (Private CDN với Signed URL); dữ liệu được mã hóa mức tệp bằng chuẩn AES-256-GCM kết hợp dịch vụ quản lý khóa KMS (Key Management Service).

---

### 2. Bảng Phân tích Đánh đổi (Trade-off Matrix)

| Tiêu chí so sánh | Phương án 1: Scale-up Monolith & TLS Truyền thống | Phương án 2: Cloud Storage, Private CDN & Client-side Decryption | Lập luận đánh đổi (Trade-off) |
| :--- | :--- | :--- | :--- |
| **Thời gian phản hồi truy vấn** | **Chậm (8 - 15s khi có tải):** Do phải truyền tải toàn bộ tệp thô dung lượng lớn từ 1 máy chủ duy nhất, băng thông dễ bị thắt cổ chai khi lượng truy cập tăng. | **Cực nhanh (<= 1.2s):** Tận dụng bộ nhớ đệm tại mạng biên (Edge Cache CDN) và cơ chế tải trước ảnh xem nhanh (thumbnail) độ phân giải thấp trước khi tải lát cắt chi tiết. | Chọn **PA 2** chấp nhận độ phức tạp cao hơn khi tích hợp thư viện đọc ảnh DICOM Web ở Client để đổi lấy tốc độ phản hồi vượt trội gấp 10 lần, đáp ứng cam kết SLA < 1.5s. |
| **Mức độ an toàn bảo vệ dữ liệu** | **Trung bình:** Chỉ bảo vệ dữ liệu trên đường truyền qua HTTPS và ổ cứng vật lý; nếu máy chủ ứng dụng bị xâm nhập (RCE/SQLi), dữ liệu bệnh án dạng thô sẽ bị lộ lọt toàn bộ. | **Rất cao (Zero Trust):** Tệp ảnh được mã hóa độc lập bằng khóa AES-256 động trước khi đẩy lên CDN; chỉ người dùng có token hợp lệ mới lấy được khóa tạm từ KMS để giải mã. | Chọn **PA 2** đánh đổi việc tiêu tốn thêm khoảng 3 - 5% năng lực tính toán CPU tại máy khách để giải mã tệp tin, đổi lại sự an toàn tuyệt đối theo chuẩn bảo mật y tế quốc tế HIPAA/ISO 27799. |

---

### 3. Khẳng định lựa chọn phương án tối ưu

> **Khẳng định lựa chọn:** Nhóm Phân tích khẳng định lựa chọn **Phương án 2 (Cloud Storage, Private CDN & Client-side Decryption)** là giải pháp tối ưu duy nhất, vì đây là kiến trúc phân tán triệt tiêu hoàn toàn nút thắt cổ chai băng thông để đạt chuẩn phản hồi dưới 1.5 giây, đồng thời bảo vệ dữ liệu y tế đa tầng đạt chuẩn HIPAA mà không làm đội chi phí hạ tầng máy chủ theo cấp số nhân như Phương án 1.

---

### 4. Bảng đặc tả Yêu cầu Phi chức năng (NFR) hoàn chỉnh

| Nhóm NFR | Chỉ số định lượng mục tiêu | Tiêu chí nghiệm thu |
| :--- | :--- | :--- |
| **Hiệu năng (Performance)** | Thời gian tải ảnh xét nghiệm <= 1.5s | Thử nghiệm tải ảnh MRI 50MB hoàn thành trong 1.2s |
| **Bảo mật (Security)** | Mã hóa dữ liệu lưu trữ & truyền tải | Không thể giải mã gói tin khi bắt gói trung gian |
| **Khả dụng (Usability)** | Cỡ chữ >= 16pt, nút bấm >= 48px | 95% bệnh nhân > 60 tuổi thao tác thành công tự lực |
| **Chịu tải (Scalability)** | **Số lượng người dùng đồng thời >= 3,000 CCU; Tỷ lệ lỗi giao dịch (Error Rate) < 0.1%** | **Giả lập Stress Test với 3,000 kết nối đồng thời trong 15 phút: hệ thống không bị crash, CPU duy trì dưới 75% và 99% yêu cầu phản hồi thành công.** |