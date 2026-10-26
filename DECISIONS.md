# DECISIONS — Quyết định đã chốt (agent không được tự đổi; muốn đổi phải hỏi người dùng)

Trạng thái: CHỐT hoặc TẠM (chờ người dùng xác nhận).

| Mã | Quyết định | Trạng thái |
|---|---|---|
| D-01 | Công nghệ: frontend React + TypeScript + Tailwind CSS; backend Node.js + NestJS + TypeScript; CSDL PostgreSQL qua Prisma; xác thực JWT + phân quyền theo vai trò (RBAC) + mật khẩu bcrypt | TẠM |
| D-02 | Một website duy nhất, có phần công khai và phần chức năng theo vai trò sau khi đăng nhập qua cùng một cổng | CHỐT |
| D-03 | Người bệnh đăng nhập bằng số CCCD (mô phỏng VNeID). Nhân viên đăng nhập bằng tài khoản riêng do quản trị viên cấp. Hai màn hình đăng nhập tách nhau để mọi thao tác duyệt, kê đơn, thu tiền gắn với một cá nhân | CHỐT |
| D-04 | Duyệt lịch hai tầng: y tá/nhân viên tiếp nhận duyệt chính (xác nhận, đổi khoa, từ chối); ca có dấu hiệu nặng hoặc khó xác định khoa thì chuyển lên bác sĩ | CHỐT |
| D-05 | Đơn thuốc: bác sĩ kê đơn, hệ thống sinh mã QR, người bệnh thanh toán, nhân viên phát thuốc quét QR xác thực rồi ghi nhận phát. KHÔNG quản lý tồn kho thuốc | CHỐT |
| D-06 | Dữ liệu y tế nền (nhóm máu, tiền sử phẫu thuật, dị ứng thuốc) lấy từ Sổ sức khỏe điện tử VNeID (MÔ PHỎNG bằng API giả) chỉ khi người bệnh đồng ý. Phải lưu bản ghi đồng ý và cho thu hồi | CHỐT |
| D-07 | Gợi ý chuyên khoa chỉ là gợi ý, không chẩn đoán. Mặc định dùng bảng ánh xạ từ khóa sang khoa, chạy nội bộ, không gửi dữ liệu ra ngoài. Đặt sau một giao diện riêng để thay được. Mô hình ngôn ngữ ngoài chỉ dùng khi demo với dữ liệu giả | CHỐT |
| D-08 | Thanh toán viện phí là MÔ PHỎNG (nút giả lập đổi trạng thái hóa đơn). Không dùng tiền thật | CHỐT |
| D-09 | Quét CCCD mô phỏng bằng quét mã QR trên CCCD hoặc nhập số CCCD. Không dùng đầu đọc chip | CHỐT |
| D-10 | Làm frontend trước với dữ liệu giả qua lớp `src/api/`, theo từng module, rồi nối backend thật cho module đó. Không làm hết frontend rồi mới làm backend | CHỐT |
| D-11 | Ngôn ngữ: tài liệu và giao diện tiếng Việt; tên biến, hàm, file bằng tiếng Anh; tên bảng và cột CSDL theo `docs/data-dictionary.md` | TẠM |
| D-12 | Ưu tiên: module nhóm A phải chạy thật; nhóm B làm sau nếu còn thời gian. Cắt nhóm B trước, không cắt tuần cuối | CHỐT |
| D-13 | Mỗi module xong phải có `docs/modules/Mxx.md` bằng tiếng Việt và chờ người dùng duyệt trước khi sang module sau | CHỐT |
| D-14 | Giao diện theo docs/design/design-system.md (token màu, font Be Vietnam Pro, thang chữ, khoảng cách, thành phần). Không dùng màu hay mã màu ngoài token. Khi lệch với bản Stitch hoặc Figma đã chốt thì bản đã chốt thắng. | CHỐT |
| D-15 | Chỉ có thiết kế desktop (Stitch). Khu nhân viên chỉ cần desktop. Khu công khai và người bệnh phải dùng được trên mobile 390: agent dựng responsive ngay từ đầu, theo quy tắc mobile trong docs/design/patient.md và design-system.md; nghiệm thu bằng acceptance-checklist.md ở độ rộng 390, không có ảnh mobile để so. | CHỐT |
| D-16 | Các chi tiết kỹ thuật xuất hiện trên giao diện nhưng thuộc ngoài phạm vi (PACS, HL7, ký số PKI, cổng BHXH, đầu đọc chip CCCD) chỉ là nhãn và dữ liệu mẫu hiển thị. Agent không làm tích hợp thật. Nhãn “CCCD gắn chip” ở NV-02 đổi thành “quét mã QR trên CCCD”. | CHỐT |
## Ngoài phạm vi (không làm, không thiết kế)

Nội trú, giường bệnh, phẫu thuật thật, nghiệp vụ cấp cứu, giám định BHYT với cơ quan bảo hiểm, quản lý tồn kho thuốc, kết nối thật với cổng thanh toán, ngân hàng, đầu đọc CCCD chip hay VNeID, liên thông EMR thật của bệnh viện, nhân sự, lương, kế toán, kho vật tư lớn, chẩn đoán bệnh tự động bằng AI.

## Lịch sử thay đổi quyết định

(Khi người dùng đồng ý đổi một quyết định, ghi: ngày, mã, nội dung cũ, nội dung mới, lý do.)
