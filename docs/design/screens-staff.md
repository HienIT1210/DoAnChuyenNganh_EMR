### Đợt 4. Nhân viên: tiếp nhận và bác sĩ (NV-01 đến NV-07, BS-01 đến BS-07), desktop 1440

| Mã | Màn hình | Yêu cầu riêng |
|---|---|---|
| NV-01 | Bảng điều khiển tiếp nhận | Nút đỏ "Quét CCCD" ở góc trên phải, ghi phím tắt F2. Bốn ô số: Chờ duyệt, Chờ tiếp nhận, Đang chờ khám, Chờ bác sĩ xem xét. Bảng hàng đợi (số thứ tự, họ tên, giờ hẹn, khoa, trạng thái) |
| NV-02 | Quét CCCD và tra cứu | Chia đôi: trái là khung camera mô phỏng, phải là ô nhập số CCCD. Kết quả có hai nhánh: tìm thấy (thẻ tóm tắt người bệnh, nút đỏ "Cấp số thứ tự") hoặc không thấy (khung thông tin "Chưa có hồ sơ", nút đỏ "Tạo hồ sơ mới từ CCCD") |
| NV-03 | Tạo hoặc xác nhận hồ sơ mới | Form điền sẵn, trường lấy từ CCCD có nhãn "Từ CCCD", cho sửa. Nút đỏ "Lưu hồ sơ" |
| NV-04 | Lịch chờ duyệt | Bảng có chip lọc theo trạng thái, khoa, ngày. Bấm một dòng mở NV-05 |
| NV-05 | Chi tiết duyệt lịch | Thông tin người bệnh ở đầu. Hai cột so sánh "Người bệnh chọn" và "Hệ thống gợi ý". Dưới đó là khối triệu chứng nguyên văn người bệnh viết (nền blue-50, cụm từ nhận ra được tô). Thanh hành động cố định ở đáy: "Xác nhận" (đỏ đặc, nút chính), "Đổi khoa" (nút phụ, chọn khoa mới), "Chuyển lên bác sĩ" (nút phụ, bắt buộc chọn dấu hiệu và nhập lý do), "Từ chối" (viền đỏ, bắt buộc nhập lý do). Sau mỗi hành động có toast và quay về danh sách |
| NV-06 | Cấp số và in phiếu | Xem trước phiếu A6 đen trắng: số thứ tự 96px, phòng khám, các bước thực hiện, QR. Nút đỏ "In phiếu" |
| NV-07 | Duyệt giấy tờ | Bảng yêu cầu, ngăn bên phải hiện chi tiết, nút đỏ "Duyệt", nút viền đỏ "Từ chối" (bắt buộc lý do) |
| BS-01 | Bảng điều khiển bác sĩ | Hai tab "Chờ khám (n)" và "Ca chuyển lên cần xem xét (n)". Mỗi người bệnh là một thẻ có cờ "Dị ứng thuốc" nếu có. Nút đỏ "Gọi khám" |
| BS-02 | Xem xét ca chuyển lên | Triệu chứng nguyên văn, ghi chú của y tá, lựa chọn quyết định (Giữ khoa, Đổi khoa, Cần khám sớm, Từ chối), ô ghi chú, nút đỏ "Gửi quyết định" |
| BS-03 | Màn hình khám bệnh | Xem yêu cầu riêng bên dưới bảng |
| BS-04 | Chỉ định cận lâm sàng | Danh mục tìm kiếm và chọn theo nhóm (xét nghiệm máu, nước tiểu, hình ảnh), danh sách đã chọn ở cột phải kèm ghi chú, nút đỏ "Gửi chỉ định" |
| BS-05 | Kê đơn thuốc | Ô tìm thuốc có gợi ý (tên, hoạt chất, hàm lượng), bảng dòng thuốc (liều, số ngày, số lượng tự tính, cách dùng). Khối dị ứng thuốc luôn hiện ở đầu màn hình. Khi thuốc xung đột dị ứng, hiện cảnh báo chặn (viền đỏ, icon, chữ "Thuốc này thuộc nhóm bệnh nhân bị dị ứng") và bắt nhập lý do mới được tiếp tục. Nút đỏ "Hoàn tất đơn và tạo mã QR", sau đó hộp thoại xem QR |
| BS-06 | Hẹn tái khám | Chọn ngày, giờ, dặn dò, nút đỏ "Lưu lịch hẹn khám lại" |
| BS-07 | Cấp giấy chuyển tuyến | Form (lý do, nơi chuyển đến, tóm tắt tình trạng), xem trước bản in A5, nút đỏ "Cấp giấy" |

