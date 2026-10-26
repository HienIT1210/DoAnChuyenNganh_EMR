### B7. Quy tắc nghiệp vụ (phải thể hiện được trên giao diện)

Hệ thống là một website duy nhất, có phần công khai và các chức năng theo vai trò sau khi đăng nhập.

Vai trò và đăng nhập:
- Khách truy cập: xem phần công khai.
- Người bệnh: đăng nhập bằng số CCCD (mô phỏng VNeID).
- Nhân viên (Y tá/tiếp nhận, Bác sĩ, Kỹ thuật viên cận lâm sàng, Thu ngân, Nhân viên phát thuốc, Cán bộ quản lý, Quản trị viên): đăng nhập bằng tên đăng nhập và mật khẩu do quản trị viên cấp. Màn hình đăng nhập nhân viên tách riêng với người bệnh, để mọi thao tác duyệt, kê đơn, thu tiền gắn với một cá nhân.

Quy tắc:
1. Đặt lịch: chọn người đăng ký (bản thân hoặc thân nhân), chọn loại hình khám (BHYT, dịch vụ, theo yêu cầu), chọn chuyên khoa hoặc "Tôi không chắc khoa nào" rồi mô tả triệu chứng để hệ thống gợi ý khoa và tạo sẵn lý do khám, chọn ngày giờ, xác nhận, nhận phiếu hẹn có mã và in được.
2. Duyệt lịch hai tầng: y tá hoặc nhân viên tiếp nhận duyệt chính (xem triệu chứng, xác nhận hoặc đổi khoa so với gợi ý). Ca có dấu hiệu nặng, khẩn hoặc khó xác định khoa thì chuyển lên bác sĩ. Người bệnh thấy: Chờ duyệt, Đã xác nhận, Đã đổi khoa, Chờ bác sĩ xem xét, Từ chối.
3. Gợi ý chuyên khoa chỉ là gợi ý, không phải chẩn đoán, và hệ thống không bao giờ nêu tên bệnh. Luôn có dòng: "Gợi ý chỉ mang tính tham khảo, không thay thế chẩn đoán của bác sĩ. Nếu có dấu hiệu nặng, hãy đến cấp cứu hoặc gọi 115." Người bệnh luôn được sửa khoa.
4. Tiếp nhận: nhân viên quét QR trên CCCD hoặc nhập số CCCD. Hệ thống lấy hồ sơ nếu đã có, hoặc tạo hồ sơ mới từ thông tin CCCD. Cấp số thứ tự và in phiếu hướng dẫn (số thứ tự, phòng khám, các bước) cho người không có điện thoại.
5. Dữ liệu y tế nền (nhóm máu, tiền sử phẫu thuật hoặc tiểu phẫu, dị ứng thuốc) lấy từ Sổ sức khỏe điện tử VNeID (mô phỏng) chỉ khi người bệnh đồng ý. Phải có hộp thoại xin đồng ý rõ ràng, nêu dữ liệu nào được lấy, hai nút "Đồng ý" và "Không đồng ý" có độ nổi bật như nhau, và cho thu hồi sau này. Bác sĩ thấy khối "Thông tin y tế nền" nổi bật khi khám, nhất là dị ứng thuốc.
6. Khám: bác sĩ xem hồ sơ, khám và chẩn đoán, chỉ định cận lâm sàng, nhận kết quả, kê đơn, hẹn tái khám. Có thể cấp giấy chuyển tuyến.
7. Đơn thuốc: bác sĩ kê đơn, hệ thống sinh mã QR, người bệnh thanh toán, đến phòng phát thuốc đưa mã QR, nhân viên quét. Hợp lệ và đã thanh toán thì phát thuốc và ghi nhận. Không có quản lý tồn kho thuốc.
8. Thanh toán viện phí là mô phỏng (nút giả lập đổi trạng thái hóa đơn), nhưng giao diện trình bày như thật: danh sách khoản phí, tổng tiền, phần BHYT chi trả, phần người bệnh trả, phương thức thanh toán, trạng thái.
9. "Bác sĩ của tôi" gom theo từng lần khám, mỗi lần khám có bác sĩ riêng. Không dồn chung một danh sách.
10. Hồ sơ khám và lộ trình khám chỉ hiện khi đã có lần khám hoặc đã đặt lịch.
11. Giấy tờ bệnh viện (ví dụ chuyển tuyến): người bệnh gửi yêu cầu, nhân viên duyệt, người bệnh tải hoặc in.
12. Ngoài phạm vi, không thiết kế: nội trú, giường bệnh, phẫu thuật, cấp cứu, tồn kho thuốc, giám định BHYT với cơ quan bảo hiểm, lương và kế toán.
13. Bảng nhãn trạng thái cố định (dùng đúng chữ này ở mọi nơi):
- Lịch hẹn: Chờ duyệt, Đã xác nhận, Đã đổi khoa, Chờ bác sĩ xem xét, Từ chối, Đã hủy.
- Hóa đơn: Chưa thanh toán, Đã thanh toán.
- Đơn thuốc: Chưa thanh toán, Đã thanh toán và chờ nhận thuốc, Đã nhận thuốc.
- Giấy tờ: Đã gửi yêu cầu, Đang duyệt, Đã duyệt, Từ chối.
- Bảo hiểm y tế: Còn hạn, Sắp hết hạn, Hết hạn.
