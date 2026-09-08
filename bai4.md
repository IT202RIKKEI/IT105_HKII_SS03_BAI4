# BÀI LÀM: KHẢO SÁT & PHÂN TÍCH TOÀN DIỆN NỀN TẢNG TMĐT RIKKEISHOP
**Vai trò:** Chuyên viên Phân tích Hệ thống (System Analyst)  
**Dự án:** Nâng cấp Flash Sale & Trải nghiệm Tìm kiếm RikkeiShop

---

## BƯỚC 1: NHẬN DIỆN 5 THÀNH PHẦN HTTT VÀ PHÂN BIỆT DỮ LIỆU VS THÔNG TIN

### 1. Nhận diện 5 thành phần HTTT tại RikkeiShop

| Thành phần HTTT | Ví dụ thực tế tại RikkeiShop | Vai trò cơ bản |
| :--- | :--- | :--- |
| **1. Phần cứng (Hardware)** | Cụm máy chủ đám mây (Cloud Server), hệ thống mạng CDN | Lưu trữ hình ảnh sản phẩm và xử lý đơn |
| **2. Phần mềm (Software)** | Website mua sắm RikkeiShop, ứng dụng di động RikkeiShop App | Cung cấp giao diện duyệt hàng và thanh toán |
| **3. Dữ liệu (Data)** | Danh mục sản phẩm, số lượng tồn kho, giỏ hàng người dùng | Dữ liệu hàng hóa và mua bán |
| **4. Con người (People)** | Khách mua hàng, Chủ shop bán hàng (Seller), Quản trị viên sàn (Admin), Đội ngũ Chăm sóc khách hàng (CSKH) | Người mua hàng, người vận hành và người bán hàng trên hệ thống |
| **5. Quy trình (Process)** | Quy trình đặt hàng - thanh toán trực tuyến - xử lý đóng gói và giao nhận vận chuyển (Order Fulfillment) | Chuẩn hóa các bước mua bán từ lúc chọn hàng đến khi hoàn tất đơn |

---

### 2. Phân biệt Dữ liệu (Data) vs Thông tin (Information)

| STT | Nội dung dữ liệu tại RikkeiShop | Dữ liệu (Data) | Thông tin (Information) | Lý do phân loại |
| :---: | :--- | :---: | :---: | :--- |
| 1 | 4.9 | [ x ] | [ ] | Số thực thô, chưa rõ là điểm đánh giá sao hay tỷ lệ giảm giá |
| 2 | Sản phẩm Áo thun nam size L có điểm đánh giá 4.9 sao từ 1.200 khách hàng | [ ] | [ x ] | Đầy đủ ngữ cảnh: Tên sản phẩm, Chỉ số đánh giá, Số lượt mua |
| 3 | GIAM50K | [ x ] | [ ] | Chuỗi ký tự mã giảm giá thô, chưa rõ điều kiện áp dụng |
| 4 | Doanh số chương trình Flash Sale ngày 09/09 đạt 3.5 tỷ VNĐ sau 2 giờ mở bán | [ ] | [ x ] | Đã được xử lý, có đầy đủ ngữ cảnh thời gian, sự kiện và mang lại giá trị phân tích kinh doanh |
| 5 | SP009, Giày thể thao, Đen, 42, 10 | [ x ] | [ ] | Chuỗi dữ liệu thô dạng danh sách thuộc tính, chưa được gán nhãn trường rõ ràng và chưa tạo thành báo cáo |

---

## BƯỚC 2: KHẢO SÁT MÔI TRƯỜNG VÀ XÁC ĐỊNH STAKEHOLDERS

### 1. Khảo sát Môi trường Hệ thống

| Yếu tố khảo sát tại RikkeiShop | Thuộc loại môi trường | Tầm ảnh hưởng đến hệ thống |
| :--- | :---: | :--- |
| Năng lực chịu tải của máy chủ vào các dịp lễ Sale lớn | Môi trường Nội bộ | Cần nâng cấp hạ tầng để web không bị sập nguồn khi đông người |
| Quy định bảo vệ quyền lợi người tiêu dùng thương mại điện tử | Môi trường Bên ngoài | Bắt buộc phải có chính sách đổi trả hàng minh bạch trong 7 ngày |
| Chương trình khuyến mãi giảm giá sâu của các sàn đối thủ | Môi trường Bên ngoài | Thúc đẩy hệ thống phát triển tính năng voucher linh hoạt |
| Mức độ thành thạo mua sắm trực tuyến của người tiêu dùng | **Môi trường Bên ngoài** | Đòi hỏi giao diện ứng dụng (UI/UX) phải đơn giản, luồng đặt hàng tối giản (1-click checkout) để người dùng ở mọi lứa tuổi thao tác dễ dàng |
| Tốc độ kết nối mạng Internet của người dùng trên điện thoại di động | **Môi trường Bên ngoài** | Ảnh hưởng trực tiếp đến thời gian tải trang; hệ thống cần nén ảnh sản phẩm, dùng CDN và hỗ trợ tải dữ liệu bất đồng bộ (Lazy loading) |

---

### 2. Xác định Stakeholders

