### Đợt 2. Luồng đặt lịch của người bệnh (PB-01 đến PB-09), mobile 390 trước, rồi 1440

Bố cục luồng đặt lịch: thanh tiến trình 5 bước (Người khám, Loại hình, Chuyên khoa, Ngày giờ, Xác nhận). Desktop: cột trái là form rộng tối đa 720px, cột phải là thẻ "Tóm tắt lịch khám" dính theo cuộn. Mobile: thẻ tóm tắt thu gọn ở đầu ("Xem tóm tắt (3 mục)"), thanh cố định ở đáy gồm nút phụ "Quay lại" và nút đỏ "Tiếp tục". Không bắt nhập lại dữ liệu đã có trong hồ sơ.

| Mã | Màn hình | Yêu cầu riêng |
|---|---|---|
| PB-01 | Trang chủ người bệnh | Từ trên xuống: lời chào kèm tên; thẻ "Bước tiếp theo của bạn" (nội dung đổi theo trạng thái: chưa có lịch, chờ duyệt, đã xác nhận, cần thanh toán, chờ nhận thuốc); "Lịch hẹn sắp tới"; ba ô lối tắt lớn; 3 thông báo gần nhất; dòng "Bạn đang xem hồ sơ của: Bản thân" kèm nút đổi người |
| PB-02 | Bước 1, chọn người khám | Thẻ chọn: "Bản thân" và từng thân nhân (tên, quan hệ, tuổi). Thẻ "Thêm thân nhân" mở form trượt từ dưới (mobile) hoặc hộp thoại (desktop) |
| PB-03 | Bước 2, chọn loại hình | Ba thẻ chọn lớn (BHYT, Dịch vụ, Theo yêu cầu). Mỗi thẻ có mô tả hai dòng và dòng "Cần mang theo" liệt kê giấy tờ. Hiện đủ nội dung, không giấu trong phần gập |
| PB-04 | Bước 3, chọn chuyên khoa | Thẻ lớn đặt trên cùng "Tôi không chắc khoa nào" (viền xanh, mô tả "Bạn mô tả triệu chứng, hệ thống gợi ý khoa phù hợp"). Bên dưới là ô tìm khoa và lưới thẻ khoa (icon, tên, một dòng mô tả) |
| PB-05 | Bước 3b, mô tả triệu chứng và gợi ý khoa | Xem chi tiết bên dưới bảng |
| PB-06 | Bước 4, chọn ngày giờ | Lịch tháng với ô 48px. Ô có 4 trạng thái bằng chữ: còn chỗ, "Còn ít", "Hết", ngày đã qua hoặc Chủ nhật (mờ, có chú thích "Không nhận lịch"). Ngày chọn: nền blue-500 chữ trắng đậm 18px. Dưới lịch: khung giờ chia "Buổi sáng" và "Buổi chiều", chip cao 48px ghi giờ và "còn 3 chỗ" hoặc "Hết chỗ". Loại hình "Theo yêu cầu" có thêm danh sách chọn bác sĩ dạng thẻ. Mobile: dải tuần có nút "Xem cả tháng" |
| PB-07 | Bước 5, xác nhận | Danh sách tóm tắt từng mục, mỗi mục có liên kết "Sửa". Ô tích "Tôi xác nhận thông tin trên là đúng". Nút đỏ "Xác nhận đặt lịch". Dòng giải thích: "Nhân viên bệnh viện sẽ duyệt lịch. Bạn xem kết quả ở mục Lịch hẹn của tôi." |
| PB-08 | Phiếu hẹn khám | Khối xác nhận "Đã gửi lịch hẹn", thẻ trạng thái Chờ duyệt kèm "Điều này nghĩa là gì: nhân viên đang kiểm tra lịch của bạn. Bạn chỉ cần đến bệnh viện khi trạng thái là Đã xác nhận." Phiếu có mã LH, QR, ngày giờ, khoa, phòng, giấy tờ cần mang. Nút đỏ "Tải phiếu về máy", nút phụ "In phiếu", nút văn bản "Xem lịch hẹn của tôi". Có bản in A6 đen trắng |
| PB-09 | Lịch hẹn của tôi | Tab "Sắp tới", "Đã qua", "Đã hủy" và chip lọc trạng thái. Mỗi lịch là một thẻ có thẻ trạng thái, ngày giờ, khoa. Hành động "Đổi lịch" (nút phụ) và "Hủy lịch" (viền đỏ, mở hộp thoại chọn lý do) |