BS-03 (làm kỹ nhất):
- Desktop 1440 chia ba cột. Cột trái 320px, dính khi cuộn: thông tin người bệnh và khối "Thông tin y tế nền" nổi bật (nhóm máu, tiền sử, và dị ứng thuốc trong khối amber-50 có icon cùng chữ). Cột giữa: các tab Khám và chẩn đoán, Chỉ định cận lâm sàng, Kết quả cận lâm sàng, Đơn thuốc, Hẹn tái khám. Cột phải 320px: lịch sử các lần khám trước dạng thu gọn, kết quả mới về có nhãn "Mới".
- Tab Khám và chẩn đoán: lý do khám (điền sẵn từ lịch đặt), bệnh sử, lưới sinh hiệu (mạch, huyết áp, nhiệt độ, nhịp thở, SpO2, cân nặng, chiều cao, BMI tự tính), khám lâm sàng, chẩn đoán (tìm mã ICD, hiển thị dạng chip), hướng xử trí, ghi chú.
- Dòng trạng thái "Đã lưu lúc 10:42" tự cập nhật. Phím tắt Ctrl+S lưu. Thanh dưới cố định: nút phụ "Lưu nháp" và nút đỏ "Hoàn tất khám" (mở hộp thoại kiểm tra mục còn thiếu).
- Bản tablet 768: ba cột thành ba tab "Người bệnh | Khám | Lịch sử", khối dị ứng vẫn dính ở đầu.

Kết thúc theo B11.

### Đợt 5. Nhân viên: cận lâm sàng, thu ngân, phát thuốc, quản lý và quản trị, desktop 1440

| Mã | Màn hình | Yêu cầu riêng |
|---|---|---|
| CLS-01 | Danh sách chỉ định cần thực hiện | Bảng theo giờ chỉ định, chip lọc loại xét nghiệm và trạng thái |
| CLS-02 | Nhập kết quả | Form theo loại xét nghiệm: bảng chỉ số với giá trị tham chiếu, tự gắn "Cao" hoặc "Thấp" kèm mũi tên, đính kèm tệp hoặc hình, nút đỏ "Gửi kết quả" |
| TN-01 | Hóa đơn chờ thu | Ô tìm theo CCCD hoặc mã hóa đơn, bảng hóa đơn |
| TN-02 | Chi tiết và thu tiền | Bảng khoản phí, BHYT chi trả, số tiền thu, chọn phương thức, nút đỏ "Xác nhận đã thu", in hóa đơn |
| PT-01 | Quét mã QR đơn thuốc | Khung quét lớn và ô nhập mã tay |
| PT-02 | Kết quả xác thực | Ba trạng thái chiếm cả vùng nội dung, chữ 32px, có icon lớn và câu hướng dẫn việc tiếp theo: "Hợp lệ và đã thanh toán" (kèm danh sách thuốc cần phát và nút đỏ "Xác nhận đã phát thuốc"), "Chưa thanh toán" (hướng người bệnh đến quầy thu ngân), "Đã phát hoặc không hợp lệ" (nêu lý do và thời điểm phát trước đó). Không chỉ dựa vào màu |
| QT-01 | Thống kê và báo cáo | Chip khoảng thời gian (Hôm nay, 7 ngày, 30 ngày, Tùy chọn). Thẻ số: số lượt khám, thời gian chờ trung bình, tỷ lệ gợi ý khoa được chấp nhận, doanh thu mô phỏng. Biểu đồ đường lượt khám theo ngày và biểu đồ cột theo khoa, chủ yếu dùng các sắc xanh, có nhãn số trực tiếp. Nút phụ "Xuất báo cáo" |
| QT-02 | Tài khoản và phân quyền | Bảng tài khoản, ngăn bên tạo hoặc sửa, gán vai trò, khóa, đặt lại mật khẩu (hộp thoại xác nhận) |
| QT-03 | Quản lý danh mục | Tab Khoa, Phòng khám, Bác sĩ, Dịch vụ và cận lâm sàng, Lịch làm việc. Mỗi tab là bảng và ngăn bên chỉnh sửa |
| QT-04 | Quản lý nội dung trang chủ | Danh sách tin, giới thiệu, đội ngũ bác sĩ. Trình soạn đơn giản có xem trước |
| QT-05 | Nhật ký thao tác | Bảng (ai, làm gì, lúc nào, màn hình nào), lọc theo người, loại thao tác, ngày, xuất tệp |

Kết thúc theo B11.