| Nhóm Stakeholder | Vai trò trong dự án | Mối quan tâm lớn nhất đối với phần mềm |
| :--- | :--- | :--- |
| **1. Khách mua hàng (Buyer)** | Người tiêu dùng cuối | Tìm kiếm hàng nhanh, thanh toán mượt mà, mã giảm giá tự áp dụng |
| **2. Bộ phận Chăm sóc khách hàng** | Hỗ trợ giải quyết khiếu nại | Tra cứu nhanh lịch sử đơn hàng và xử lý yêu cầu đổi trả tiền |
| **3. Người bán hàng (Seller)** | Đối tác cung cấp nguồn hàng và trực tiếp vận hành gian hàng | Quản lý kho hàng chính xác theo thời gian thực (tránh tồn kho ảo dịp Flash Sale), thao tác xử lý đơn nhanh và đối soát dòng tiền doanh thu minh bạch |

---

## BƯỚC 3: LỰA CHỌN KỸ THUẬT THU THẬP YÊU CẦU VÀ SOẠN CÂU HỎI MẪU

### 1. Lựa chọn Kỹ thuật Thu thập Yêu cầu

| STT | Tình huống khảo sát tại RikkeiShop | Kỹ thuật phù hợp nhất | Lý do lựa chọn ngắn gọn |
| :---: | :--- | :--- | :--- |
| 1 | Thu thập đánh giá mức độ hài lòng từ 100.000 người mua hàng qua ứng dụng | Bảng câu hỏi / Khảo sát (Survey) | Quy mô rất lớn, thu thập nhanh số liệu định lượng về trải nghiệm |
| 2 | Trao đổi với Giám đốc Marketing về chiến lược phát hành mã giảm giá Tết | Phỏng vấn (Interview) | Lãnh đạo cấp cao, cần trao đổi sâu về quy tắc nghiệp vụ |
| 3 | Ngồi cạnh người dùng để xem thao tác tìm kiếm và thêm hàng vào giỏ | **Quan sát thực tế (Observation)** | Cho phép theo dõi hành vi tương tác thật, nhận diện điểm nghẽn (Pain Points) và thao tác lúng túng của người dùng mà bảng hỏi không mô tả được |
| 4 | Đọc tài liệu cổng thanh toán VNPay/Momo để nắm rõ quy chuẩn kết nối API | Nghiên cứu tài liệu (Document Analysis) | Tài liệu kỹ thuật tích hợp đã được chuẩn hóa |
| 5 | Thu thập ý kiến của nhóm 20 chủ shop lớn về tính năng quản lý kho hàng | **Nhóm tập trung (Focus Group) / Hội thảo yêu cầu** | Nhóm đối tượng có cùng quy mô vừa phải (20 người); thảo luận nhóm giúp kích hoạt nhiều ý kiến đa chiều và thống nhất nghiệp vụ quản lý kho |

---

### 2. Soạn câu hỏi trắc nghiệm khảo sát hành vi mua hàng

* **Câu hỏi:** "Hình thức thanh toán nào bạn thường xuyên sử dụng nhất khi mua hàng trên RikkeiShop?"
  * **A.** Thanh toán tiền mặt khi nhận hàng (COD)
  * **B.** Quét mã VietQR / Chuyển khoản ngân hàng tự động
  * **C.** Ví điện tử (MoMo, ZaloPay, ShopeePay)
  * **D.** Thẻ thanh toán quốc tế (Visa, MasterCard, JCB)

---

## BƯỚC 4: PHÂN LOẠI YÊU CẦU CHỨC NĂNG (FR) VÀ PHI CHỨC NĂNG (NFR)

| STT | Phát biểu yêu cầu | Phân loại (FR / NFR) | Mã định danh đề xuất | Câu hỏi cốt lõi giải thích |
| :---: | :--- | :---: | :---: | :--- |
| (1) | Khách hàng có thể tìm kiếm sản phẩm theo tên và lọc theo mức giá | FR | FR-01 | Tính năng tìm kiếm hệ thống cung cấp (LÀM GÌ) |
| (2) | Hệ thống phải xử lý được 50.000 đơn hàng đồng thời trong khung giờ Flash Sale | NFR | NFR-01 | Tiêu chuẩn chịu tải hiệu năng cao (TỐT NHƯ THẾ NÀO) |
| (3) | Thông tin thẻ ngân hàng của người mua phải được mã hóa chuẩn bảo mật quốc tế | NFR | NFR-02 | Tiêu chuẩn an toàn dữ liệu tài chính (TỐT NHƯ THẾ NÀO) |
| (4) | Người dùng có thể đánh giá sao và viết bình luận kèm hình ảnh sản phẩm | **FR** | **FR-02** | Chức năng cung cấp công cụ tương tác, phản hồi và chia sẻ trải nghiệm sản phẩm của người dùng (LÀM GÌ) |
| (5) | Thời gian phản hồi khi bấm nút Thanh toán không được vượt quá 1.5 giây | **NFR** | **NFR-03** | Tiêu chuẩn chất lượng về hiệu năng và tốc độ phản hồi của giao dịch (TỐT NHƯ THẾ NÀO) |

---

## BƯỚC 5: ĐẶC TẢ USER STORY CHUẨN BA THÀNH PHẦN

| Thành phần User Story | Nội dung đặc tả cho Người mua hàng |
| :--- | :--- |
| **Là một (Vai trò - Who):** | Người mua hàng trên RikkeiShop |
| **Tôi muốn (Hành động - What):** | Nhận thông báo tự động (Push notification/Email) ngay khi sản phẩm trong danh sách yêu thích được giảm giá hoặc mở bán trong khung giờ Flash Sale |
| **Để (Lợi ích - Why):** | Tôi có thể kịp thời vào đặt mua sản phẩm với mức giá ưu đãi nhất trước khi hết số lượng khuyến mại, tiết kiệm chi phí mà không cần mất công vào kiểm tra giá thủ công hàng ngày. |