PB-05 chi tiết:
- Đầu màn hình luôn có khung khẩn cấp thu gọn: "Nếu bạn khó thở, đau ngực, ngất hoặc chảy máu nhiều, hãy gọi 115 hoặc đến cấp cứu ngay", kèm nút "Gọi 115".
- Ô nhập triệu chứng (đếm ký tự, tối đa 500, tối thiểu 10) và các chip chọn nhanh (Đau bụng, Ho, Sốt, Đau đầu, Đau khớp, Chóng mặt, Phát ban, Tiểu buốt). Bấm chip thì thêm chữ vào ô nhập.
- Dòng quyền riêng tư với biểu tượng khóa: "Nội dung bạn mô tả được xử lý trong hệ thống của bệnh viện."
- Nút đỏ "Gợi ý khoa". Trạng thái đang tải: skeleton và câu "Đang đọc mô tả của bạn". Chuyển sang kết quả sau khoảng 1,5 giây.
- Kết quả: thẻ chính "Khoa gợi ý: Nội tiêu hóa" kèm "Vì sao gợi ý khoa này" (các cụm từ hệ thống nhận ra được gạch dưới bằng blue-100 ngay trong câu của người bệnh); tối đa hai thẻ khoa thay thế dạng radio; ô "Lý do khám" đã điền sẵn và cho sửa; nút văn bản "Tôi muốn tự chọn khoa" (về PB-04); dòng lưu ý nguyên văn ở B7 mục 3.
- Biến thể khẩn: khi mô tả có dấu hiệu nặng, thay khối kết quả bằng khung khẩn cấp lớn (viền đỏ 2px, biểu tượng, chữ "Mô tả của bạn có dấu hiệu cần được xem ngay", nút đỏ "Gọi 115", nút phụ "Tôi vẫn muốn đặt lịch khám thường"). Không nêu tên bệnh.

Kết thúc theo B11.

### Đợt 3. Hồ sơ cá nhân người bệnh (PB-10 đến PB-21)

Cấu trúc: desktop có menu dọc bên trái với các mục Thông tin cá nhân, Thông tin y tế nền, Thân nhân, Hồ sơ khám, Kết quả, Đơn thuốc, Hóa đơn, Bác sĩ của tôi, Giấy tờ, Bảo hiểm y tế, Thông báo. Mobile: trang menu dạng danh sách có icon và nhãn, bấm vào mở trang con có nút "Quay lại".

| Mã | Màn hình | Yêu cầu riêng |
|---|---|---|
| PB-10 | Tổng quan hồ sơ | Thẻ thông tin chính, mức hoàn thiện hồ sơ bằng chữ ("Còn thiếu 1 mục"), lối tắt tới các mục. Hồ sơ khám và lộ trình chỉ hiện khi đã có lần khám hoặc đã đặt lịch, nếu chưa có thì hiện khung rỗng "Bạn chưa có lần khám nào" kèm nút "Đặt lịch khám" |
| PB-11 | Thông tin cá nhân và y tế nền | Hai khối. Khối "Thông tin y tế nền" có thẻ trạng thái đồng bộ VNeID ("Chưa đồng bộ" hoặc "Đã đồng bộ ngày 26/10/2026"), công tắc đồng ý/thu hồi, và dữ liệu nhóm máu, bệnh đã mắc và phẫu thuật, dị ứng thuốc. Dị ứng thuốc nằm trong khối amber-50 có icon và nhãn "Dị ứng thuốc: Penicillin" |
| PB-12 | Xin đồng ý lấy dữ liệu VNeID | Hộp thoại (mobile: bottom sheet). Tiêu đề "Cho phép bệnh viện lấy dữ liệu từ Sổ sức khỏe điện tử VNeID?". Danh sách dữ liệu sẽ lấy, ai được xem (bác sĩ trực tiếp khám cho bạn), mục đích, cách thu hồi. Hai nút "Đồng ý" (đỏ đặc) và "Không đồng ý" (nút phụ) cùng cỡ. Không tích sẵn, không dùng cách trình bày đẩy người dùng chọn một bên. Có hộp thoại thu hồi và câu nêu hệ quả sau khi thu hồi |
| PB-13 | Thân nhân | Danh sách thẻ (tên, quan hệ, quyền xem). Form thêm thân nhân: họ tên, quan hệ, ngày sinh, CCCD (trẻ em có thể không có CCCD). Khi chuyển sang xem hồ sơ thân nhân, đầu trang có dải cố định "Bạn đang xem hồ sơ của Nguyễn Thị Lan" và nút "Quay về hồ sơ của tôi" |
| PB-14 | Hồ sơ khám | Danh sách các lần khám theo thời gian. Chi tiết một lần khám có đường thời gian dọc: Tiếp nhận, Khám, Xét nghiệm và chẩn đoán hình ảnh, Kết luận của bác sĩ, Đơn thuốc, Thanh toán, mỗi mốc có nhãn Đã xong, Đang thực hiện hoặc Chưa tới |
| PB-15 | Kết quả khám và xét nghiệm | Thẻ theo từng kết quả (tên, ngày, nơi thực hiện), nút "Xem" và "Tải về". Chỉ số bất thường có chữ "Cao" hoặc "Thấp" kèm mũi tên, không chỉ dùng màu |
| PB-16 | Đơn thuốc | Danh sách và chi tiết. Chi tiết có QR cỡ 240px ở đầu, gợi ý "Tăng độ sáng màn hình khi đưa mã cho nhân viên", thẻ trạng thái, danh sách thuốc với cách dùng bằng lời thường ("Sáng 1 viên, tối 1 viên, uống sau ăn") |
| PB-17 | Hóa đơn và thanh toán | Danh sách hóa đơn. Chi tiết: bảng khoản phí, Tổng chi phí, BHYT chi trả, "Bạn cần trả" bằng chữ số lớn. Phương thức: Thẻ ngân hàng, Ví điện tử, Quét QR chuyển khoản, Thanh toán tại quầy. Nút đỏ "Thanh toán 128.300 đ", sau đó màn hình xử lý và biên nhận. Nhãn nhỏ "Chế độ mô phỏng" trên màn hình thanh toán |
| PB-18 | Bác sĩ của tôi | Gom theo từng lần khám (ngày, khoa), mỗi nhóm có bác sĩ riêng, không có danh sách chung |
| PB-19 | Giấy tờ bệnh viện | Danh sách giấy tờ, form "Yêu cầu giấy chuyển sang bệnh viện khác" (lý do, bệnh viện muốn chuyển đến, ghi chú), đường theo dõi Đã gửi yêu cầu, Đang duyệt, Đã duyệt, rồi "Tải về" và "In" |
| PB-20 | Bảo hiểm y tế | Thẻ BHYT che bớt số, hạn thẻ, nơi đăng ký KCB ban đầu, thẻ trạng thái Còn hạn, Sắp hết hạn hoặc Hết hạn kèm câu hướng dẫn việc cần làm |
| PB-21 | Thông báo | Nhóm theo ngày, thông báo chưa đọc có chấm xanh và chữ "Mới", bấm vào đi tới màn hình liên quan |

Kết thúc theo B11.
