# Từ điển dữ liệu EMR

## Phần 1. Tổng quan

Cơ sở dữ liệu gồm 35 bảng trên PostgreSQL, truy cập qua Prisma, giữ nguyên toàn bộ danh sách bảng gợi ý của bạn (không gộp, tách hay đổi tên). Phần này nêu các quy ước chung áp dụng cho mọi bảng ở Phần 2.

### 1.1 Nguyên tắc đặt tên

- Tên bảng và tên cột dùng `snake_case`, tiếng Việt không dấu, tên bảng ở số ít (`nguoi_benh`, không phải `cac_nguoi_benh`). Chỉ viết tắt những từ quen thuộc: `cccd`, `bhyt`, `cls` (trong tên cột), `qr`.
- Khóa chính luôn tên `id`. Khóa ngoại tên `<tên bảng cha>_id`, ví dụ `nguoi_benh_id`, `lich_hen_id`. Khi một bảng có hai khóa ngoại cùng trỏ về một bảng cha, thêm hậu tố vai trò: `nguoi_dat_id`, `khoa_goi_y_id`, `khoa_y_ta_xac_nhan_id`.
- Mã nghiệp vụ hiển thị cho con người bắt đầu bằng `ma_` (`ma_khoa`, `ma_lich_hen`). Cột thời điểm kết thúc bằng `_luc` (kiểu `TIMESTAMPTZ`), cột ngày bắt đầu bằng `ngay_` (kiểu `DATE`), cột đúng/sai bắt đầu bằng `la_`, `co_`, `da_` hoặc `dang_`.
- Giá trị ENUM viết `UPPER_SNAKE_CASE` không dấu (`CHO_DUYET`); tên hiển thị tiếng Việt có dấu nằm ở Phần 3.
- Chỉ mục đặt tên `idx_<bảng>_<cột>`, ràng buộc duy nhất đặt tên `uq_<bảng>_<cột>`.

### 1.2 Quy ước kiểu dữ liệu (PostgreSQL)

| Nhóm | Kiểu PostgreSQL | Dùng cho |
|---|---|---|
| Khóa | `UUID` | Mọi cột `id` và mọi khóa ngoại |
| Văn bản ngắn | `VARCHAR(n)` | Tên, mã, số điện thoại, đường dẫn; luôn ghi độ dài tối đa |
| Văn bản dài | `TEXT` | Mô tả triệu chứng, nội dung bài viết, lời dặn |
| Mã cố định độ dài | `CHAR(n)` | Số CCCD (12 số), số thẻ BHYT (15 ký tự) |
| Số nguyên | `SMALLINT`, `INTEGER` | Số lượng, phần trăm, số thứ tự |
| Tiền tệ | `NUMERIC(12,0)` | Mọi số tiền VND (không dùng số thực dấu phẩy động để tránh sai số làm tròn) |
| Đúng/sai | `BOOLEAN` | Các cờ; luôn `NOT NULL` kèm giá trị mặc định |
| Ngày | `DATE` | Ngày sinh, ngày hẹn, ngày tái khám |
| Giờ | `TIME` | Giờ hẹn, giờ bắt đầu ca làm việc |
| Thời điểm | `TIMESTAMPTZ` | Mọi thời điểm sự kiện; lưu UTC, hiển thị giờ Việt Nam (UTC+7) |
| Danh sách cố định | `ENUM` (Prisma `enum`) | Mọi cột trạng thái và loại; liệt kê ở Phần 3 |
| Dữ liệu cấu trúc thay đổi | `JSONB` | Kết quả cận lâm sàng, dữ liệu trước/sau trong nhật ký |
| Địa chỉ mạng | `INET` | Địa chỉ IP trong nhật ký |

### 1.3 Quy ước khóa chính

Mọi bảng dùng khóa chính `id UUID`, sinh bằng `gen_random_uuid()` (Prisma: `@default(uuid())`). Chọn UUID thay vì `BIGSERIAL` vì ba lý do: (1) không đoán được, tránh việc người dùng đổi số trên đường dẫn để xem hồ sơ người khác; (2) không lộ số lượng bệnh nhân hay hóa đơn; (3) sinh được ở phía ứng dụng, thuận tiện khi nạp dữ liệu mẫu. Nhược điểm là chỉ mục lớn hơn một chút, không đáng kể ở quy mô đề tài. Mọi khóa ngoại đều là `UUID`, nên khớp kiểu với khóa chính được tham chiếu.

UUID không thân thiện khi đọc, vì vậy mỗi thực thể người dùng nhìn thấy có thêm một mã nghiệp vụ riêng (`ma_benh_nhan`, `ma_lich_hen`, `ma_don_thuoc`). Mã QR trên phiếu hẹn và đơn thuốc là một token ngẫu nhiên (`ma_qr`, tối thiểu 128 bit), không chứa dữ liệu bệnh, dữ liệu thật luôn tra từ cơ sở dữ liệu.

### 1.4 Cột chung

| Cột | Kiểu | Mặc định | Ý nghĩa | Bảng áp dụng |
|---|---|---|---|---|
| `tao_luc` | `TIMESTAMPTZ NOT NULL` | `now()` | Thời điểm tạo bản ghi | Mọi bảng |
| `cap_nhat_luc` | `TIMESTAMPTZ NOT NULL` | `now()` | Thời điểm sửa gần nhất (Prisma `@updatedAt`) | Mọi bảng trừ 3 bảng chỉ thêm: `lich_su_trang_thai_lich_hen`, `phat_thuoc`, `nhat_ky_thao_tac` |
| `xoa_luc` | `TIMESTAMPTZ NULL` | `NULL` | Xóa mềm: có giá trị nghĩa là đã ẩn | `nguoi_benh`, `than_nhan`, `nhan_vien`, `lan_kham`, `tin_tuc` |

Các bảng danh mục không dùng `xoa_luc` mà dùng cột `dang_su_dung BOOLEAN` (ngừng sử dụng nhưng vẫn giữ để các hồ sơ cũ còn tham chiếu được). Các bảng nghiệp vụ còn lại không xóa, chỉ chuyển trạng thái (`HUY`, `DA_HUY`).

### 1.5 Quy ước định dạng dữ liệu

- CCCD: 12 chữ số. Số điện thoại: 10 chữ số, bắt đầu bằng 0. Số thẻ BHYT: 15 ký tự. Email: chuẩn RFC 5322 rút gọn, chữ thường.
- Tiền: VND, số nguyên, không âm (`CHECK >= 0`).
- Văn bản: UTF-8, cắt khoảng trắng hai đầu; không lưu chuỗi rỗng, dùng `NULL`.
- Thời điểm: `TIMESTAMPTZ` lưu UTC; ngày giờ hẹn khám lưu theo giờ địa phương của bệnh viện bằng `DATE` + `TIME`.

### 1.6 Các quyết định thiết kế cần biết trước

1. `tai_khoan` chỉ dành cho nhân viên. Người bệnh đăng nhập bằng CCCD (mô phỏng VNeID) nên không có mật khẩu và không có bản ghi trong `tai_khoan`.
1. `nguoi_benh.ma_cccd` cho phép `NULL` để lưu được thân nhân dưới 14 tuổi chưa có CCCD. Thân nhân luôn là một bản ghi `nguoi_benh` riêng, `than_nhan` chỉ lưu quan hệ giữa hai hồ sơ.
1. `thong_tin_y_te_nen` lưu mỗi dòng một mục (một nhóm máu, một dị ứng, một tiền sử phẫu thuật) để thêm/bớt và thu hồi từng mục dễ dàng.
1. Quyền không có bảng riêng: quyền gắn cố định với 9 vai trò theo Phần 6, quản trị viên phân quyền bằng cách gán vai trò cho tài khoản.
1. Chi tiết các quyết định còn lại (lịch làm việc theo ngày cụ thể, một hóa đơn cho mỗi lần khám, …) nằm ở phần mô tả từng bảng và được tổng hợp ở Phần 8.

### 1.7 Bảng tổng hợp danh sách bảng

Cột “Cột chung”: T = `tao_luc`, C = `cap_nhat_luc`, X = `xoa_luc`.

| STT | Tên bảng | Mô tả | Nhóm nghiệp vụ | Vai trò dùng chính | Cột chung |
|---|---|---|---|---|---|
| 1 | `tai_khoan` | Tài khoản đăng nhập của nhân viên | Người dùng và phân quyền | Quản trị viên | T, C |
| 2 | `vai_tro` | Danh mục 9 vai trò của hệ thống | Người dùng và phân quyền | Quản trị viên | T, C |
| 3 | `nhan_vien` | Hồ sơ cá nhân của nhân viên, gắn một tài khoản | Người dùng và phân quyền | Quản trị viên, Cán bộ quản lý | T, C, X |
| 4 | `nguoi_benh` | Hồ sơ người bệnh, định danh bằng CCCD | Người bệnh | Người bệnh, Y tá | T, C, X |
| 5 | `than_nhan` | Quan hệ giữa người bệnh và thân nhân | Người bệnh | Người bệnh | T, C, X |
| 6 | `thong_tin_y_te_nen` | Nhóm máu, tiền sử phẫu thuật, dị ứng thuốc | Người bệnh | Người bệnh, Bác sĩ | T, C |
| 7 | `dong_y_chia_se_du_lieu` | Bản ghi đồng ý chia sẻ dữ liệu y tế nền | Người bệnh | Người bệnh | T, C |
| 8 | `bao_hiem_y_te` | Thẻ bảo hiểm y tế của người bệnh | Người bệnh | Người bệnh, Y tá, Thu ngân | T, C |
| 9 | `khoa` | Danh mục khoa (lâm sàng, cận lâm sàng) | Danh mục | Quản trị viên | T, C |
| 10 | `phong_kham` | Danh mục phòng thuộc khoa | Danh mục | Quản trị viên | T, C |
| 11 | `bac_si` | Hồ sơ chuyên môn của bác sĩ | Danh mục | Quản trị viên, Khách truy cập (xem) | T, C |
| 12 | `lich_lam_viec` | Ca làm việc của bác sĩ theo ngày và phòng | Danh mục | Cán bộ quản lý | T, C |
| 13 | `dich_vu_kham` | Dịch vụ khám và đơn giá theo loại hình khám | Danh mục | Quản trị viên | T, C |
| 14 | `danh_muc_can_lam_sang` | Danh mục xét nghiệm, chẩn đoán hình ảnh | Danh mục | Quản trị viên, Bác sĩ | T, C |
| 15 | `danh_muc_thuoc` | Danh mục thuốc (không quản lý tồn kho) | Danh mục | Quản trị viên, Bác sĩ | T, C |
| 16 | `tu_khoa_trieu_chung` | Ánh xạ từ khóa triệu chứng sang khoa | Danh mục | Quản trị viên | T, C |
| 17 | `lich_hen` | Lịch hẹn khám ngoại trú | Đặt lịch và tiếp nhận | Người bệnh, Y tá | T, C |
| 18 | `lich_su_trang_thai_lich_hen` | Lịch sử đổi trạng thái và đổi khoa của lịch hẹn | Đặt lịch và tiếp nhận | Y tá, Bác sĩ | T |
| 19 | `goi_y_chuyen_khoa` | Kết quả gợi ý chuyên khoa từ mô tả triệu chứng | Đặt lịch và tiếp nhận | Người bệnh, Y tá | T, C |
| 20 | `so_thu_tu` | Số thứ tự khám cấp khi tiếp nhận | Đặt lịch và tiếp nhận | Y tá | T, C |
| 21 | `lan_kham` | Một lần khám, gắn một bác sĩ | Khám bệnh | Bác sĩ | T, C, X |
| 22 | `chan_doan` | Chẩn đoán của một lần khám | Khám bệnh | Bác sĩ | T, C |
| 23 | `chi_dinh_can_lam_sang` | Chỉ định cận lâm sàng của bác sĩ | Khám bệnh | Bác sĩ, Kỹ thuật viên | T, C |
| 24 | `ket_qua_can_lam_sang` | Kết quả cận lâm sàng | Khám bệnh | Kỹ thuật viên | T, C |
| 25 | `hen_tai_kham` | Hẹn tái khám do bác sĩ đặt | Khám bệnh | Bác sĩ | T, C |
| 26 | `don_thuoc` | Đơn thuốc kèm mã QR | Đơn thuốc | Bác sĩ, Nhân viên phát thuốc | T, C |
| 27 | `chi_tiet_don_thuoc` | Từng dòng thuốc trong đơn | Đơn thuốc | Bác sĩ | T, C |
| 28 | `phat_thuoc` | Ghi nhận lần phát thuốc | Đơn thuốc | Nhân viên phát thuốc | T |
| 29 | `hoa_don` | Hóa đơn viện phí của một lần khám | Tài chính | Thu ngân, Người bệnh | T, C |
| 30 | `chi_tiet_hoa_don` | Từng khoản trong hóa đơn | Tài chính | Thu ngân | T, C |
| 31 | `thanh_toan` | Lần thanh toán (mô phỏng) của hóa đơn | Tài chính | Thu ngân, Người bệnh | T, C |
| 32 | `giay_to_benh_vien` | Yêu cầu và duyệt giấy tờ bệnh viện | Giấy tờ | Người bệnh, Y tá, Bác sĩ | T, C |
| 33 | `tin_tuc` | Tin tức, giới thiệu, hướng dẫn trên trang công khai | Nội dung và hệ thống | Cán bộ quản lý, Khách truy cập (xem) | T, C, X |
| 34 | `thong_bao` | Thông báo gửi người bệnh | Nội dung và hệ thống | Người bệnh | T, C |
| 35 | `nhat_ky_thao_tac` | Nhật ký thao tác và truy cập dữ liệu | Nội dung và hệ thống | Quản trị viên, Cán bộ quản lý | T |

## Phần 2. Từ điển dữ liệu chi tiết

Mỗi bảng gồm một dòng mô tả mục đích, bảng cột 12 cột theo đúng mẫu yêu cầu, rồi phần khóa ngoại, chỉ mục và quy tắc nghiệp vụ. Trong cột “Khóa”: PK là khóa chính, FK là khóa ngoại, UK là ràng buộc duy nhất. Mức nhạy cảm dùng 4 bậc: Công khai, Nội bộ, Nhạy cảm (định danh), Rất nhạy cảm (y tế). Ví dụ đều là dữ liệu giả.

### 2.1 Nhóm người dùng và phân quyền

#### 2.1.1 Bảng tai_khoan (Tài khoản nhân viên)

Mục đích: lưu thông tin đăng nhập của nhân viên do quản trị viên cấp, tách biệt hoàn toàn với người bệnh.

| STT | Tên cột | Tên hiển thị | Kiểu dữ liệu | Độ dài/định dạng | Khóa | Bắt buộc | Mặc định | Ràng buộc/Quy tắc | Mô tả | Ví dụ | Mức nhạy cảm |
|---|---|---|---|---|---|---|---|---|---|---|---|
| 1 | `id` | Mã tài khoản | UUID | 36 ký tự | PK | Y | `gen_random_uuid()` | Duy nhất | Khóa chính nội bộ | 7c9e6679-7425-40de-944b-e07fc1f90ae7 | Nội bộ |
| 2 | `ten_dang_nhap` | Tên đăng nhập | VARCHAR | 4-50, `[a-z0-9._]` | UK | Y |  | Duy nhất, so sánh không phân biệt hoa thường | Tên nhân viên dùng để đăng nhập | bs.nguyenvana | Nhạy cảm |
| 3 | `mat_khau_bam` | Mật khẩu (đã băm) | VARCHAR | 60 ký tự, bcrypt |  | Y |  | Chỉ lưu băm bcrypt (cost từ 10), không bao giờ trả ra API | Chuỗi băm của mật khẩu | $2b$10$N9qo8uLOickgx2ZMRZoMye... | Nhạy cảm |
| 4 | `vai_tro_id` | Vai trò | UUID | 36 ký tự | FK | Y |  | Phải trỏ tới vai trò có `la_vai_tro_nhan_vien = true` | Vai trò của tài khoản | 5d1f0a2e-8b3c-4c7d-9e11-2a6b7c8d9e01 | Nội bộ |
| 5 | `trang_thai` | Trạng thái tài khoản | ENUM `trang_thai_tai_khoan` | HOAT_DONG, TAM_KHOA, VO_HIEU |  | Y | `HOAT_DONG` | Chỉ tài khoản HOAT_DONG mới đăng nhập được | Trạng thái sử dụng | HOAT_DONG | Nội bộ |
| 6 | `so_lan_dang_nhap_sai` | Số lần đăng nhập sai liên tiếp | SMALLINT | 0-10 |  | Y | 0 | `CHECK` 0-10; về 0 khi đăng nhập đúng; đạt 5 thì đặt `khoa_den_luc` | Đếm để chống dò mật khẩu | 0 | Nội bộ |
| 7 | `khoa_den_luc` | Khóa tạm đến lúc | TIMESTAMPTZ | ISO 8601 |  | N | `NULL` | Sau thời điểm này mới được thử lại | Thời điểm hết khóa tạm | 2026-10-26T04:30:00Z | Nội bộ |
| 8 | `dang_nhap_cuoi_luc` | Đăng nhập gần nhất lúc | TIMESTAMPTZ | ISO 8601 |  | N | `NULL` |  | Lần đăng nhập thành công gần nhất | 2026-10-26T01:05:00Z | Nội bộ |
| 9 | `phai_doi_mat_khau` | Phải đổi mật khẩu | BOOLEAN | true/false |  | Y | `true` | `true` khi mới cấp hoặc quản trị viên đặt lại | Bắt buộc đổi mật khẩu ở lần đăng nhập kế tiếp | true | Nội bộ |
| 10 | `tao_luc` | Tạo lúc | TIMESTAMPTZ | ISO 8601 |  | Y | `now()` |  | Thời điểm tạo | 2026-09-01T02:00:00Z | Nội bộ |
| 11 | `cap_nhat_luc` | Cập nhật lúc | TIMESTAMPTZ | ISO 8601 |  | Y | `now()` | Tự cập nhật khi sửa | Thời điểm sửa gần nhất | 2026-10-26T01:05:00Z | Nội bộ |

- Khóa ngoại: `vai_tro_id` → `vai_tro(id)`.
- Chỉ mục: `uq_tai_khoan_ten_dang_nhap` trên `lower(ten_dang_nhap)`; `idx_tai_khoan_vai_tro_id`.
- Quy tắc: người bệnh không có dòng ở bảng này. Mật khẩu không có bản rõ; đặt lại mật khẩu tạo băm mới. Vô hiệu tài khoản bằng `trang_thai`, không xóa, để giữ liên kết với nhật ký thao tác.

#### 2.1.2 Bảng vai_tro (Vai trò)

Mục đích: danh mục 9 vai trò cố định của hệ thống; quyền của từng vai trò được đặt trong mã nguồn theo Phần 6.

| STT | Tên cột | Tên hiển thị | Kiểu dữ liệu | Độ dài/định dạng | Khóa | Bắt buộc | Mặc định | Ràng buộc/Quy tắc | Mô tả | Ví dụ | Mức nhạy cảm |
|---|---|---|---|---|---|---|---|---|---|---|---|
| 1 | `id` | Mã vai trò | UUID | 36 ký tự | PK | Y | `gen_random_uuid()` | Duy nhất | Khóa chính nội bộ | 5d1f0a2e-8b3c-4c7d-9e11-2a6b7c8d9e01 | Nội bộ |
| 2 | `ma_vai_tro` | Mã vai trò | VARCHAR | 1-30, `[A-Z_]` | UK | Y |  | Duy nhất; chỉ gồm 9 giá trị ở Phần 3 | Mã dùng trong mã nguồn và token đăng nhập | BAC_SI | Nội bộ |
| 3 | `ten_vai_tro` | Tên vai trò | VARCHAR | 1-100 |  | Y |  |  | Tên hiển thị tiếng Việt có dấu | Bác sĩ | Nội bộ |
| 4 | `mo_ta` | Mô tả | VARCHAR | 0-255 |  | N | `NULL` |  | Phạm vi việc của vai trò | Khám, chẩn đoán, kê đơn | Nội bộ |
| 5 | `la_vai_tro_nhan_vien` | Là vai trò nhân viên | BOOLEAN | true/false |  | Y | `true` | `false` với KHACH và NGUOI_BENH (không có tài khoản nhân viên) | Phân biệt vai trò có thể gán cho tài khoản | true | Nội bộ |
| 6 | `tao_luc` | Tạo lúc | TIMESTAMPTZ | ISO 8601 |  | Y | `now()` |  | Thời điểm tạo | 2026-09-01T02:00:00Z | Nội bộ |
| 7 | `cap_nhat_luc` | Cập nhật lúc | TIMESTAMPTZ | ISO 8601 |  | Y | `now()` | Tự cập nhật khi sửa | Thời điểm sửa gần nhất | 2026-09-01T02:00:00Z | Nội bộ |

- Khóa ngoại: không có.
- Chỉ mục: `uq_vai_tro_ma_vai_tro`.
- Quy tắc: chỉ nạp bằng `seed`, không thêm/sửa/xóa trong lúc chạy; quản trị viên chỉ gán vai trò, không đổi quyền.

#### 2.1.3 Bảng nhan_vien (Nhân viên)

Mục đích: lưu thông tin cá nhân tối thiểu của nhân viên, gắn một-một với tài khoản; lương, hợp đồng, chấm công nằm ngoài phạm vi.

| STT | Tên cột | Tên hiển thị | Kiểu dữ liệu | Độ dài/định dạng | Khóa | Bắt buộc | Mặc định | Ràng buộc/Quy tắc | Mô tả | Ví dụ | Mức nhạy cảm |
|---|---|---|---|---|---|---|---|---|---|---|---|
| 1 | `id` | Mã nhân viên (nội bộ) | UUID | 36 ký tự | PK | Y | `gen_random_uuid()` | Duy nhất | Khóa chính nội bộ | 9a8b7c6d-5e4f-4a3b-8c2d-1e0f9a8b7c6d | Nội bộ |
| 2 | `tai_khoan_id` | Tài khoản | UUID | 36 ký tự | FK, UK | Y |  | Mỗi tài khoản thuộc đúng một nhân viên | Liên kết 1-1 với tài khoản đăng nhập | 7c9e6679-7425-40de-944b-e07fc1f90ae7 | Nội bộ |
| 3 | `ma_nhan_vien` | Mã nhân viên | VARCHAR | 4-20, `[A-Z0-9]` | UK | Y |  | Duy nhất | Mã hiển thị do bệnh viện cấp | NV0123 | Nội bộ |
| 4 | `ho_ten` | Họ và tên | VARCHAR | 1-100 |  | Y |  | Cắt khoảng trắng hai đầu | Họ tên đầy đủ | Nguyễn Văn An | Nội bộ |
| 5 | `so_dien_thoai` | Số điện thoại | VARCHAR | 10 số, `^0[0-9]{9}$` |  | N | `NULL` |  | Số liên hệ công việc | 0901234567 | Nội bộ |
| 6 | `email` | Email | VARCHAR | tối đa 255 | UK | N | `NULL` | Duy nhất khi có; chữ thường | Email công việc | an.nv@example.vn | Nội bộ |
| 7 | `chuc_danh` | Chức danh | VARCHAR | 0-100 |  | N | `NULL` |  | Chức danh chuyên môn | Điều dưỡng | Nội bộ |
| 8 | `tao_luc` | Tạo lúc | TIMESTAMPTZ | ISO 8601 |  | Y | `now()` |  | Thời điểm tạo | 2026-09-01T02:00:00Z | Nội bộ |
| 9 | `cap_nhat_luc` | Cập nhật lúc | TIMESTAMPTZ | ISO 8601 |  | Y | `now()` | Tự cập nhật khi sửa | Thời điểm sửa gần nhất | 2026-09-01T02:00:00Z | Nội bộ |
| 10 | `xoa_luc` | Xóa lúc | TIMESTAMPTZ | ISO 8601 |  | N | `NULL` | Có giá trị nghĩa là đã nghỉ việc/ẩn | Xóa mềm | `NULL` | Nội bộ |

- Khóa ngoại: `tai_khoan_id` → `tai_khoan(id)`, quy tắc xóa `RESTRICT`.
- Chỉ mục: `uq_nhan_vien_tai_khoan_id`, `uq_nhan_vien_ma_nhan_vien`, `uq_nhan_vien_email` (chỉ khi khác `NULL`).
- Quy tắc: nhân viên nghỉ việc được xóa mềm và đồng thời đặt `tai_khoan.trang_thai = VO_HIEU`; không xóa cứng vì các lần khám, duyệt lịch, nhật ký còn tham chiếu. Nhân viên có vai trò BAC_SI phải có thêm một dòng ở `bac_si`.

### 2.2 Nhóm người bệnh

#### 2.2.1 Bảng nguoi_benh (Người bệnh)

Mục đích: hồ sơ hành chính của người bệnh, định danh bằng CCCD; cũng là nơi lưu hồ sơ thân nhân (kể cả trẻ chưa có CCCD).

| STT | Tên cột | Tên hiển thị | Kiểu dữ liệu | Độ dài/định dạng | Khóa | Bắt buộc | Mặc định | Ràng buộc/Quy tắc | Mô tả | Ví dụ | Mức nhạy cảm |
|---|---|---|---|---|---|---|---|---|---|---|---|
| 1 | `id` | Mã người bệnh (nội bộ) | UUID | 36 ký tự | PK | Y | `gen_random_uuid()` | Duy nhất | Khóa chính nội bộ | 1b2c3d4e-0001-4a5b-8c6d-7e8f9a0b1c2d | Nội bộ |
| 2 | `ma_benh_nhan` | Mã bệnh nhân | VARCHAR | 10 ký tự, `^BN[0-9]{8}$` | UK | Y |  | Duy nhất; hệ thống tự sinh | Mã hồ sơ in trên phiếu | BN00012345 | Nhạy cảm |
| 3 | `ma_cccd` | Số CCCD | CHAR | 12 chữ số, `^[0-9]{12}$` | UK | N | `NULL` | Duy nhất khi có; bắt buộc với người từ 14 tuổi trở lên (kiểm ở tầng ứng dụng) | Số định danh dùng để đăng nhập và tra cứu | 079012345678 | Nhạy cảm |
| 4 | `ho_ten` | Họ và tên | VARCHAR | 1-100 |  | Y |  | Cắt khoảng trắng hai đầu | Họ tên đầy đủ | Trần Thị Bình | Nhạy cảm |
| 5 | `ngay_sinh` | Ngày sinh | DATE | `YYYY-MM-DD` |  | Y |  | Không lớn hơn ngày hiện tại | Ngày sinh | 1985-03-14 | Nhạy cảm |
| 6 | `gioi_tinh` | Giới tính | ENUM `gioi_tinh` | NAM, NU, KHAC |  | Y |  |  | Giới tính | NU | Nhạy cảm |
| 7 | `so_dien_thoai` | Số điện thoại | VARCHAR | 10 số, `^0[0-9]{9}$` |  | N | `NULL` |  | Số liên hệ nhận thông báo | 0912345678 | Nhạy cảm |
| 8 | `email` | Email | VARCHAR | tối đa 255 |  | N | `NULL` | Chữ thường, đúng định dạng | Email nhận thông báo | binh.tt@example.vn | Nhạy cảm |
| 9 | `dia_chi` | Địa chỉ | VARCHAR | 0-255 |  | N | `NULL` |  | Địa chỉ thường trú | 12 Đường A, Quận 10, TP.HCM | Nhạy cảm |
| 10 | `nguon_tao` | Nguồn tạo hồ sơ | ENUM `nguon_tao_ho_so` | DANG_KY_TRUC_TUYEN, TIEP_NHAN_TAI_QUAY |  | Y | `DANG_KY_TRUC_TUYEN` |  | Hồ sơ do người bệnh tự đăng ký hay do y tá tạo tại quầy | TIEP_NHAN_TAI_QUAY | Nội bộ |
| 11 | `dang_nhap_cuoi_luc` | Đăng nhập gần nhất lúc | TIMESTAMPTZ | ISO 8601 |  | N | `NULL` |  | Lần đăng nhập gần nhất bằng CCCD | 2026-10-26T01:05:00Z | Nội bộ |
| 12 | `tao_luc` | Tạo lúc | TIMESTAMPTZ | ISO 8601 |  | Y | `now()` |  | Thời điểm tạo | 2026-10-20T03:00:00Z | Nội bộ |
| 13 | `cap_nhat_luc` | Cập nhật lúc | TIMESTAMPTZ | ISO 8601 |  | Y | `now()` | Tự cập nhật khi sửa | Thời điểm sửa gần nhất | 2026-10-20T03:00:00Z | Nội bộ |
| 14 | `xoa_luc` | Xóa lúc | TIMESTAMPTZ | ISO 8601 |  | N | `NULL` | Có giá trị nghĩa là hồ sơ bị ẩn | Xóa mềm | `NULL` | Nội bộ |

- Khóa ngoại: không có.
- Chỉ mục: `uq_nguoi_benh_ma_benh_nhan`, `uq_nguoi_benh_ma_cccd` (chỉ khi khác `NULL`), `idx_nguoi_benh_ho_ten` (tìm theo tên), `idx_nguoi_benh_so_dien_thoai`.
- Quy tắc: tra cứu khi tiếp nhận bằng `ma_cccd`; không tìm thấy thì y tá tạo mới với `nguon_tao = TIEP_NHAN_TAI_QUAY`. Người bệnh đăng nhập bằng CCCD nên không có cột mật khẩu. Không xóa cứng dù đã có hay chưa có lần khám (xem Phần 5).

#### 2.2.2 Bảng than_nhan (Thân nhân)

Mục đích: lưu quan hệ giữa hai hồ sơ người bệnh để một người đặt lịch và xem hồ sơ thay cho thân nhân.

| STT | Tên cột | Tên hiển thị | Kiểu dữ liệu | Độ dài/định dạng | Khóa | Bắt buộc | Mặc định | Ràng buộc/Quy tắc | Mô tả | Ví dụ | Mức nhạy cảm |
|---|---|---|---|---|---|---|---|---|---|---|---|
| 1 | `id` | Mã quan hệ | UUID | 36 ký tự | PK | Y | `gen_random_uuid()` | Duy nhất | Khóa chính nội bộ | 2c3d4e5f-0002-4b6c-9d7e-8f9a0b1c2d3e | Nội bộ |
| 2 | `nguoi_benh_id` | Người quản lý hồ sơ | UUID | 36 ký tự | FK | Y |  | Khác `nguoi_than_id` | Người đăng nhập và quản lý thân nhân | 1b2c3d4e-0001-4a5b-8c6d-7e8f9a0b1c2d | Nhạy cảm |
| 3 | `nguoi_than_id` | Hồ sơ thân nhân | UUID | 36 ký tự | FK | Y |  | Khác `nguoi_benh_id` | Hồ sơ người bệnh của thân nhân | 1b2c3d4e-0002-4a5b-8c6d-7e8f9a0b1c2d | Nhạy cảm |
| 4 | `quan_he` | Quan hệ | ENUM `quan_he_than_nhan` | Xem Phần 3 |  | Y |  | Đọc là “`nguoi_than_id` là ... của `nguoi_benh_id`” | Thân nhân là gì của người quản lý | CON | Nhạy cảm |
| 5 | `tao_luc` | Tạo lúc | TIMESTAMPTZ | ISO 8601 |  | Y | `now()` |  | Thời điểm tạo | 2026-10-20T03:10:00Z | Nội bộ |
| 6 | `cap_nhat_luc` | Cập nhật lúc | TIMESTAMPTZ | ISO 8601 |  | Y | `now()` | Tự cập nhật khi sửa | Thời điểm sửa gần nhất | 2026-10-20T03:10:00Z | Nội bộ |
| 7 | `xoa_luc` | Xóa lúc | TIMESTAMPTZ | ISO 8601 |  | N | `NULL` | Có giá trị nghĩa là đã gỡ liên kết | Xóa mềm | `NULL` | Nội bộ |

- Khóa ngoại: `nguoi_benh_id` → `nguoi_benh(id)`, `nguoi_than_id` → `nguoi_benh(id)`, cả hai `RESTRICT`.
- Chỉ mục: `uq_than_nhan_cap` duy nhất trên (`nguoi_benh_id`, `nguoi_than_id`) khi `xoa_luc IS NULL`; `idx_than_nhan_nguoi_than_id`.
- Quy tắc: `CHECK (nguoi_benh_id <> nguoi_than_id)`. Chỉ được đặt lịch và xem hồ sơ thân nhân khi liên kết còn hiệu lực (`xoa_luc IS NULL`). Quan hệ có hướng: một cặp có thể có hai dòng ngược nhau nếu cả hai đều muốn quản lý hồ sơ của nhau.

#### 2.2.3 Bảng thong_tin_y_te_nen (Thông tin y tế nền)

Mục đích: lưu từng mục dữ liệu y tế nền (nhóm máu, tiền sử phẫu thuật hoặc tiểu phẫu, dị ứng thuốc), mỗi dòng một mục, kèm nguồn dữ liệu.

| STT | Tên cột | Tên hiển thị | Kiểu dữ liệu | Độ dài/định dạng | Khóa | Bắt buộc | Mặc định | Ràng buộc/Quy tắc | Mô tả | Ví dụ | Mức nhạy cảm |
|---|---|---|---|---|---|---|---|---|---|---|---|
| 1 | `id` | Mã mục | UUID | 36 ký tự | PK | Y | `gen_random_uuid()` | Duy nhất | Khóa chính nội bộ | 3d4e5f6a-0003-4c7d-8e8f-9a0b1c2d3e4f | Nội bộ |
| 2 | `nguoi_benh_id` | Người bệnh | UUID | 36 ký tự | FK | Y |  |  | Chủ của thông tin | 1b2c3d4e-0001-4a5b-8c6d-7e8f9a0b1c2d | Rất nhạy cảm |
| 3 | `loai_thong_tin` | Loại thông tin | ENUM `loai_thong_tin_y_te_nen` | NHOM_MAU, TIEN_SU_PHAU_THUAT, DI_UNG_THUOC |  | Y |  |  | Quyết định ý nghĩa các cột còn lại | DI_UNG_THUOC | Rất nhạy cảm |
| 4 | `ten_muc` | Nội dung mục | VARCHAR | 1-200 |  | Y |  | Nếu `NHOM_MAU` thì phải thuộc danh sách nhóm máu ở Phần 3 | Nhóm máu, tên phẫu thuật, hoặc hoạt chất gây dị ứng | Penicillin | Rất nhạy cảm |
| 5 | `nam_thuc_hien` | Năm thực hiện | SMALLINT | 4 chữ số |  | N | `NULL` | Chỉ dùng khi `TIEN_SU_PHAU_THUAT`; từ 1900 đến năm hiện tại | Năm phẫu thuật | 2018 | Rất nhạy cảm |
| 6 | `muc_do_di_ung` | Mức độ dị ứng | ENUM `muc_do_di_ung` | NHE, TRUNG_BINH, NANG |  | N | `NULL` | Chỉ dùng khi `DI_UNG_THUOC` | Mức độ phản ứng | NANG | Rất nhạy cảm |
| 7 | `ghi_chu` | Ghi chú | VARCHAR | 0-500 |  | N | `NULL` |  | Biểu hiện phản ứng hoặc ghi chú thêm | Nổi mẩn, khó thở | Rất nhạy cảm |
| 8 | `nguon_du_lieu` | Nguồn dữ liệu | ENUM `nguon_du_lieu_y_te_nen` | VNEID, NOI_BO |  | Y | `NOI_BO` |  | Dữ liệu lấy từ Sổ sức khỏe điện tử hay do bệnh viện nhập | VNEID | Nội bộ |
| 9 | `dong_y_id` | Bản ghi đồng ý | UUID | 36 ký tự | FK | N | `NULL` | Bắt buộc khi `nguon_du_lieu = VNEID`; `NULL` khi `NOI_BO` | Sự đồng ý cho phép lấy mục này | 4e5f6a7b-0004-4d8e-9f9a-0b1c2d3e4f5a | Nội bộ |
| 10 | `tao_luc` | Tạo lúc | TIMESTAMPTZ | ISO 8601 |  | Y | `now()` |  | Thời điểm nhận mục vào hệ thống | 2026-10-26T01:10:00Z | Nội bộ |
| 11 | `cap_nhat_luc` | Cập nhật lúc | TIMESTAMPTZ | ISO 8601 |  | Y | `now()` | Tự cập nhật khi sửa | Thời điểm sửa gần nhất | 2026-10-26T01:10:00Z | Nội bộ |

- Khóa ngoại: `nguoi_benh_id` → `nguoi_benh(id)` `RESTRICT`; `dong_y_id` → `dong_y_chia_se_du_lieu(id)` `RESTRICT`.
- Chỉ mục: `idx_thong_tin_y_te_nen_nguoi_benh_id_loai` trên (`nguoi_benh_id`, `loai_thong_tin`); `uq_thong_tin_y_te_nen_nhom_mau` duy nhất trên `nguoi_benh_id` khi `loai_thong_tin = NHOM_MAU`.
- Quy tắc: mỗi người bệnh có tối đa một dòng `NHOM_MAU`. Dữ liệu `VNEID` chỉ hiển thị cho bác sĩ khi bản ghi đồng ý tương ứng chưa bị thu hồi (`thu_hoi_luc IS NULL`); sau thu hồi, các dòng đó bị ẩn khỏi lần khám mới nhưng không bị xóa. Dữ liệu `NOI_BO` không cần đồng ý chia sẻ.

#### 2.2.4 Bảng dong_y_chia_se_du_lieu (Đồng ý chia sẻ dữ liệu)

Mục đích: ghi nhận người bệnh đồng ý cho bệnh viện lấy từng phạm vi dữ liệu từ Sổ sức khỏe điện tử VNeID (mô phỏng), có thời điểm đồng ý và thu hồi.

| STT | Tên cột | Tên hiển thị | Kiểu dữ liệu | Độ dài/định dạng | Khóa | Bắt buộc | Mặc định | Ràng buộc/Quy tắc | Mô tả | Ví dụ | Mức nhạy cảm |
|---|---|---|---|---|---|---|---|---|---|---|---|
| 1 | `id` | Mã đồng ý | UUID | 36 ký tự | PK | Y | `gen_random_uuid()` | Duy nhất | Khóa chính nội bộ | 4e5f6a7b-0004-4d8e-9f9a-0b1c2d3e4f5a | Nội bộ |
| 2 | `nguoi_benh_id` | Người bệnh | UUID | 36 ký tự | FK | Y |  |  | Người có dữ liệu được chia sẻ | 1b2c3d4e-0001-4a5b-8c6d-7e8f9a0b1c2d | Nhạy cảm |
| 3 | `nguoi_dong_y_id` | Người đồng ý | UUID | 36 ký tự | FK | Y |  | Là chính `nguoi_benh_id` hoặc người đại diện được liên kết trong `than_nhan` | Người bấm đồng ý (bản thân hoặc người giám hộ) | 1b2c3d4e-0001-4a5b-8c6d-7e8f9a0b1c2d | Nhạy cảm |
| 4 | `pham_vi_du_lieu` | Phạm vi dữ liệu | ENUM `pham_vi_chia_se` | NHOM_MAU, TIEN_SU_PHAU_THUAT, DI_UNG_THUOC |  | Y |  | Mỗi dòng một phạm vi | Loại dữ liệu được phép lấy | DI_UNG_THUOC | Nội bộ |
| 5 | `dong_y_luc` | Đồng ý lúc | TIMESTAMPTZ | ISO 8601 |  | Y | `now()` |  | Thời điểm đồng ý | 2026-10-26T01:08:00Z | Nội bộ |
| 6 | `thu_hoi_luc` | Thu hồi lúc | TIMESTAMPTZ | ISO 8601 |  | N | `NULL` | Lớn hơn hoặc bằng `dong_y_luc` | Thời điểm thu hồi, `NULL` nếu còn hiệu lực | `NULL` | Nội bộ |
| 7 | `tao_luc` | Tạo lúc | TIMESTAMPTZ | ISO 8601 |  | Y | `now()` |  | Thời điểm tạo bản ghi | 2026-10-26T01:08:00Z | Nội bộ |
| 8 | `cap_nhat_luc` | Cập nhật lúc | TIMESTAMPTZ | ISO 8601 |  | Y | `now()` | Tự cập nhật khi sửa | Thời điểm sửa gần nhất | 2026-10-26T01:08:00Z | Nội bộ |

- Khóa ngoại: `nguoi_benh_id` → `nguoi_benh(id)` `RESTRICT`; `nguoi_dong_y_id` → `nguoi_benh(id)` `RESTRICT`.
- Chỉ mục: `uq_dong_y_hieu_luc` duy nhất trên (`nguoi_benh_id`, `pham_vi_du_lieu`) khi `thu_hoi_luc IS NULL`.
- Quy tắc: không sửa đồng ý cũ; thu hồi bằng cách điền `thu_hoi_luc`, đồng ý lại tạo dòng mới. Chỉ được lấy dữ liệu từ VNeID khi có dòng còn hiệu lực đúng phạm vi. Mỗi lần đồng ý, thu hồi và xem dữ liệu nền đều ghi `nhat_ky_thao_tac`.

#### 2.2.5 Bảng bao_hiem_y_te (Bảo hiểm y tế)

Mục đích: lưu thẻ BHYT của người bệnh để áp mức hưởng khi tính hóa đơn; không giám định với cơ quan bảo hiểm.

| STT | Tên cột | Tên hiển thị | Kiểu dữ liệu | Độ dài/định dạng | Khóa | Bắt buộc | Mặc định | Ràng buộc/Quy tắc | Mô tả | Ví dụ | Mức nhạy cảm |
|---|---|---|---|---|---|---|---|---|---|---|---|
| 1 | `id` | Mã thẻ (nội bộ) | UUID | 36 ký tự | PK | Y | `gen_random_uuid()` | Duy nhất | Khóa chính nội bộ | 5f6a7b8c-0005-4e9f-8a0b-1c2d3e4f5a6b | Nội bộ |
| 2 | `nguoi_benh_id` | Người bệnh | UUID | 36 ký tự | FK | Y |  |  | Chủ thẻ | 1b2c3d4e-0001-4a5b-8c6d-7e8f9a0b1c2d | Nhạy cảm |
| 3 | `so_the_bhyt` | Số thẻ BHYT | CHAR | 15 ký tự, 2 chữ + 13 số | UK | Y |  | `^[A-Z]{2}[0-9]{13}$`; duy nhất | Số thẻ ghi trên thẻ | DN4797912345678 | Nhạy cảm |
| 4 | `muc_huong` | Mức hưởng (%) | SMALLINT | 0-100 |  | Y |  | `CHECK` 0-100; thường là 80, 95, 100 | Tỷ lệ BHYT chi trả | 80 | Nhạy cảm |
| 5 | `noi_dang_ky_kcb` | Nơi đăng ký khám chữa bệnh ban đầu | VARCHAR | 0-200 |  | N | `NULL` |  | Tên cơ sở đăng ký trên thẻ | Bệnh viện Nguyễn Tri Phương | Nhạy cảm |
| 6 | `hieu_luc_tu` | Hiệu lực từ | DATE | `YYYY-MM-DD` |  | Y |  |  | Ngày thẻ bắt đầu có hiệu lực | 2026-01-01 | Nhạy cảm |
| 7 | `hieu_luc_den` | Hiệu lực đến | DATE | `YYYY-MM-DD` |  | Y |  | `CHECK (hieu_luc_den >= hieu_luc_tu)` | Ngày thẻ hết hiệu lực | 2026-12-31 | Nhạy cảm |
| 8 | `tao_luc` | Tạo lúc | TIMESTAMPTZ | ISO 8601 |  | Y | `now()` |  | Thời điểm tạo | 2026-10-20T03:05:00Z | Nội bộ |
| 9 | `cap_nhat_luc` | Cập nhật lúc | TIMESTAMPTZ | ISO 8601 |  | Y | `now()` | Tự cập nhật khi sửa | Thời điểm sửa gần nhất | 2026-10-20T03:05:00Z | Nội bộ |

- Khóa ngoại: `nguoi_benh_id` → `nguoi_benh(id)` `RESTRICT`.
- Chỉ mục: `uq_bao_hiem_y_te_so_the_bhyt`, `idx_bao_hiem_y_te_nguoi_benh_id`.
- Quy tắc: một người có thể có nhiều thẻ theo thời gian; thẻ áp dụng cho một lần khám là thẻ có ngày khám nằm trong [`hieu_luc_tu`, `hieu_luc_den`]. Hệ thống chỉ lưu và kiểm tra định dạng, không xác thực với cơ quan bảo hiểm.

### 2.3 Nhóm danh mục

Các bảng danh mục dùng cột `dang_su_dung` thay cho xóa mềm: ngừng sử dụng thì ẩn khỏi lựa chọn mới nhưng các hồ sơ cũ vẫn tham chiếu được.

#### 2.3.1 Bảng khoa (Khoa)

Mục đích: danh mục khoa, gồm khoa lâm sàng (người bệnh chọn khi đặt lịch) và khoa cận lâm sàng (chỉ dùng cho phòng xét nghiệm, chẩn đoán hình ảnh).

| STT | Tên cột | Tên hiển thị | Kiểu dữ liệu | Độ dài/định dạng | Khóa | Bắt buộc | Mặc định | Ràng buộc/Quy tắc | Mô tả | Ví dụ | Mức nhạy cảm |
|---|---|---|---|---|---|---|---|---|---|---|---|
| 1 | `id` | Mã khoa (nội bộ) | UUID | 36 ký tự | PK | Y | `gen_random_uuid()` | Duy nhất | Khóa chính nội bộ | 6a7b8c9d-0006-4fa0-9b1c-2d3e4f5a6b7c | Nội bộ |
| 2 | `ma_khoa` | Mã khoa | VARCHAR | 2-20, `[A-Z0-9_]` | UK | Y |  | Duy nhất | Mã hiển thị | NOI_TIM_MACH | Nội bộ |
| 3 | `ten_khoa` | Tên khoa | VARCHAR | 1-100 |  | Y |  |  | Tên khoa hiển thị | Khoa Nội tim mạch | Công khai |
| 4 | `loai_khoa` | Loại khoa | ENUM `loai_khoa` | LAM_SANG, CAN_LAM_SANG |  | Y | `LAM_SANG` | Chỉ khoa `LAM_SANG` mới chọn được khi đặt lịch | Phân loại khoa | LAM_SANG | Nội bộ |
| 5 | `mo_ta` | Mô tả | TEXT |  |  | N | `NULL` |  | Giới thiệu khoa trên trang công khai | Khám và điều trị bệnh tim mạch | Công khai |
| 6 | `dang_su_dung` | Đang sử dụng | BOOLEAN | true/false |  | Y | `true` |  | `false` là ngừng nhận đặt lịch | true | Nội bộ |
| 7 | `tao_luc` | Tạo lúc | TIMESTAMPTZ | ISO 8601 |  | Y | `now()` |  | Thời điểm tạo | 2026-09-01T02:00:00Z | Nội bộ |
| 8 | `cap_nhat_luc` | Cập nhật lúc | TIMESTAMPTZ | ISO 8601 |  | Y | `now()` | Tự cập nhật khi sửa | Thời điểm sửa gần nhất | 2026-09-01T02:00:00Z | Nội bộ |

- Khóa ngoại: không có.
- Chỉ mục: `uq_khoa_ma_khoa`.
- Quy tắc: không xóa khoa đã có phòng, bác sĩ hoặc lịch hẹn; chỉ đặt `dang_su_dung = false`.

#### 2.3.2 Bảng phong_kham (Phòng)

Mục đích: danh mục phòng thuộc một khoa (phòng khám, phòng xét nghiệm, phòng chẩn đoán hình ảnh), dùng để xếp lịch, cấp số thứ tự và in phiếu hướng dẫn.

| STT | Tên cột | Tên hiển thị | Kiểu dữ liệu | Độ dài/định dạng | Khóa | Bắt buộc | Mặc định | Ràng buộc/Quy tắc | Mô tả | Ví dụ | Mức nhạy cảm |
|---|---|---|---|---|---|---|---|---|---|---|---|
| 1 | `id` | Mã phòng (nội bộ) | UUID | 36 ký tự | PK | Y | `gen_random_uuid()` | Duy nhất | Khóa chính nội bộ | 7b8c9d0e-0007-4ab1-8c2d-3e4f5a6b7c8d | Nội bộ |
| 2 | `khoa_id` | Khoa | UUID | 36 ký tự | FK | Y |  |  | Khoa quản lý phòng | 6a7b8c9d-0006-4fa0-9b1c-2d3e4f5a6b7c | Nội bộ |
| 3 | `ma_phong` | Mã phòng | VARCHAR | 2-20 | UK | Y |  | Duy nhất | Mã hiển thị | P203 | Nội bộ |
| 4 | `ten_phong` | Tên phòng | VARCHAR | 1-100 |  | Y |  |  | Tên hiển thị | Phòng khám tim mạch 2 | Nội bộ |
| 5 | `vi_tri` | Vị trí | VARCHAR | 0-100 |  | N | `NULL` |  | Chỉ đường từ cổng, in trên phiếu hướng dẫn | Tầng 2, khu A | Nội bộ |
| 6 | `dang_su_dung` | Đang sử dụng | BOOLEAN | true/false |  | Y | `true` |  | `false` là ngừng sử dụng | true | Nội bộ |
| 7 | `tao_luc` | Tạo lúc | TIMESTAMPTZ | ISO 8601 |  | Y | `now()` |  | Thời điểm tạo | 2026-09-01T02:00:00Z | Nội bộ |
| 8 | `cap_nhat_luc` | Cập nhật lúc | TIMESTAMPTZ | ISO 8601 |  | Y | `now()` | Tự cập nhật khi sửa | Thời điểm sửa gần nhất | 2026-09-01T02:00:00Z | Nội bộ |

- Khóa ngoại: `khoa_id` → `khoa(id)` `RESTRICT`.
- Chỉ mục: `uq_phong_kham_ma_phong`, `idx_phong_kham_khoa_id`.
- Quy tắc: mỗi phòng thuộc đúng một khoa.

#### 2.3.3 Bảng bac_si (Bác sĩ)

Mục đích: hồ sơ chuyên môn và thông tin giới thiệu công khai của nhân viên có vai trò Bác sĩ; họ tên lấy từ `nhan_vien`.

| STT | Tên cột | Tên hiển thị | Kiểu dữ liệu | Độ dài/định dạng | Khóa | Bắt buộc | Mặc định | Ràng buộc/Quy tắc | Mô tả | Ví dụ | Mức nhạy cảm |
|---|---|---|---|---|---|---|---|---|---|---|---|
| 1 | `id` | Mã bác sĩ (nội bộ) | UUID | 36 ký tự | PK | Y | `gen_random_uuid()` | Duy nhất | Khóa chính nội bộ | 8c9d0e1f-0008-4bc2-9d3e-4f5a6b7c8d9e | Nội bộ |
| 2 | `nhan_vien_id` | Nhân viên | UUID | 36 ký tự | FK, UK | Y |  | Nhân viên phải có vai trò BAC_SI | Liên kết 1-1 với nhân viên | 9a8b7c6d-5e4f-4a3b-8c2d-1e0f9a8b7c6d | Nội bộ |
| 3 | `khoa_id` | Khoa công tác | UUID | 36 ký tự | FK | Y |  | Khoa phải có `loai_khoa = LAM_SANG` | Khoa bác sĩ khám | 6a7b8c9d-0006-4fa0-9b1c-2d3e4f5a6b7c | Công khai |
| 4 | `so_chung_chi_hanh_nghe` | Số chứng chỉ hành nghề | VARCHAR | 0-30 | UK | N | `NULL` | Duy nhất khi có | Số chứng chỉ hành nghề | 012345/HCM-CCHN | Nội bộ |
| 5 | `hoc_ham_hoc_vi` | Học hàm, học vị | VARCHAR | 0-50 |  | N | `NULL` |  | Danh xưng chuyên môn | Thạc sĩ, Bác sĩ CKI | Công khai |
| 6 | `gioi_thieu` | Giới thiệu | TEXT |  |  | N | `NULL` |  | Giới thiệu kinh nghiệm, hiển thị trên trang công khai | 15 năm kinh nghiệm tim mạch can thiệp | Công khai |
| 7 | `anh_dai_dien_url` | Ảnh đại diện | VARCHAR | 0-500, URL |  | N | `NULL` |  | Đường dẫn ảnh | /uploads/bacsi/an.jpg | Công khai |
| 8 | `hien_thi_cong_khai` | Hiển thị công khai | BOOLEAN | true/false |  | Y | `true` |  | Có hiện trên trang đội ngũ bác sĩ hay không | true | Nội bộ |
| 9 | `dang_su_dung` | Đang sử dụng | BOOLEAN | true/false |  | Y | `true` | `false` thì không nhận lịch mới | Còn nhận khám không | true | Nội bộ |
| 10 | `tao_luc` | Tạo lúc | TIMESTAMPTZ | ISO 8601 |  | Y | `now()` |  | Thời điểm tạo | 2026-09-01T02:00:00Z | Nội bộ |
| 11 | `cap_nhat_luc` | Cập nhật lúc | TIMESTAMPTZ | ISO 8601 |  | Y | `now()` | Tự cập nhật khi sửa | Thời điểm sửa gần nhất | 2026-09-01T02:00:00Z | Nội bộ |

- Khóa ngoại: `nhan_vien_id` → `nhan_vien(id)` `RESTRICT`; `khoa_id` → `khoa(id)` `RESTRICT`.
- Chỉ mục: `uq_bac_si_nhan_vien_id`, `idx_bac_si_khoa_id`.
- Quy tắc: trang công khai chỉ trả về họ tên, khoa, học hàm học vị, giới thiệu và ảnh của bác sĩ có `hien_thi_cong_khai = true` và `dang_su_dung = true`; không trả số chứng chỉ hay email.

#### 2.3.4 Bảng lich_lam_viec (Lịch làm việc)

Mục đích: ca làm việc của một bác sĩ tại một phòng vào một ngày cụ thể, dùng để biết khung giờ nào của mỗi khoa còn nhận đặt lịch.

| STT | Tên cột | Tên hiển thị | Kiểu dữ liệu | Độ dài/định dạng | Khóa | Bắt buộc | Mặc định | Ràng buộc/Quy tắc | Mô tả | Ví dụ | Mức nhạy cảm |
|---|---|---|---|---|---|---|---|---|---|---|---|
| 1 | `id` | Mã ca làm việc | UUID | 36 ký tự | PK | Y | `gen_random_uuid()` | Duy nhất | Khóa chính nội bộ | 9d0e1f2a-0009-4cd3-8e4f-5a6b7c8d9e0f | Nội bộ |
| 2 | `bac_si_id` | Bác sĩ | UUID | 36 ký tự | FK | Y |  |  | Bác sĩ trực ca | 8c9d0e1f-0008-4bc2-9d3e-4f5a6b7c8d9e | Nội bộ |
| 3 | `phong_kham_id` | Phòng | UUID | 36 ký tự | FK | Y |  | Phòng phải thuộc đúng khoa của bác sĩ | Phòng khám của ca | 7b8c9d0e-0007-4ab1-8c2d-3e4f5a6b7c8d | Nội bộ |
| 4 | `ngay_lam_viec` | Ngày làm việc | DATE | `YYYY-MM-DD` |  | Y |  |  | Ngày của ca | 2026-11-05 | Nội bộ |
| 5 | `gio_bat_dau` | Giờ bắt đầu | TIME | `HH:MM` |  | Y |  |  | Giờ bắt đầu ca | 08:00 | Nội bộ |
| 6 | `gio_ket_thuc` | Giờ kết thúc | TIME | `HH:MM` |  | Y |  | `CHECK (gio_ket_thuc > gio_bat_dau)` | Giờ kết thúc ca | 11:30 | Nội bộ |
| 7 | `thoi_luong_khung_phut` | Thời lượng mỗi khung (phút) | SMALLINT | 5-120 |  | Y | 15 | `CHECK` 5-120 | Độ dài một khung giờ cho người bệnh chọn | 15 | Nội bộ |
| 8 | `so_luot_toi_da_moi_khung` | Số lượt tối đa mỗi khung | SMALLINT | 1-20 |  | Y | 2 | `CHECK` 1-20 | Số lịch hẹn tối đa trong một khung | 2 | Nội bộ |
| 9 | `dang_mo_dat_lich` | Đang mở đặt lịch | BOOLEAN | true/false |  | Y | `true` |  | `false` là tạm đóng ca (bác sĩ nghỉ đột xuất) | true | Nội bộ |
| 10 | `tao_luc` | Tạo lúc | TIMESTAMPTZ | ISO 8601 |  | Y | `now()` |  | Thời điểm tạo | 2026-10-01T02:00:00Z | Nội bộ |
| 11 | `cap_nhat_luc` | Cập nhật lúc | TIMESTAMPTZ | ISO 8601 |  | Y | `now()` | Tự cập nhật khi sửa | Thời điểm sửa gần nhất | 2026-10-01T02:00:00Z | Nội bộ |

- Khóa ngoại: `bac_si_id` → `bac_si(id)` `RESTRICT`; `phong_kham_id` → `phong_kham(id)` `RESTRICT`.
- Chỉ mục: `uq_lich_lam_viec_bac_si_ngay_gio` trên (`bac_si_id`, `ngay_lam_viec`, `gio_bat_dau`); `idx_lich_lam_viec_ngay` trên (`ngay_lam_viec`, `phong_kham_id`).
- Quy tắc: một bác sĩ không có hai ca chồng giờ trong cùng ngày. Các khung giờ cho người bệnh chọn được tính từ `gio_bat_dau` đến `gio_ket_thuc` chia theo `thoi_luong_khung_phut`. Một khung còn chỗ khi số lịch hẹn còn hiệu lực của khoa trong khung chưa vượt tổng `so_luot_toi_da_moi_khung` của các ca đang mở (`dang_mo_dat_lich = true`) trong khoa đó.

#### 2.3.5 Bảng dich_vu_kham (Dịch vụ khám)

Mục đích: danh mục dịch vụ khám và đơn giá theo loại hình khám (BHYT, dịch vụ, theo yêu cầu), dùng để tính dòng “khám” trên hóa đơn.

| STT | Tên cột | Tên hiển thị | Kiểu dữ liệu | Độ dài/định dạng | Khóa | Bắt buộc | Mặc định | Ràng buộc/Quy tắc | Mô tả | Ví dụ | Mức nhạy cảm |
|---|---|---|---|---|---|---|---|---|---|---|---|
| 1 | `id` | Mã dịch vụ (nội bộ) | UUID | 36 ký tự | PK | Y | `gen_random_uuid()` | Duy nhất | Khóa chính nội bộ | 0e1f2a3b-0010-4de4-9f5a-6b7c8d9e0f1a | Nội bộ |
| 2 | `ma_dich_vu` | Mã dịch vụ | VARCHAR | 2-20 | UK | Y |  | Duy nhất | Mã hiển thị | KHAM_TM_BHYT | Nội bộ |
| 3 | `ten_dich_vu` | Tên dịch vụ | VARCHAR | 1-150 |  | Y |  |  | Tên dịch vụ hiển thị | Khám tim mạch (BHYT) | Công khai |
| 4 | `khoa_id` | Khoa | UUID | 36 ký tự | FK | N | `NULL` | `NULL` = áp dụng mọi khoa lâm sàng | Khoa áp dụng dịch vụ | 6a7b8c9d-0006-4fa0-9b1c-2d3e4f5a6b7c | Công khai |
| 5 | `loai_hinh_kham` | Loại hình khám | ENUM `loai_hinh_kham` | BHYT, DICH_VU, THEO_YEU_CAU |  | Y |  |  | Loại hình áp dụng cho đơn giá | BHYT | Công khai |
| 6 | `don_gia` | Đơn giá (VND) | NUMERIC | (12,0) |  | Y |  | `CHECK >= 0` | Giá một lần khám | 38700 | Công khai |
| 7 | `duoc_bhyt_chi_tra` | Được BHYT chi trả | BOOLEAN | true/false |  | Y | `false` | Chỉ được `true` khi `loai_hinh_kham = BHYT` | Dùng để chia phần BHYT trên hóa đơn | true | Nội bộ |
| 8 | `dang_su_dung` | Đang sử dụng | BOOLEAN | true/false |  | Y | `true` |  | `false` là ngừng áp dụng | true | Nội bộ |
| 9 | `tao_luc` | Tạo lúc | TIMESTAMPTZ | ISO 8601 |  | Y | `now()` |  | Thời điểm tạo | 2026-09-01T02:00:00Z | Nội bộ |
| 10 | `cap_nhat_luc` | Cập nhật lúc | TIMESTAMPTZ | ISO 8601 |  | Y | `now()` | Tự cập nhật khi sửa | Thời điểm sửa gần nhất | 2026-09-01T02:00:00Z | Nội bộ |

- Khóa ngoại: `khoa_id` → `khoa(id)` `RESTRICT`.
- Chỉ mục: `uq_dich_vu_kham_ma_dich_vu`; `uq_dich_vu_kham_khoa_loai_hinh` duy nhất trên (`khoa_id`, `loai_hinh_kham`) khi `dang_su_dung` và `khoa_id` khác `NULL`.
- Quy tắc: đơn giá đổi thì tạo dịch vụ mới, không sửa giá dịch vụ đã dùng; `chi_tiet_hoa_don` cũng sao chép đơn giá tại thời điểm lập.

#### 2.3.6 Bảng danh_muc_can_lam_sang (Danh mục cận lâm sàng)

Mục đích: danh mục các xét nghiệm, chẩn đoán hình ảnh, thăm dò chức năng mà bác sĩ có thể chỉ định, kèm đơn giá và phòng thực hiện.

| STT | Tên cột | Tên hiển thị | Kiểu dữ liệu | Độ dài/định dạng | Khóa | Bắt buộc | Mặc định | Ràng buộc/Quy tắc | Mô tả | Ví dụ | Mức nhạy cảm |
|---|---|---|---|---|---|---|---|---|---|---|---|
| 1 | `id` | Mã cận lâm sàng (nội bộ) | UUID | 36 ký tự | PK | Y | `gen_random_uuid()` | Duy nhất | Khóa chính nội bộ | 1f2a3b4c-0011-4ef5-8a6b-7c8d9e0f1a2b | Nội bộ |
| 2 | `ma_cls` | Mã cận lâm sàng | VARCHAR | 2-20 | UK | Y |  | Duy nhất | Mã hiển thị | XN_CTM | Nội bộ |
| 3 | `ten_cls` | Tên cận lâm sàng | VARCHAR | 1-150 |  | Y |  |  | Tên hạng mục | Công thức máu | Công khai |
| 4 | `nhom_cls` | Nhóm cận lâm sàng | ENUM `nhom_can_lam_sang` | XET_NGHIEM, CHAN_DOAN_HINH_ANH, THAM_DO_CHUC_NANG |  | Y |  |  | Phân nhóm hạng mục | XET_NGHIEM | Nội bộ |
| 5 | `phong_thuc_hien_id` | Phòng thực hiện | UUID | 36 ký tự | FK | N | `NULL` | Phòng phải thuộc khoa `CAN_LAM_SANG` | Phòng mặc định để in phiếu hướng dẫn | 7b8c9d0e-0007-4ab1-8c2d-3e4f5a6b7c8d | Nội bộ |
| 6 | `don_gia` | Đơn giá (VND) | NUMERIC | (12,0) |  | Y |  | `CHECK >= 0` | Giá một lần thực hiện | 85000 | Công khai |
| 7 | `duoc_bhyt_chi_tra` | Được BHYT chi trả | BOOLEAN | true/false |  | Y | `false` |  | Dùng để chia phần BHYT trên hóa đơn | true | Nội bộ |
| 8 | `huong_dan_chuan_bi` | Hướng dẫn chuẩn bị | VARCHAR | 0-500 |  | N | `NULL` |  | Lưu ý cho người bệnh, in trên phiếu hướng dẫn | Nhịn ăn 8 giờ trước khi lấy máu | Công khai |
| 9 | `dang_su_dung` | Đang sử dụng | BOOLEAN | true/false |  | Y | `true` |  | `false` là ngừng cho chỉ định mới | true | Nội bộ |
| 10 | `tao_luc` | Tạo lúc | TIMESTAMPTZ | ISO 8601 |  | Y | `now()` |  | Thời điểm tạo | 2026-09-01T02:00:00Z | Nội bộ |
| 11 | `cap_nhat_luc` | Cập nhật lúc | TIMESTAMPTZ | ISO 8601 |  | Y | `now()` | Tự cập nhật khi sửa | Thời điểm sửa gần nhất | 2026-09-01T02:00:00Z | Nội bộ |

- Khóa ngoại: `phong_thuc_hien_id` → `phong_kham(id)` `SET NULL`.
- Chỉ mục: `uq_danh_muc_can_lam_sang_ma_cls`, `idx_danh_muc_can_lam_sang_nhom_cls`.
- Quy tắc: không sửa đơn giá hạng mục đã có chỉ định; đổi giá thì tạo hạng mục mới và ngừng hạng mục cũ.

#### 2.3.7 Bảng danh_muc_thuoc (Danh mục thuốc)

Mục đích: danh mục thuốc để bác sĩ chọn khi kê đơn. Hệ thống không quản lý tồn kho nên bảng này không có cột số lượng tồn, lô, hạn dùng.

| STT | Tên cột | Tên hiển thị | Kiểu dữ liệu | Độ dài/định dạng | Khóa | Bắt buộc | Mặc định | Ràng buộc/Quy tắc | Mô tả | Ví dụ | Mức nhạy cảm |
|---|---|---|---|---|---|---|---|---|---|---|---|
| 1 | `id` | Mã thuốc (nội bộ) | UUID | 36 ký tự | PK | Y | `gen_random_uuid()` | Duy nhất | Khóa chính nội bộ | 2a3b4c5d-0012-4fa6-9b7c-8d9e0f1a2b3c | Nội bộ |
| 2 | `ma_thuoc` | Mã thuốc | VARCHAR | 2-20 | UK | Y |  | Duy nhất | Mã hiển thị | T0001 | Nội bộ |
| 3 | `ten_thuoc` | Tên thuốc | VARCHAR | 1-150 |  | Y |  |  | Tên biệt dược hoặc tên thương mại | Amlodipin 5mg | Nội bộ |
| 4 | `hoat_chat` | Hoạt chất | VARCHAR | 1-150 |  | Y |  | Chuẩn hóa tên để đối chiếu dị ứng thuốc | Hoạt chất dùng để cảnh báo dị ứng | Amlodipine | Nội bộ |
| 5 | `ham_luong` | Hàm lượng | VARCHAR | 0-50 |  | N | `NULL` |  | Hàm lượng mỗi đơn vị | 5 mg | Nội bộ |
| 6 | `dang_bao_che` | Dạng bào chế | VARCHAR | 0-50 |  | N | `NULL` |  | Dạng thuốc | Viên nén | Nội bộ |
| 7 | `don_vi_tinh` | Đơn vị tính | VARCHAR | 1-20 |  | Y |  |  | Đơn vị tính số lượng và giá | Viên | Nội bộ |
| 8 | `don_gia` | Đơn giá (VND) | NUMERIC | (12,0) |  | Y |  | `CHECK >= 0`; giá mỗi `don_vi_tinh` | Giá bán mỗi đơn vị | 1200 | Nội bộ |
| 9 | `duoc_bhyt_chi_tra` | Được BHYT chi trả | BOOLEAN | true/false |  | Y | `false` |  | Dùng để chia phần BHYT trên hóa đơn | true | Nội bộ |
| 10 | `dang_su_dung` | Đang sử dụng | BOOLEAN | true/false |  | Y | `true` |  | `false` là không cho kê mới | true | Nội bộ |
| 11 | `tao_luc` | Tạo lúc | TIMESTAMPTZ | ISO 8601 |  | Y | `now()` |  | Thời điểm tạo | 2026-09-01T02:00:00Z | Nội bộ |
| 12 | `cap_nhat_luc` | Cập nhật lúc | TIMESTAMPTZ | ISO 8601 |  | Y | `now()` | Tự cập nhật khi sửa | Thời điểm sửa gần nhất | 2026-09-01T02:00:00Z | Nội bộ |

- Khóa ngoại: không có.
- Chỉ mục: `uq_danh_muc_thuoc_ma_thuoc`, `idx_danh_muc_thuoc_ten_thuoc` (tìm khi kê đơn), `idx_danh_muc_thuoc_hoat_chat`.
- Quy tắc: khi kê đơn, nếu `hoat_chat` trùng với một dòng `DI_UNG_THUOC` còn hiển thị của người bệnh, hệ thống cảnh báo (không chặn); bác sĩ quyết định.

#### 2.3.8 Bảng tu_khoa_trieu_chung (Từ khóa triệu chứng)

Mục đích: bảng ánh xạ từ khóa triệu chứng sang khoa, là dữ liệu của bộ gợi ý chuyên khoa chạy nội bộ (rule-based).

| STT | Tên cột | Tên hiển thị | Kiểu dữ liệu | Độ dài/định dạng | Khóa | Bắt buộc | Mặc định | Ràng buộc/Quy tắc | Mô tả | Ví dụ | Mức nhạy cảm |
|---|---|---|---|---|---|---|---|---|---|---|---|
| 1 | `id` | Mã từ khóa | UUID | 36 ký tự | PK | Y | `gen_random_uuid()` | Duy nhất | Khóa chính nội bộ | 3b4c5d6e-0013-4ab7-8c8d-9e0f1a2b3c4d | Nội bộ |
| 2 | `khoa_id` | Khoa được gợi ý | UUID | 36 ký tự | FK | Y |  | Khoa phải có `loai_khoa = LAM_SANG` | Khoa tương ứng với từ khóa | 6a7b8c9d-0006-4fa0-9b1c-2d3e4f5a6b7c | Nội bộ |
| 3 | `tu_khoa` | Từ khóa | VARCHAR | 1-100 |  | Y |  |  | Từ hoặc cụm từ triệu chứng có dấu | đau ngực | Nội bộ |
| 4 | `tu_khoa_chuan_hoa` | Từ khóa chuẩn hóa | VARCHAR | 1-100, chữ thường không dấu |  | Y |  | Ứng dụng tự sinh từ `tu_khoa` | Dạng dùng để so khớp | dau nguc | Nội bộ |
| 5 | `trong_so` | Trọng số | SMALLINT | 1-5 |  | Y | 1 | `CHECK` 1-5 | Mức đặc trưng của từ khóa với khoa, cộng dồn để xếp hạng | 3 | Nội bộ |
| 6 | `la_dau_hieu_nang` | Là dấu hiệu nặng | BOOLEAN | true/false |  | Y | `false` |  | Nếu khớp, ca được chuyển lên bác sĩ | true | Nội bộ |
| 7 | `dang_su_dung` | Đang sử dụng | BOOLEAN | true/false |  | Y | `true` |  | `false` là không dùng khi gợi ý | true | Nội bộ |
| 8 | `tao_luc` | Tạo lúc | TIMESTAMPTZ | ISO 8601 |  | Y | `now()` |  | Thời điểm tạo | 2026-09-01T02:00:00Z | Nội bộ |
| 9 | `cap_nhat_luc` | Cập nhật lúc | TIMESTAMPTZ | ISO 8601 |  | Y | `now()` | Tự cập nhật khi sửa | Thời điểm sửa gần nhất | 2026-09-01T02:00:00Z | Nội bộ |

- Khóa ngoại: `khoa_id` → `khoa(id)` `RESTRICT`.
- Chỉ mục: `uq_tu_khoa_trieu_chung_tu_khoa_khoa` duy nhất trên (`tu_khoa_chuan_hoa`, `khoa_id`); `idx_tu_khoa_trieu_chung_tu_khoa_chuan_hoa`.
- Quy tắc: một từ khóa có thể ánh xạ sang nhiều khoa. Khi gợi ý, cộng `trong_so` các từ khóa khớp theo từng khoa và lấy khoa điểm cao nhất; không từ khóa nào khớp là “khó xác định khoa”. Bảng này chứa từ khóa, không chứa dữ liệu của người bệnh.

### 2.4 Nhóm đặt lịch và tiếp nhận

#### 2.4.1 Bảng lich_hen (Lịch hẹn)

Mục đích: lịch hẹn khám ngoại trú do người bệnh đặt cho bản thân hoặc thân nhân; là bảng trung tâm nối đặt lịch, duyệt lịch, tiếp nhận và lần khám.

| STT | Tên cột | Tên hiển thị | Kiểu dữ liệu | Độ dài/định dạng | Khóa | Bắt buộc | Mặc định | Ràng buộc/Quy tắc | Mô tả | Ví dụ | Mức nhạy cảm |
|---|---|---|---|---|---|---|---|---|---|---|---|
| 1 | `id` | Mã lịch hẹn (nội bộ) | UUID | 36 ký tự | PK | Y | `gen_random_uuid()` | Duy nhất | Khóa chính nội bộ | c1d2e3f4-0017-4a01-8b02-0c1d2e3f4a5b | Nội bộ |
| 2 | `ma_lich_hen` | Mã lịch hẹn | VARCHAR | 12 ký tự, `^LH[0-9]{6}[A-Z0-9]{4}$` | UK | Y |  | Duy nhất; hệ thống tự sinh (LH + ngày yyMMdd + 4 ký tự ngẫu nhiên) | Mã in trên phiếu hẹn | LH261105A7K2 | Nhạy cảm |
| 3 | `ma_qr` | Mã QR phiếu hẹn | VARCHAR | 43 ký tự base64url (256 bit) | UK | Y |  | Duy nhất; ngẫu nhiên, không chứa dữ liệu bệnh | Token mã hóa vào mã QR trên phiếu hẹn | kP3x9mQ2vL8aR5tY7uW1eZ4cN6bH0jG3sD9fA2xK8oM | Nhạy cảm |
| 4 | `nguoi_benh_id` | Người khám | UUID | 36 ký tự | FK | Y |  |  | Người sẽ được khám | 1b2c3d4e-0002-4a5b-8c6d-7e8f9a0b1c2d | Nhạy cảm |
| 5 | `nguoi_dat_id` | Người đặt lịch | UUID | 36 ký tự | FK | Y |  | Bằng `nguoi_benh_id`, hoặc có liên kết còn hiệu lực trong `than_nhan` | Người đăng nhập để đặt | 1b2c3d4e-0001-4a5b-8c6d-7e8f9a0b1c2d | Nhạy cảm |
| 6 | `loai_hinh_kham` | Loại hình khám | ENUM `loai_hinh_kham` | BHYT, DICH_VU, THEO_YEU_CAU |  | Y |  | `BHYT` chỉ được chọn khi người khám có thẻ còn hiệu lực vào `ngay_hen` | Loại hình người bệnh chọn | BHYT | Nội bộ |
| 7 | `khoa_id` | Khoa | UUID | 36 ký tự | FK | Y |  | Khoa có `loai_khoa = LAM_SANG` | Khoa hiện tại của lịch: ban đầu là khoa người bệnh chọn, đổi khi y tá đổi khoa | 6a7b8c9d-0006-4fa0-9b1c-2d3e4f5a6b7c | Nội bộ |
| 8 | `ngay_hen` | Ngày hẹn | DATE | `YYYY-MM-DD` |  | Y |  | Không được trong quá khứ lúc đặt; phải còn chỗ theo `lich_lam_viec` | Ngày khám | 2026-11-05 | Nhạy cảm |
| 9 | `gio_hen` | Giờ hẹn | TIME | `HH:MM` |  | Y |  | Nằm trên đúng lưới khung giờ của ca làm việc | Khung giờ bắt đầu | 08:30 | Nhạy cảm |
| 10 | `ly_do_kham` | Lý do khám | VARCHAR | 0-500 |  | N | `NULL` |  | Lý do do người bệnh nhập hoặc tạo sẵn từ gợi ý | Đau ngực, khó thở khi leo cầu thang | Rất nhạy cảm |
| 11 | `trang_thai` | Trạng thái lịch hẹn | ENUM `trang_thai_lich_hen` | Xem Phần 3 |  | Y | `CHO_DUYET` | Chuyển trạng thái theo sơ đồ ở Phần 3; mỗi lần đổi ghi `lich_su_trang_thai_lich_hen` | Trạng thái hiện tại | CHO_DUYET | Nội bộ |
| 12 | `tao_luc` | Tạo lúc | TIMESTAMPTZ | ISO 8601 |  | Y | `now()` |  | Thời điểm đặt lịch | 2026-10-26T02:00:00Z | Nội bộ |
| 13 | `cap_nhat_luc` | Cập nhật lúc | TIMESTAMPTZ | ISO 8601 |  | Y | `now()` | Tự cập nhật khi sửa | Thời điểm sửa gần nhất | 2026-10-26T02:00:00Z | Nội bộ |

- Khóa ngoại: `nguoi_benh_id` → `nguoi_benh(id)` `RESTRICT`; `nguoi_dat_id` → `nguoi_benh(id)` `RESTRICT`; `khoa_id` → `khoa(id)` `RESTRICT`.
- Chỉ mục: `uq_lich_hen_ma_lich_hen`, `uq_lich_hen_ma_qr`; `idx_lich_hen_nguoi_benh_id`; `idx_lich_hen_khoa_ngay_gio` trên (`khoa_id`, `ngay_hen`, `gio_hen`) để đếm chỗ trống; `idx_lich_hen_trang_thai` (màn hình duyệt lịch); `uq_lich_hen_khong_trung` duy nhất trên (`nguoi_benh_id`, `ngay_hen`, `gio_hen`) khi `trang_thai` khác `TU_CHOI`, `DA_HUY`, `VANG_MAT`.
- Quy tắc: không lưu riêng người duyệt, thời điểm duyệt và lý do ở bảng này; các thông tin đó lấy từ dòng mới nhất của `lich_su_trang_thai_lich_hen`. Khi đổi khoa, `khoa_id` được cập nhật và khoa cũ, khoa mới được ghi vào lịch sử. Người bệnh chỉ hủy được khi trạng thái còn `CHO_DUYET`, `DA_XAC_NHAN` hoặc `DA_DOI_KHOA`.

#### 2.4.2 Bảng lich_su_trang_thai_lich_hen (Lịch sử trạng thái lịch hẹn)

Mục đích: nhật ký chỉ thêm (append-only) mỗi lần lịch hẹn đổi trạng thái hoặc đổi khoa: ai làm, lúc nào, vì sao.

| STT | Tên cột | Tên hiển thị | Kiểu dữ liệu | Độ dài/định dạng | Khóa | Bắt buộc | Mặc định | Ràng buộc/Quy tắc | Mô tả | Ví dụ | Mức nhạy cảm |
|---|---|---|---|---|---|---|---|---|---|---|---|
| 1 | `id` | Mã dòng lịch sử | UUID | 36 ký tự | PK | Y | `gen_random_uuid()` | Duy nhất | Khóa chính nội bộ | d2e3f4a5-0018-4b02-9c03-1d2e3f4a5b6c | Nội bộ |
| 2 | `lich_hen_id` | Lịch hẹn | UUID | 36 ký tự | FK | Y |  |  | Lịch hẹn bị đổi | c1d2e3f4-0017-4a01-8b02-0c1d2e3f4a5b | Nội bộ |
| 3 | `trang_thai_cu` | Trạng thái cũ | ENUM `trang_thai_lich_hen` | Xem Phần 3 |  | N | `NULL` | `NULL` ở dòng đầu tiên khi tạo lịch | Trạng thái trước khi đổi | CHO_DUYET | Nội bộ |
| 4 | `trang_thai_moi` | Trạng thái mới | ENUM `trang_thai_lich_hen` | Xem Phần 3 |  | Y |  | Phải là bước chuyển hợp lệ từ `trang_thai_cu` | Trạng thái sau khi đổi | DA_XAC_NHAN | Nội bộ |
| 5 | `khoa_cu_id` | Khoa cũ | UUID | 36 ký tự | FK | N | `NULL` | Chỉ có giá trị khi đổi khoa | Khoa trước khi đổi | 6a7b8c9d-0006-4fa0-9b1c-2d3e4f5a6b7c | Nội bộ |
| 6 | `khoa_moi_id` | Khoa mới | UUID | 36 ký tự | FK | N | `NULL` | Có giá trị khi và chỉ khi `khoa_cu_id` có giá trị; hai khoa phải khác nhau | Khoa sau khi đổi | 6a7b8c9d-0016-4fa0-9b1c-2d3e4f5a6b7d | Nội bộ |
| 7 | `nhan_vien_thuc_hien_id` | Nhân viên thực hiện | UUID | 36 ký tự | FK | N | `NULL` | Không đồng thời có `nguoi_benh_thuc_hien_id` | Y tá hoặc bác sĩ duyệt | 9a8b7c6d-5e4f-4a3b-8c2d-1e0f9a8b7c6d | Nội bộ |
| 8 | `nguoi_benh_thuc_hien_id` | Người bệnh thực hiện | UUID | 36 ký tự | FK | N | `NULL` | Không đồng thời có `nhan_vien_thuc_hien_id`; cả hai `NULL` là hệ thống tự đổi | Người bệnh tự hủy hoặc đặt | 1b2c3d4e-0001-4a5b-8c6d-7e8f9a0b1c2d | Nhạy cảm |
| 9 | `ly_do` | Lý do | VARCHAR | 0-500 |  | N | `NULL` | Bắt buộc khi đổi sang `TU_CHOI`, `DA_DOI_KHOA`, `CHO_BAC_SI_XEM_XET`, `DA_HUY` | Lý do đổi trạng thái | Đổi sang Khoa Nội tổng quát vì triệu chứng không đặc hiệu | Rất nhạy cảm |
| 10 | `tao_luc` | Thời điểm đổi | TIMESTAMPTZ | ISO 8601 |  | Y | `now()` | Không được sửa | Thời điểm xảy ra thay đổi | 2026-10-26T02:30:00Z | Nội bộ |

- Khóa ngoại: `lich_hen_id` → `lich_hen(id)` `CASCADE`; `khoa_cu_id`, `khoa_moi_id` → `khoa(id)` `RESTRICT`; `nhan_vien_thuc_hien_id` → `nhan_vien(id)` `RESTRICT`; `nguoi_benh_thuc_hien_id` → `nguoi_benh(id)` `RESTRICT`.
- Chỉ mục: `idx_lich_su_lich_hen_lich_hen_id_tao_luc` trên (`lich_hen_id`, `tao_luc`).
- Quy tắc: chỉ thêm, không sửa, không xóa (không có `cap_nhat_luc`). Dòng đầu tiên của mỗi lịch có `trang_thai_cu = NULL` và `trang_thai_moi = CHO_DUYET`. Duyệt chính là dòng do y tá tạo; ca chuyển lên bác sĩ có dòng `CHO_BAC_SI_XEM_XET` và dòng tiếp theo do bác sĩ tạo.

#### 2.4.3 Bảng goi_y_chuyen_khoa (Gợi ý chuyên khoa)

Mục đích: lưu lần người bệnh nhờ hệ thống gợi ý khoa từ mô tả triệu chứng: khoa hệ thống gợi ý, khoa người bệnh chọn, khoa y tá xác nhận và phương thức gợi ý, để thống kê tỷ lệ chấp nhận. Chỉ có dòng này khi người bệnh có dùng gợi ý.

| STT | Tên cột | Tên hiển thị | Kiểu dữ liệu | Độ dài/định dạng | Khóa | Bắt buộc | Mặc định | Ràng buộc/Quy tắc | Mô tả | Ví dụ | Mức nhạy cảm |
|---|---|---|---|---|---|---|---|---|---|---|---|
| 1 | `id` | Mã gợi ý | UUID | 36 ký tự | PK | Y | `gen_random_uuid()` | Duy nhất | Khóa chính nội bộ | e3f4a5b6-0019-4c03-8d04-2e3f4a5b6c7d | Nội bộ |
| 2 | `lich_hen_id` | Lịch hẹn | UUID | 36 ký tự | FK, UK | Y |  | Mỗi lịch có tối đa một dòng gợi ý | Lịch hẹn dùng gợi ý này | c1d2e3f4-0017-4a01-8b02-0c1d2e3f4a5b | Nội bộ |
| 3 | `mo_ta_trieu_chung` | Mô tả triệu chứng | TEXT | 1-2000 ký tự |  | Y |  | Không để trống | Nội dung người bệnh mô tả tự do | Đau tức ngực bên trái, khó thở khi leo cầu thang | Rất nhạy cảm |
| 4 | `phuong_thuc_goi_y` | Phương thức gợi ý | ENUM `phuong_thuc_goi_y` | TU_KHOA, LLM |  | Y | `TU_KHOA` | `LLM` chỉ dùng khi trình diễn với dữ liệu giả | Cách hệ thống tìm ra khoa gợi ý | TU_KHOA | Nội bộ |
| 5 | `khoa_goi_y_id` | Khoa hệ thống gợi ý | UUID | 36 ký tự | FK | N | `NULL` | `NULL` khi khó xác định khoa; nếu có thì là khoa `LAM_SANG` | Khoa điểm cao nhất | 6a7b8c9d-0006-4fa0-9b1c-2d3e4f5a6b7c | Nội bộ |
| 6 | `ly_do_goi_y` | Lý do gợi ý | VARCHAR | 0-500 |  | N | `NULL` |  | Giải thích, ví dụ các từ khóa đã khớp | Khớp từ khóa: đau ngực, khó thở | Nội bộ |
| 7 | `co_dau_hieu_nang` | Có dấu hiệu nặng | BOOLEAN | true/false |  | Y | `false` | `true` khi khớp từ khóa có `la_dau_hieu_nang = true` | Cờ để chuyển ca lên bác sĩ | true | Rất nhạy cảm |
| 8 | `khoa_nguoi_benh_chon_id` | Khoa người bệnh chọn | UUID | 36 ký tự | FK | Y |  | Ban đầu bằng `lich_hen.khoa_id` | Khoa người bệnh quyết định sau khi xem gợi ý | 6a7b8c9d-0006-4fa0-9b1c-2d3e4f5a6b7c | Nội bộ |
| 9 | `khoa_y_ta_xac_nhan_id` | Khoa y tá xác nhận | UUID | 36 ký tự | FK | N | `NULL` | Được điền khi y tá duyệt; khoa `LAM_SANG` | Khoa cuối cùng sau duyệt | 6a7b8c9d-0006-4fa0-9b1c-2d3e4f5a6b7c | Nội bộ |
| 10 | `tao_luc` | Tạo lúc | TIMESTAMPTZ | ISO 8601 |  | Y | `now()` |  | Thời điểm gợi ý | 2026-10-26T02:00:00Z | Nội bộ |
| 11 | `cap_nhat_luc` | Cập nhật lúc | TIMESTAMPTZ | ISO 8601 |  | Y | `now()` | Tự cập nhật khi sửa | Thời điểm sửa gần nhất | 2026-10-26T02:20:00Z | Nội bộ |

- Khóa ngoại: `lich_hen_id` → `lich_hen(id)` `CASCADE`; `khoa_goi_y_id`, `khoa_nguoi_benh_chon_id`, `khoa_y_ta_xac_nhan_id` → `khoa(id)` `RESTRICT`.
- Chỉ mục: `uq_goi_y_chuyen_khoa_lich_hen_id`; `idx_goi_y_chuyen_khoa_phuong_thuc` (thống kê).
- Quy tắc: gợi ý chỉ mang tính tham khảo. Tỷ lệ người bệnh chấp nhận = số dòng có `khoa_nguoi_benh_chon_id = khoa_goi_y_id` chia tổng số dòng có `khoa_goi_y_id` khác `NULL`; tỷ lệ y tá xác nhận giữ nguyên gợi ý tính tương tự với `khoa_y_ta_xac_nhan_id`; cả hai thống kê theo `phuong_thuc_goi_y`. Ca có `co_dau_hieu_nang = true` hoặc `khoa_goi_y_id IS NULL` được y tá chuyển lên bác sĩ. `mo_ta_trieu_chung` là dữ liệu y tế nhạy cảm (xem Phần 5).

#### 2.4.4 Bảng so_thu_tu (Số thứ tự)

Mục đích: số thứ tự khám do y tá cấp khi tiếp nhận người bệnh đến khám, kèm phòng khám để in phiếu hướng dẫn.

| STT | Tên cột | Tên hiển thị | Kiểu dữ liệu | Độ dài/định dạng | Khóa | Bắt buộc | Mặc định | Ràng buộc/Quy tắc | Mô tả | Ví dụ | Mức nhạy cảm |
|---|---|---|---|---|---|---|---|---|---|---|---|
| 1 | `id` | Mã số thứ tự (nội bộ) | UUID | 36 ký tự | PK | Y | `gen_random_uuid()` | Duy nhất | Khóa chính nội bộ | f4a5b6c7-0020-4d04-9e05-3f4a5b6c7d8e | Nội bộ |
| 2 | `lich_hen_id` | Lịch hẹn | UUID | 36 ký tự | FK, UK | Y |  | Mỗi lịch có tối đa một số thứ tự; lịch phải ở trạng thái `DA_XAC_NHAN` hoặc `DA_DOI_KHOA` | Lịch được tiếp nhận | c1d2e3f4-0017-4a01-8b02-0c1d2e3f4a5b | Nội bộ |
| 3 | `phong_kham_id` | Phòng khám | UUID | 36 ký tự | FK | Y |  | Phòng phải thuộc khoa hiện tại của lịch | Phòng người bệnh đến khám | 7b8c9d0e-0007-4ab1-8c2d-3e4f5a6b7c8d | Nội bộ |
| 4 | `ngay_kham` | Ngày khám | DATE | `YYYY-MM-DD` |  | Y |  | Bằng `lich_hen.ngay_hen` | Ngày của dãy số | 2026-11-05 | Nội bộ |
| 5 | `so_thu_tu` | Số thứ tự | SMALLINT | 1-9999 |  | Y |  | `CHECK >= 1`; tăng dần theo phòng và ngày | Số gọi khám | 12 | Nội bộ |
| 6 | `nhan_vien_cap_id` | Nhân viên cấp số | UUID | 36 ký tự | FK | Y |  | Vai trò Y tá/Nhân viên tiếp nhận | Người cấp số | 9a8b7c6d-5e4f-4a3b-8c2d-1e0f9a8b7c6d | Nội bộ |
| 7 | `in_phieu_luc` | In phiếu lúc | TIMESTAMPTZ | ISO 8601 |  | N | `NULL` | `NULL` nếu chưa in | Lần in phiếu hướng dẫn gần nhất | 2026-11-05T01:20:00Z | Nội bộ |
| 8 | `tao_luc` | Tạo lúc | TIMESTAMPTZ | ISO 8601 |  | Y | `now()` |  | Thời điểm cấp số | 2026-11-05T01:15:00Z | Nội bộ |
| 9 | `cap_nhat_luc` | Cập nhật lúc | TIMESTAMPTZ | ISO 8601 |  | Y | `now()` | Tự cập nhật khi sửa | Thời điểm sửa gần nhất | 2026-11-05T01:20:00Z | Nội bộ |

- Khóa ngoại: `lich_hen_id` → `lich_hen(id)` `RESTRICT`; `phong_kham_id` → `phong_kham(id)` `RESTRICT`; `nhan_vien_cap_id` → `nhan_vien(id)` `RESTRICT`.
- Chỉ mục: `uq_so_thu_tu_lich_hen_id`; `uq_so_thu_tu_phong_ngay_so` duy nhất trên (`phong_kham_id`, `ngay_kham`, `so_thu_tu`).
- Quy tắc: số thứ tự cấp trong một giao dịch để không trùng khi hai y tá cùng cấp. `ngay_kham` lấy từ `lich_hen.ngay_hen` để ràng buộc duy nhất theo ngày thực hiện được.

### 2.5 Nhóm khám bệnh

#### 2.5.1 Bảng lan_kham (Lần khám)

Mục đích: một lần khám cụ thể của người bệnh với một bác sĩ. Đây là đơn vị gom hồ sơ “Bác sĩ của tôi”: mỗi lần khám gắn đúng một bác sĩ, không dồn chung.

| STT | Tên cột | Tên hiển thị | Kiểu dữ liệu | Độ dài/định dạng | Khóa | Bắt buộc | Mặc định | Ràng buộc/Quy tắc | Mô tả | Ví dụ | Mức nhạy cảm |
|---|---|---|---|---|---|---|---|---|---|---|---|
| 1 | `id` | Mã lần khám (nội bộ) | UUID | 36 ký tự | PK | Y | `gen_random_uuid()` | Duy nhất | Khóa chính nội bộ | a5b6c7d8-0021-4e05-8f06-4a5b6c7d8e9f | Nội bộ |
| 2 | `ma_lan_kham` | Mã lần khám | VARCHAR | 12 ký tự, `^LK[0-9]{10}$` | UK | Y |  | Duy nhất; LK + yyMMdd + số thứ tự 4 chữ số trong ngày | Mã hiển thị trên hồ sơ, đơn thuốc, hóa đơn | LK2611050001 | Nhạy cảm |
| 3 | `lich_hen_id` | Lịch hẹn | UUID | 36 ký tự | FK, UK | Y |  | Mỗi lịch có tối đa một lần khám; lịch phải ở `DA_DEN_KHAM` | Lịch hẹn sinh ra lần khám | c1d2e3f4-0017-4a01-8b02-0c1d2e3f4a5b | Nội bộ |
| 4 | `nguoi_benh_id` | Người bệnh | UUID | 36 ký tự | FK | Y |  | Phải bằng `lich_hen.nguoi_benh_id` | Người được khám; lưu lại để tra hồ sơ theo người bệnh không phải nối qua lịch hẹn | 1b2c3d4e-0002-4a5b-8c6d-7e8f9a0b1c2d | Rất nhạy cảm |
| 5 | `bac_si_id` | Bác sĩ khám | UUID | 36 ký tự | FK | Y |  | Bác sĩ phải thuộc khoa hiện tại của lịch hẹn | Bác sĩ phụ trách lần khám này | 8c9d0e1f-0008-4bc2-9d3e-4f5a6b7c8d9e | Nội bộ |
| 6 | `phong_kham_id` | Phòng khám | UUID | 36 ký tự | FK | Y |  | Phòng phải trùng phòng ở `so_thu_tu` | Phòng diễn ra lần khám | 7b8c9d0e-0007-4ab1-8c2d-3e4f5a6b7c8d | Nội bộ |
| 7 | `dich_vu_kham_id` | Dịch vụ khám | UUID | 36 ký tự | FK | Y |  | Khớp khoa và `loai_hinh_kham` của lịch hẹn | Dịch vụ áp giá để tính dòng “khám” trên hóa đơn | 0e1f2a3b-0010-4de4-9f5a-6b7c8d9e0f1a | Nội bộ |
| 8 | `bao_hiem_y_te_id` | Thẻ BHYT áp dụng | UUID | 36 ký tự | FK | N | `NULL` | Bắt buộc khi `loai_hinh_kham = BHYT`; thẻ thuộc đúng người bệnh và còn hiệu lực ngày khám | Thẻ được dùng cho lần khám này | 5f6a7b8c-0005-4e9f-8a0b-1c2d3e4f5a6b | Nhạy cảm |
| 9 | `benh_su` | Bệnh sử | TEXT |  |  | N | `NULL` |  | Diễn tiến bệnh do bác sĩ ghi | Đau ngực từng cơn 2 tuần nay | Rất nhạy cảm |
| 10 | `kham_lam_sang` | Khám lâm sàng | TEXT |  |  | N | `NULL` |  | Kết quả thăm khám của bác sĩ | Tim đều, phổi trong | Rất nhạy cảm |
| 11 | `loi_dan` | Lời dặn | TEXT |  |  | N | `NULL` |  | Hướng dẫn sau khám cho người bệnh | Uống thuốc đều, hạn chế muối | Rất nhạy cảm |
| 12 | `trang_thai` | Trạng thái lần khám | ENUM `trang_thai_lan_kham` | DANG_KHAM, CHO_KET_QUA_CLS, HOAN_THANH, DA_HUY |  | Y | `DANG_KHAM` | Xem Phần 3 | Tiến độ của lần khám | DANG_KHAM | Nội bộ |
| 13 | `bat_dau_luc` | Bắt đầu lúc | TIMESTAMPTZ | ISO 8601 |  | Y | `now()` |  | Lúc bác sĩ bắt đầu khám | 2026-11-05T01:40:00Z | Nội bộ |
| 14 | `ket_thuc_luc` | Kết thúc lúc | TIMESTAMPTZ | ISO 8601 |  | N | `NULL` | Lớn hơn hoặc bằng `bat_dau_luc` | Lúc lần khám hoàn thành | 2026-11-05T02:10:00Z | Nội bộ |
| 15 | `tao_luc` | Tạo lúc | TIMESTAMPTZ | ISO 8601 |  | Y | `now()` |  | Thời điểm tạo | 2026-11-05T01:40:00Z | Nội bộ |
| 16 | `cap_nhat_luc` | Cập nhật lúc | TIMESTAMPTZ | ISO 8601 |  | Y | `now()` | Tự cập nhật khi sửa | Thời điểm sửa gần nhất | 2026-11-05T02:10:00Z | Nội bộ |
| 17 | `xoa_luc` | Xóa lúc | TIMESTAMPTZ | ISO 8601 |  | N | `NULL` | Chỉ quản trị viên đặt, dùng khi tạo nhầm | Xóa mềm | `NULL` | Nội bộ |

- Khóa ngoại: `lich_hen_id` → `lich_hen(id)` `RESTRICT`; `nguoi_benh_id` → `nguoi_benh(id)` `RESTRICT`; `bac_si_id` → `bac_si(id)` `RESTRICT`; `phong_kham_id` → `phong_kham(id)` `RESTRICT`; `dich_vu_kham_id` → `dich_vu_kham(id)` `RESTRICT`; `bao_hiem_y_te_id` → `bao_hiem_y_te(id)` `SET NULL`.
- Chỉ mục: `uq_lan_kham_ma_lan_kham`, `uq_lan_kham_lich_hen_id`; `idx_lan_kham_nguoi_benh_id_bat_dau` trên (`nguoi_benh_id`, `bat_dau_luc` giảm dần) cho lịch sử khám; `idx_lan_kham_bac_si_id` cho hồ sơ “Bác sĩ của tôi”.
- Quy tắc: nhóm theo `bac_si_id` để hiển thị “Bác sĩ của tôi”; mỗi lần khám chỉ có một bác sĩ. Lần khám đã `HOAN_THANH` không sửa nội dung, chỉ bổ sung qua ghi chú có dấu vết trong `nhat_ky_thao_tac`. Người bệnh chỉ xem được lần khám của mình và thân nhân được liên kết.

#### 2.5.2 Bảng chan_doan (Chẩn đoán)

Mục đích: các chẩn đoán bác sĩ đưa ra trong một lần khám (sơ bộ, chính, kèm theo).

| STT | Tên cột | Tên hiển thị | Kiểu dữ liệu | Độ dài/định dạng | Khóa | Bắt buộc | Mặc định | Ràng buộc/Quy tắc | Mô tả | Ví dụ | Mức nhạy cảm |
|---|---|---|---|---|---|---|---|---|---|---|---|
| 1 | `id` | Mã chẩn đoán | UUID | 36 ký tự | PK | Y | `gen_random_uuid()` | Duy nhất | Khóa chính nội bộ | b6c7d8e9-0022-4f06-9a07-5b6c7d8e9f0a | Nội bộ |
| 2 | `lan_kham_id` | Lần khám | UUID | 36 ký tự | FK | Y |  |  | Lần khám có chẩn đoán này | a5b6c7d8-0021-4e05-8f06-4a5b6c7d8e9f | Nội bộ |
| 3 | `loai_chan_doan` | Loại chẩn đoán | ENUM `loai_chan_doan` | SO_BO, CHINH, KEM_THEO |  | Y | `SO_BO` | Mỗi lần khám có tối đa một dòng `CHINH` | Mức độ chắc chắn và vai trò của chẩn đoán | CHINH | Rất nhạy cảm |
| 4 | `ma_icd10` | Mã ICD-10 | VARCHAR | 3-10, ví dụ `I20.9` |  | N | `NULL` | Đúng định dạng ICD-10 khi có | Mã bệnh quốc tế | I20.9 | Rất nhạy cảm |
| 5 | `ten_chan_doan` | Tên chẩn đoán | VARCHAR | 1-255 |  | Y |  |  | Tên bệnh hoặc tình trạng | Đau thắt ngực không ổn định | Rất nhạy cảm |
| 6 | `ghi_chu` | Ghi chú | VARCHAR | 0-500 |  | N | `NULL` |  | Ghi chú thêm của bác sĩ | Chờ kết quả ECG | Rất nhạy cảm |
| 7 | `tao_luc` | Tạo lúc | TIMESTAMPTZ | ISO 8601 |  | Y | `now()` |  | Thời điểm ghi | 2026-11-05T02:00:00Z | Nội bộ |
| 8 | `cap_nhat_luc` | Cập nhật lúc | TIMESTAMPTZ | ISO 8601 |  | Y | `now()` | Tự cập nhật khi sửa | Thời điểm sửa gần nhất | 2026-11-05T02:00:00Z | Nội bộ |

- Khóa ngoại: `lan_kham_id` → `lan_kham(id)` `CASCADE` (chỉ xảy ra khi xóa cứng lần khám để dọn dữ liệu thử, thực tế dùng xóa mềm).
- Chỉ mục: `idx_chan_doan_lan_kham_id`; `uq_chan_doan_chinh` duy nhất trên `lan_kham_id` khi `loai_chan_doan = CHINH`.
- Quy tắc: một lần khám có thể có nhiều chẩn đoán `KEM_THEO`. Không có bảng danh mục ICD-10 riêng vì ngoài phạm vi, mã và tên được nhập trực tiếp.

#### 2.5.3 Bảng chi_dinh_can_lam_sang (Chỉ định cận lâm sàng)

Mục đích: từng chỉ định xét nghiệm, chẩn đoán hình ảnh mà bác sĩ giao trong một lần khám, để kỹ thuật viên nhận và nhập kết quả.

| STT | Tên cột | Tên hiển thị | Kiểu dữ liệu | Độ dài/định dạng | Khóa | Bắt buộc | Mặc định | Ràng buộc/Quy tắc | Mô tả | Ví dụ | Mức nhạy cảm |
|---|---|---|---|---|---|---|---|---|---|---|---|
| 1 | `id` | Mã chỉ định | UUID | 36 ký tự | PK | Y | `gen_random_uuid()` | Duy nhất | Khóa chính nội bộ | c7d8e9f0-0023-4a07-8b08-6c7d8e9f0a1b | Nội bộ |
| 2 | `lan_kham_id` | Lần khám | UUID | 36 ký tự | FK | Y |  |  | Lần khám phát sinh chỉ định | a5b6c7d8-0021-4e05-8f06-4a5b6c7d8e9f | Nội bộ |
| 3 | `danh_muc_can_lam_sang_id` | Hạng mục cận lâm sàng | UUID | 36 ký tự | FK | Y |  | Hạng mục đang `dang_su_dung` lúc chỉ định | Xét nghiệm hoặc kỹ thuật được giao | 1f2a3b4c-0011-4ef5-8a6b-7c8d9e0f1a2b | Nội bộ |
| 4 | `ly_do_chi_dinh` | Lý do chỉ định | VARCHAR | 0-255 |  | N | `NULL` |  | Lý do bác sĩ chỉ định | Loại trừ thiếu máu | Rất nhạy cảm |
| 5 | `trang_thai` | Trạng thái chỉ định | ENUM `trang_thai_chi_dinh_cls` | DA_CHI_DINH, DANG_THUC_HIEN, DA_CO_KET_QUA, DA_HUY |  | Y | `DA_CHI_DINH` | Xem Phần 3 | Tiến độ thực hiện | DA_CHI_DINH | Nội bộ |
| 6 | `ky_thuat_vien_id` | Kỹ thuật viên tiếp nhận | UUID | 36 ký tự | FK | N | `NULL` | Trỏ tới `nhan_vien` có vai trò KY_THUAT_VIEN | Người nhận thực hiện | 9a8b7c6d-0099-4a3b-8c2d-1e0f9a8b7c6d | Nội bộ |
| 7 | `tao_luc` | Tạo lúc | TIMESTAMPTZ | ISO 8601 |  | Y | `now()` |  | Thời điểm chỉ định | 2026-11-05T01:50:00Z | Nội bộ |
| 8 | `cap_nhat_luc` | Cập nhật lúc | TIMESTAMPTZ | ISO 8601 |  | Y | `now()` | Tự cập nhật khi sửa | Thời điểm sửa gần nhất | 2026-11-05T01:50:00Z | Nội bộ |

- Khóa ngoại: `lan_kham_id` → `lan_kham(id)` `RESTRICT`; `danh_muc_can_lam_sang_id` → `danh_muc_can_lam_sang(id)` `RESTRICT`; `ky_thuat_vien_id` → `nhan_vien(id)` `RESTRICT`.
- Chỉ mục: `idx_chi_dinh_cls_lan_kham_id`; `idx_chi_dinh_cls_trang_thai` (hàng chờ của kỹ thuật viên).
- Quy tắc: chỉ bác sĩ của lần khám được tạo và hủy chỉ định; chỉ định đã `DA_CO_KET_QUA` không hủy được. Khi còn chỉ định ở `DA_CHI_DINH` hoặc `DANG_THUC_HIEN`, `lan_kham.trang_thai` là `CHO_KET_QUA_CLS`; khi mọi chỉ định đã có kết quả hoặc bị hủy, bác sĩ quay lại `DANG_KHAM` để kê đơn.

#### 2.5.4 Bảng ket_qua_can_lam_sang (Kết quả cận lâm sàng)

Mục đích: kết quả của một chỉ định cận lâm sàng do kỹ thuật viên nhập, dạng cấu trúc thay đổi theo từng loại xét nghiệm.

| STT | Tên cột | Tên hiển thị | Kiểu dữ liệu | Độ dài/định dạng | Khóa | Bắt buộc | Mặc định | Ràng buộc/Quy tắc | Mô tả | Ví dụ | Mức nhạy cảm |
|---|---|---|---|---|---|---|---|---|---|---|---|
| 1 | `id` | Mã kết quả | UUID | 36 ký tự | PK | Y | `gen_random_uuid()` | Duy nhất | Khóa chính nội bộ | d8e9f0a1-0024-4b08-9c09-7d8e9f0a1b2c | Nội bộ |
| 2 | `chi_dinh_can_lam_sang_id` | Chỉ định | UUID | 36 ký tự | FK, UK | Y |  | Mỗi chỉ định có tối đa một kết quả | Chỉ định được trả kết quả | c7d8e9f0-0023-4a07-8b08-6c7d8e9f0a1b | Nội bộ |
| 3 | `ket_qua_chi_tiet` | Kết quả chi tiết | JSONB | Danh sách chỉ số dạng `{ten, gia_tri, don_vi, tham_chieu}` |  | N | `NULL` | Phải là JSON hợp lệ; có ít nhất một trong `ket_qua_chi_tiet`, `ket_luan`, `tep_dinh_kem_url` | Chỉ số xét nghiệm có cấu trúc | `[{"ten":"WBC","gia_tri":6.8,"don_vi":"G/L"}]` | Rất nhạy cảm |
| 4 | `ket_luan` | Kết luận | TEXT |  |  | N | `NULL` |  | Nhận định của người đọc kết quả | Không phát hiện bất thường | Rất nhạy cảm |
| 5 | `tep_dinh_kem_url` | Tệp đính kèm | VARCHAR | 0-500, URL |  | N | `NULL` |  | Ảnh hoặc PDF kết quả | /private/cls/ecg-0001.pdf | Rất nhạy cảm |
| 6 | `ky_thuat_vien_id` | Kỹ thuật viên nhập | UUID | 36 ký tự | FK | Y |  | Nhân viên có vai trò KY_THUAT_VIEN | Người nhập kết quả | 9a8b7c6d-0099-4a3b-8c2d-1e0f9a8b7c6d | Nội bộ |
| 7 | `tao_luc` | Nhập lúc | TIMESTAMPTZ | ISO 8601 |  | Y | `now()` |  | Thời điểm nhập kết quả | 2026-11-05T02:05:00Z | Nội bộ |
| 8 | `cap_nhat_luc` | Cập nhật lúc | TIMESTAMPTZ | ISO 8601 |  | Y | `now()` | Tự cập nhật khi sửa | Thời điểm sửa gần nhất | 2026-11-05T02:05:00Z | Nội bộ |

- Khóa ngoại: `chi_dinh_can_lam_sang_id` → `chi_dinh_can_lam_sang(id)` `RESTRICT`; `ky_thuat_vien_id` → `nhan_vien(id)` `RESTRICT`.
- Chỉ mục: `uq_ket_qua_cls_chi_dinh_id`.
- Quy tắc: nhập kết quả thì chuyển chỉ định tương ứng sang `DA_CO_KET_QUA` trong cùng giao dịch. Sửa kết quả sau khi nhập phải ghi `nhat_ky_thao_tac` với dữ liệu cũ và mới. Tệp đính kèm lưu ở nơi không công khai và chỉ trả qua API có kiểm tra quyền.

#### 2.5.5 Bảng hen_tai_kham (Hẹn tái khám)

Mục đích: lời hẹn tái khám do bác sĩ đặt sau một lần khám, để nhắc người bệnh và liên kết với lịch hẹn khi người bệnh đã đặt.

| STT | Tên cột | Tên hiển thị | Kiểu dữ liệu | Độ dài/định dạng | Khóa | Bắt buộc | Mặc định | Ràng buộc/Quy tắc | Mô tả | Ví dụ | Mức nhạy cảm |
|---|---|---|---|---|---|---|---|---|---|---|---|
| 1 | `id` | Mã hẹn tái khám | UUID | 36 ký tự | PK | Y | `gen_random_uuid()` | Duy nhất | Khóa chính nội bộ | e9f0a1b2-0025-4c09-8d0a-8e9f0a1b2c3d | Nội bộ |
| 2 | `lan_kham_id` | Lần khám | UUID | 36 ký tự | FK, UK | Y |  | Mỗi lần khám có tối đa một lời hẹn tái khám | Lần khám đặt lời hẹn | a5b6c7d8-0021-4e05-8f06-4a5b6c7d8e9f | Nội bộ |
| 3 | `ngay_tai_kham` | Ngày tái khám | DATE | `YYYY-MM-DD` |  | Y |  | Sau ngày của lần khám | Ngày bác sĩ hẹn quay lại | 2026-11-19 | Nhạy cảm |
| 4 | `ghi_chu` | Ghi chú | VARCHAR | 0-500 |  | N | `NULL` |  | Dặn dò trước khi tái khám | Mang theo kết quả huyết áp tại nhà | Rất nhạy cảm |
| 5 | `lich_hen_tai_kham_id` | Lịch hẹn tái khám | UUID | 36 ký tự | FK | N | `NULL` | Lịch cùng người bệnh, ngày không trước lần khám gốc | Được điền khi người bệnh đặt lịch cho lần tái khám | `NULL` | Nội bộ |
| 6 | `tao_luc` | Tạo lúc | TIMESTAMPTZ | ISO 8601 |  | Y | `now()` |  | Thời điểm đặt hẹn | 2026-11-05T02:10:00Z | Nội bộ |
| 7 | `cap_nhat_luc` | Cập nhật lúc | TIMESTAMPTZ | ISO 8601 |  | Y | `now()` | Tự cập nhật khi sửa | Thời điểm sửa gần nhất | 2026-11-05T02:10:00Z | Nội bộ |

- Khóa ngoại: `lan_kham_id` → `lan_kham(id)` `RESTRICT`; `lich_hen_tai_kham_id` → `lich_hen(id)` `SET NULL`.
- Chỉ mục: `uq_hen_tai_kham_lan_kham_id`; `idx_hen_tai_kham_ngay` trên `ngay_tai_kham` (quét nhắc lịch).
- Quy tắc: hệ thống gửi `thong_bao` loại `NHAC_TAI_KHAM` trước ngày tái khám nếu `lich_hen_tai_kham_id IS NULL`. Người bệnh đặt lịch tái khám thì sinh `lich_hen` mới theo quy trình đặt lịch bình thường rồi gắn vào cột này.

### 2.6 Nhóm đơn thuốc

#### 2.6.1 Bảng don_thuoc (Đơn thuốc)

Mục đích: đơn thuốc ngoại trú do bác sĩ kê sau một lần khám, kèm mã QR để nhân viên phát thuốc xác thực (hợp lệ, đã thanh toán, chưa phát). Bảng không quản lý tồn kho thuốc.

| STT | Tên cột | Tên hiển thị | Kiểu dữ liệu | Độ dài/định dạng | Khóa | Bắt buộc | Mặc định | Ràng buộc/Quy tắc | Mô tả | Ví dụ | Mức nhạy cảm |
|---|---|---|---|---|---|---|---|---|---|---|---|
| 1 | `id` | Mã đơn thuốc (nội bộ) | UUID | 36 ký tự | PK | Y | `gen_random_uuid()` | Duy nhất | Khóa chính nội bộ | f0a1b2c3-0026-4d0a-9e0b-9f0a1b2c3d4e | Nội bộ |
| 2 | `ma_don_thuoc` | Mã đơn thuốc | VARCHAR | 12 ký tự, ^DT[0-9]{10}$ | UK | Y |  | Duy nhất; DT + yyMMdd + số thứ tự 4 chữ số trong ngày | Mã hiển thị trên đơn in và màn hình | DT2611050001 | Nhạy cảm |
| 3 | `ma_qr` | Mã QR đơn thuốc | VARCHAR | 43 ký tự base64url (256 bit) | UK | Y |  | Duy nhất; ngẫu nhiên, không chứa dữ liệu bệnh; sinh khi lưu đơn | Token để nhân viên phát thuốc quét xác thực | Zq7mR2xT9vB4nK1pL6yH3wS8dF5gJ0cA2eU7iO4bX9Y | Nhạy cảm |
| 4 | `lan_kham_id` | Lần khám | UUID | 36 ký tự | FK | Y |  | Lần khám phải ở DANG_KHAM hoặc CHO_KET_QUA_CLS lúc kê; mỗi lần khám có tối đa một đơn khác DA_HUY | Lần khám sinh ra đơn; bác sĩ kê đơn là bác sĩ của lần khám | a5b6c7d8-0021-4e05-8f06-4a5b6c7d8e9f | Nội bộ |
| 5 | `trang_thai` | Trạng thái đơn thuốc | ENUM | `trang_thai_don_thuoc`: `CHUA_THANH_TOAN`, `DA_THANH_TOAN_CHO_NHAN`, `DA_NHAN`, `DA_HUY` |  | Y | `CHUA_THANH_TOAN` | Chuyển trạng thái theo sơ đồ ở Phần 3 | Trạng thái thanh toán và nhận thuốc | DA_THANH_TOAN_CHO_NHAN | Nội bộ |
| 6 | `han_nhan_den` | Hạn nhận thuốc đến | DATE | YYYY-MM-DD |  | Y |  | CHECK >= ngày kê (giờ VN); ứng dụng đặt theo cấu hình, ví dụ ngày kê + 7 ngày | Sau ngày này đơn không còn hợp lệ để phát | 2026-11-12 | Nội bộ |
| 7 | `ghi_chu` | Ghi chú đơn | VARCHAR | 0-500 |  | N | `NULL` |  | Dặn dò chung cho cả đơn | Uống thuốc đều, đo huyết áp mỗi sáng | Rất nhạy cảm |
| 8 | `tao_luc` | Tạo lúc | TIMESTAMPTZ | ISO 8601 |  | Y | `now()` |  | Thời điểm bác sĩ kê đơn | 2026-11-05T02:08:00Z | Nội bộ |
| 9 | `cap_nhat_luc` | Cập nhật lúc | TIMESTAMPTZ | ISO 8601 |  | Y | `now()` | Tự cập nhật khi sửa | Thời điểm sửa gần nhất | 2026-11-05T02:20:00Z | Nội bộ |

- Khóa ngoại: `lan_kham_id` → `lan_kham(id)` RESTRICT.
- Chỉ mục: `uq_don_thuoc_ma_don_thuoc`, `uq_don_thuoc_ma_qr`; `uq_don_thuoc_lan_kham_hieu_luc` duy nhất trên `lan_kham_id` khi `trang_thai` khác `DA_HUY`; `idx_don_thuoc_trang_thai` (màn hình phát thuốc).
- Quy tắc: bác sĩ kê đơn chính là `lan_kham.bac_si_id`, không lưu lặp. Đơn phải có ít nhất một dòng `chi_tiet_don_thuoc`. Khi `hoa_don` của lần khám chuyển `DA_THANH_TOAN`, hệ thống chuyển đơn `CHUA_THANH_TOAN` sang `DA_THANH_TOAN_CHO_NHAN` trong cùng giao dịch. Chỉ sửa dòng thuốc hoặc hủy đơn (`DA_HUY`) khi đơn còn `CHUA_THANH_TOAN`; khi đó các khoản thuốc tương ứng trong hóa đơn chưa thanh toán được cập nhật và tính lại tổng. Người bệnh xem được mã QR của đơn mình ngay sau khi kê, nhưng chỉ phát được khi đơn ở `DA_THANH_TOAN_CHO_NHAN` và còn trong `han_nhan_den`.

#### 2.6.2 Bảng chi_tiet_don_thuoc (Chi tiết đơn thuốc)

Mục đích: từng dòng thuốc trong một đơn, gồm thuốc, số lượng, liều dùng và số ngày dùng.

| STT | Tên cột | Tên hiển thị | Kiểu dữ liệu | Độ dài/định dạng | Khóa | Bắt buộc | Mặc định | Ràng buộc/Quy tắc | Mô tả | Ví dụ | Mức nhạy cảm |
|---|---|---|---|---|---|---|---|---|---|---|---|
| 1 | `id` | Mã dòng thuốc | UUID | 36 ký tự | PK | Y | `gen_random_uuid()` | Duy nhất | Khóa chính nội bộ | a1b2c3d4-0027-4e0b-8f0c-0a1b2c3d4e5f | Nội bộ |
| 2 | `don_thuoc_id` | Đơn thuốc | UUID | 36 ký tự | FK | Y |  | Không trùng thuốc trong cùng đơn | Đơn thuốc chứa dòng này | f0a1b2c3-0026-4d0a-9e0b-9f0a1b2c3d4e | Nội bộ |
| 3 | `danh_muc_thuoc_id` | Thuốc | UUID | 36 ký tự | FK | Y |  | Thuốc phải đang `dang_su_dung` lúc kê | Thuốc được kê (tra từ danh mục) | 2a3b4c5d-0012-4fa6-9b7c-8d9e0f1a2b3c | Rất nhạy cảm |
| 4 | `so_luong` | Số lượng | INTEGER | 1-9999 |  | Y |  | CHECK 1-9999; tính theo `danh_muc_thuoc.don_vi_tinh` | Tổng số đơn vị thuốc cấp cho người bệnh | 30 | Rất nhạy cảm |
| 5 | `lieu_dung` | Liều dùng | VARCHAR | 1-255 |  | Y |  |  | Liều mỗi lần và số lần mỗi ngày | 1 viên/lần, 1 lần/ngày | Rất nhạy cảm |
| 6 | `cach_dung` | Cách dùng | VARCHAR | 0-255 |  | N | `NULL` |  | Thời điểm và cách uống/dùng | Uống buổi sáng, sau ăn | Rất nhạy cảm |
| 7 | `so_ngay_dung` | Số ngày dùng | SMALLINT | 1-365 |  | Y |  | CHECK 1-365 | Số ngày người bệnh dùng thuốc | 30 | Rất nhạy cảm |
| 8 | `tao_luc` | Tạo lúc | TIMESTAMPTZ | ISO 8601 |  | Y | `now()` |  | Thời điểm tạo | 2026-11-05T02:08:00Z | Nội bộ |
| 9 | `cap_nhat_luc` | Cập nhật lúc | TIMESTAMPTZ | ISO 8601 |  | Y | `now()` | Tự cập nhật khi sửa | Thời điểm sửa gần nhất | 2026-11-05T02:08:00Z | Nội bộ |

- Khóa ngoại: `don_thuoc_id` → `don_thuoc(id)` CASCADE (dòng thuốc là một phần của đơn, đơn không bị xóa cứng nên cascade chỉ là ràng buộc toàn vẹn); `danh_muc_thuoc_id` → `danh_muc_thuoc(id)` RESTRICT.
- Chỉ mục: `uq_chi_tiet_don_thuoc_don_thuoc_thuoc` duy nhất trên (`don_thuoc_id`, `danh_muc_thuoc_id`); `idx_chi_tiet_don_thuoc_danh_muc_thuoc_id` (thống kê thuốc được kê).
- Quy tắc: không kiểm tra hay trừ tồn kho. Khi lưu dòng, ứng dụng đối chiếu `danh_muc_thuoc.hoat_chat` với các mục `DI_UNG_THUOC` còn hiệu lực của người bệnh trong `thong_tin_y_te_nen` và hiện cảnh báo cho bác sĩ (cảnh báo, không chặn). Đơn giá không lưu ở đây mà được sao chép vào `chi_tiet_hoa_don` lúc lập hóa đơn. Chỉ sửa được khi đơn còn `CHUA_THANH_TOAN`.

#### 2.6.3 Bảng phat_thuoc (Phát thuốc)

Mục đích: ghi nhận một lần phát thuốc cho một đơn sau khi nhân viên phát thuốc quét QR và xác thực thành công. Bảng chỉ thêm, không sửa, không xóa.

| STT | Tên cột | Tên hiển thị | Kiểu dữ liệu | Độ dài/định dạng | Khóa | Bắt buộc | Mặc định | Ràng buộc/Quy tắc | Mô tả | Ví dụ | Mức nhạy cảm |
|---|---|---|---|---|---|---|---|---|---|---|---|
| 1 | `id` | Mã lần phát thuốc | UUID | 36 ký tự | PK | Y | `gen_random_uuid()` | Duy nhất | Khóa chính nội bộ | b2c3d4e5-0028-4f0c-9a0d-1b2c3d4e5f6a | Nội bộ |
| 2 | `don_thuoc_id` | Đơn thuốc | UUID | 36 ký tự | FK, UK | Y |  | Mỗi đơn chỉ được phát một lần; đơn phải ở DA_THANH_TOAN_CHO_NHAN lúc ghi | Đơn thuốc được phát | f0a1b2c3-0026-4d0a-9e0b-9f0a1b2c3d4e | Nội bộ |
| 3 | `nhan_vien_phat_id` | Nhân viên phát thuốc | UUID | 36 ký tự | FK | Y |  | Nhân viên có vai trò PHAT_THUOC | Người quét QR và phát thuốc | 9a8b7c6d-0031-4a3b-8c2d-1e0f9a8b7c6d | Nội bộ |
| 4 | `ghi_chu` | Ghi chú | VARCHAR | 0-255 |  | N | `NULL` |  | Ghi chú khi phát thuốc | Đã hướng dẫn cách dùng | Nội bộ |
| 5 | `tao_luc` | Tạo lúc | TIMESTAMPTZ | ISO 8601 |  | Y | `now()` |  | Thời điểm phát thuốc | 2026-11-05T02:35:00Z | Nội bộ |

- Khóa ngoại: `don_thuoc_id` → `don_thuoc(id)` RESTRICT; `nhan_vien_phat_id` → `nhan_vien(id)` RESTRICT.
- Chỉ mục: `uq_phat_thuoc_don_thuoc_id` (chặn phát hai lần); `idx_phat_thuoc_nhan_vien_phat_id_tao_luc` (báo cáo theo nhân viên).
- Quy tắc xác thực khi quét QR: (1) `don_thuoc.ma_qr` khớp, (2) `trang_thai` = `DA_THANH_TOAN_CHO_NHAN`, (3) `han_nhan_den` chưa quá hạn, (4) chưa có dòng `phat_thuoc` của đơn. Đủ cả bốn điều kiện thì thêm dòng `phat_thuoc` và chuyển đơn sang `DA_NHAN` trong cùng giao dịch; thiếu điều kiện nào thì từ chối và báo đúng lý do (không hợp lệ, chưa thanh toán, quá hạn, đã phát). Mọi lần quét, kể cả thất bại, ghi `nhat_ky_thao_tac`. Không ghi số lượng thực phát và không có tồn kho.

### 2.7 Nhóm tài chính

#### 2.7.1 Bảng hoa_don (Hóa đơn)

Mục đích: hóa đơn viện phí mô phỏng của một lần khám, tổng hợp các khoản khám, cận lâm sàng, thuốc, tách phần BHYT chi trả và phần người bệnh trả.

| STT | Tên cột | Tên hiển thị | Kiểu dữ liệu | Độ dài/định dạng | Khóa | Bắt buộc | Mặc định | Ràng buộc/Quy tắc | Mô tả | Ví dụ | Mức nhạy cảm |
|---|---|---|---|---|---|---|---|---|---|---|---|
| 1 | `id` | Mã hóa đơn (nội bộ) | UUID | 36 ký tự | PK | Y | `gen_random_uuid()` | Duy nhất | Khóa chính nội bộ | c3d4e5f6-0029-4a0d-8b0e-2c3d4e5f6a7b | Nội bộ |
| 2 | `ma_hoa_don` | Mã hóa đơn | VARCHAR | 12 ký tự, ^HD[0-9]{10}$ | UK | Y |  | Duy nhất; HD + yyMMdd + số thứ tự 4 chữ số trong ngày | Mã hiển thị trên hóa đơn | HD2611050001 | Nhạy cảm |
| 3 | `lan_kham_id` | Lần khám | UUID | 36 ký tự | FK | Y |  | Mỗi lần khám có tối đa một hóa đơn khác DA_HUY | Lần khám phát sinh hóa đơn | a5b6c7d8-0021-4e05-8f06-4a5b6c7d8e9f | Nội bộ |
| 4 | `nguoi_benh_id` | Người bệnh | UUID | 36 ký tự | FK | Y |  | Phải bằng `lan_kham.nguoi_benh_id` | Người bệnh được tính phí (để tra "hóa đơn của tôi") | 1b2c3d4e-0001-4a5b-8c6d-7e8f9a0b1c2d | Nhạy cảm |
| 5 | `muc_huong_ap_dung` | Mức hưởng BHYT áp dụng (%) | SMALLINT | 0-100 |  | Y | `0` | CHECK 0-100; sao chép từ `bao_hiem_y_te.muc_huong` của lần khám, 0 nếu không dùng BHYT | Mức hưởng tại thời điểm lập, không đổi về sau | 80 | Nhạy cảm |
| 6 | `tong_tien` | Tổng tiền | NUMERIC | (12,0), VND |  | Y | `0` | CHECK >= 0; bằng tổng `chi_tiet_hoa_don.thanh_tien` | Tổng chi phí các khoản | 159700 | Nhạy cảm |
| 7 | `tong_bhyt_chi_tra` | Tổng BHYT chi trả | NUMERIC | (12,0), VND |  | Y | `0` | CHECK >= 0; bằng tổng `chi_tiet_hoa_don.bhyt_chi_tra` | Phần quỹ BHYT chi trả | 127760 | Nhạy cảm |
| 8 | `tong_nguoi_benh_tra` | Tổng người bệnh trả | NUMERIC | (12,0), VND |  | Y | `0` | CHECK >= 0; bằng `tong_tien` trừ `tong_bhyt_chi_tra` | Phần người bệnh phải thanh toán | 31940 | Nhạy cảm |
| 9 | `trang_thai` | Trạng thái hóa đơn | ENUM | `trang_thai_hoa_don`: `CHUA_THANH_TOAN`, `DA_THANH_TOAN`, `DA_HUY` |  | Y | `CHUA_THANH_TOAN` | Chuyển trạng thái theo sơ đồ ở Phần 3 | Trạng thái thanh toán | DA_THANH_TOAN | Nội bộ |
| 10 | `da_thanh_toan_luc` | Đã thanh toán lúc | TIMESTAMPTZ | ISO 8601 |  | N | `NULL` | Có giá trị khi và chỉ khi trang_thai = DA_THANH_TOAN | Thời điểm hóa đơn được thanh toán đủ | 2026-11-05T02:20:00Z | Nội bộ |
| 11 | `ly_do_huy` | Lý do hủy | VARCHAR | 0-255 |  | N | `NULL` | Bắt buộc khi trang_thai = DA_HUY | Lý do hủy hóa đơn | Lần khám bị hủy do tạo nhầm | Nội bộ |
| 12 | `tao_luc` | Tạo lúc | TIMESTAMPTZ | ISO 8601 |  | Y | `now()` |  | Thời điểm lập hóa đơn | 2026-11-05T02:09:00Z | Nội bộ |
| 13 | `cap_nhat_luc` | Cập nhật lúc | TIMESTAMPTZ | ISO 8601 |  | Y | `now()` | Tự cập nhật khi sửa | Thời điểm sửa gần nhất | 2026-11-05T02:20:00Z | Nội bộ |

- Khóa ngoại: `lan_kham_id` → `lan_kham(id)` RESTRICT; `nguoi_benh_id` → `nguoi_benh(id)` RESTRICT.
- Chỉ mục: `uq_hoa_don_ma_hoa_don`; `uq_hoa_don_lan_kham_hieu_luc` duy nhất trên `lan_kham_id` khi `trang_thai` khác `DA_HUY`; `idx_hoa_don_nguoi_benh_id_tao_luc`; `idx_hoa_don_trang_thai` (màn hình thu ngân).
- Quy tắc: hóa đơn được lập tự động ở lần đầu bác sĩ chỉ định cận lâm sàng hoặc kê đơn (tùy việc nào đến trước); khoản khám lấy từ `lan_kham.dich_vu_kham_id`, khoản cận lâm sàng và thuốc được thêm hoặc bớt theo chỉ định và đơn khi hóa đơn còn `CHUA_THANH_TOAN`. Ba cột tổng được tính lại từ `chi_tiet_hoa_don` trong cùng giao dịch với mọi thay đổi khoản. Hóa đơn `DA_THANH_TOAN` bị khóa, không sửa khoản. Nếu `tong_nguoi_benh_tra` = 0 (BHYT chi trả toàn bộ) thì hệ thống tự chuyển `DA_THANH_TOAN` mà không cần dòng `thanh_toan`. Người bệnh chỉ xem hóa đơn của mình và thân nhân được liên kết. Đây là thanh toán mô phỏng, không kết nối cổng thanh toán hay cơ quan bảo hiểm.

#### 2.7.2 Bảng chi_tiet_hoa_don (Chi tiết hóa đơn)

Mục đích: từng khoản trong hóa đơn (khám, cận lâm sàng hoặc thuốc), sao chép tên và đơn giá tại thời điểm lập để hóa đơn không đổi khi danh mục đổi giá.

| STT | Tên cột | Tên hiển thị | Kiểu dữ liệu | Độ dài/định dạng | Khóa | Bắt buộc | Mặc định | Ràng buộc/Quy tắc | Mô tả | Ví dụ | Mức nhạy cảm |
|---|---|---|---|---|---|---|---|---|---|---|---|
| 1 | `id` | Mã khoản hóa đơn | UUID | 36 ký tự | PK | Y | `gen_random_uuid()` | Duy nhất | Khóa chính nội bộ | d4e5f6a7-0030-4b0e-9c0f-3d4e5f6a7b8c | Nội bộ |
| 2 | `hoa_don_id` | Hóa đơn | UUID | 36 ký tự | FK | Y |  | Hóa đơn phải ở CHUA_THANH_TOAN khi thêm, sửa, xóa khoản | Hóa đơn chứa khoản này | c3d4e5f6-0029-4a0d-8b0e-2c3d4e5f6a7b | Nội bộ |
| 3 | `loai_khoan` | Loại khoản | ENUM | `loai_khoan_hoa_don`: `KHAM`, `CAN_LAM_SANG`, `THUOC` |  | Y |  | Quyết định cột nguồn nào có giá trị (xem ba cột kế tiếp) | Nhóm khoản thu | THUOC | Nội bộ |
| 4 | `dich_vu_kham_id` | Dịch vụ khám | UUID | 36 ký tự | FK | N | `NULL` | Có giá trị khi và chỉ khi loai_khoan = KHAM | Dịch vụ khám được tính phí | NULL | Nội bộ |
| 5 | `chi_dinh_can_lam_sang_id` | Chỉ định cận lâm sàng | UUID | 36 ký tự | FK | N | `NULL` | Có giá trị khi và chỉ khi loai_khoan = CAN_LAM_SANG | Chỉ định cận lâm sàng được tính phí | NULL | Rất nhạy cảm |
| 6 | `chi_tiet_don_thuoc_id` | Dòng thuốc | UUID | 36 ký tự | FK | N | `NULL` | Có giá trị khi và chỉ khi loai_khoan = THUOC | Dòng thuốc trong đơn được tính phí | a1b2c3d4-0027-4e0b-8f0c-0a1b2c3d4e5f | Rất nhạy cảm |
| 7 | `ten_khoan` | Tên khoản | VARCHAR | 1-200 |  | Y |  | Sao chép tên dịch vụ, cận lâm sàng hoặc thuốc lúc lập | Tên hiển thị trên hóa đơn | Amlodipin 5mg | Rất nhạy cảm |
| 8 | `so_luong` | Số lượng | INTEGER | 1-9999 |  | Y | `1` | CHECK 1-9999; khoản KHAM và CAN_LAM_SANG luôn là 1 | Số lượng tính phí | 30 | Nhạy cảm |
| 9 | `don_gia` | Đơn giá | NUMERIC | (12,0), VND |  | Y |  | CHECK >= 0; sao chép từ danh mục lúc lập | Giá một đơn vị tại thời điểm lập | 1200 | Nhạy cảm |
| 10 | `thanh_tien` | Thành tiền | NUMERIC | (12,0), VND |  | Y |  | CHECK = so_luong × don_gia | Chi phí của khoản | 36000 | Nhạy cảm |
| 11 | `bhyt_chi_tra` | BHYT chi trả | NUMERIC | (12,0), VND |  | Y | `0` | CHECK >= 0; bằng ROUND(thanh_tien × muc_huong_ap_dung / 100) nếu mục nguồn có duoc_bhyt_chi_tra = true và lần khám thuộc loại BHYT, ngược lại 0 | Phần quỹ BHYT chi trả | 28800 | Nhạy cảm |
| 12 | `nguoi_benh_tra` | Người bệnh trả | NUMERIC | (12,0), VND |  | Y |  | CHECK = thanh_tien - bhyt_chi_tra | Phần người bệnh phải trả | 7200 | Nhạy cảm |
| 13 | `tao_luc` | Tạo lúc | TIMESTAMPTZ | ISO 8601 |  | Y | `now()` |  | Thời điểm tạo | 2026-11-05T02:09:00Z | Nội bộ |
| 14 | `cap_nhat_luc` | Cập nhật lúc | TIMESTAMPTZ | ISO 8601 |  | Y | `now()` | Tự cập nhật khi sửa | Thời điểm sửa gần nhất | 2026-11-05T02:09:00Z | Nội bộ |

- Khóa ngoại: `hoa_don_id` → `hoa_don(id)` CASCADE (khoản là một phần của hóa đơn, hóa đơn không bị xóa cứng); `dich_vu_kham_id` → `dich_vu_kham(id)`, `chi_dinh_can_lam_sang_id` → `chi_dinh_can_lam_sang(id)`, `chi_tiet_don_thuoc_id` → `chi_tiet_don_thuoc(id)` đều RESTRICT.
- Chỉ mục: `idx_chi_tiet_hoa_don_hoa_don_id`; `idx_chi_tiet_hoa_don_chi_dinh_can_lam_sang_id`; `idx_chi_tiet_hoa_don_chi_tiet_don_thuoc_id`; `idx_chi_tiet_hoa_don_loai_khoan` (thống kê doanh thu theo nhóm khoản).
- Quy tắc: CHECK đúng một trong ba cột nguồn có giá trị và khớp `loai_khoan`. Mỗi chỉ định cận lâm sàng và mỗi dòng thuốc chỉ nằm trong tối đa một hóa đơn khác `DA_HUY` (kiểm ở tầng ứng dụng vì không biểu diễn được bằng chỉ mục duy nhất đơn giản khi hóa đơn cũ bị hủy). Chỉ định cận lâm sàng `DA_HUY` không được tính phí. Không sửa `don_gia` thủ công.

#### 2.7.3 Bảng thanh_toan (Thanh toán)

Mục đích: mỗi lần thanh toán mô phỏng của một hóa đơn do người bệnh tự trả trên website hoặc nhân viên thu ngân thu tại quầy; lần thất bại vẫn được lưu để truy vết.

| STT | Tên cột | Tên hiển thị | Kiểu dữ liệu | Độ dài/định dạng | Khóa | Bắt buộc | Mặc định | Ràng buộc/Quy tắc | Mô tả | Ví dụ | Mức nhạy cảm |
|---|---|---|---|---|---|---|---|---|---|---|---|
| 1 | `id` | Mã lần thanh toán | UUID | 36 ký tự | PK | Y | `gen_random_uuid()` | Duy nhất | Khóa chính nội bộ | e5f6a7b8-0031-4c0f-8d10-4e5f6a7b8c9d | Nội bộ |
| 2 | `ma_giao_dich` | Mã giao dịch | VARCHAR | 14 ký tự, ^GD[0-9]{6}[A-Z0-9]{6}$ | UK | Y |  | Duy nhất; GD + yyMMdd + 6 ký tự ngẫu nhiên | Mã giao dịch mô phỏng hiển thị trên biên lai | GD261105X8M2QP | Nhạy cảm |
| 3 | `hoa_don_id` | Hóa đơn | UUID | 36 ký tự | FK | Y |  | Hóa đơn phải ở CHUA_THANH_TOAN khi tạo giao dịch | Hóa đơn được thanh toán | c3d4e5f6-0029-4a0d-8b0e-2c3d4e5f6a7b | Nội bộ |
| 4 | `so_tien` | Số tiền | NUMERIC | (12,0), VND |  | Y |  | CHECK > 0; phải bằng hoa_don.tong_nguoi_benh_tra (thanh toán một lần đủ) | Số tiền người bệnh trả | 31940 | Nhạy cảm |
| 5 | `phuong_thuc` | Phương thức thanh toán | ENUM | `phuong_thuc_thanh_toan`: `TIEN_MAT`, `CHUYEN_KHOAN`, `THE`, `VI_DIEN_TU` |  | Y |  | Tất cả chỉ mô phỏng; `TIEN_MAT` chỉ dùng khi thu ngân thu tại quầy | Hình thức thanh toán | VI_DIEN_TU | Nội bộ |
| 6 | `trang_thai` | Trạng thái thanh toán | ENUM | `trang_thai_thanh_toan`: `CHO_XU_LY`, `THANH_CONG`, `THAT_BAI` |  | Y | `CHO_XU_LY` | Chuyển trạng thái theo Phần 3 | Kết quả của lần thanh toán | THANH_CONG | Nội bộ |
| 7 | `nhan_vien_thu_id` | Nhân viên thu | UUID | 36 ký tự | FK | N | `NULL` | Đúng một trong hai: nhan_vien_thu_id hoặc nguoi_tra_id có giá trị; nhân viên có vai trò THU_NGAN | Thu ngân thu tiền tại quầy | NULL | Nội bộ |
| 8 | `nguoi_tra_id` | Người trả | UUID | 36 ký tự | FK | N | `NULL` | Đúng một trong hai; là chính người bệnh hoặc người có liên kết còn hiệu lực trong than_nhan | Người bệnh hoặc thân nhân tự thanh toán trên website | 1b2c3d4e-0001-4a5b-8c6d-7e8f9a0b1c2d | Nhạy cảm |
| 9 | `thanh_toan_luc` | Thanh toán lúc | TIMESTAMPTZ | ISO 8601 |  | N | `NULL` | Có giá trị khi và chỉ khi trang_thai = THANH_CONG | Thời điểm thanh toán thành công | 2026-11-05T02:20:00Z | Nội bộ |
| 10 | `ly_do_that_bai` | Lý do thất bại | VARCHAR | 0-255 |  | N | `NULL` | Bắt buộc khi trang_thai = THAT_BAI | Lý do giao dịch mô phỏng thất bại | Số dư không đủ | Nội bộ |
| 11 | `tao_luc` | Tạo lúc | TIMESTAMPTZ | ISO 8601 |  | Y | `now()` |  | Thời điểm tạo giao dịch | 2026-11-05T02:19:30Z | Nội bộ |
| 12 | `cap_nhat_luc` | Cập nhật lúc | TIMESTAMPTZ | ISO 8601 |  | Y | `now()` | Tự cập nhật khi sửa | Thời điểm sửa gần nhất | 2026-11-05T02:20:00Z | Nội bộ |

- Khóa ngoại: `hoa_don_id` → `hoa_don(id)` RESTRICT; `nhan_vien_thu_id` → `nhan_vien(id)` RESTRICT; `nguoi_tra_id` → `nguoi_benh(id)` RESTRICT.
- Chỉ mục: `uq_thanh_toan_ma_giao_dich`; `uq_thanh_toan_hoa_don_thanh_cong` duy nhất trên `hoa_don_id` khi `trang_thai` = `THANH_CONG` (mỗi hóa đơn chỉ trả thành công một lần); `idx_thanh_toan_hoa_don_id`; `idx_thanh_toan_nhan_vien_thu_id_thanh_toan_luc` (đối soát ca thu ngân).
- Quy tắc: một hóa đơn có thể có nhiều dòng `thanh_toan` (các lần `THAT_BAI` rồi một lần `THANH_CONG`). Khi dòng chuyển `THANH_CONG`, trong cùng giao dịch: điền `thanh_toan_luc`, chuyển `hoa_don` sang `DA_THANH_TOAN`, chuyển `don_thuoc` đang `CHUA_THANH_TOAN` sang `DA_THANH_TOAN_CHO_NHAN`, tạo `thong_bao` cho người bệnh. Không lưu số thẻ hay thông tin tài khoản thật. Không có hoàn tiền trong phạm vi đề tài.

### 2.8 Nhóm giấy tờ

#### 2.8.1 Bảng giay_to_benh_vien (Giấy tờ bệnh viện)

Mục đích: yêu cầu giấy tờ của người bệnh (chuyển tuyến...) và kết quả duyệt của nhân viên; giấy được duyệt sinh tệp PDF để người bệnh tải hoặc in. Bác sĩ cũng có thể chủ động cấp giấy chuyển tuyến trong lúc khám.

| STT | Tên cột | Tên hiển thị | Kiểu dữ liệu | Độ dài/định dạng | Khóa | Bắt buộc | Mặc định | Ràng buộc/Quy tắc | Mô tả | Ví dụ | Mức nhạy cảm |
|---|---|---|---|---|---|---|---|---|---|---|---|
| 1 | `id` | Mã giấy tờ (nội bộ) | UUID | 36 ký tự | PK | Y | `gen_random_uuid()` | Duy nhất | Khóa chính nội bộ | f6a7b8c9-0032-4d10-9e11-5f6a7b8c9d0e | Nội bộ |
| 2 | `ma_giay_to` | Mã giấy tờ | VARCHAR | 12 ký tự, ^GT[0-9]{10}$ | UK | Y |  | Duy nhất; GT + yyMMdd + số thứ tự 4 chữ số trong ngày | Mã hiển thị trên giấy | GT2611060001 | Nhạy cảm |
| 3 | `nguoi_benh_id` | Người bệnh | UUID | 36 ký tự | FK | Y |  |  | Người bệnh được cấp giấy | 1b2c3d4e-0001-4a5b-8c6d-7e8f9a0b1c2d | Rất nhạy cảm |
| 4 | `lan_kham_id` | Lần khám liên quan | UUID | 36 ký tự | FK | N | `NULL` | Bắt buộc khi loai_giay_to là CHUYEN_TUYEN hoặc NGHI_OM; lần khám phải của đúng nguoi_benh_id | Lần khám làm căn cứ cho giấy | a5b6c7d8-0021-4e05-8f06-4a5b6c7d8e9f | Rất nhạy cảm |
| 5 | `loai_giay_to` | Loại giấy tờ | ENUM | `loai_giay_to`: `CHUYEN_TUYEN`, `XAC_NHAN_KHAM_BENH`, `NGHI_OM`, `TOM_TAT_KHAM_BENH` |  | Y |  | Xem Phần 3 | Loại giấy được yêu cầu | CHUYEN_TUYEN | Rất nhạy cảm |
| 6 | `nguoi_yeu_cau_id` | Người yêu cầu | UUID | 36 ký tự | FK | N | `NULL` | NULL khi bác sĩ chủ động cấp; nếu có thì là chính nguoi_benh_id hoặc người có liên kết còn hiệu lực trong than_nhan | Người bệnh hoặc thân nhân gửi yêu cầu | 1b2c3d4e-0001-4a5b-8c6d-7e8f9a0b1c2d | Nhạy cảm |
| 7 | `ly_do_yeu_cau` | Lý do yêu cầu | VARCHAR | 0-500 |  | N | `NULL` |  | Lý do người bệnh nêu khi yêu cầu | Cần chuyển tuyến để làm thêm xét nghiệm chuyên sâu | Rất nhạy cảm |
| 8 | `noi_chuyen_den` | Nơi chuyển đến | VARCHAR | 0-200 |  | N | `NULL` | Bắt buộc khi loai_giay_to = CHUYEN_TUYEN | Cơ sở y tế tiếp nhận | Bệnh viện Chợ Rẫy | Rất nhạy cảm |
| 9 | `noi_dung` | Nội dung giấy | TEXT |  |  | N | `NULL` | Bắt buộc khi trang_thai = DA_DUYET | Nội dung do nhân viên soạn khi duyệt (tóm tắt bệnh, lý do chuyển tuyến...) | Đau thắt ngực không ổn định, đề nghị chụp mạch vành | Rất nhạy cảm |
| 10 | `trang_thai` | Trạng thái giấy tờ | ENUM | `trang_thai_giay_to`: `CHO_DUYET`, `DA_DUYET`, `TU_CHOI`, `DA_HUY` |  | Y | `CHO_DUYET` | Chuyển trạng thái theo Phần 3; bác sĩ chủ động cấp thì tạo thẳng DA_DUYET | Trạng thái xử lý | DA_DUYET | Nội bộ |
| 11 | `nhan_vien_duyet_id` | Nhân viên duyệt | UUID | 36 ký tự | FK | N | `NULL` | Có khi DA_DUYET hoặc TU_CHOI; CHUYEN_TUYEN và NGHI_OM phải do bác sĩ duyệt | Người duyệt hoặc từ chối | 9a8b7c6d-5e4f-4a3b-8c2d-1e0f9a8b7c6d | Nội bộ |
| 12 | `duyet_luc` | Duyệt lúc | TIMESTAMPTZ | ISO 8601 |  | N | `NULL` | Có khi DA_DUYET hoặc TU_CHOI | Thời điểm duyệt hoặc từ chối | 2026-11-06T04:30:00Z | Nội bộ |
| 13 | `ly_do_tu_choi` | Lý do từ chối | VARCHAR | 0-500 |  | N | `NULL` | Bắt buộc khi trang_thai = TU_CHOI | Lý do nhân viên từ chối | Cần khám lại trước khi chuyển tuyến | Rất nhạy cảm |
| 14 | `tep_giay_to_url` | Đường dẫn tệp giấy | VARCHAR | 0-500, URL |  | N | `NULL` | Có khi DA_DUYET; tệp PDF lưu nơi không công khai | Tệp để người bệnh tải hoặc in | /private/giayto/GT2611060001.pdf | Rất nhạy cảm |
| 15 | `tao_luc` | Tạo lúc | TIMESTAMPTZ | ISO 8601 |  | Y | `now()` |  | Thời điểm yêu cầu | 2026-11-06T03:00:00Z | Nội bộ |
| 16 | `cap_nhat_luc` | Cập nhật lúc | TIMESTAMPTZ | ISO 8601 |  | Y | `now()` | Tự cập nhật khi sửa | Thời điểm sửa gần nhất | 2026-11-06T04:30:00Z | Nội bộ |

- Khóa ngoại: `nguoi_benh_id`, `nguoi_yeu_cau_id` → `nguoi_benh(id)` RESTRICT; `lan_kham_id` → `lan_kham(id)` RESTRICT; `nhan_vien_duyet_id` → `nhan_vien(id)` RESTRICT.
- Chỉ mục: `uq_giay_to_benh_vien_ma_giay_to`; `idx_giay_to_benh_vien_nguoi_benh_id`; `idx_giay_to_benh_vien_trang_thai_loai` trên (`trang_thai`, `loai_giay_to`) cho màn hình duyệt; `idx_giay_to_benh_vien_lan_kham_id`.
- Quy tắc: người bệnh chỉ hủy (`DA_HUY`) được khi giấy còn `CHO_DUYET`. Giấy đã `DA_DUYET` hoặc `TU_CHOI` không sửa; muốn đổi thì gửi yêu cầu mới. Chỉ tải hoặc in được khi `DA_DUYET`, qua API có kiểm tra quyền. Hệ thống gửi `thong_bao` loại `GIAY_TO_CAP_NHAT` khi trạng thái đổi; thao tác duyệt, từ chối và cấp tệp ghi `nhat_ky_thao_tac`. Giấy tờ lưu cùng thời hạn với hồ sơ y tế (xem Phần 5).

### 2.9 Nhóm nội dung và hệ thống

#### 2.9.1 Bảng tin_tuc (Tin tức)

Mục đích: nội dung trang công khai (tin tức, giới thiệu bệnh viện, hướng dẫn khám) do Cán bộ quản lý hoặc Quản trị viên soạn.

| STT | Tên cột | Tên hiển thị | Kiểu dữ liệu | Độ dài/định dạng | Khóa | Bắt buộc | Mặc định | Ràng buộc/Quy tắc | Mô tả | Ví dụ | Mức nhạy cảm |
|---|---|---|---|---|---|---|---|---|---|---|---|
| 1 | `id` | Mã bài viết | UUID | 36 ký tự | PK | Y | `gen_random_uuid()` | Duy nhất | Khóa chính nội bộ | a7b8c9d0-0033-4e11-8f12-6a7b8c9d0e1f | Nội bộ |
| 2 | `tieu_de` | Tiêu đề | VARCHAR | 1-200 |  | Y |  | Cắt khoảng trắng hai đầu | Tiêu đề bài viết | Hướng dẫn đặt lịch khám trực tuyến | Công khai |
| 3 | `duong_dan_slug` | Đường dẫn rút gọn | VARCHAR | 1-200, [a-z0-9-] | UK | Y |  | Duy nhất; ứng dụng tự sinh từ tiêu đề, không đổi sau khi xuất bản | Phần đường dẫn của bài trên website | huong-dan-dat-lich-kham-truc-tuyen | Công khai |
| 4 | `loai_noi_dung` | Loại nội dung | ENUM | `loai_noi_dung_tin_tuc`: `TIN_TUC`, `GIOI_THIEU`, `HUONG_DAN` |  | Y | `TIN_TUC` | Xem Phần 3 | Phân loại để hiện đúng mục trang chủ | HUONG_DAN | Công khai |
| 5 | `tom_tat` | Tóm tắt | VARCHAR | 0-500 |  | N | `NULL` |  | Đoạn mô tả ngắn trên danh sách | Các bước đặt lịch khám và nhận phiếu hẹn có mã QR | Công khai |
| 6 | `noi_dung` | Nội dung | TEXT |  |  | Y |  | Định dạng Markdown; lọc mã độc (XSS) khi lưu và khi hiển thị | Nội dung đầy đủ của bài | Bước 1: chọn loại hình khám... | Công khai |
| 7 | `anh_bia_url` | Đường dẫn ảnh bìa | VARCHAR | 0-500, URL |  | N | `NULL` |  | Ảnh đại diện bài viết | /uploads/tin/dat-lich.jpg | Công khai |
| 8 | `trang_thai` | Trạng thái bài viết | ENUM | `trang_thai_tin_tuc`: `NHAP`, `DA_DANG`, `AN` |  | Y | `NHAP` | Chuyển trạng thái theo Phần 3 | Bài đang nháp, đang đăng hay đã ẩn | DA_DANG | Nội bộ |
| 9 | `xuat_ban_luc` | Xuất bản lúc | TIMESTAMPTZ | ISO 8601 |  | N | `NULL` | Điền ở lần đầu chuyển DA_DANG; có thể đặt giờ tương lai để hẹn đăng | Thời điểm bài bắt đầu hiển thị công khai | 2026-10-15T01:00:00Z | Công khai |
| 10 | `tac_gia_id` | Tác giả | UUID | 36 ký tự | FK | Y |  | Nhân viên có vai trò CAN_BO_QUAN_LY hoặc QUAN_TRI_VIEN | Nhân viên soạn bài | 9a8b7c6d-0032-4a3b-8c2d-1e0f9a8b7c6d | Nội bộ |
| 11 | `tao_luc` | Tạo lúc | TIMESTAMPTZ | ISO 8601 |  | Y | `now()` |  | Thời điểm tạo | 2026-10-14T08:00:00Z | Nội bộ |
| 12 | `cap_nhat_luc` | Cập nhật lúc | TIMESTAMPTZ | ISO 8601 |  | Y | `now()` | Tự cập nhật khi sửa | Thời điểm sửa gần nhất | 2026-10-15T01:00:00Z | Nội bộ |
| 13 | `xoa_luc` | Xóa lúc | TIMESTAMPTZ | ISO 8601 |  | N | `NULL` | Có giá trị nghĩa là bài đã bị xóa mềm | Thời điểm xóa mềm | NULL | Nội bộ |

- Khóa ngoại: `tac_gia_id` → `nhan_vien(id)` RESTRICT.
- Chỉ mục: `uq_tin_tuc_duong_dan_slug`; `idx_tin_tuc_loai_trang_thai_xuat_ban` trên (`loai_noi_dung`, `trang_thai`, `xuat_ban_luc` giảm dần) cho trang chủ.
- Quy tắc: khách truy cập chỉ thấy bài có `trang_thai` = `DA_DANG`, `xoa_luc IS NULL` và `xuat_ban_luc` không quá thời điểm hiện tại. Không đưa dữ liệu người bệnh vào nội dung bài. Sửa bài đã đăng vẫn giữ `xuat_ban_luc` ban đầu.

#### 2.9.2 Bảng thong_bao (Thông báo)

Mục đích: thông báo trong ứng dụng gửi người bệnh (hoặc người đặt lịch hộ) khi lịch hẹn, kết quả, hóa đơn, giấy tờ có thay đổi và các lời nhắc.

| STT | Tên cột | Tên hiển thị | Kiểu dữ liệu | Độ dài/định dạng | Khóa | Bắt buộc | Mặc định | Ràng buộc/Quy tắc | Mô tả | Ví dụ | Mức nhạy cảm |
|---|---|---|---|---|---|---|---|---|---|---|---|
| 1 | `id` | Mã thông báo | UUID | 36 ký tự | PK | Y | `gen_random_uuid()` | Duy nhất | Khóa chính nội bộ | b8c9d0e1-0034-4f12-9a13-7b8c9d0e1f2a | Nội bộ |
| 2 | `nguoi_nhan_id` | Người nhận | UUID | 36 ký tự | FK | Y |  | Là người bệnh hoặc người đặt lịch hộ (lich_hen.nguoi_dat_id) | Người bệnh nhận thông báo | 1b2c3d4e-0001-4a5b-8c6d-7e8f9a0b1c2d | Nhạy cảm |
| 3 | `loai_thong_bao` | Loại thông báo | ENUM | `loai_thong_bao`: `LICH_HEN_CAP_NHAT`, `NHAC_LICH_HEN`, `NHAC_TAI_KHAM`, `CO_KET_QUA_CLS`, `HOA_DON_MOI`, `THANH_TOAN_THANH_CONG`, `GIAY_TO_CAP_NHAT` |  | Y |  | Xem Phần 3 | Sự kiện sinh ra thông báo | THANH_TOAN_THANH_CONG | Nội bộ |
| 4 | `tieu_de` | Tiêu đề | VARCHAR | 1-150 |  | Y |  |  | Tiêu đề ngắn | Thanh toán thành công | Nhạy cảm |
| 5 | `noi_dung` | Nội dung | VARCHAR | 1-500 |  | Y |  | Chỉ thông tin hành chính; không ghi chẩn đoán, triệu chứng, tên thuốc, kết quả | Nội dung thông báo | Hóa đơn HD2611050001 đã thanh toán. Vui lòng nhận thuốc tại quầy phát thuốc. | Nhạy cảm |
| 6 | `duong_dan_lien_ket` | Đường dẫn liên kết | VARCHAR | 0-255 |  | N | `NULL` | Đường dẫn nội bộ của ứng dụng, dùng mã nghiệp vụ, không dùng id nội bộ | Nơi mở khi người dùng bấm thông báo | /hoa-don/HD2611050001 | Nội bộ |
| 7 | `da_doc` | Đã đọc | BOOLEAN | true/false |  | Y | `false` |  | Người nhận đã mở thông báo chưa | false | Nội bộ |
| 8 | `doc_luc` | Đọc lúc | TIMESTAMPTZ | ISO 8601 |  | N | `NULL` | Có giá trị khi và chỉ khi da_doc = true | Thời điểm đọc | NULL | Nội bộ |
| 9 | `tao_luc` | Tạo lúc | TIMESTAMPTZ | ISO 8601 |  | Y | `now()` |  | Thời điểm gửi | 2026-11-05T02:20:10Z | Nội bộ |
| 10 | `cap_nhat_luc` | Cập nhật lúc | TIMESTAMPTZ | ISO 8601 |  | Y | `now()` | Tự cập nhật khi sửa | Thời điểm sửa gần nhất | 2026-11-05T02:20:10Z | Nội bộ |

- Khóa ngoại: `nguoi_nhan_id` → `nguoi_benh(id)` RESTRICT.
- Chỉ mục: `idx_thong_bao_nguoi_nhan_id_da_doc_tao_luc` trên (`nguoi_nhan_id`, `da_doc`, `tao_luc` giảm dần) cho chuông thông báo; `idx_thong_bao_loai_thong_bao_tao_luc` (quét nhắc lịch).
- Quy tắc: thông báo do hệ thống sinh, không do người dùng tạo. `NHAC_LICH_HEN` gửi một lần trước ngày hẹn; `NHAC_TAI_KHAM` gửi trước ngày tái khám nếu chưa có lịch tái khám (xem `hen_tai_kham`); ứng dụng kiểm tra trùng trước khi gửi. Người nhận chỉ xem và đánh dấu đã đọc thông báo của mình. Thông báo là dữ liệu tạm thời, được dọn theo Phần 5.

#### 2.9.3 Bảng nhat_ky_thao_tac (Nhật ký thao tác)

Mục đích: nhật ký kiểm toán (audit log) ghi thao tác của nhân viên và các lần truy cập dữ liệu nhạy cảm; chỉ thêm, không sửa, không xóa.

| STT | Tên cột | Tên hiển thị | Kiểu dữ liệu | Độ dài/định dạng | Khóa | Bắt buộc | Mặc định | Ràng buộc/Quy tắc | Mô tả | Ví dụ | Mức nhạy cảm |
|---|---|---|---|---|---|---|---|---|---|---|---|
| 1 | `id` | Mã dòng nhật ký | UUID | 36 ký tự | PK | Y | `gen_random_uuid()` | Duy nhất | Khóa chính nội bộ | c9d0e1f2-0035-4a13-8b14-8c9d0e1f2a3b | Nội bộ |
| 2 | `nhan_vien_id` | Nhân viên thực hiện | UUID | 36 ký tự | FK | N | `NULL` | NULL khi sự kiện do hệ thống hoặc do người bệnh thực hiện, hoặc đăng nhập sai với tên đăng nhập không tồn tại | Nhân viên gây ra thao tác | 9a8b7c6d-0031-4a3b-8c2d-1e0f9a8b7c6d | Nội bộ |
| 3 | `hanh_dong` | Hành động | ENUM | `hanh_dong_nhat_ky`: `TAO`, `SUA`, `XOA_MEM`, `XEM`, `DUYET`, `DANG_NHAP`, `DANG_NHAP_THAT_BAI`, `DANG_XUAT`, `XUAT_DU_LIEU` |  | Y |  | Xem Phần 3 | Loại thao tác | XEM | Nội bộ |
| 4 | `ten_bang` | Tên bảng | VARCHAR | 1-50 |  | N | `NULL` | Bắt buộc trừ DANG_NHAP, DANG_NHAP_THAT_BAI, DANG_XUAT | Bảng dữ liệu bị tác động | don_thuoc | Nội bộ |
| 5 | `ban_ghi_id` | Mã bản ghi | UUID | 36 ký tự |  | N | `NULL` | Không đặt khóa ngoại vì trỏ tới nhiều bảng | id của bản ghi bị tác động | f0a1b2c3-0026-4d0a-9e0b-9f0a1b2c3d4e | Nội bộ |
| 6 | `nguoi_benh_id` | Người bệnh liên quan | UUID | 36 ký tự | FK | N | `NULL` | Điền khi thao tác chạm dữ liệu của một người bệnh | Để tra "ai đã xem hoặc sửa hồ sơ của người bệnh này" | 1b2c3d4e-0001-4a5b-8c6d-7e8f9a0b1c2d | Rất nhạy cảm |
| 7 | `du_lieu_truoc` | Dữ liệu trước | JSONB | Đối tượng JSON |  | N | `NULL` | Chỉ có khi SUA, XOA_MEM; không chứa mật khẩu băm; che CCCD (3 số cuối) | Giá trị cũ của các cột thay đổi | {"trang_thai":"CHUA_THANH_TOAN"} | Rất nhạy cảm |
| 8 | `du_lieu_sau` | Dữ liệu sau | JSONB | Đối tượng JSON |  | N | `NULL` | Chỉ có khi TAO, SUA, DUYET; cùng quy tắc che dữ liệu | Giá trị mới của các cột thay đổi | {"trang_thai":"DA_NHAN"} | Rất nhạy cảm |
| 9 | `mo_ta` | Mô tả | VARCHAR | 0-255 |  | N | `NULL` | Với DANG_NHAP_THAT_BAI ghi lý do và tên đăng nhập đã thử | Diễn giải ngắn của thao tác | Quét QR, đơn hợp lệ, đã phát thuốc | Nội bộ |
| 10 | `dia_chi_ip` | Địa chỉ IP | INET | IPv4 hoặc IPv6 |  | N | `NULL` |  | Địa chỉ mạng của thiết bị thao tác | 203.0.113.25 | Nhạy cảm |
| 11 | `tao_luc` | Tạo lúc | TIMESTAMPTZ | ISO 8601 |  | Y | `now()` |  | Thời điểm thao tác | 2026-11-05T02:35:00Z | Nội bộ |

- Khóa ngoại: `nhan_vien_id` → `nhan_vien(id)` RESTRICT; `nguoi_benh_id` → `nguoi_benh(id)` RESTRICT.
- Chỉ mục: `idx_nhat_ky_thao_tac_nhan_vien_id_tao_luc`; `idx_nhat_ky_thao_tac_nguoi_benh_id_tao_luc`; `idx_nhat_ky_thao_tac_ten_bang_ban_ghi_id`; `idx_nhat_ky_thao_tac_hanh_dong_tao_luc`.
- Quy tắc: tài khoản kết nối của ứng dụng chỉ có quyền INSERT và SELECT trên bảng này; không có UPDATE hay DELETE. Phải ghi: đăng nhập (thành công, thất bại), đăng xuất, mọi thao tác tạo, sửa, xóa mềm, duyệt trên dữ liệu y tế, hóa đơn, giấy tờ, tài khoản; mọi lần nhân viên xem hồ sơ người bệnh, dữ liệu y tế nền, mô tả triệu chứng, kết quả cận lâm sàng; quét QR phát thuốc; đồng ý và thu hồi chia sẻ dữ liệu. Chỉ Quản trị viên và Cán bộ quản lý được xem bảng này. Thời hạn lưu xem Phần 5.

## Phần 3. Danh mục giá trị cố định (ENUM)

Phần này liệt kê toàn bộ giá trị cho phép của mọi cột trạng thái và cột loại ở Phần 2, gồm 29 danh sách. Mỗi danh sách ghi: tên kiểu ENUM (Prisma enum, dùng đúng tên này trong schema), các cột sử dụng, giá trị (UPPER_SNAKE_CASE không dấu), tên hiển thị tiếng Việt có dấu và ý nghĩa. Riêng vai trò (`vai_tro.ma_vai_tro`) và nhóm máu (`thong_tin_y_te_nen.ten_muc`) là danh sách giá trị cố định nhưng lưu bằng VARCHAR, vì vai trò đã có bảng chuẩn `vai_tro` và nhóm máu có ký tự + và −. Với cột có luồng chuyển trạng thái, danh sách kèm bảng chuyển hợp lệ; sơ đồ dạng văn bản của lịch hẹn, đơn thuốc và hóa đơn nằm ở mục 3.7.

### 3.1 Nhóm người dùng và người bệnh

#### 3.1.1 Vai trò (vai_tro.ma_vai_tro)

Cột dùng: `vai_tro.ma_vai_tro` (VARCHAR 1-30). Đúng 9 giá trị, nạp bằng seed; quyền chi tiết của từng vai trò ở Phần 6.

| Giá trị | Tên hiển thị | Ý nghĩa |
|---|---|---|
| `KHACH` | Khách truy cập | Xem trang công khai (tin tức, giới thiệu, bác sĩ, hướng dẫn); không đăng nhập, không có dòng ở `tai_khoan` (`la_vai_tro_nhan_vien` = false). |
| `NGUOI_BENH` | Người bệnh | Đăng nhập bằng CCCD (mô phỏng VNeID); đặt lịch cho mình và thân nhân, xem hồ sơ, hóa đơn, đơn thuốc, yêu cầu giấy tờ; không có dòng ở `tai_khoan`. |
| `Y_TA` | Y tá / Nhân viên tiếp nhận | Duyệt lịch hẹn (xác nhận, đổi khoa, từ chối, chuyển lên bác sĩ), tiếp nhận, tạo hồ sơ mới, cấp số thứ tự, in phiếu hướng dẫn, duyệt giấy tờ. |
| `BAC_SI` | Bác sĩ | Xem xét lịch chuyển lên, khám, chẩn đoán, chỉ định cận lâm sàng, kê đơn, hẹn tái khám, cấp và duyệt giấy tờ; có thêm một dòng ở `bac_si`. |
| `KY_THUAT_VIEN` | Kỹ thuật viên cận lâm sàng | Nhận chỉ định cận lâm sàng, thực hiện và nhập kết quả. |
| `THU_NGAN` | Nhân viên thu ngân | Lập và thu hóa đơn tại quầy (mô phỏng), đối soát ca thu. |
| `PHAT_THUOC` | Nhân viên phát thuốc | Quét QR đơn thuốc, xác thực và ghi nhận phát thuốc. |
| `CAN_BO_QUAN_LY` | Cán bộ quản lý | Xem thống kê báo cáo, quản lý lịch làm việc và tin tức, xem nhật ký thao tác. |
| `QUAN_TRI_VIEN` | Quản trị viên | Cấp và vô hiệu tài khoản, gán vai trò, quản lý danh mục và nội dung, xem nhật ký thao tác. |

#### 3.1.2 Trạng thái tài khoản (trang_thai_tai_khoan)

Cột dùng: `tai_khoan.trang_thai`. Mặc định `HOAT_DONG`.

| Giá trị | Tên hiển thị | Ý nghĩa |
|---|---|---|
| `HOAT_DONG` | Hoạt động | Đăng nhập được. |
| `TAM_KHOA` | Tạm khóa | Khóa tạm do đăng nhập sai quá số lần quy định hoặc do quản trị viên khóa; không đăng nhập được. |
| `VO_HIEU` | Vô hiệu | Nhân viên nghỉ việc hoặc tài khoản bị thu hồi; không đăng nhập lại, vẫn giữ để nhật ký và hồ sơ cũ tham chiếu được. |

| Từ | Sang | Điều kiện / người thực hiện |
|---|---|---|
| `HOAT_DONG` | `TAM_KHOA` | Hệ thống khi `so_lan_dang_nhap_sai` đạt ngưỡng (điền `khoa_den_luc`), hoặc quản trị viên. |
| `TAM_KHOA` | `HOAT_DONG` | Hệ thống khi quá `khoa_den_luc`, hoặc quản trị viên mở khóa. |
| `HOAT_DONG`, `TAM_KHOA` | `VO_HIEU` | Quản trị viên, kèm xóa mềm `nhan_vien` khi nghỉ việc. |
| `VO_HIEU` | `HOAT_DONG` | Chỉ quản trị viên kích hoạt lại, phải ghi `nhat_ky_thao_tac`. |

#### 3.1.3 Giới tính (gioi_tinh)

Cột dùng: `nguoi_benh.gioi_tinh`.

| Giá trị | Tên hiển thị | Ý nghĩa |
|---|---|---|
| `NAM` | Nam | Giới tính nam. |
| `NU` | Nữ | Giới tính nữ. |
| `KHAC` | Khác | Giới tính khác hoặc không muốn nêu. |

#### 3.1.4 Nhóm máu (danh sách giá trị của thong_tin_y_te_nen.ten_muc khi loai_thong_tin = NHOM_MAU)

Lưu bằng VARCHAR theo đúng ký hiệu dưới đây (không ENUM vì có ký tự + và −). Người bệnh chưa biết nhóm máu thì không có dòng NHOM_MAU, không dùng giá trị “không xác định”.

| Giá trị | Tên hiển thị | Ý nghĩa |
|---|---|---|
| `A+` | Nhóm máu A, Rh dương | Hệ ABO nhóm A, yếu tố Rh dương. |
| `A-` | Nhóm máu A, Rh âm | Hệ ABO nhóm A, yếu tố Rh âm. |
| `B+` | Nhóm máu B, Rh dương | Hệ ABO nhóm B, yếu tố Rh dương. |
| `B-` | Nhóm máu B, Rh âm | Hệ ABO nhóm B, yếu tố Rh âm. |
| `AB+` | Nhóm máu AB, Rh dương | Hệ ABO nhóm AB, yếu tố Rh dương. |
| `AB-` | Nhóm máu AB, Rh âm | Hệ ABO nhóm AB, yếu tố Rh âm. |
| `O+` | Nhóm máu O, Rh dương | Hệ ABO nhóm O, yếu tố Rh dương. |
| `O-` | Nhóm máu O, Rh âm | Hệ ABO nhóm O, yếu tố Rh âm. |

#### 3.1.5 Quan hệ thân nhân (quan_he_than_nhan)

Cột dùng: `than_nhan.quan_he`. Đọc là “`nguoi_than_id` là <giá trị> của `nguoi_benh_id`”; `nguoi_benh_id` là người quản lý hồ sơ. Ví dụ: cha đặt lịch cho con thì `nguoi_benh_id` = cha, `nguoi_than_id` = con, `quan_he` = `CON`.

| Giá trị | Tên hiển thị | Ý nghĩa |
|---|---|---|
| `CHA_ME` | Cha / Mẹ | Thân nhân là cha hoặc mẹ của người quản lý. |
| `CON` | Con | Thân nhân là con của người quản lý. |
| `VO_CHONG` | Vợ / Chồng | Thân nhân là vợ hoặc chồng. |
| `ANH_CHI_EM` | Anh / Chị / Em | Thân nhân là anh, chị, em ruột. |
| `ONG_BA` | Ông / Bà | Thân nhân là ông hoặc bà. |
| `CHAU` | Cháu | Thân nhân là cháu của người quản lý. |
| `NGUOI_GIAM_HO` | Người giám hộ | Thân nhân do người quản lý giám hộ (không thuộc các quan hệ trên). |
| `KHAC` | Khác | Quan hệ khác; ghi rõ hơn ở nơi khác nếu cần, hệ thống không lưu thêm. |

#### 3.1.6 Nguồn tạo hồ sơ người bệnh (nguon_tao_ho_so)

Cột dùng: `nguoi_benh.nguon_tao`. Mặc định `DANG_KY_TRUC_TUYEN`.

| Giá trị | Tên hiển thị | Ý nghĩa |
|---|---|---|
| `DANG_KY_TRUC_TUYEN` | Đăng ký trực tuyến | Người bệnh tự tạo hồ sơ trên website khi đăng nhập lần đầu bằng CCCD. |
| `TIEP_NHAN_TAI_QUAY` | Tiếp nhận tại quầy | Y tá tạo hồ sơ mới khi tiếp nhận (quét QR CCCD không tìm thấy hồ sơ). |

#### 3.1.7 Loại thông tin y tế nền (loai_thong_tin_y_te_nen)

Cột dùng: `thong_tin_y_te_nen.loai_thong_tin`.

| Giá trị | Tên hiển thị | Ý nghĩa |
|---|---|---|
| `NHOM_MAU` | Nhóm máu | Mỗi người tối đa một dòng; `ten_muc` thuộc danh sách ở 3.1.4. |
| `TIEN_SU_PHAU_THUAT` | Tiền sử phẫu thuật / tiểu phẫu | `ten_muc` là tên phẫu thuật hoặc tiểu phẫu; `nam_thuc_hien` là năm thực hiện. |
| `DI_UNG_THUOC` | Dị ứng thuốc | `ten_muc` là hoạt chất hoặc tên thuốc gây dị ứng; dùng `muc_do_di_ung` và `ghi_chu`. |

#### 3.1.8 Mức độ dị ứng (muc_do_di_ung)

Cột dùng: `thong_tin_y_te_nen.muc_do_di_ung`, chỉ có giá trị khi `loai_thong_tin` = `DI_UNG_THUOC`, các trường hợp khác để NULL.

| Giá trị | Tên hiển thị | Ý nghĩa |
|---|---|---|
| `NHE` | Nhẹ | Phản ứng nhẹ (ví dụ nổi mẩn nhẹ, ngứa). |
| `TRUNG_BINH` | Trung bình | Phản ứng đáng kể, cần xử trí nhưng không đe dọa tính mạng. |
| `NANG` | Nặng | Phản ứng nặng (ví dụ khó thở, sốc phản vệ); khi kê đơn, cảnh báo hiển thị nổi bật. |

#### 3.1.9 Nguồn dữ liệu y tế nền (nguon_du_lieu_y_te_nen)

Cột dùng: `thong_tin_y_te_nen.nguon_du_lieu`. Mặc định `NOI_BO`.

| Giá trị | Tên hiển thị | Ý nghĩa |
|---|---|---|
| `VNEID` | Sổ sức khỏe điện tử VNeID (mô phỏng) | Lấy từ VNeID, chỉ khi người bệnh đồng ý đúng phạm vi và chưa thu hồi; phải có `dong_y_id`. |
| `NOI_BO` | Nội bộ bệnh viện | Do nhân viên bệnh viện nhập hoặc người bệnh khai tại bệnh viện; không cần bản ghi đồng ý chia sẻ. |

#### 3.1.10 Phạm vi chia sẻ dữ liệu (pham_vi_chia_se)

Cột dùng: `dong_y_chia_se_du_lieu.pham_vi_du_lieu`. Mỗi dòng đồng ý chỉ cho một phạm vi; giá trị trùng với 3.1.7 vì mỗi phạm vi tương ứng một loại thông tin y tế nền.

| Giá trị | Tên hiển thị | Ý nghĩa |
|---|---|---|
| `NHOM_MAU` | Nhóm máu | Đồng ý cho lấy nhóm máu từ VNeID. |
| `TIEN_SU_PHAU_THUAT` | Tiền sử phẫu thuật / tiểu phẫu | Đồng ý cho lấy tiền sử phẫu thuật, tiểu phẫu từ VNeID. |
| `DI_UNG_THUOC` | Dị ứng thuốc | Đồng ý cho lấy thông tin dị ứng thuốc từ VNeID. |

### 3.2 Nhóm danh mục

#### 3.2.1 Loại khoa (loai_khoa)

Cột dùng: `khoa.loai_khoa`. Mặc định `LAM_SANG`.

| Giá trị | Tên hiển thị | Ý nghĩa |
|---|---|---|
| `LAM_SANG` | Lâm sàng | Khoa khám bệnh; người bệnh chọn được khi đặt lịch, có bác sĩ và phòng khám. |
| `CAN_LAM_SANG` | Cận lâm sàng | Khoa xét nghiệm, chẩn đoán hình ảnh; chỉ chứa phòng thực hiện cận lâm sàng, không hiện khi đặt lịch. |

#### 3.2.2 Loại hình khám (loai_hinh_kham)

Cột dùng: `dich_vu_kham.loai_hinh_kham`, `lich_hen.loai_hinh_kham`.

| Giá trị | Tên hiển thị | Ý nghĩa |
|---|---|---|
| `BHYT` | Khám BHYT | Dùng thẻ bảo hiểm y tế; chỉ chọn được khi người khám có thẻ còn hiệu lực vào `ngay_hen`; hóa đơn tách phần BHYT chi trả theo `muc_huong`. |
| `DICH_VU` | Khám dịch vụ | Người bệnh tự chi trả theo đơn giá dịch vụ khám loại này, không áp mức hưởng BHYT. |
| `THEO_YEU_CAU` | Khám theo yêu cầu | Người bệnh tự chi trả theo đơn giá dịch vụ khám loại theo yêu cầu. |

#### 3.2.3 Nhóm cận lâm sàng (nhom_can_lam_sang)

Cột dùng: `danh_muc_can_lam_sang.nhom_cls`.

| Giá trị | Tên hiển thị | Ý nghĩa |
|---|---|---|
| `XET_NGHIEM` | Xét nghiệm | Xét nghiệm máu, nước tiểu, sinh hóa, vi sinh... |
| `CHAN_DOAN_HINH_ANH` | Chẩn đoán hình ảnh | X-quang, siêu âm, CT, MRI... |
| `THAM_DO_CHUC_NANG` | Thăm dò chức năng | Điện tim, đo chức năng hô hấp, nội soi... |

### 3.3 Nhóm đặt lịch và tiếp nhận

#### 3.3.1 Trạng thái lịch hẹn (trang_thai_lich_hen)

Cột dùng: `lich_hen.trang_thai` (mặc định `CHO_DUYET`), `lich_su_trang_thai_lich_hen.trang_thai_cu` và `trang_thai_moi`. Duyệt hai tầng: y tá duyệt chính, ca nặng hoặc khó xác định khoa chuyển lên bác sĩ.

| Giá trị | Tên hiển thị | Ý nghĩa |
|---|---|---|
| `CHO_DUYET` | Chờ duyệt | Người bệnh vừa đặt lịch, chờ y tá xem xét. Trạng thái khởi tạo. |
| `DA_XAC_NHAN` | Đã xác nhận | Y tá hoặc bác sĩ xác nhận, giữ nguyên khoa; người bệnh nhận phiếu hẹn có mã và QR. |
| `DA_DOI_KHOA` | Đã đổi khoa | Y tá hoặc bác sĩ chuyển sang khoa khác và xác nhận lịch; `khoa_id` được cập nhật, khoa cũ và khoa mới ghi ở lịch sử. |
| `CHO_BAC_SI_XEM_XET` | Chờ bác sĩ xem xét | Y tá chuyển lên bác sĩ vì ca nặng hoặc không xác định được khoa. |
| `TU_CHOI` | Từ chối | Lịch không được nhận (có lý do); trạng thái cuối. |
| `DA_HUY` | Đã hủy | Người đặt hủy lịch, hoặc nhân viên hủy khi lần khám bị hủy; trạng thái cuối. |
| `DA_DEN_KHAM` | Đã đến khám | Người bệnh đã được tiếp nhận tại quầy và được cấp số thứ tự. |
| `HOAN_THANH` | Hoàn thành | Lần khám của lịch đã hoàn thành; trạng thái cuối. |
| `VANG_MAT` | Vắng mặt | Đã qua ngày hẹn mà người bệnh không đến; trạng thái cuối. |

Bảng chuyển trạng thái hợp lệ (mỗi bước chuyển ghi một dòng ở `lich_su_trang_thai_lich_hen`; cột lý do bắt buộc ở các bước có ghi “có lý do”):

| Từ | Sang | Ai thực hiện và điều kiện |
|---|---|---|
| (tạo mới) | `CHO_DUYET` | Người bệnh hoặc người đặt hộ; dòng lịch sử đầu có `trang_thai_cu` = NULL. |
| `CHO_DUYET` | `DA_XAC_NHAN` | Y tá duyệt, giữ nguyên khoa. |
| `CHO_DUYET` | `DA_DOI_KHOA` | Y tá đổi khoa (có lý do). |
| `CHO_DUYET` | `CHO_BAC_SI_XEM_XET` | Y tá chuyển lên bác sĩ khi `co_dau_hieu_nang` = true hoặc không xác định được khoa (có lý do). |
| `CHO_DUYET` | `TU_CHOI` | Y tá từ chối (có lý do). |
| `CHO_DUYET` | `DA_HUY` | Người bệnh hoặc người đặt tự hủy (có lý do). |
| `CHO_BAC_SI_XEM_XET` | `DA_XAC_NHAN` | Bác sĩ xác nhận giữ nguyên khoa. |
| `CHO_BAC_SI_XEM_XET` | `DA_DOI_KHOA` | Bác sĩ chuyển sang khoa phù hợp (có lý do). |
| `CHO_BAC_SI_XEM_XET` | `TU_CHOI` | Bác sĩ từ chối (có lý do). |
| `DA_XAC_NHAN` | `DA_DOI_KHOA` | Y tá hoặc bác sĩ đổi khoa trước khi người bệnh đến (có lý do). |
| `DA_DOI_KHOA` | `DA_DOI_KHOA` | Đổi khoa thêm lần nữa trước khi người bệnh đến (có lý do); vẫn ghi thêm một dòng lịch sử. |
| `DA_XAC_NHAN`, `DA_DOI_KHOA` | `DA_HUY` | Người bệnh hoặc người đặt hủy (có lý do). |
| `DA_XAC_NHAN`, `DA_DOI_KHOA` | `DA_DEN_KHAM` | Y tá tiếp nhận và cấp số thứ tự ở `so_thu_tu`. |
| `DA_XAC_NHAN`, `DA_DOI_KHOA` | `VANG_MAT` | Hệ thống hoặc y tá khi qua ngày hẹn mà chưa tiếp nhận (hai cột người thực hiện cùng NULL nếu hệ thống tự đổi). |
| `DA_DEN_KHAM` | `HOAN_THANH` | Hệ thống khi `lan_kham.trang_thai` chuyển `HOAN_THANH`. |
| `DA_DEN_KHAM` | `DA_HUY` | Nhân viên khi `lan_kham` bị hủy, ví dụ nhập nhầm hoặc người bệnh bỏ về (có lý do). |

Trạng thái cuối (không chuyển tiếp): `TU_CHOI`, `DA_HUY`, `VANG_MAT`, `HOAN_THANH`. Người bệnh chỉ tự hủy được khi lịch ở `CHO_DUYET`, `DA_XAC_NHAN` hoặc `DA_DOI_KHOA`. Ràng buộc chống trùng lịch (`uq_lich_hen_khong_trung`) bỏ qua các lịch `TU_CHOI`, `DA_HUY`, `VANG_MAT`.

#### 3.3.2 Phương thức gợi ý chuyên khoa (phuong_thuc_goi_y)

Cột dùng: `goi_y_chuyen_khoa.phuong_thuc_goi_y`. Mặc định `TU_KHOA`. Dùng để thống kê tỷ lệ chấp nhận gợi ý theo từng phương thức.

| Giá trị | Tên hiển thị | Ý nghĩa |
|---|---|---|
| `TU_KHOA` | Từ khóa (rule-based) | Cộng điểm các từ khóa ở `tu_khoa_trieu_chung` khớp với mô tả triệu chứng; là phương thức chạy thật của hệ thống. |
| `LLM` | Mô hình ngôn ngữ lớn (LLM) | Gợi ý bằng mô hình ngôn ngữ; chỉ dùng khi trình diễn với dữ liệu giả, không đưa dữ liệu bệnh nhân thật vào. |

### 3.4 Nhóm khám bệnh

#### 3.4.1 Trạng thái lần khám (trang_thai_lan_kham)

Cột dùng: `lan_kham.trang_thai`. Mặc định `DANG_KHAM`.

| Giá trị | Tên hiển thị | Ý nghĩa |
|---|---|---|
| `DANG_KHAM` | Đang khám | Bác sĩ đang khám, chẩn đoán, kê đơn. Trạng thái khởi tạo khi tạo lần khám từ lịch `DA_DEN_KHAM`. |
| `CHO_KET_QUA_CLS` | Chờ kết quả cận lâm sàng | Còn ít nhất một chỉ định cận lâm sàng ở `DA_CHI_DINH` hoặc `DANG_THUC_HIEN`. |
| `HOAN_THANH` | Hoàn thành | Bác sĩ kết thúc khám; nội dung bị khóa, chỉ bổ sung qua ghi chú có dấu vết ở nhật ký. |
| `DA_HUY` | Đã hủy | Lần khám bị hủy (nhập nhầm, người bệnh bỏ về); không còn hiển thị trong hồ sơ “Bác sĩ của tôi”. |

| Từ | Sang | Điều kiện |
|---|---|---|
| `DANG_KHAM` | `CHO_KET_QUA_CLS` | Bác sĩ chỉ định cận lâm sàng. |
| `CHO_KET_QUA_CLS` | `DANG_KHAM` | Mọi chỉ định đã có kết quả hoặc bị hủy; bác sĩ quay lại để chẩn đoán, kê đơn. |
| `DANG_KHAM` | `HOAN_THANH` | Bác sĩ của lần khám kết thúc; không còn chỉ định chưa có kết quả. |
| `DANG_KHAM`, `CHO_KET_QUA_CLS` | `DA_HUY` | Bác sĩ hoặc y tá hủy; kéo theo lịch hẹn chuyển `DA_HUY` (xem 3.3.1) và ghi nhật ký. |

#### 3.4.2 Loại chẩn đoán (loai_chan_doan)

Cột dùng: `chan_doan.loai_chan_doan`. Mặc định `SO_BO`. Mỗi lần khám có tối đa một dòng `CHINH`.

| Giá trị | Tên hiển thị | Ý nghĩa |
|---|---|---|
| `SO_BO` | Sơ bộ | Chẩn đoán ban đầu khi chưa có kết quả cận lâm sàng. |
| `CHINH` | Chính | Chẩn đoán xác định, nguyên nhân chính của lần khám; tối đa một dòng mỗi lần khám. |
| `KEM_THEO` | Kèm theo | Bệnh hoặc tình trạng đi kèm; có thể nhiều dòng. |

#### 3.4.3 Trạng thái chỉ định cận lâm sàng (trang_thai_chi_dinh_cls)

Cột dùng: `chi_dinh_can_lam_sang.trang_thai`. Mặc định `DA_CHI_DINH`.

| Giá trị | Tên hiển thị | Ý nghĩa |
|---|---|---|
| `DA_CHI_DINH` | Đã chỉ định | Bác sĩ vừa chỉ định, kỹ thuật viên chưa nhận. |
| `DANG_THUC_HIEN` | Đang thực hiện | Kỹ thuật viên đã nhận và đang thực hiện (điền `ky_thuat_vien_id`). |
| `DA_CO_KET_QUA` | Đã có kết quả | Đã có dòng `ket_qua_can_lam_sang`; không hủy được nữa. |
| `DA_HUY` | Đã hủy | Bác sĩ hủy chỉ định khi chưa có kết quả; khoản tương ứng không tính phí. |

| Từ | Sang | Ai thực hiện |
|---|---|---|
| `DA_CHI_DINH` | `DANG_THUC_HIEN` | Kỹ thuật viên nhận chỉ định từ hàng chờ. |
| `DANG_THUC_HIEN` | `DA_CO_KET_QUA` | Kỹ thuật viên nhập kết quả (cùng giao dịch với việc thêm `ket_qua_can_lam_sang`). |
| `DA_CHI_DINH`, `DANG_THUC_HIEN` | `DA_HUY` | Bác sĩ của lần khám. |

### 3.5 Nhóm đơn thuốc và tài chính

#### 3.5.1 Trạng thái đơn thuốc (trang_thai_don_thuoc)

Cột dùng: `don_thuoc.trang_thai`. Mặc định `CHUA_THANH_TOAN`.

| Giá trị | Tên hiển thị | Ý nghĩa |
|---|---|---|
| `CHUA_THANH_TOAN` | Chưa thanh toán | Đơn vừa kê, hóa đơn của lần khám chưa thanh toán; còn sửa được dòng thuốc và hủy được. Chưa phát được thuốc. |
| `DA_THANH_TOAN_CHO_NHAN` | Đã thanh toán, chờ nhận | Hóa đơn đã thanh toán; đơn hợp lệ để quét QR và phát thuốc trong hạn `han_nhan_den`. |
| `DA_NHAN` | Đã nhận | Đã có dòng `phat_thuoc`; trạng thái cuối, không phát lần hai. |
| `DA_HUY` | Đã hủy | Bác sĩ hủy đơn khi còn `CHUA_THANH_TOAN`; trạng thái cuối. |

| Từ | Sang | Điều kiện / người thực hiện |
|---|---|---|
| `CHUA_THANH_TOAN` | `DA_THANH_TOAN_CHO_NHAN` | Hệ thống, cùng giao dịch khi `hoa_don` chuyển `DA_THANH_TOAN` (kể cả khi `tong_nguoi_benh_tra` = 0). |
| `DA_THANH_TOAN_CHO_NHAN` | `DA_NHAN` | Nhân viên phát thuốc quét QR; đủ 4 điều kiện xác thực (mã QR khớp, đã thanh toán, trong hạn, chưa phát). |
| `CHUA_THANH_TOAN` | `DA_HUY` | Bác sĩ của lần khám; các khoản thuốc trong hóa đơn chưa thanh toán được bỏ và tính lại tổng. |

Quá hạn `han_nhan_den` không có trạng thái riêng: đơn vẫn ở `DA_THANH_TOAN_CHO_NHAN` nhưng bị từ chối khi quét (báo “quá hạn”).

#### 3.5.2 Trạng thái hóa đơn (trang_thai_hoa_don)

Cột dùng: `hoa_don.trang_thai`. Mặc định `CHUA_THANH_TOAN`.

| Giá trị | Tên hiển thị | Ý nghĩa |
|---|---|---|
| `CHUA_THANH_TOAN` | Chưa thanh toán | Hóa đơn đang mở; còn thêm bớt khoản theo chỉ định và đơn thuốc. |
| `DA_THANH_TOAN` | Đã thanh toán | Đã có thanh toán `THANH_CONG` hoặc phần người bệnh trả bằng 0; hóa đơn bị khóa. |
| `DA_HUY` | Đã hủy | Hóa đơn bị hủy khi chưa thanh toán (có `ly_do_huy`); không hoàn tiền trong phạm vi đề tài. |

| Từ | Sang | Điều kiện / người thực hiện |
|---|---|---|
| `CHUA_THANH_TOAN` | `DA_THANH_TOAN` | Khi có `thanh_toan` chuyển `THANH_CONG`, hoặc tự động nếu `tong_nguoi_benh_tra` = 0. |
| `CHUA_THANH_TOAN` | `DA_HUY` | Thu ngân hoặc hệ thống (ví dụ lần khám bị hủy); bắt buộc `ly_do_huy`. |

#### 3.5.3 Loại khoản hóa đơn (loai_khoan_hoa_don)

Cột dùng: `chi_tiet_hoa_don.loai_khoan`; quyết định cột nguồn nào có giá trị (đúng một trong ba).

| Giá trị | Tên hiển thị | Ý nghĩa |
|---|---|---|
| `KHAM` | Khám | Khoản tiền khám; cột nguồn `dich_vu_kham_id`. |
| `CAN_LAM_SANG` | Cận lâm sàng | Khoản xét nghiệm, chẩn đoán hình ảnh; cột nguồn `chi_dinh_can_lam_sang_id`. |
| `THUOC` | Thuốc | Khoản thuốc; cột nguồn `chi_tiet_don_thuoc_id`. |

#### 3.5.4 Phương thức thanh toán (phuong_thuc_thanh_toan)

Cột dùng: `thanh_toan.phuong_thuc`. Mọi phương thức đều mô phỏng, không kết nối cổng thanh toán và không lưu số thẻ hay tài khoản thật.

| Giá trị | Tên hiển thị | Ý nghĩa |
|---|---|---|
| `TIEN_MAT` | Tiền mặt | Thu ngân thu tại quầy (mô phỏng); chỉ dùng khi có `nhan_vien_thu_id`. |
| `CHUYEN_KHOAN` | Chuyển khoản | Người bệnh chuyển khoản ngân hàng (mô phỏng). |
| `THE` | Thẻ ngân hàng | Thanh toán bằng thẻ (mô phỏng). |
| `VI_DIEN_TU` | Ví điện tử | Thanh toán bằng ví điện tử (mô phỏng). |

#### 3.5.5 Trạng thái thanh toán (trang_thai_thanh_toan)

Cột dùng: `thanh_toan.trang_thai`. Mặc định `CHO_XU_LY`.

| Giá trị | Tên hiển thị | Ý nghĩa |
|---|---|---|
| `CHO_XU_LY` | Chờ xử lý | Giao dịch đã tạo, chưa có kết quả. |
| `THANH_CONG` | Thành công | Giao dịch thành công; mỗi hóa đơn chỉ có một dòng `THANH_CONG`. Trạng thái cuối. |
| `THAT_BAI` | Thất bại | Giao dịch không thành công (ghi `ly_do_that_bai`), giữ để truy vết; người bệnh được thử lại bằng dòng mới. Trạng thái cuối. |

| Từ | Sang | Hệ quả |
|---|---|---|
| `CHO_XU_LY` | `THANH_CONG` | Điền `thanh_toan_luc`; `hoa_don` sang `DA_THANH_TOAN`; `don_thuoc` đang `CHUA_THANH_TOAN` sang `DA_THANH_TOAN_CHO_NHAN`; tạo `thong_bao` `THANH_TOAN_THANH_CONG`; cùng một giao dịch. |
| `CHO_XU_LY` | `THAT_BAI` | Ghi `ly_do_that_bai`; hóa đơn giữ nguyên `CHUA_THANH_TOAN`. |

### 3.6 Nhóm giấy tờ, nội dung và hệ thống

#### 3.6.1 Loại giấy tờ (loai_giay_to)

Cột dùng: `giay_to_benh_vien.loai_giay_to`. Nội dung giấy tờ là dữ liệu y tế nên mức nhạy cảm của cột là Rất nhạy cảm.

| Giá trị | Tên hiển thị | Ý nghĩa |
|---|---|---|
| `CHUYEN_TUYEN` | Giấy chuyển tuyến | Cần `noi_chuyen_den`; người bệnh yêu cầu hoặc bác sĩ chủ động cấp trong lúc khám. |
| `XAC_NHAN_KHAM_BENH` | Giấy xác nhận khám bệnh | Xác nhận người bệnh đã khám tại bệnh viện vào ngày nhất định. |
| `NGHI_OM` | Giấy nghỉ ốm | Xác nhận thời gian cần nghỉ do bệnh, căn cứ một lần khám. |
| `TOM_TAT_KHAM_BENH` | Tóm tắt khám bệnh | Tóm tắt chẩn đoán và điều trị của một lần khám. |

#### 3.6.2 Trạng thái giấy tờ (trang_thai_giay_to)

Cột dùng: `giay_to_benh_vien.trang_thai`. Mặc định `CHO_DUYET`; bác sĩ chủ động cấp thì tạo thẳng `DA_DUYET`.

| Giá trị | Tên hiển thị | Ý nghĩa |
|---|---|---|
| `CHO_DUYET` | Chờ duyệt | Người bệnh vừa gửi yêu cầu, chờ nhân viên duyệt. |
| `DA_DUYET` | Đã duyệt | Đã sinh tệp PDF; người bệnh tải hoặc in qua API có kiểm tra quyền. Trạng thái cuối. |
| `TU_CHOI` | Từ chối | Nhân viên từ chối kèm `ly_do_tu_choi`; muốn đổi thì gửi yêu cầu mới. Trạng thái cuối. |
| `DA_HUY` | Đã hủy | Người bệnh rút yêu cầu khi còn `CHO_DUYET`. Trạng thái cuối. |

| Từ | Sang | Ai thực hiện |
|---|---|---|
| (tạo mới) | `CHO_DUYET` | Người bệnh gửi yêu cầu. |
| (tạo mới) | `DA_DUYET` | Bác sĩ chủ động cấp (ví dụ giấy chuyển tuyến) trong lúc khám. |
| `CHO_DUYET` | `DA_DUYET` | Nhân viên có quyền duyệt (Y tá hoặc Bác sĩ, xem Phần 6); điền `nhan_vien_duyet_id`, `duyet_luc`, `tep_giay_to_url`. |
| `CHO_DUYET` | `TU_CHOI` | Nhân viên có quyền duyệt; bắt buộc `ly_do_tu_choi`. |
| `CHO_DUYET` | `DA_HUY` | Người bệnh yêu cầu. |

#### 3.6.3 Loại nội dung tin tức (loai_noi_dung_tin_tuc)

Cột dùng: `tin_tuc.loai_noi_dung`. Mặc định `TIN_TUC`.

| Giá trị | Tên hiển thị | Ý nghĩa |
|---|---|---|
| `TIN_TUC` | Tin tức | Bài tin, thông báo của bệnh viện. |
| `GIOI_THIEU` | Giới thiệu | Nội dung giới thiệu bệnh viện, khoa, dịch vụ. |
| `HUONG_DAN` | Hướng dẫn | Hướng dẫn đặt lịch, quy trình khám, giấy tờ cần mang. |

#### 3.6.4 Trạng thái tin tức (trang_thai_tin_tuc)

Cột dùng: `tin_tuc.trang_thai`. Mặc định `NHAP`.

| Giá trị | Tên hiển thị | Ý nghĩa |
|---|---|---|
| `NHAP` | Nháp | Đang soạn; chỉ nhân viên có quyền soạn thấy. |
| `DA_DANG` | Đã đăng | Hiện trên trang công khai khi `xuat_ban_luc` không quá thời điểm hiện tại và `xoa_luc` IS NULL. |
| `AN` | Ẩn | Gỡ khỏi trang công khai nhưng vẫn lưu để đăng lại. |

| Từ | Sang | Ghi chú |
|---|---|---|
| `NHAP` | `DA_DANG` | Điền `xuat_ban_luc` lần đầu; sửa bài đã đăng vẫn giữ giá trị này. |
| `DA_DANG` | `AN` | Gỡ bài khỏi trang công khai. |
| `AN` | `DA_DANG` | Đăng lại, giữ `xuat_ban_luc` ban đầu. |
| `AN` | `NHAP` | Đưa về nháp để soạn lại. |

#### 3.6.5 Loại thông báo (loai_thong_bao)

Cột dùng: `thong_bao.loai_thong_bao`. Thông báo do hệ thống sinh.

| Giá trị | Tên hiển thị | Ý nghĩa |
|---|---|---|
| `LICH_HEN_CAP_NHAT` | Lịch hẹn cập nhật | Lịch hẹn đổi trạng thái hoặc đổi khoa (xác nhận, đổi khoa, từ chối, hủy). |
| `NHAC_LICH_HEN` | Nhắc lịch hẹn | Nhắc một lần trước ngày hẹn khám. |
| `NHAC_TAI_KHAM` | Nhắc tái khám | Nhắc trước ngày tái khám nếu người bệnh chưa đặt lịch tái khám. |
| `CO_KET_QUA_CLS` | Có kết quả cận lâm sàng | Chỉ định cận lâm sàng đã có kết quả. |
| `HOA_DON_MOI` | Hóa đơn mới | Hóa đơn của lần khám được lập. |
| `THANH_TOAN_THANH_CONG` | Thanh toán thành công | Thanh toán hóa đơn thành công; đơn thuốc sẵn sàng nhận. |
| `GIAY_TO_CAP_NHAT` | Giấy tờ cập nhật | Yêu cầu giấy tờ được duyệt hoặc bị từ chối. |

#### 3.6.6 Hành động nhật ký (hanh_dong_nhat_ky)

Cột dùng: `nhat_ky_thao_tac.hanh_dong`. Danh sách các thao tác bắt buộc ghi nhật ký nêu ở quy tắc của bảng `nhat_ky_thao_tac` (mục 2.9.3).

| Giá trị | Tên hiển thị | Ý nghĩa |
|---|---|---|
| `TAO` | Tạo | Tạo bản ghi mới ở bảng dữ liệu y tế, hóa đơn, giấy tờ, tài khoản... |
| `SUA` | Sửa | Sửa bản ghi; ghi `du_lieu_truoc` và `du_lieu_sau`. |
| `XOA_MEM` | Xóa mềm | Đặt `xoa_luc` hoặc hủy bản ghi; ghi `du_lieu_truoc`. |
| `XEM` | Xem | Nhân viên xem dữ liệu nhạy cảm (hồ sơ, dữ liệu y tế nền, mô tả triệu chứng, kết quả cận lâm sàng) và quét QR phát thuốc. |
| `DUYET` | Duyệt | Duyệt, từ chối lịch hẹn hoặc giấy tờ. |
| `DANG_NHAP` | Đăng nhập | Đăng nhập thành công. |
| `DANG_NHAP_THAT_BAI` | Đăng nhập thất bại | Đăng nhập sai; `mo_ta` ghi lý do và tên đăng nhập đã thử. |
| `DANG_XUAT` | Đăng xuất | Đăng xuất. |
| `XUAT_DU_LIEU` | Xuất dữ liệu | Xuất báo cáo hoặc dữ liệu ra tệp. |

### 3.7 Sơ đồ chuyển trạng thái (dạng văn bản)

Ký hiệu: A → B là bước chuyển hợp lệ; dấu “|” là các lựa chọn thay thế; trạng thái cuối không chuyển tiếp.

#### 3.7.1 Lịch hẹn

- Luồng chính: (tạo) → `CHO_DUYET` → `DA_XAC_NHAN` → `DA_DEN_KHAM` → `HOAN_THANH`
- Đổi khoa: `CHO_DUYET` → `DA_DOI_KHOA` → `DA_DEN_KHAM` → `HOAN_THANH`
- Chuyển bác sĩ: `CHO_DUYET` → `CHO_BAC_SI_XEM_XET` → `DA_XAC_NHAN` | `DA_DOI_KHOA` | `TU_CHOI`
- Từ chối: `CHO_DUYET` → `TU_CHOI` (cuối)
- Hủy: `CHO_DUYET` | `DA_XAC_NHAN` | `DA_DOI_KHOA` → `DA_HUY` (cuối); `DA_DEN_KHAM` → `DA_HUY` khi lần khám bị hủy
- Vắng mặt: `DA_XAC_NHAN` | `DA_DOI_KHOA` → `VANG_MAT` (cuối)
- Đổi khoa thêm: `DA_XAC_NHAN` | `DA_DOI_KHOA` → `DA_DOI_KHOA`

#### 3.7.2 Đơn thuốc

- Luồng chính: (kê đơn) → `CHUA_THANH_TOAN` → `DA_THANH_TOAN_CHO_NHAN` → `DA_NHAN` (cuối)
- Hủy: `CHUA_THANH_TOAN` → `DA_HUY` (cuối)
- Điều kiện bước 1 → 2: `hoa_don` của lần khám chuyển `DA_THANH_TOAN`; điều kiện bước 2 → 3: quét QR đủ 4 điều kiện (mã QR khớp, đã thanh toán, còn hạn, chưa phát).

#### 3.7.3 Hóa đơn

- Luồng chính: (lập tự động) → `CHUA_THANH_TOAN` → `DA_THANH_TOAN` (cuối, khóa)
- Hủy: `CHUA_THANH_TOAN` → `DA_HUY` (cuối)
- Chuỗi liên quan: `thanh_toan` `CHO_XU_LY` → `THANH_CONG` ⇒ `hoa_don` `DA_THANH_TOAN` ⇒ `don_thuoc` `DA_THANH_TOAN_CHO_NHAN`; `thanh_toan` `THAT_BAI` không đổi hóa đơn.

### 3.8 Bảng tra cứu ENUM theo cột

Dùng để đối chiếu: mọi cột trạng thái và loại ở Phần 2 đều có danh sách ở Phần 3.

| Mục | Tên danh sách | Cột sử dụng | Số giá trị |
|---|---|---|---|
| 3.1.1 | vai_tro | `vai_tro.ma_vai_tro` (VARCHAR) | 9 |
| 3.1.2 | trang_thai_tai_khoan | `tai_khoan.trang_thai` | 3 |
| 3.1.3 | gioi_tinh | `nguoi_benh.gioi_tinh` | 3 |
| 3.1.4 | (nhóm máu, VARCHAR) | `thong_tin_y_te_nen.ten_muc` khi NHOM_MAU | 8 |
| 3.1.5 | quan_he_than_nhan | `than_nhan.quan_he` | 8 |
| 3.1.6 | nguon_tao_ho_so | `nguoi_benh.nguon_tao` | 2 |
| 3.1.7 | loai_thong_tin_y_te_nen | `thong_tin_y_te_nen.loai_thong_tin` | 3 |
| 3.1.8 | muc_do_di_ung | `thong_tin_y_te_nen.muc_do_di_ung` | 3 |
| 3.1.9 | nguon_du_lieu_y_te_nen | `thong_tin_y_te_nen.nguon_du_lieu` | 2 |
| 3.1.10 | pham_vi_chia_se | `dong_y_chia_se_du_lieu.pham_vi_du_lieu` | 3 |
| 3.2.1 | loai_khoa | `khoa.loai_khoa` | 2 |
| 3.2.2 | loai_hinh_kham | `dich_vu_kham.loai_hinh_kham`, `lich_hen.loai_hinh_kham` | 3 |
| 3.2.3 | nhom_can_lam_sang | `danh_muc_can_lam_sang.nhom_cls` | 3 |
| 3.3.1 | trang_thai_lich_hen | `lich_hen.trang_thai`, `lich_su_trang_thai_lich_hen.trang_thai_cu`, `trang_thai_moi` | 9 |
| 3.3.2 | phuong_thuc_goi_y | `goi_y_chuyen_khoa.phuong_thuc_goi_y` | 2 |
| 3.4.1 | trang_thai_lan_kham | `lan_kham.trang_thai` | 4 |
| 3.4.2 | loai_chan_doan | `chan_doan.loai_chan_doan` | 3 |
| 3.4.3 | trang_thai_chi_dinh_cls | `chi_dinh_can_lam_sang.trang_thai` | 4 |
| 3.5.1 | trang_thai_don_thuoc | `don_thuoc.trang_thai` | 4 |
| 3.5.2 | trang_thai_hoa_don | `hoa_don.trang_thai` | 3 |
| 3.5.3 | loai_khoan_hoa_don | `chi_tiet_hoa_don.loai_khoan` | 3 |
| 3.5.4 | phuong_thuc_thanh_toan | `thanh_toan.phuong_thuc` | 4 |
| 3.5.5 | trang_thai_thanh_toan | `thanh_toan.trang_thai` | 3 |
| 3.6.1 | loai_giay_to | `giay_to_benh_vien.loai_giay_to` | 4 |
| 3.6.2 | trang_thai_giay_to | `giay_to_benh_vien.trang_thai` | 4 |
| 3.6.3 | loai_noi_dung_tin_tuc | `tin_tuc.loai_noi_dung` | 3 |
| 3.6.4 | trang_thai_tin_tuc | `tin_tuc.trang_thai` | 3 |
| 3.6.5 | loai_thong_bao | `thong_bao.loai_thong_bao` | 7 |
| 3.6.6 | hanh_dong_nhat_ky | `nhat_ky_thao_tac.hanh_dong` | 9 |

## Phần 4. Quan hệ giữa các bảng

Phần này liệt kê 68 khóa ngoại của 35 bảng. Mọi khóa ngoại là UUID, cùng kiểu với khóa chính được tham chiếu. Mọi cột khóa ngoại đều có chỉ mục (ở mục Chỉ mục của từng bảng ở Phần 2, hoặc nằm trong chỉ mục duy nhất đã nêu).

### 4.1 Nguyên tắc chọn quy tắc xóa

- `RESTRICT` là mặc định: dữ liệu y tế, tài chính, nhật ký không được mất khi xóa bản ghi cha; thay vào đó dùng xóa mềm (`xoa_luc`), `dang_su_dung` hoặc chuyển trạng thái.
- `CASCADE` chỉ dùng cho bảng con là một phần không tách rời của cha (lịch sử trạng thái, gợi ý chuyên khoa, chẩn đoán, dòng thuốc, khoản hóa đơn); vì các bảng cha không bị xóa cứng, quy tắc này chủ yếu để dọn dữ liệu thử.
- `SET NULL` chỉ dùng khi liên kết là phụ và cột cho phép NULL (`phong_thuc_hien_id`, `bao_hiem_y_te_id`, `lich_hen_tai_kham_id`).

### 4.2 Bảng liệt kê quan hệ

Kiểu quan hệ tính theo ràng buộc duy nhất: 1-1 khi khóa ngoại có chỉ mục duy nhất.

| STT | Bảng cha | Bảng con | Kiểu | Khóa ngoại | Xóa | Lý do |
|---|---|---|---|---|---|---|
| 1 | `vai_tro` | `tai_khoan` | 1-N | `vai_tro_id` | RESTRICT | Không xóa vai trò đang được gán |
| 2 | `tai_khoan` | `nhan_vien` | 1-1 | `tai_khoan_id` | RESTRICT | Giữ nhân viên khi còn tài khoản; vô hiệu thay vì xóa |
| 3 | `nguoi_benh` | `than_nhan` | 1-N | `nguoi_benh_id` | RESTRICT | Người quản lý hồ sơ; không xóa cứng hồ sơ |
| 4 | `nguoi_benh` | `than_nhan` | 1-N | `nguoi_than_id` | RESTRICT | Thân nhân là hồ sơ riêng; không xóa cứng |
| 5 | `nguoi_benh` | `thong_tin_y_te_nen` | 1-N | `nguoi_benh_id` | RESTRICT | Dữ liệu y tế phải lưu giữ theo Phần 5 |
| 6 | `dong_y_chia_se_du_lieu` | `thong_tin_y_te_nen` | 1-N | `dong_y_id` | RESTRICT | Dòng VNeID phải truy được bản ghi đồng ý |
| 7 | `nguoi_benh` | `dong_y_chia_se_du_lieu` | 1-N | `nguoi_benh_id` | RESTRICT | Bằng chứng đồng ý phải giữ |
| 8 | `nguoi_benh` | `dong_y_chia_se_du_lieu` | 1-N | `nguoi_dong_y_id` | RESTRICT | Biết ai đã bấm đồng ý (bản thân hoặc người quản lý) |
| 9 | `nguoi_benh` | `bao_hiem_y_te` | 1-N | `nguoi_benh_id` | RESTRICT | Thẻ BHYT liên quan hóa đơn cũ |
| 10 | `khoa` | `phong_kham` | 1-N | `khoa_id` | RESTRICT | Không xóa khoa đã có phòng |
| 11 | `nhan_vien` | `bac_si` | 1-1 | `nhan_vien_id` | RESTRICT | Bác sĩ luôn là một nhân viên |
| 12 | `khoa` | `bac_si` | 1-N | `khoa_id` | RESTRICT | Không xóa khoa đã có bác sĩ |
| 13 | `bac_si` | `lich_lam_viec` | 1-N | `bac_si_id` | RESTRICT | Lịch cũ phục vụ thống kê |
| 14 | `phong_kham` | `lich_lam_viec` | 1-N | `phong_kham_id` | RESTRICT | Lịch cũ còn tham chiếu phòng |
| 15 | `khoa` | `dich_vu_kham` | 1-N | `khoa_id` | RESTRICT | Đơn giá cũ còn dùng ở hóa đơn |
| 16 | `phong_kham` | `danh_muc_can_lam_sang` | 1-N | `phong_thuc_hien_id` | SET NULL | Đổi hoặc bỏ phòng thì hạng mục vẫn còn |
| 17 | `khoa` | `tu_khoa_trieu_chung` | 1-N | `khoa_id` | RESTRICT | Không xóa khoa còn từ khóa ánh xạ |
| 18 | `nguoi_benh` | `lich_hen` | 1-N | `nguoi_benh_id` | RESTRICT | Người được khám |
| 19 | `nguoi_benh` | `lich_hen` | 1-N | `nguoi_dat_id` | RESTRICT | Người đặt hộ; lưu để truy vết |
| 20 | `khoa` | `lich_hen` | 1-N | `khoa_id` | RESTRICT | Khoa hiện tại của lịch |
| 21 | `lich_hen` | `lich_su_trang_thai_lich_hen` | 1-N | `lich_hen_id` | CASCADE | Lịch sử là phần của lịch; chỉ xóa khi dọn dữ liệu thử |
| 22 | `khoa` | `lich_su_trang_thai_lich_hen` | 1-N | `khoa_cu_id` | RESTRICT | Khoa trước khi đổi |
| 23 | `khoa` | `lich_su_trang_thai_lich_hen` | 1-N | `khoa_moi_id` | RESTRICT | Khoa sau khi đổi |
| 24 | `nhan_vien` | `lich_su_trang_thai_lich_hen` | 1-N | `nhan_vien_thuc_hien_id` | RESTRICT | Truy vết người duyệt |
| 25 | `nguoi_benh` | `lich_su_trang_thai_lich_hen` | 1-N | `nguoi_benh_thuc_hien_id` | RESTRICT | Truy vết người tự hủy/đặt |
| 26 | `lich_hen` | `goi_y_chuyen_khoa` | 1-1 | `lich_hen_id` | CASCADE | Gợi ý là phần của lịch |
| 27 | `khoa` | `goi_y_chuyen_khoa` | 1-N | `khoa_goi_y_id` | RESTRICT | Thống kê chấp nhận gợi ý |
| 28 | `khoa` | `goi_y_chuyen_khoa` | 1-N | `khoa_nguoi_benh_chon_id` | RESTRICT | Thống kê chấp nhận gợi ý |
| 29 | `khoa` | `goi_y_chuyen_khoa` | 1-N | `khoa_y_ta_xac_nhan_id` | RESTRICT | Thống kê y tá giữ gợi ý |
| 30 | `lich_hen` | `so_thu_tu` | 1-1 | `lich_hen_id` | RESTRICT | Số đã cấp không mất theo lịch |
| 31 | `phong_kham` | `so_thu_tu` | 1-N | `phong_kham_id` | RESTRICT | Phòng được hướng dẫn tới |
| 32 | `nhan_vien` | `so_thu_tu` | 1-N | `nhan_vien_cap_id` | RESTRICT | Truy vết y tá cấp số |
| 33 | `lich_hen` | `lan_kham` | 1-1 | `lich_hen_id` | RESTRICT | Mỗi lịch sinh tối đa một lần khám |
| 34 | `nguoi_benh` | `lan_kham` | 1-N | `nguoi_benh_id` | RESTRICT | Hồ sơ y tế không xóa |
| 35 | `bac_si` | `lan_kham` | 1-N | `bac_si_id` | RESTRICT | Gom “Bác sĩ của tôi” |
| 36 | `phong_kham` | `lan_kham` | 1-N | `phong_kham_id` | RESTRICT | Phòng khám thực tế |
| 37 | `dich_vu_kham` | `lan_kham` | 1-N | `dich_vu_kham_id` | RESTRICT | Tính khoản khám |
| 38 | `bao_hiem_y_te` | `lan_kham` | 1-N | `bao_hiem_y_te_id` | SET NULL | Thẻ bị xóa thì lần khám vẫn giữ |
| 39 | `lan_kham` | `chan_doan` | 1-N | `lan_kham_id` | CASCADE | Chẩn đoán là phần của lần khám |
| 40 | `lan_kham` | `chi_dinh_can_lam_sang` | 1-N | `lan_kham_id` | RESTRICT | Chỉ định đã tính phí |
| 41 | `danh_muc_can_lam_sang` | `chi_dinh_can_lam_sang` | 1-N | `danh_muc_can_lam_sang_id` | RESTRICT | Hạng mục đã dùng không xóa |
| 42 | `nhan_vien` | `chi_dinh_can_lam_sang` | 1-N | `ky_thuat_vien_id` | RESTRICT | Kỹ thuật viên nhận việc |
| 43 | `chi_dinh_can_lam_sang` | `ket_qua_can_lam_sang` | 1-1 | `chi_dinh_can_lam_sang_id` | RESTRICT | Kết quả y tế không mất theo chỉ định |
| 44 | `nhan_vien` | `ket_qua_can_lam_sang` | 1-N | `ky_thuat_vien_id` | RESTRICT | Người nhập kết quả |
| 45 | `lan_kham` | `hen_tai_kham` | 1-1 | `lan_kham_id` | RESTRICT | Mỗi lần khám tối đa một hẹn tái khám |
| 46 | `lich_hen` | `hen_tai_kham` | 1-N | `lich_hen_tai_kham_id` | SET NULL | Lịch tái khám bị bỏ thì hẹn vẫn còn để nhắc |
| 47 | `lan_kham` | `don_thuoc` | 1-N | `lan_kham_id` | RESTRICT | Tối đa một đơn hiệu lực (chỉ mục duy nhất) |
| 48 | `don_thuoc` | `chi_tiet_don_thuoc` | 1-N | `don_thuoc_id` | CASCADE | Dòng thuốc là phần của đơn |
| 49 | `danh_muc_thuoc` | `chi_tiet_don_thuoc` | 1-N | `danh_muc_thuoc_id` | RESTRICT | Thuốc đã kê không xóa |
| 50 | `don_thuoc` | `phat_thuoc` | 1-1 | `don_thuoc_id` | RESTRICT | Chứng từ phát thuốc giữ lại |
| 51 | `nhan_vien` | `phat_thuoc` | 1-N | `nhan_vien_phat_id` | RESTRICT | Báo cáo theo người phát |
| 52 | `lan_kham` | `hoa_don` | 1-N | `lan_kham_id` | RESTRICT | Tối đa một hóa đơn hiệu lực |
| 53 | `nguoi_benh` | `hoa_don` | 1-N | `nguoi_benh_id` | RESTRICT | Người được tính phí |
| 54 | `hoa_don` | `chi_tiet_hoa_don` | 1-N | `hoa_don_id` | CASCADE | Khoản là phần của hóa đơn |
| 55 | `dich_vu_kham` | `chi_tiet_hoa_don` | 1-N | `dich_vu_kham_id` | RESTRICT | Nguồn khoản KHAM |
| 56 | `chi_dinh_can_lam_sang` | `chi_tiet_hoa_don` | 1-N | `chi_dinh_can_lam_sang_id` | RESTRICT | Nguồn khoản CAN_LAM_SANG |
| 57 | `chi_tiet_don_thuoc` | `chi_tiet_hoa_don` | 1-N | `chi_tiet_don_thuoc_id` | RESTRICT | Nguồn khoản THUOC |
| 58 | `hoa_don` | `thanh_toan` | 1-N | `hoa_don_id` | RESTRICT | Giữ cả lần thất bại để truy vết |
| 59 | `nhan_vien` | `thanh_toan` | 1-N | `nhan_vien_thu_id` | RESTRICT | Đối soát ca thu ngân |
| 60 | `nguoi_benh` | `thanh_toan` | 1-N | `nguoi_tra_id` | RESTRICT | Người tự trả trên web |
| 61 | `nguoi_benh` | `giay_to_benh_vien` | 1-N | `nguoi_benh_id` | RESTRICT | Chủ giấy tờ |
| 62 | `nguoi_benh` | `giay_to_benh_vien` | 1-N | `nguoi_yeu_cau_id` | RESTRICT | Người gửi yêu cầu (có thể là thân nhân) |
| 63 | `lan_kham` | `giay_to_benh_vien` | 1-N | `lan_kham_id` | RESTRICT | Căn cứ khám của giấy |
| 64 | `nhan_vien` | `giay_to_benh_vien` | 1-N | `nhan_vien_duyet_id` | RESTRICT | Truy vết người duyệt |
| 65 | `nhan_vien` | `tin_tuc` | 1-N | `tac_gia_id` | RESTRICT | Tác giả bài viết |
| 66 | `nguoi_benh` | `thong_bao` | 1-N | `nguoi_nhan_id` | RESTRICT | Thông báo dọn theo Phần 5 |
| 67 | `nhan_vien` | `nhat_ky_thao_tac` | 1-N | `nhan_vien_id` | RESTRICT | Nhật ký không mất |
| 68 | `nguoi_benh` | `nhat_ky_thao_tac` | 1-N | `nguoi_benh_id` | RESTRICT | Tra “ai đã xem hồ sơ” |

### 4.3 Quan hệ nhiều-nhiều (N-N)

Không có bảng N-N riêng ngoài các bảng nghiệp vụ sau, mỗi bảng là bảng trung gian mang thêm dữ liệu:

| Hai bảng | Bảng trung gian | Ý nghĩa |
|---|---|---|
| `nguoi_benh` – `nguoi_benh` | `than_nhan` | Một người quản lý nhiều thân nhân và ngược lại; có thêm `quan_he`. |
| `bac_si` – `phong_kham` | `lich_lam_viec` | Bác sĩ làm ở nhiều phòng theo ngày; có thêm ngày và giờ. |
| `don_thuoc` – `danh_muc_thuoc` | `chi_tiet_don_thuoc` | Một đơn nhiều thuốc; có thêm số lượng, liều dùng. |
| `nguoi_benh` – `bac_si` | `lan_kham` | Người bệnh khám với nhiều bác sĩ; mỗi lần khám đúng một bác sĩ. |
| `hoa_don` – nguồn khoản | `chi_tiet_hoa_don` | Hóa đơn gồm nhiều khoản từ ba nguồn. |

### 4.4 Sơ đồ ERD (Mermaid)

Chia 5 sơ đồ theo nhóm nghiệp vụ; mỗi sơ đồ chỉ nêu khóa chính và khóa ngoại để gọn (cột đầy đủ xem Phần 2). Dán từng khối vào công cụ hỗ trợ Mermaid (ví dụ mermaid.live). Ký hiệu: `||--o{` là 1-N, `||--o|` là 1-1.

#### 4.4.1 ERD – Nhóm người dùng và người bệnh

`erDiagram`

`vai_tro ||--o{ tai_khoan : "vai_tro_id"`

`tai_khoan ||--o| nhan_vien : "tai_khoan_id"`

`nguoi_benh ||--o{ than_nhan : "nguoi_benh_id"`

`nguoi_benh ||--o{ than_nhan : "nguoi_than_id"`

`nguoi_benh ||--o{ thong_tin_y_te_nen : "nguoi_benh_id"`

`dong_y_chia_se_du_lieu ||--o{ thong_tin_y_te_nen : "dong_y_id"`

`nguoi_benh ||--o{ dong_y_chia_se_du_lieu : "nguoi_benh_id"`

`nguoi_benh ||--o{ dong_y_chia_se_du_lieu : "nguoi_dong_y_id"`

`nguoi_benh ||--o{ bao_hiem_y_te : "nguoi_benh_id"`

`vai_tro {`

`uuid id PK`

`}`

`tai_khoan {`

`uuid id PK`

`uuid vai_tro_id FK`

`}`

`nhan_vien {`

`uuid id PK`

`uuid tai_khoan_id FK`

`}`

`nguoi_benh {`

`uuid id PK`

`}`

`than_nhan {`

`uuid id PK`

`uuid nguoi_benh_id FK`

`uuid nguoi_than_id FK`

`}`

`thong_tin_y_te_nen {`

`uuid id PK`

`uuid nguoi_benh_id FK`

`uuid dong_y_id FK`

`}`

`dong_y_chia_se_du_lieu {`

`uuid id PK`

`uuid nguoi_benh_id FK`

`uuid nguoi_dong_y_id FK`

`}`

`bao_hiem_y_te {`

`uuid id PK`

`uuid nguoi_benh_id FK`

`}`

#### 4.4.2 ERD – Nhóm danh mục

`erDiagram`

`khoa ||--o{ phong_kham : "khoa_id"`

`nhan_vien ||--o| bac_si : "nhan_vien_id"`

`khoa ||--o{ bac_si : "khoa_id"`

`bac_si ||--o{ lich_lam_viec : "bac_si_id"`

`phong_kham ||--o{ lich_lam_viec : "phong_kham_id"`

`khoa ||--o{ dich_vu_kham : "khoa_id"`

`phong_kham ||--o{ danh_muc_can_lam_sang : "phong_thuc_hien_id"`

`khoa ||--o{ tu_khoa_trieu_chung : "khoa_id"`

`khoa {`

`uuid id PK`

`}`

`phong_kham {`

`uuid id PK`

`uuid khoa_id FK`

`}`

`nhan_vien {`

`uuid id PK`

`}`

`bac_si {`

`uuid id PK`

`uuid nhan_vien_id FK`

`uuid khoa_id FK`

`}`

`lich_lam_viec {`

`uuid id PK`

`uuid bac_si_id FK`

`uuid phong_kham_id FK`

`}`

`dich_vu_kham {`

`uuid id PK`

`uuid khoa_id FK`

`}`

`danh_muc_can_lam_sang {`

`uuid id PK`

`uuid phong_thuc_hien_id FK`

`}`

`tu_khoa_trieu_chung {`

`uuid id PK`

`uuid khoa_id FK`

`}`

#### 4.4.3 ERD – Nhóm đặt lịch và tiếp nhận

`erDiagram`

`nguoi_benh ||--o{ lich_hen : "nguoi_benh_id"`

`nguoi_benh ||--o{ lich_hen : "nguoi_dat_id"`

`khoa ||--o{ lich_hen : "khoa_id"`

`lich_hen ||--o{ lich_su_trang_thai_lich_hen : "lich_hen_id"`

`khoa ||--o{ lich_su_trang_thai_lich_hen : "khoa_cu_id"`

`khoa ||--o{ lich_su_trang_thai_lich_hen : "khoa_moi_id"`

`nhan_vien ||--o{ lich_su_trang_thai_lich_hen : "nhan_vien_thuc_hien_id"`

`nguoi_benh ||--o{ lich_su_trang_thai_lich_hen : "nguoi_benh_thuc_hien_id"`

`lich_hen ||--o| goi_y_chuyen_khoa : "lich_hen_id"`

`khoa ||--o{ goi_y_chuyen_khoa : "khoa_goi_y_id"`

`khoa ||--o{ goi_y_chuyen_khoa : "khoa_nguoi_benh_chon_id"`

`khoa ||--o{ goi_y_chuyen_khoa : "khoa_y_ta_xac_nhan_id"`

`lich_hen ||--o| so_thu_tu : "lich_hen_id"`

`phong_kham ||--o{ so_thu_tu : "phong_kham_id"`

`nhan_vien ||--o{ so_thu_tu : "nhan_vien_cap_id"`

`nguoi_benh {`

`uuid id PK`

`}`

`lich_hen {`

`uuid id PK`

`uuid nguoi_benh_id FK`

`uuid nguoi_dat_id FK`

`uuid khoa_id FK`

`}`

`khoa {`

`uuid id PK`

`}`

`lich_su_trang_thai_lich_hen {`

`uuid id PK`

`uuid lich_hen_id FK`

`uuid khoa_cu_id FK`

`uuid khoa_moi_id FK`

`uuid nhan_vien_thuc_hien_id FK`

`uuid nguoi_benh_thuc_hien_id FK`

`}`

`nhan_vien {`

`uuid id PK`

`}`

`goi_y_chuyen_khoa {`

`uuid id PK`

`uuid lich_hen_id FK`

`uuid khoa_goi_y_id FK`

`uuid khoa_nguoi_benh_chon_id FK`

`uuid khoa_y_ta_xac_nhan_id FK`

`}`

`so_thu_tu {`

`uuid id PK`

`uuid lich_hen_id FK`

`uuid phong_kham_id FK`

`uuid nhan_vien_cap_id FK`

`}`

`phong_kham {`

`uuid id PK`

`}`

#### 4.4.4 ERD – Nhóm khám bệnh

`erDiagram`

`lich_hen ||--o| lan_kham : "lich_hen_id"`

`nguoi_benh ||--o{ lan_kham : "nguoi_benh_id"`

`bac_si ||--o{ lan_kham : "bac_si_id"`

`phong_kham ||--o{ lan_kham : "phong_kham_id"`

`dich_vu_kham ||--o{ lan_kham : "dich_vu_kham_id"`

`bao_hiem_y_te ||--o{ lan_kham : "bao_hiem_y_te_id"`

`lan_kham ||--o{ chan_doan : "lan_kham_id"`

`lan_kham ||--o{ chi_dinh_can_lam_sang : "lan_kham_id"`

`danh_muc_can_lam_sang ||--o{ chi_dinh_can_lam_sang : "danh_muc_can_lam_sang_id"`

`nhan_vien ||--o{ chi_dinh_can_lam_sang : "ky_thuat_vien_id"`

`chi_dinh_can_lam_sang ||--o| ket_qua_can_lam_sang : "chi_dinh_can_lam_sang_id"`

`nhan_vien ||--o{ ket_qua_can_lam_sang : "ky_thuat_vien_id"`

`lan_kham ||--o| hen_tai_kham : "lan_kham_id"`

`lich_hen ||--o{ hen_tai_kham : "lich_hen_tai_kham_id"`

`lich_hen {`

`uuid id PK`

`}`

`lan_kham {`

`uuid id PK`

`uuid lich_hen_id FK`

`uuid nguoi_benh_id FK`

`uuid bac_si_id FK`

`uuid phong_kham_id FK`

`uuid dich_vu_kham_id FK`

`uuid bao_hiem_y_te_id FK`

`}`

`nguoi_benh {`

`uuid id PK`

`}`

`bac_si {`

`uuid id PK`

`}`

`phong_kham {`

`uuid id PK`

`}`

`dich_vu_kham {`

`uuid id PK`

`}`

`bao_hiem_y_te {`

`uuid id PK`

`}`

`chan_doan {`

`uuid id PK`

`uuid lan_kham_id FK`

`}`

`chi_dinh_can_lam_sang {`

`uuid id PK`

`uuid lan_kham_id FK`

`uuid danh_muc_can_lam_sang_id FK`

`uuid ky_thuat_vien_id FK`

`}`

`danh_muc_can_lam_sang {`

`uuid id PK`

`}`

`nhan_vien {`

`uuid id PK`

`}`

`ket_qua_can_lam_sang {`

`uuid id PK`

`uuid chi_dinh_can_lam_sang_id FK`

`uuid ky_thuat_vien_id FK`

`}`

`hen_tai_kham {`

`uuid id PK`

`uuid lan_kham_id FK`

`uuid lich_hen_tai_kham_id FK`

`}`

#### 4.4.5 ERD – Nhóm đơn thuốc, tài chính, giấy tờ, hệ thống

`erDiagram`

`lan_kham ||--o{ don_thuoc : "lan_kham_id"`

`don_thuoc ||--o{ chi_tiet_don_thuoc : "don_thuoc_id"`

`danh_muc_thuoc ||--o{ chi_tiet_don_thuoc : "danh_muc_thuoc_id"`

`don_thuoc ||--o| phat_thuoc : "don_thuoc_id"`

`nhan_vien ||--o{ phat_thuoc : "nhan_vien_phat_id"`

`lan_kham ||--o{ hoa_don : "lan_kham_id"`

`nguoi_benh ||--o{ hoa_don : "nguoi_benh_id"`

`hoa_don ||--o{ chi_tiet_hoa_don : "hoa_don_id"`

`dich_vu_kham ||--o{ chi_tiet_hoa_don : "dich_vu_kham_id"`

`chi_dinh_can_lam_sang ||--o{ chi_tiet_hoa_don : "chi_dinh_can_lam_sang_id"`

`chi_tiet_don_thuoc ||--o{ chi_tiet_hoa_don : "chi_tiet_don_thuoc_id"`

`hoa_don ||--o{ thanh_toan : "hoa_don_id"`

`nhan_vien ||--o{ thanh_toan : "nhan_vien_thu_id"`

`nguoi_benh ||--o{ thanh_toan : "nguoi_tra_id"`

`nguoi_benh ||--o{ giay_to_benh_vien : "nguoi_benh_id"`

`nguoi_benh ||--o{ giay_to_benh_vien : "nguoi_yeu_cau_id"`

`lan_kham ||--o{ giay_to_benh_vien : "lan_kham_id"`

`nhan_vien ||--o{ giay_to_benh_vien : "nhan_vien_duyet_id"`

`nhan_vien ||--o{ tin_tuc : "tac_gia_id"`

`nguoi_benh ||--o{ thong_bao : "nguoi_nhan_id"`

`nhan_vien ||--o{ nhat_ky_thao_tac : "nhan_vien_id"`

`nguoi_benh ||--o{ nhat_ky_thao_tac : "nguoi_benh_id"`

`lan_kham {`

`uuid id PK`

`}`

`don_thuoc {`

`uuid id PK`

`uuid lan_kham_id FK`

`}`

`chi_tiet_don_thuoc {`

`uuid id PK`

`uuid don_thuoc_id FK`

`uuid danh_muc_thuoc_id FK`

`}`

`danh_muc_thuoc {`

`uuid id PK`

`}`

`phat_thuoc {`

`uuid id PK`

`uuid don_thuoc_id FK`

`uuid nhan_vien_phat_id FK`

`}`

`nhan_vien {`

`uuid id PK`

`}`

`hoa_don {`

`uuid id PK`

`uuid lan_kham_id FK`

`uuid nguoi_benh_id FK`

`}`

`nguoi_benh {`

`uuid id PK`

`}`

`chi_tiet_hoa_don {`

`uuid id PK`

`uuid hoa_don_id FK`

`uuid dich_vu_kham_id FK`

`uuid chi_dinh_can_lam_sang_id FK`

`uuid chi_tiet_don_thuoc_id FK`

`}`

`dich_vu_kham {`

`uuid id PK`

`}`

`chi_dinh_can_lam_sang {`

`uuid id PK`

`}`

`thanh_toan {`

`uuid id PK`

`uuid hoa_don_id FK`

`uuid nhan_vien_thu_id FK`

`uuid nguoi_tra_id FK`

`}`

`giay_to_benh_vien {`

`uuid id PK`

`uuid nguoi_benh_id FK`

`uuid nguoi_yeu_cau_id FK`

`uuid lan_kham_id FK`

`uuid nhan_vien_duyet_id FK`

`}`

`tin_tuc {`

`uuid id PK`

`uuid tac_gia_id FK`

`}`

`thong_bao {`

`uuid id PK`

`uuid nguoi_nhan_id FK`

`}`

`nhat_ky_thao_tac {`

`uuid id PK`

`uuid nhan_vien_id FK`

`uuid nguoi_benh_id FK`

`}`

## Phần 5. Phân loại và bảo vệ dữ liệu

Phần này tổng hợp mức nhạy cảm đã gán cho từng cột ở Phần 2 và đề xuất biện pháp bảo vệ. Mức nhạy cảm của từng cột nằm ở cột cuối của bảng Phần 2; phần này không đổi mức đó mà bổ sung cách xử lý. Ba cột `so_the_bhyt`, `ma_cccd` và các cột y tế là nhóm cần chú ý nhất.

### 5.1 Bốn mức nhạy cảm

| Mức | Ý nghĩa | Ví dụ | Số cột |
|---|---|---|---|
| Công khai | Hiển thị cho khách truy cập, không cần đăng nhập | `khoa.ten_khoa`, `tin_tuc.noi_dung`, `dich_vu_kham.don_gia` | 20 |
| Nội bộ | Dữ liệu vận hành, khóa, mã, thời điểm; chỉ người dùng đã đăng nhập thấy | `id`, `tao_luc`, `trang_thai` | 244 |
| Nhạy cảm (định danh) | Định danh người hoặc giao dịch cá nhân | `nguoi_benh.ma_cccd`, `ho_ten`, `so_the_bhyt`, `hoa_don.tong_tien` | 53 |
| Rất nhạy cảm (y tế) | Dữ liệu sức khỏe: nhóm máu, dị ứng, triệu chứng, chẩn đoán, kết quả, đơn thuốc, nội dung giấy tờ | `goi_y_chuyen_khoa.mo_ta_trieu_chung`, `chan_doan.ten_chan_doan`, `ket_qua_can_lam_sang.ket_qua_chi_tiet` | 43 |

Tổng cộng 360 cột trong 35 bảng. Phân bố theo bảng:

| Bảng | Công khai | Nội bộ | Nhạy cảm | Rất nhạy cảm |
|---|---|---|---|---|
| `tai_khoan` | 0 | 9 | 2 | 0 |
| `vai_tro` | 0 | 7 | 0 | 0 |
| `nhan_vien` | 0 | 10 | 0 | 0 |
| `nguoi_benh` | 0 | 6 | 8 | 0 |
| `than_nhan` | 0 | 4 | 3 | 0 |
| `thong_tin_y_te_nen` | 0 | 5 | 0 | 6 |
| `dong_y_chia_se_du_lieu` | 0 | 6 | 2 | 0 |
| `bao_hiem_y_te` | 0 | 3 | 6 | 0 |
| `khoa` | 2 | 6 | 0 | 0 |
| `phong_kham` | 0 | 8 | 0 | 0 |
| `bac_si` | 4 | 7 | 0 | 0 |
| `lich_lam_viec` | 0 | 11 | 0 | 0 |
| `dich_vu_kham` | 4 | 6 | 0 | 0 |
| `danh_muc_can_lam_sang` | 3 | 8 | 0 | 0 |
| `danh_muc_thuoc` | 0 | 12 | 0 | 0 |
| `tu_khoa_trieu_chung` | 0 | 9 | 0 | 0 |
| `lich_hen` | 0 | 6 | 6 | 1 |
| `lich_su_trang_thai_lich_hen` | 0 | 8 | 1 | 1 |
| `goi_y_chuyen_khoa` | 0 | 9 | 0 | 2 |
| `so_thu_tu` | 0 | 9 | 0 | 0 |
| `lan_kham` | 0 | 11 | 2 | 4 |
| `chan_doan` | 0 | 4 | 0 | 4 |
| `chi_dinh_can_lam_sang` | 0 | 7 | 0 | 1 |
| `ket_qua_can_lam_sang` | 0 | 5 | 0 | 3 |
| `hen_tai_kham` | 0 | 5 | 1 | 1 |
| `don_thuoc` | 0 | 6 | 2 | 1 |
| `chi_tiet_don_thuoc` | 0 | 4 | 0 | 5 |
| `phat_thuoc` | 0 | 5 | 0 | 0 |
| `hoa_don` | 0 | 7 | 6 | 0 |
| `chi_tiet_hoa_don` | 0 | 6 | 5 | 3 |
| `thanh_toan` | 0 | 9 | 3 | 0 |
| `giay_to_benh_vien` | 0 | 6 | 2 | 8 |
| `tin_tuc` | 7 | 6 | 0 | 0 |
| `thong_bao` | 0 | 7 | 3 | 0 |
| `nhat_ky_thao_tac` | 0 | 7 | 1 | 3 |

### 5.2 Biện pháp chung theo mức

| Mức | Lưu trữ | Truy cập | Nhật ký |
|---|---|---|---|
| Công khai | Lưu bình thường | Mọi người, qua API chỉ trả đúng cột công khai | Không |
| Nội bộ | Lưu bình thường; mã hóa ổ đĩa và sao lưu | Người dùng đã đăng nhập, theo Phần 6 | Chỉ thao tác ghi |
| Nhạy cảm (định danh) | Mã hóa ổ đĩa (cơ sở dữ liệu và sao lưu); truyền qua HTTPS; ẩn một phần khi hiển thị | Chủ hồ sơ, thân nhân liên kết và nhân viên có nhiệm vụ | Ghi mọi lần nhân viên xem đầy đủ (không che) |
| Rất nhạy cảm (y tế) | Như trên; cột văn bản tự do mã hóa thêm ở tầng ứng dụng (5.3) | Chủ hồ sơ, thân nhân liên kết, nhân viên đang xử lý ca đó | Ghi mọi lần xem và sửa |

### 5.3 Biện pháp theo cột trọng yếu

“Mã hóa ứng dụng” là AES-256-GCM thực hiện trong tầng ứng dụng trước khi ghi, khóa giữ ngoài cơ sở dữ liệu (biến môi trường hoặc kho khóa) và có số phiên bản khóa để xoay vòng. Chỉ áp dụng cho văn bản tự do không cần lọc, tìm kiếm hay thống kê theo nội dung. Cột mã hóa phải lưu dạng TEXT ở tầng vật lý; độ dài ở Phần 2 là độ dài nội dung trước khi mã hóa, kiểm tra ở ứng dụng.

| Cột | Biện pháp lưu | Hiển thị | Ai được xem | Nhật ký |
|---|---|---|---|---|
| `tai_khoan.mat_khau_bam` | Băm bcrypt (hệ số chi phí tối thiểu 10), không lưu bản rõ | Không bao giờ trả ra API hay ghi vào nhật ký | Không ai | Có (đặt lại, đổi) |
| `tai_khoan.ten_dang_nhap` | Lưu rõ (cần tra cứu) | Đầy đủ | Quản trị viên; chủ tài khoản | Đăng nhập, đăng nhập thất bại |
| `nguoi_benh.ma_cccd` | Lưu rõ (cần tra cứu khi tiếp nhận) trong ổ đĩa mã hóa | Chỉ hiện 3 số cuối (`*********123`); y tá xem đủ khi tiếp nhận | Chủ hồ sơ; Y tá; Bác sĩ ở ca đang xử lý | Có khi xem đủ |
| `nguoi_benh.ho_ten`, `ngay_sinh`, `gioi_tinh` | Lưu rõ | Họ tên đủ cho nhân viên cần định danh | Chủ hồ sơ, thân nhân; Y tá, Bác sĩ, Kỹ thuật viên, Thu ngân, Phát thuốc ở ca đang xử lý | Có với Bác sĩ, Y tá |
| `nguoi_benh.so_dien_thoai`, `email`, `dia_chi` | Lưu rõ | Che bớt: số điện thoại chỉ hiện 3 số đầu và 2 số cuối; email che phần tên; địa chỉ chỉ hiện phường/quận | Chủ hồ sơ; Y tá (đủ); vai trò khác không xem | Có khi Y tá xem đủ |
| `bao_hiem_y_te.so_the_bhyt` | Lưu rõ trong ổ đĩa mã hóa | Hiện 4 ký tự cuối | Chủ hồ sơ; Y tá; Thu ngân | Có khi xem đủ |
| `lich_hen.ma_qr`, `don_thuoc.ma_qr` | Token ngẫu nhiên tối thiểu 128 bit, không chứa dữ liệu bệnh | Chỉ hiện dưới dạng ảnh QR cho chủ hồ sơ; không ghi vào nhật ký hay URL | Chủ hồ sơ; Y tá (phiếu hẹn); Phát thuốc (đơn) | Có khi quét |
| `goi_y_chuyen_khoa.mo_ta_trieu_chung` | Mã hóa ứng dụng; xem mục 5.4 | Đầy đủ cho người được xem | Chủ hồ sơ, người đặt hộ; Y tá và Bác sĩ khi duyệt hoặc khám | Có (XEM) |
| `lich_hen.ly_do_kham`, `lich_su_trang_thai_lich_hen.ly_do` | Mã hóa ứng dụng | Đầy đủ cho người được xem | Như trên | Có (XEM) |
| `lan_kham.benh_su`, `kham_lam_sang`, `loi_dan` | Mã hóa ứng dụng | Đầy đủ | Chủ hồ sơ; Bác sĩ của lần khám; Y tá ở ca liên quan chỉ xem lời dặn | Có (XEM, SUA) |
| `chan_doan.*`, `ket_qua_can_lam_sang.ket_qua_chi_tiet`, `ket_luan` | `ma_icd10`, `loai_chan_doan` lưu rõ (cần thống kê); `ten_chan_doan`, `ghi_chu`, `ket_qua_chi_tiet`, `ket_luan` mã hóa ứng dụng | Đầy đủ | Chủ hồ sơ; Bác sĩ của lần khám; Kỹ thuật viên chỉ kết quả mình nhập | Có (XEM, SUA) |
| `chi_tiet_don_thuoc.*`, `don_thuoc.ghi_chu` | `lieu_dung`, `cach_dung` mã hóa ứng dụng; thuốc, số lượng lưu rõ (cần tính hóa đơn) | Đầy đủ | Chủ hồ sơ; Bác sĩ kê đơn; Phát thuốc (khi quét); Thu ngân thấy tên thuốc và tiền, không thấy liều dùng | Có khi quét, sửa |
| `thong_tin_y_te_nen.*` | Lưu rõ trong ổ đĩa mã hóa (cần đối chiếu dị ứng khi kê đơn); `ghi_chu` mã hóa ứng dụng | Đầy đủ | Chủ hồ sơ; Bác sĩ (VNEID chỉ khi chưa thu hồi đồng ý); Y tá | Có (XEM, SUA) |
| `giay_to_benh_vien.noi_dung`, `ly_do_yeu_cau`, `ly_do_tu_choi`, `noi_chuyen_den` | Mã hóa ứng dụng | Đầy đủ | Chủ hồ sơ; nhân viên có quyền duyệt | Có (XEM, DUYET) |
| `ket_qua_can_lam_sang.tep_dinh_kem_url`, `giay_to_benh_vien.tep_giay_to_url` | Tệp ở kho riêng, mã hóa; đường dẫn không đoán được | Chỉ trả đường dẫn ký có thời hạn ngắn (vài phút) qua API kiểm tra quyền | Như dữ liệu gốc | Có (XEM) |
| `thong_bao.tieu_de`, `noi_dung` | Lưu rõ; Phần 2 đã cấm ghi chẩn đoán, triệu chứng, tên thuốc | Đầy đủ | Chủ hồ sơ | Không |
| `nhat_ky_thao_tac.du_lieu_truoc`, `du_lieu_sau` | Không chứa `mat_khau_bam`; CCCD che 3 số cuối; văn bản y tế tự do thay bằng tên cột, không lưu nội dung | Chỉ để điều tra | Quản trị viên, Cán bộ quản lý | Truy cập bảng không tự ghi vòng lặp |
| `nhat_ky_thao_tac.dia_chi_ip` | Lưu rõ | Đầy đủ | Quản trị viên, Cán bộ quản lý | Không |

Các cột lọc hoặc thống kê (`co_dau_hieu_nang`, `ma_icd10`, `loai_chan_doan`, các cột trạng thái) không mã hóa ứng dụng vì sẽ mất khả năng truy vấn. Cán bộ quản lý và Quản trị viên không xem được bản ghi y tế của từng người bệnh; chỉ xem báo cáo tổng hợp (Phần 6). Ngoài ra: kết nối cơ sở dữ liệu và website luôn qua TLS, bản sao lưu mã hóa, tài khoản kết nối của ứng dụng không có quyền DDL.

### 5.4 Quy tắc riêng cho mô tả triệu chứng

`goi_y_chuyen_khoa.mo_ta_trieu_chung` (và `lich_hen.ly_do_kham`) là dữ liệu y tế nhạy cảm, mức Rất nhạy cảm, vì người bệnh tự viết và có thể kể cả bệnh nền, thuốc đang dùng.

- Lưu: mã hóa ứng dụng; không đưa vào nhật ký ứng dụng, thông báo lỗi, URL, tham số truy vấn hay công cụ phân tích; không đưa vào `thong_bao`.
- Xem: chủ hồ sơ và người đặt hộ còn liên kết; Y tá khi duyệt lịch; Bác sĩ khi lịch được chuyển lên hoặc khi khám người bệnh đó. Không cho Kỹ thuật viên, Thu ngân, Phát thuốc, Cán bộ quản lý, Quản trị viên xem nội dung.
- Thống kê tỷ lệ chấp nhận gợi ý chỉ dùng `khoa_goi_y_id`, `khoa_nguoi_benh_chon_id`, `khoa_y_ta_xac_nhan_id`, `phuong_thuc_goi_y`; không đọc nội dung mô tả.
- Bộ gợi ý rule-based chạy trong hệ thống. Phương thức `LLM` chỉ dùng với dữ liệu giả khi trình diễn; không gửi mô tả của người bệnh thật ra dịch vụ bên ngoài.
- Mỗi lần nhân viên mở nội dung ghi `nhat_ky_thao_tac` hành động `XEM`, `ten_bang` = `goi_y_chuyen_khoa`, kèm `nguoi_benh_id`.
- Lịch không sinh lần khám (`TU_CHOI`, `DA_HUY`, `VANG_MAT`): xóa dòng `goi_y_chuyen_khoa` sau 2 năm kể từ ngày hẹn, sau khi đã tổng hợp thống kê; vì cột bắt buộc nên xóa cả dòng thay vì để trống. `lich_hen.ly_do_kham` đặt NULL cùng lúc.

### 5.5 Lưu giữ và xóa mềm hồ sơ y tế

Thời hạn dưới đây là đề xuất cấu hình cho đồ án, không phải ý kiến pháp lý. Luật Khám bệnh, chữa bệnh 2023 yêu cầu cơ sở lưu trữ và bảo mật hồ sơ bệnh án nhưng mình chưa xác minh được số năm cụ thể cho bệnh án ngoại trú trong các nguồn tra cứu; cần đối chiếu quy định hiện hành của Bộ Y tế hoặc quy chế của bệnh viện trước khi chốt.

| Nhóm dữ liệu | Bảng | Thời hạn lưu (đề xuất) | Cách xóa |
|---|---|---|---|
| Hồ sơ y tế | `nguoi_benh`, `thong_tin_y_te_nen`, `lan_kham`, `chan_doan`, `chi_dinh_can_lam_sang`, `ket_qua_can_lam_sang`, `hen_tai_kham`, `don_thuoc`, `chi_tiet_don_thuoc`, `giay_to_benh_vien` | Theo quy định lưu bệnh án; đặt mặc định 10 năm kể từ lần khám cuối của người bệnh, cấu hình được | Chỉ xóa mềm (`xoa_luc`) đối với `nguoi_benh`, `lan_kham`; các bảng còn lại không xóa. Hết hạn: Quản trị viên chạy quy trình ẩn danh hóa hoặc xóa cứng theo lô, có biên bản và ghi `nhat_ky_thao_tac` |
| Đồng ý chia sẻ | `dong_y_chia_se_du_lieu` | Cùng thời hạn hồ sơ; không sửa, chỉ thu hồi bằng `thu_hoi_luc` | Không xóa |
| Tài chính | `hoa_don`, `chi_tiet_hoa_don`, `thanh_toan`, `bao_hiem_y_te` | Tối thiểu cùng hồ sơ y tế | Không xóa; hóa đơn sai chuyển `DA_HUY` kèm lý do |
| Lịch hẹn có lần khám | `lich_hen`, `lich_su_trang_thai_lich_hen`, `goi_y_chuyen_khoa`, `so_thu_tu` | Cùng hồ sơ y tế | Không xóa |
| Lịch hẹn không có lần khám | `lich_hen`, `goi_y_chuyen_khoa` | 2 năm kể từ ngày hẹn (xem 5.4) | Xóa nội dung triệu chứng như 5.4 |
| Thông báo | `thong_bao` | 90 ngày kể từ `tao_luc` nếu đã đọc; 1 năm nếu chưa đọc | Xóa cứng theo lô (dữ liệu tạm thời) |
| Nhật ký thao tác | `nhat_ky_thao_tac` | Tối thiểu 5 năm (đề xuất); chỉ thêm, không sửa, không xóa trong thời hạn | Hết hạn: lưu trữ ngoại tuyến rồi xóa theo lô do Quản trị viên, có biên bản |
| Nội dung công khai | `tin_tuc` | Không giới hạn | Xóa mềm (`xoa_luc`) hoặc chuyển `AN` |
| Danh mục | `khoa`, `phong_kham`, `bac_si`, `dich_vu_kham`, `danh_muc_*`, `tu_khoa_trieu_chung` | Vĩnh viễn (hồ sơ cũ còn tham chiếu) | Đặt `dang_su_dung` = false, không xóa |
| Tài khoản và nhân viên | `tai_khoan`, `nhan_vien` | Vĩnh viễn (nhật ký còn tham chiếu) | Vô hiệu (`VO_HIEU`) và xóa mềm `nhan_vien` |

Quy tắc xóa mềm: bản ghi có `xoa_luc` khác NULL bị ẩn khỏi mọi màn hình người dùng và mọi thống kê, nhưng khóa ngoại vẫn hợp lệ. Chỉ Quản trị viên khôi phục được (đặt lại NULL) và phải ghi `nhat_ky_thao_tac`. Người bệnh yêu cầu xóa hồ sơ thì chỉ xóa mềm; xóa cứng chỉ khi hết thời hạn lưu giữ.

## Phần 6. Ma trận vai trò – dữ liệu (CRUD)

Ma trận cho 35 bảng và 9 vai trò. C = tạo, R = xem, U = sửa, D = xóa mềm hoặc hủy (không có xóa cứng ở bất kỳ vai trò nào; xóa cứng chỉ do quy trình lưu giữ ở Phần 5.5). Ô trống là không có quyền. Quyền gắn cố định với vai trò trong mã nguồn, không có bảng phân quyền riêng (Phần 1.6).

Cột: Khách = Khách truy cập (`KHACH`), NB = Người bệnh (`NGUOI_BENH`), YT = Y tá (`Y_TA`), BS = Bác sĩ (`BAC_SI`), KTV = Kỹ thuật viên (`KY_THUAT_VIEN`), TN = Thu ngân (`THU_NGAN`), PT = Phát thuốc (`PHAT_THUOC`), QL = Cán bộ quản lý (`CAN_BO_QUAN_LY`), QT = Quản trị viên (`QUAN_TRI_VIEN`).

Phạm vi (chỉ số trên): ¹ chỉ dữ liệu của chính mình và thân nhân còn liên kết; ² chỉ người bệnh thuộc việc đang xử lý của vai trò đó và chỉ các cột cần cho việc đó, mọi lần xem ghi nhật ký; ³ chỉ qua báo cáo tổng hợp, không xem từng bản ghi cá nhân; ⁴ chỉ lần khám, đơn, chỉ định thuộc chính bác sĩ đó (`bac_si_id` của mình), hoặc hồ sơ cá nhân của mình với `bac_si`.

| Bảng | Khách | NB | YT | BS | KTV | TN | PT | QL | QT |
|---|---|---|---|---|---|---|---|---|---|
| tai_khoan |  |  |  |  |  |  |  |  | CRU |
| vai_tro |  |  |  |  |  |  |  |  | R |
| nhan_vien |  |  |  |  |  |  |  | R | CRUD |
| nguoi_benh |  | CRU¹ | CRU | R² | R² | R² | R² | R³ |  |
| than_nhan |  | CRUD¹ | R² |  |  |  |  |  |  |
| thong_tin_y_te_nen |  | CRU¹ | CRU | CRU² |  |  |  |  |  |
| dong_y_chia_se_du_lieu |  | CRU¹ | R² | R² |  |  |  |  |  |
| bao_hiem_y_te |  | CRU¹ | CRU | R² |  | R² |  |  |  |
| khoa | R | R | R | R | R | R | R | R | CRUD |
| phong_kham |  | R | R | R | R |  | R | R | CRUD |
| bac_si | R | R | R | RU⁴ | R | R | R | R | CRUD |
| lich_lam_viec |  | R | R | R |  |  |  | CRUD | R |
| dich_vu_kham | R | R | R | R |  | R |  | R | CRUD |
| danh_muc_can_lam_sang | R | R | R | R | R | R |  | R | CRUD |
| danh_muc_thuoc |  |  |  | R |  | R | R | R | CRUD |
| tu_khoa_trieu_chung |  |  |  |  |  |  |  | R | CRUD |
| lich_hen |  | CRU¹ | CRU | RU² |  |  |  | R³ |  |
| lich_su_trang_thai_lich_hen |  | R¹ | CR | CR² |  |  |  | R³ |  |
| goi_y_chuyen_khoa |  | CR¹ | RU | R² |  |  |  | R³ |  |
| so_thu_tu |  | R¹ | CRU | R² |  |  |  |  |  |
| lan_kham |  | R¹ | R² | CRUD⁴ | R² | R² |  | R³ |  |
| chan_doan |  | R¹ |  | CRU⁴ |  |  |  | R³ |  |
| chi_dinh_can_lam_sang |  | R¹ |  | CRUD⁴ | RU | R² |  | R³ |  |
| ket_qua_can_lam_sang |  | R¹ |  | R⁴ | CRU |  |  |  |  |
| hen_tai_kham |  | R¹ | R² | CRU⁴ |  |  |  | R³ |  |
| don_thuoc |  | R¹ |  | CRUD⁴ |  | R² | RU | R³ |  |
| chi_tiet_don_thuoc |  | R¹ |  | CRUD⁴ |  | R² | R |  |  |
| phat_thuoc |  | R¹ |  |  |  |  | CR | R³ |  |
| hoa_don |  | R¹ | R² | R⁴ |  | CRUD |  | R³ |  |
| chi_tiet_hoa_don |  | R¹ |  |  |  | R |  | R³ |  |
| thanh_toan |  | CR¹ |  |  |  | CRU |  | R³ |  |
| giay_to_benh_vien |  | CRU¹ | RU | CRU² |  |  |  | R³ |  |
| tin_tuc | R | R | R | R | R | R | R | CRUD | CRUD |
| thong_bao |  | RU¹ |  |  |  |  |  |  |  |
| nhat_ky_thao_tac |  |  |  |  |  |  |  | R | R |

### Ghi chú cho ma trận

- Mọi nhân viên có quyền sửa `tai_khoan.mat_khau_bam` của chính mình (đổi mật khẩu), dù hàng `tai_khoan` chỉ ghi QT.
- Các thao tác do hệ thống thực hiện không thuộc vai trò nào: tạo `thong_bao`, `hoa_don` và `chi_tiet_hoa_don` (tự động theo chỉ định và đơn thuốc), chuyển `don_thuoc` sang `DA_THANH_TOAN_CHO_NHAN`, tạo dòng `nhat_ky_thao_tac`. Vì vậy Thu ngân chỉ R trên `chi_tiet_hoa_don`; CRUD của Thu ngân trên `hoa_don` gồm hủy hóa đơn chưa thanh toán và xử lý thu.
- Người bệnh “đăng nhập” bằng CCCD (mô phỏng VNeID): khi đăng nhập lần đầu, hệ thống tạo `nguoi_benh` thay họ, do đó NB có C ở `nguoi_benh`.
- `lich_su_trang_thai_lich_hen`, `phat_thuoc`, `nhat_ky_thao_tac` chỉ thêm: không ai có U hay D. `ket_qua_can_lam_sang` sửa được nhưng phải ghi nhật ký với dữ liệu cũ và mới.
- Hồ sơ y tế không có D ngoài `lan_kham` (hủy lần khám), `chi_dinh_can_lam_sang` (hủy khi chưa có kết quả) và `don_thuoc` (hủy khi chưa thanh toán), đều là chuyển trạng thái, không xóa dòng.
- QL và QT không có R trực tiếp trên bảng y tế; thống kê qua truy vấn tổng hợp (³). QT không xem nội dung y tế dù quản lý tài khoản và danh mục.
- Bác sĩ xem hồ sơ y tế nền và lịch sử khám của người bệnh đang khám với mình (²); đây là giả định cần xác nhận ở Phần 8.

## Phần 7. Dữ liệu mẫu

Toàn bộ dữ liệu dưới đây là dữ liệu giả: tên người Việt tự đặt, CCCD dạng `079xxxxxxxxx` không thuộc người thật, số thẻ BHYT, số điện thoại và email đều không tồn tại. Ba dòng mẫu của mỗi bảng thuộc ba ca khám liền mạch (mục 7.1); khóa ngoại giữa các bảng khớp nhau, và mọi giá trị trạng thái đều nằm trong Phần 3.

Cách đọc: mỗi bảng được trình bày theo chiều dọc để vừa khổ giấy. Mỗi hàng là một cột của bảng; ba cột Dòng 1, Dòng 2, Dòng 3 là ba bản ghi, ứng với Ca 1, Ca 2, Ca 3. Dưới mỗi khóa ngoại có chú thích mã nghiệp vụ hoặc tên để dễ đối chiếu. Thời điểm TIMESTAMPTZ ghi theo giờ UTC (giờ Việt Nam = UTC + 7); `ngay_hen` và `gio_hen` ghi theo giờ địa phương. Các id mới của Phần 7 không trùng với các ví dụ rời ở Phần 2; chỉ người bệnh Trần Thị Bình, thân nhân Lê Minh Khoa, khoa Nội tim mạch, phòng P203, bác sĩ Nguyễn Văn An, dịch vụ khám tim mạch, thuốc Amlodipin và thẻ BHYT dùng lại id ở Phần 2.

### 7.1 Ba ca khám và dòng thời gian

| Ca | Người khám (người đặt) | Khoa và loại hình | Điểm cần minh họa | Trạng thái cuối |
|---|---|---|---|---|
| 1 | Trần Thị Bình (tự đặt) | Nội tim mạch; BHYT 80% | Gợi ý đúng khoa, có dấu hiệu nặng nên y tá chuyển lên bác sĩ; dùng thẻ BHYT; đã thanh toán và nhận thuốc | Lịch HOAN_THANH; đơn DA_NHAN |
| 2 | Lê Minh Khoa, 8 tuổi (mẹ là Trần Thị Bình đặt hộ) | Nhi; dịch vụ | Thân nhân chưa có CCCD; người đặt chọn khác khoa hệ thống gợi ý, y tá đổi sang Nhi; thanh toán thất bại một lần rồi thu tại quầy | Lịch HOAN_THANH; đơn DA_THANH_TOAN_CHO_NHAN |
| 3 | Nguyễn Văn Cường (tự đặt) | Tai mũi họng; theo yêu cầu | Đang khám, hóa đơn và đơn thuốc chưa thanh toán | Lịch DA_DEN_KHAM; đơn CHUA_THANH_TOAN |

| Mốc (UTC) | Ca 1 | Ca 2 | Ca 3 |
|---|---|---|---|
| Đặt lịch (`lich_hen.tao_luc`) | 2026-10-26T02:00Z | 2026-10-26T02:30Z | 2026-10-26T04:00Z |
| Y tá hoặc bác sĩ duyệt | 02:20Z y tá chuyển bác sĩ (co_dau_hieu_nang = true); 03:10Z bác sĩ xác nhận (DA_XAC_NHAN) | 03:00Z y tá đổi từ Nội tổng quát sang Nhi (DA_DOI_KHOA) | 04:30Z y tá xác nhận (DA_XAC_NHAN) |
| Tiếp nhận, cấp số thứ tự (DA_DEN_KHAM) | 2026-11-05T01:15Z | 2026-11-06T01:50Z | 2026-11-06T03:15Z |
| Bắt đầu khám (`lan_kham.bat_dau_luc`) | 2026-11-05T01:40Z | 2026-11-06T02:05Z | 2026-11-06T03:35Z |
| Lập hóa đơn (`hoa_don.tao_luc`) | 2026-11-05T01:50Z (chỉ định cận lâm sàng) | 2026-11-06T02:25Z (kê đơn) | 2026-11-06T03:40Z (chỉ định cận lâm sàng) |
| Kê đơn (`don_thuoc.tao_luc`) | 2026-11-05T02:08Z | 2026-11-06T02:25Z | 2026-11-06T03:50Z |
| Kết thúc khám | 2026-11-05T02:10Z | 2026-11-06T02:30Z | Đang khám |
| Thanh toán | 02:15Z thành công (ví điện tử, người bệnh tự trả) | 02:38Z thất bại (thẻ); 02:45Z thành công (tiền mặt tại quầy) | Chưa thanh toán |
| Phát thuốc (`phat_thuoc.tao_luc`) | 2026-11-05T02:25Z | Chưa nhận | Chưa thanh toán nên chưa phát được |

### 7.2 Dữ liệu tham chiếu (danh mục dùng làm khóa ngoại)

Các dòng danh mục dưới đây giả định đã có sẵn trong các bảng tương ứng; chúng cho biết id mà các khóa ngoại ở 7.3 đến 7.11 trỏ tới.

| Bảng | Mã hoặc tên | id |
|---|---|---|
| khoa | NOI_TIM_MACH: Khoa Nội tim mạch | `6a7b8c9d-0006-4fa0-9b1c-2d3e4f5a6b7c` |
| khoa | NOI_TONG_QUAT: Khoa Nội tổng quát | `6a7b8c9d-0016-4fa0-9b1c-2d3e4f5a6b7d` |
| khoa | NHI: Khoa Nhi | `6a7b8c9d-0026-4fa0-9b1c-2d3e4f5a6b7e` |
| khoa | TAI_MUI_HONG: Khoa Tai mũi họng | `6a7b8c9d-0036-4fa0-9b1c-2d3e4f5a6b7f` |
| phong_kham | P203: Phòng khám tim mạch 2 (Khoa Nội tim mạch) | `7b8c9d0e-0007-4ab1-8c2d-3e4f5a6b7c8d` |
| phong_kham | P105: Phòng khám nhi 1 (Khoa Nhi) | `7b8c9d0e-0017-4ab1-8c2d-3e4f5a6b7c8e` |
| phong_kham | P118: Phòng khám tai mũi họng (Khoa Tai mũi họng) | `7b8c9d0e-0027-4ab1-8c2d-3e4f5a6b7c8f` |
| bac_si | BS. Nguyễn Văn An (Khoa Nội tim mạch) | `8c9d0e1f-0008-4bc2-9d3e-4f5a6b7c8d9e` |
| bac_si | BS. Phạm Thanh Hà (Khoa Nhi) | `8c9d0e1f-0018-4bc2-9d3e-4f5a6b7c8d9f` |
| bac_si | BS. Võ Quốc Hùng (Khoa Tai mũi họng) | `8c9d0e1f-0028-4bc2-9d3e-4f5a6b7c8da0` |
| dich_vu_kham | KHAM_TM_BHYT: Khám tim mạch (BHYT), 38700 VND | `0e1f2a3b-0010-4de4-9f5a-6b7c8d9e0f1a` |
| dich_vu_kham | KHAM_NHI_DV: Khám nhi (dịch vụ), 150000 VND | `0e1f2a3b-0020-4de4-9f5a-6b7c8d9e0f1b` |
| dich_vu_kham | KHAM_TMH_YC: Khám tai mũi họng (theo yêu cầu), 300000 VND | `0e1f2a3b-0030-4de4-9f5a-6b7c8d9e0f1c` |
| danh_muc_thuoc | T0001: Amlodipin 5mg, 1200 VND/viên | `2a3b4c5d-0012-4fa6-9b7c-8d9e0f1a2b3c` |
| danh_muc_thuoc | T0102: Siro ho thảo dược 100ml, 45000 VND/chai | `2a3b4c5d-0022-4fa6-9b7c-8d9e0f1a2b3d` |
| danh_muc_thuoc | T0203: Xịt mũi nước muối sinh lý 0,9%, 35000 VND/chai | `2a3b4c5d-0032-4fa6-9b7c-8d9e0f1a2b3e` |
| bao_hiem_y_te | Thẻ DN4797912345678 của Trần Thị Bình, hưởng 80%, 2026-01-01 đến 2026-12-31 | `5f6a7b8c-0005-4e9f-8a0b-1c2d3e4f5a6b` |
| than_nhan | Trần Thị Bình là mẹ (CON) của Lê Minh Khoa, liên kết còn hiệu lực | `2c3d4e5f-0002-4b6c-9d7e-8f9a0b1c2d3e` |
| nhan_vien | Đặng Thị Lan (NV0301), vai trò THU_NGAN | `9a8b7c6d-0201-4a3b-8c2d-1e0f9a8b7c6d` |
| nhan_vien | Bùi Văn Tâm (NV0401), vai trò PHAT_THUOC | `9a8b7c6d-0301-4a3b-8c2d-1e0f9a8b7c6d` |

### 7.3 Bảng nguoi_benh

| Cột | Dòng 1 | Dòng 2 | Dòng 3 |
|---|---|---|---|
| `id` | `1b2c3d4e-0001-4a5b-8c6d-7e8f9a0b1c2d` | `1b2c3d4e-0002-4a5b-8c6d-7e8f9a0b1c2d` | `1b2c3d4e-0003-4a5b-8c6d-7e8f9a0b1c2d` |
| `ma_benh_nhan` | `BN00012345` | `BN00012346` | `BN00012347` |
| `ma_cccd` | `079012345678` | NULL | `079088112233` |
| `ho_ten` | Trần Thị Bình | Lê Minh Khoa | Nguyễn Văn Cường |
| `ngay_sinh` | `1985-03-14` | `2018-06-09` | `1978-07-22` |
| `gioi_tinh` | `NU` | `NAM` | `NAM` |
| `so_dien_thoai` | `0912345678` | `0912345678` | `0987654321` |
| `email` | `binh.tt@example.vn` | NULL | `cuong.nv@example.vn` |
| `dia_chi` | 12 Đường A, Phường 4, Quận 10, TP.HCM | 12 Đường A, Phường 4, Quận 10, TP.HCM | 45 Đường B, Phường 12, Quận 5, TP.HCM |
| `nguon_tao` | `DANG_KY_TRUC_TUYEN` | `DANG_KY_TRUC_TUYEN` | `DANG_KY_TRUC_TUYEN` |
| `dang_nhap_cuoi_luc` | `2026-10-26T01:55:00Z` | NULL | `2026-10-26T03:55:00Z` |
| `tao_luc` | `2026-10-20T03:00:00Z` | `2026-10-20T03:10:00Z` | `2026-10-26T03:50:00Z` |
| `cap_nhat_luc` | `2026-10-26T01:55:00Z` | `2026-10-20T03:10:00Z` | `2026-10-26T03:55:00Z` |
| `xoa_luc` | NULL | NULL | NULL |

Dòng 2 là thân nhân dưới 14 tuổi nên `ma_cccd` để NULL; hồ sơ do người mẹ tạo nên số điện thoại nhận thông báo là số của mẹ, và không có `dang_nhap_cuoi_luc` vì thân nhân không tự đăng nhập.

### 7.4 Bảng lich_hen

| Cột | Dòng 1 | Dòng 2 | Dòng 3 |
|---|---|---|---|
| `id` | `c1d2e3f4-1001-4a01-8b02-0c1d2e3f4a01` | `c1d2e3f4-1002-4a02-8b02-0c1d2e3f4a02` | `c1d2e3f4-1003-4a03-8b02-0c1d2e3f4a03` |
| `ma_lich_hen` | `LH261105K4P7` | `LH261106T9X2` | `LH261106M3Q8` |
| `ma_qr` | `OdR-zLBztqrYi_rvGbv_a_Qx0nby3pkIrvS2pn9v7rZ` | `HTaTc8eH2yOynBcI5QyVHjjMtMgl7K0KlBmoKiGBHOt` | `X5ty_EbK4ko-3Wumw5225KRoqJE9vtYnmhmUtry06Yu` |
| `nguoi_benh_id` | `1b2c3d4e-0001-4a5b-8c6d-7e8f9a0b1c2d`<br>Trần Thị Bình (BN00012345) | `1b2c3d4e-0002-4a5b-8c6d-7e8f9a0b1c2d`<br>Lê Minh Khoa (BN00012346) | `1b2c3d4e-0003-4a5b-8c6d-7e8f9a0b1c2d`<br>Nguyễn Văn Cường (BN00012347) |
| `nguoi_dat_id` | `1b2c3d4e-0001-4a5b-8c6d-7e8f9a0b1c2d`<br>Trần Thị Bình (BN00012345) | `1b2c3d4e-0001-4a5b-8c6d-7e8f9a0b1c2d`<br>Trần Thị Bình (BN00012345) | `1b2c3d4e-0003-4a5b-8c6d-7e8f9a0b1c2d`<br>Nguyễn Văn Cường (BN00012347) |
| `loai_hinh_kham` | `BHYT` | `DICH_VU` | `THEO_YEU_CAU` |
| `khoa_id` | `6a7b8c9d-0006-4fa0-9b1c-2d3e4f5a6b7c`<br>Khoa Nội tim mạch | `6a7b8c9d-0026-4fa0-9b1c-2d3e4f5a6b7e`<br>Khoa Nhi | `6a7b8c9d-0036-4fa0-9b1c-2d3e4f5a6b7f`<br>Khoa Tai mũi họng |
| `ngay_hen` | `2026-11-05` | `2026-11-06` | `2026-11-06` |
| `gio_hen` | `08:30` | `09:00` | `10:30` |
| `ly_do_kham` | Đau tức ngực bên trái, khó thở khi leo cầu thang | Sốt cao 2 ngày, ho có đờm, ăn kém | Đau họng, nghẹt mũi kéo dài 3 tuần |
| `trang_thai` | `HOAN_THANH` | `HOAN_THANH` | `DA_DEN_KHAM` |
| `tao_luc` | `2026-10-26T02:00:00Z` | `2026-10-26T02:30:00Z` | `2026-10-26T04:00:00Z` |
| `cap_nhat_luc` | `2026-11-05T02:10:00Z` | `2026-11-06T02:30:00Z` | `2026-11-06T03:15:00Z` |

Dòng 2 do mẹ đặt hộ nên `nguoi_dat_id` khác `nguoi_benh_id`, và `khoa_id` là Nhi (khoa sau khi y tá đổi). `ma_lich_hen` có phần ngày chính là ngày hẹn (yyMMdd), theo ví dụ ở Phần 2.

### 7.5 Bảng goi_y_chuyen_khoa

| Cột | Dòng 1 | Dòng 2 | Dòng 3 |
|---|---|---|---|
| `id` | `e3f4a5b6-1001-4c03-8d04-2e3f4a5b6c01` | `e3f4a5b6-1002-4c03-8d04-2e3f4a5b6c02` | `e3f4a5b6-1003-4c03-8d04-2e3f4a5b6c03` |
| `lich_hen_id` | `c1d2e3f4-1001-4a01-8b02-0c1d2e3f4a01`<br>LH261105K4P7 | `c1d2e3f4-1002-4a02-8b02-0c1d2e3f4a02`<br>LH261106T9X2 | `c1d2e3f4-1003-4a03-8b02-0c1d2e3f4a03`<br>LH261106M3Q8 |
| `mo_ta_trieu_chung` | Đau tức ngực bên trái, khó thở khi leo cầu thang | Bé sốt cao 2 ngày, ho có đờm, ăn kém | Đau họng, nghẹt mũi kéo dài 3 tuần, thỉnh thoảng ù tai |
| `phuong_thuc_goi_y` | `TU_KHOA` | `TU_KHOA` | `TU_KHOA` |
| `khoa_goi_y_id` | `6a7b8c9d-0006-4fa0-9b1c-2d3e4f5a6b7c`<br>Khoa Nội tim mạch | `6a7b8c9d-0026-4fa0-9b1c-2d3e4f5a6b7e`<br>Khoa Nhi | `6a7b8c9d-0036-4fa0-9b1c-2d3e4f5a6b7f`<br>Khoa Tai mũi họng |
| `ly_do_goi_y` | Khớp từ khóa: đau ngực, khó thở | Khớp từ khóa: sốt cao, ho có đờm | Khớp từ khóa: đau họng, nghẹt mũi, ù tai |
| `co_dau_hieu_nang` | `true` | `false` | `false` |
| `khoa_nguoi_benh_chon_id` | `6a7b8c9d-0006-4fa0-9b1c-2d3e4f5a6b7c`<br>Khoa Nội tim mạch | `6a7b8c9d-0016-4fa0-9b1c-2d3e4f5a6b7d`<br>Khoa Nội tổng quát | `6a7b8c9d-0036-4fa0-9b1c-2d3e4f5a6b7f`<br>Khoa Tai mũi họng |
| `khoa_y_ta_xac_nhan_id` | `6a7b8c9d-0006-4fa0-9b1c-2d3e4f5a6b7c`<br>Khoa Nội tim mạch | `6a7b8c9d-0026-4fa0-9b1c-2d3e4f5a6b7e`<br>Khoa Nhi | `6a7b8c9d-0036-4fa0-9b1c-2d3e4f5a6b7f`<br>Khoa Tai mũi họng |
| `tao_luc` | `2026-10-26T02:00:00Z` | `2026-10-26T02:30:00Z` | `2026-10-26T04:00:00Z` |
| `cap_nhat_luc` | `2026-10-26T03:10:00Z` | `2026-10-26T03:00:00Z` | `2026-10-26T04:30:00Z` |

Dòng 1 và 3: người bệnh chấp nhận gợi ý và y tá giữ nguyên. Dòng 2: người đặt chọn khác gợi ý (Nội tổng quát) và y tá đổi về đúng khoa gợi ý (Nhi); với ba dòng này tỷ lệ người bệnh chấp nhận gợi ý là 2/3 và tỷ lệ y tá giữ nguyên gợi ý là 3/3. Dòng 1 có `co_dau_hieu_nang` = true nên ca đi qua bác sĩ.

### 7.6 Bảng lan_kham

| Cột | Dòng 1 | Dòng 2 | Dòng 3 |
|---|---|---|---|
| `id` | `a5b6c7d8-1001-4e05-8f06-4a5b6c7d8e01` | `a5b6c7d8-1002-4e05-8f06-4a5b6c7d8e02` | `a5b6c7d8-1003-4e05-8f06-4a5b6c7d8e03` |
| `ma_lan_kham` | `LK2611050001` | `LK2611060001` | `LK2611060002` |
| `lich_hen_id` | `c1d2e3f4-1001-4a01-8b02-0c1d2e3f4a01`<br>LH261105K4P7 | `c1d2e3f4-1002-4a02-8b02-0c1d2e3f4a02`<br>LH261106T9X2 | `c1d2e3f4-1003-4a03-8b02-0c1d2e3f4a03`<br>LH261106M3Q8 |
| `nguoi_benh_id` | `1b2c3d4e-0001-4a5b-8c6d-7e8f9a0b1c2d`<br>Trần Thị Bình (BN00012345) | `1b2c3d4e-0002-4a5b-8c6d-7e8f9a0b1c2d`<br>Lê Minh Khoa (BN00012346) | `1b2c3d4e-0003-4a5b-8c6d-7e8f9a0b1c2d`<br>Nguyễn Văn Cường (BN00012347) |
| `bac_si_id` | `8c9d0e1f-0008-4bc2-9d3e-4f5a6b7c8d9e`<br>BS. Nguyễn Văn An | `8c9d0e1f-0018-4bc2-9d3e-4f5a6b7c8d9f`<br>BS. Phạm Thanh Hà | `8c9d0e1f-0028-4bc2-9d3e-4f5a6b7c8da0`<br>BS. Võ Quốc Hùng |
| `phong_kham_id` | `7b8c9d0e-0007-4ab1-8c2d-3e4f5a6b7c8d`<br>P203 | `7b8c9d0e-0017-4ab1-8c2d-3e4f5a6b7c8e`<br>P105 | `7b8c9d0e-0027-4ab1-8c2d-3e4f5a6b7c8f`<br>P118 |
| `dich_vu_kham_id` | `0e1f2a3b-0010-4de4-9f5a-6b7c8d9e0f1a`<br>KHAM_TM_BHYT | `0e1f2a3b-0020-4de4-9f5a-6b7c8d9e0f1b`<br>KHAM_NHI_DV | `0e1f2a3b-0030-4de4-9f5a-6b7c8d9e0f1c`<br>KHAM_TMH_YC |
| `bao_hiem_y_te_id` | `5f6a7b8c-0005-4e9f-8a0b-1c2d3e4f5a6b`<br>Thẻ DN4797912345678 | NULL | NULL |
| `benh_su` | Đau ngực từng cơn 2 tuần nay | Sốt cao 2 ngày, ho có đờm, ăn kém | Đau họng, nghẹt mũi kéo dài 3 tuần |
| `kham_lam_sang` | Tim đều, phổi trong | Họng đỏ, phổi có ran ẩm nhẹ, không co kéo | NULL |
| `loi_dan` | Uống thuốc đều, hạn chế muối | Uống nhiều nước, theo dõi nhiệt độ, tái khám nếu sốt cao kéo dài | NULL |
| `trang_thai` | `HOAN_THANH` | `HOAN_THANH` | `DANG_KHAM` |
| `bat_dau_luc` | `2026-11-05T01:40:00Z` | `2026-11-06T02:05:00Z` | `2026-11-06T03:35:00Z` |
| `ket_thuc_luc` | `2026-11-05T02:10:00Z` | `2026-11-06T02:30:00Z` | NULL |
| `tao_luc` | `2026-11-05T01:40:00Z` | `2026-11-06T02:05:00Z` | `2026-11-06T03:35:00Z` |
| `cap_nhat_luc` | `2026-11-05T02:10:00Z` | `2026-11-06T02:30:00Z` | `2026-11-06T03:50:00Z` |
| `xoa_luc` | NULL | NULL | NULL |

Mỗi lần khám gắn đúng một bác sĩ, nên ba người bệnh có ba bác sĩ khác nhau trong mục “Bác sĩ của tôi”. Chỉ dòng 1 dùng thẻ BHYT. Dòng 3 vẫn DANG_KHAM nên chưa có `ket_thuc_luc`.

### 7.7 Bảng don_thuoc

| Cột | Dòng 1 | Dòng 2 | Dòng 3 |
|---|---|---|---|
| `id` | `f0a1b2c3-1001-4d0a-9e0b-9f0a1b2c3d01` | `f0a1b2c3-1002-4d0a-9e0b-9f0a1b2c3d02` | `f0a1b2c3-1003-4d0a-9e0b-9f0a1b2c3d03` |
| `ma_don_thuoc` | `DT2611050001` | `DT2611060001` | `DT2611060002` |
| `ma_qr` | `2pSm6Kbxs1S6g5uY0cYUjtQQr09l0MLmO85RW0COHS7` | `crAbqkRCHfbFIq5TJJ21s9wJq_f-xw5KXAbNDNpBHS3` | `fyDTsi8N5pe4jNfwq9Ux9YXIY4eYhLp2ojbSjWBEu7C` |
| `lan_kham_id` | `a5b6c7d8-1001-4e05-8f06-4a5b6c7d8e01`<br>LK2611050001 | `a5b6c7d8-1002-4e05-8f06-4a5b6c7d8e02`<br>LK2611060001 | `a5b6c7d8-1003-4e05-8f06-4a5b6c7d8e03`<br>LK2611060002 |
| `trang_thai` | `DA_NHAN` | `DA_THANH_TOAN_CHO_NHAN` | `CHUA_THANH_TOAN` |
| `han_nhan_den` | `2026-11-12` | `2026-11-13` | `2026-11-13` |
| `ghi_chu` | Uống thuốc đều, đo huyết áp mỗi sáng | Cho bé uống thuốc sau ăn | NULL |
| `tao_luc` | `2026-11-05T02:08:00Z` | `2026-11-06T02:25:00Z` | `2026-11-06T03:50:00Z` |
| `cap_nhat_luc` | `2026-11-05T02:25:00Z` | `2026-11-06T02:45:00Z` | `2026-11-06T03:50:00Z` |

Ba dòng thể hiện ba trạng thái của đơn: đã nhận (đã có dòng `phat_thuoc`, xem 7.11), đã thanh toán chờ nhận, chưa thanh toán. Hạn nhận thuốc đặt bằng ngày khám cộng 7 ngày.

### 7.8 Bảng chi_tiet_don_thuoc

| Cột | Dòng 1 | Dòng 2 | Dòng 3 |
|---|---|---|---|
| `id` | `a1b2c3d4-1001-4e0b-8f0c-0a1b2c3d4e01` | `a1b2c3d4-1002-4e0b-8f0c-0a1b2c3d4e02` | `a1b2c3d4-1003-4e0b-8f0c-0a1b2c3d4e03` |
| `don_thuoc_id` | `f0a1b2c3-1001-4d0a-9e0b-9f0a1b2c3d01`<br>DT2611050001 | `f0a1b2c3-1002-4d0a-9e0b-9f0a1b2c3d02`<br>DT2611060001 | `f0a1b2c3-1003-4d0a-9e0b-9f0a1b2c3d03`<br>DT2611060002 |
| `danh_muc_thuoc_id` | `2a3b4c5d-0012-4fa6-9b7c-8d9e0f1a2b3c`<br>Amlodipin 5mg | `2a3b4c5d-0022-4fa6-9b7c-8d9e0f1a2b3d`<br>Siro ho thảo dược 100ml | `2a3b4c5d-0032-4fa6-9b7c-8d9e0f1a2b3e`<br>Xịt mũi nước muối sinh lý 0,9% |
| `so_luong` | `30` | `1` | `1` |
| `lieu_dung` | 1 viên/lần, 1 lần/ngày | 5 ml/lần, 3 lần/ngày | 2 nhát mỗi bên mũi/lần, 3 lần/ngày |
| `cach_dung` | Uống buổi sáng, sau ăn | Uống sau ăn | Xịt vào từng bên mũi |
| `so_ngay_dung` | `30` | `5` | `7` |
| `tao_luc` | `2026-11-05T02:08:00Z` | `2026-11-06T02:25:00Z` | `2026-11-06T03:50:00Z` |
| `cap_nhat_luc` | `2026-11-05T02:08:00Z` | `2026-11-06T02:25:00Z` | `2026-11-06T03:50:00Z` |

Mỗi đơn mẫu chỉ có một dòng thuốc cho gọn; đơn thực tế có thể có nhiều dòng, mỗi dòng một thuốc khác nhau. Liều dùng trong dữ liệu mẫu chỉ để minh họa kiểu dữ liệu, không phải chỉ dẫn điều trị.

### 7.9 Bảng hoa_don

| Cột | Dòng 1 | Dòng 2 | Dòng 3 |
|---|---|---|---|
| `id` | `b2c3d4e5-1001-4f0c-9a0d-1b2c3d4e5f01` | `b2c3d4e5-1002-4f0c-9a0d-1b2c3d4e5f02` | `b2c3d4e5-1003-4f0c-9a0d-1b2c3d4e5f03` |
| `ma_hoa_don` | `HD2611050001` | `HD2611060001` | `HD2611060002` |
| `lan_kham_id` | `a5b6c7d8-1001-4e05-8f06-4a5b6c7d8e01`<br>LK2611050001 | `a5b6c7d8-1002-4e05-8f06-4a5b6c7d8e02`<br>LK2611060001 | `a5b6c7d8-1003-4e05-8f06-4a5b6c7d8e03`<br>LK2611060002 |
| `nguoi_benh_id` | `1b2c3d4e-0001-4a5b-8c6d-7e8f9a0b1c2d`<br>Trần Thị Bình (BN00012345) | `1b2c3d4e-0002-4a5b-8c6d-7e8f9a0b1c2d`<br>Lê Minh Khoa (BN00012346) | `1b2c3d4e-0003-4a5b-8c6d-7e8f9a0b1c2d`<br>Nguyễn Văn Cường (BN00012347) |
| `muc_huong_ap_dung` | `80` | `0` | `0` |
| `tong_tien` | `159700` | `195000` | `585000` |
| `tong_bhyt_chi_tra` | `127760` | `0` | `0` |
| `tong_nguoi_benh_tra` | `31940` | `195000` | `585000` |
| `trang_thai` | `DA_THANH_TOAN` | `DA_THANH_TOAN` | `CHUA_THANH_TOAN` |
| `da_thanh_toan_luc` | `2026-11-05T02:15:00Z` | `2026-11-06T02:45:00Z` | NULL |
| `ly_do_huy` | NULL | NULL | NULL |
| `tao_luc` | `2026-11-05T01:50:00Z` | `2026-11-06T02:25:00Z` | `2026-11-06T03:40:00Z` |
| `cap_nhat_luc` | `2026-11-05T02:15:00Z` | `2026-11-06T02:45:00Z` | `2026-11-06T03:50:00Z` |

Cách tính ba cột tổng từ các khoản của hóa đơn (bảng `chi_tiet_hoa_don`, không liệt kê riêng ở đây). Mỗi ô ghi: số lượng × đơn giá = thành tiền; BHYT chi trả = làm tròn(thành tiền × mức hưởng / 100) nếu khoản được BHYT và lần khám thuộc loại BHYT.

| Khoản | Hóa đơn 1 (mức hưởng 80%) | Hóa đơn 2 (mức hưởng 0) | Hóa đơn 3 (mức hưởng 0) |
|---|---|---|---|
| Khoản 1 | Khám tim mạch (BHYT): 1 × 38700 = 38700; BHYT 30960 | Khám nhi (dịch vụ): 1 × 150000 = 150000; BHYT 0 | Khám tai mũi họng (theo yêu cầu): 1 × 300000 = 300000; BHYT 0 |
| Khoản 2 | Công thức máu: 1 × 85000 = 85000; BHYT 68000 | Siro ho thảo dược 100ml: 1 × 45000 = 45000; BHYT 0 | Nội soi tai mũi họng: 1 × 250000 = 250000; BHYT 0 |
| Khoản 3 | Amlodipin 5mg: 30 × 1200 = 36000; BHYT 28800 | — | Xịt mũi nước muối sinh lý 0,9%: 1 × 35000 = 35000; BHYT 0 |
| tong_tien | 159700 | 195000 | 585000 |
| tong_bhyt_chi_tra | 127760 | 0 | 0 |
| tong_nguoi_benh_tra | 31940 | 195000 | 585000 |

### 7.10 Bảng thanh_toan

| Cột | Dòng 1 | Dòng 2 | Dòng 3 |
|---|---|---|---|
| `id` | `d4e5f6a7-1001-4a0e-8b0f-3d4e5f6a7b01` | `d4e5f6a7-1002-4a0e-8b0f-3d4e5f6a7b02` | `d4e5f6a7-1003-4a0e-8b0f-3d4e5f6a7b03` |
| `ma_giao_dich` | `GD261105Q8W3ZA` | `GD261106H2K7NB` | `GD261106C5R9TD` |
| `hoa_don_id` | `b2c3d4e5-1001-4f0c-9a0d-1b2c3d4e5f01`<br>HD2611050001 | `b2c3d4e5-1002-4f0c-9a0d-1b2c3d4e5f02`<br>HD2611060001 | `b2c3d4e5-1002-4f0c-9a0d-1b2c3d4e5f02`<br>HD2611060001 |
| `so_tien` | `31940` | `195000` | `195000` |
| `phuong_thuc` | `VI_DIEN_TU` | `THE` | `TIEN_MAT` |
| `trang_thai` | `THANH_CONG` | `THAT_BAI` | `THANH_CONG` |
| `nhan_vien_thu_id` | NULL | NULL | `9a8b7c6d-0201-4a3b-8c2d-1e0f9a8b7c6d`<br>Đặng Thị Lan (NV0301) |
| `nguoi_tra_id` | `1b2c3d4e-0001-4a5b-8c6d-7e8f9a0b1c2d`<br>Trần Thị Bình (BN00012345) | `1b2c3d4e-0001-4a5b-8c6d-7e8f9a0b1c2d`<br>Trần Thị Bình (BN00012345) | NULL |
| `thanh_toan_luc` | `2026-11-05T02:15:00Z` | NULL | `2026-11-06T02:45:00Z` |
| `ly_do_that_bai` | NULL | Thẻ không đủ hạn mức (mô phỏng) | NULL |
| `tao_luc` | `2026-11-05T02:14:00Z` | `2026-11-06T02:38:00Z` | `2026-11-06T02:44:00Z` |
| `cap_nhat_luc` | `2026-11-05T02:15:00Z` | `2026-11-06T02:38:30Z` | `2026-11-06T02:45:00Z` |

Dòng 1 là thanh toán thành công của hóa đơn 1. Dòng 2 và dòng 3 cùng thuộc hóa đơn 2: lần đầu thất bại, lần sau thu tiền mặt tại quầy thành công. Hóa đơn 3 chưa có dòng thanh toán nào. Mỗi hóa đơn có đúng một dòng THANH_CONG và `so_tien` bằng `tong_nguoi_benh_tra`.

### 7.11 Bảng phat_thuoc (bổ trợ để hoàn tất luồng)

Bảng này không nằm trong tám bảng bắt buộc nhưng cần một dòng để thấy luồng kết thúc ở bước phát thuốc: đơn của ca 1 sau khi quét QR hợp lệ.

| Cột | Dòng 1 |
|---|---|
| `id` | `c3d4e5f6-1001-4b0d-8c0e-2c3d4e5f6a01` |
| `don_thuoc_id` | `f0a1b2c3-1001-4d0a-9e0b-9f0a1b2c3d01`<br>DT2611050001 |
| `nhan_vien_phat_id` | `9a8b7c6d-0301-4a3b-8c2d-1e0f9a8b7c6d`<br>Bùi Văn Tâm (NV0401) |
| `ghi_chu` | NULL |
| `tao_luc` | `2026-11-05T02:25:00Z` |

### 7.12 Kiểm tra nhất quán của dữ liệu mẫu

- Mọi khóa ngoại trong 8 bảng (và `phat_thuoc`) trỏ tới id có trong 7.2 hoặc trong các bảng mẫu; không có id trùng nhau.
- Bác sĩ, phòng khám và dịch vụ khám của mỗi lần khám đều thuộc đúng khoa hiện tại của lịch hẹn; dịch vụ khám khớp loại hình khám.
- Thẻ BHYT thuộc đúng người khám và còn hiệu lực vào ngày hẹn; chỉ lần khám loại BHYT có `bao_hiem_y_te_id`.
- Người đặt hộ (ca 2) có liên kết còn hiệu lực trong `than_nhan`; thân nhân dưới 14 tuổi không có CCCD.
- `tong_tien`, `tong_bhyt_chi_tra`, `tong_nguoi_benh_tra` khớp phép tính ở 7.9; `so_tien` của thanh toán bằng `tong_nguoi_benh_tra`.
- Trạng thái khớp nhau: hóa đơn DA_THANH_TOAN thì đơn thuốc ở DA_THANH_TOAN_CHO_NHAN hoặc DA_NHAN; hóa đơn CHUA_THANH_TOAN thì đơn ở CHUA_THANH_TOAN; lịch HOAN_THANH khi lần khám HOAN_THANH.
- Mỗi lịch hẹn có tối đa một lần khám, mỗi lần khám tối đa một đơn thuốc và một hóa đơn, mỗi hóa đơn tối đa một thanh toán THANH_CONG; thứ tự thời gian của các mốc tăng dần trong từng ca.

## Phần 8. Kiểm tra chéo và giả định

Phần này gồm bốn mục: bảng đối chiếu chức năng với bảng và cột phục vụ (8.1), kết quả kiểm tra chéo giữa các phần của tài liệu (8.2), danh sách giả định đã đặt ra (8.3) và danh sách câu hỏi cần xác nhận (8.4).

### 8.1 Bảng đối chiếu chức năng với bảng và cột phục vụ

Mỗi chức năng của hệ thống (gom theo 11 nhóm nghiệp vụ trong đề bài) được đối chiếu với bảng và cột chứa dữ liệu của nó. Ghi `bảng.cột` khi chỉ cột đó phục vụ chức năng; ghi tên bảng khi cả bảng phục vụ.

| STT | Nhóm nghiệp vụ | Chức năng | Vai trò | Bảng và cột phục vụ |
|---|---|---|---|---|
| 1 | Công khai | Xem tin tức, giới thiệu, hướng dẫn | Khách, mọi vai trò | `tin_tuc` (loai_noi_dung, trang_thai, xuat_ban_luc); chỉ bài DA_DANG, chưa xóa mềm |
| 2 | Công khai | Xem khoa, đội ngũ bác sĩ, bảng giá | Khách, mọi vai trò | `khoa`, `bac_si` (khoa_id, hien_thi_cong_khai) cùng `nhan_vien.ho_ten`, `dich_vu_kham`, `danh_muc_can_lam_sang` |
| 3 | 1. Đặt lịch | Đăng nhập bằng CCCD (mô phỏng VNeID), tạo hồ sơ lần đầu | Người bệnh | `nguoi_benh.ma_cccd`, `nguoi_benh.nguon_tao`, `nguoi_benh.dang_nhap_cuoi_luc` |
| 4 | 1. Đặt lịch | Đặt lịch cho bản thân hoặc thân nhân | Người bệnh | `than_nhan` (nguoi_benh_id, nguoi_than_id, quan_he); `lich_hen.nguoi_benh_id`, `lich_hen.nguoi_dat_id` |
| 5 | 1. Đặt lịch | Chọn loại hình khám (BHYT, dịch vụ, theo yêu cầu) | Người bệnh | `lich_hen.loai_hinh_kham`; `bao_hiem_y_te` (so_the_bhyt, hieu_luc_tu, hieu_luc_den) kiểm tra thẻ khi chọn BHYT; `dich_vu_kham.loai_hinh_kham` |
| 6 | 1. Đặt lịch | Chọn chuyên khoa | Người bệnh | `lich_hen.khoa_id`; `khoa` |
| 7 | 1. Đặt lịch | Chọn ngày, giờ; xem khung giờ còn chỗ | Người bệnh | `lich_hen.ngay_hen`, `lich_hen.gio_hen`; `lich_lam_viec` (ngay_lam_viec, gio_bat_dau, gio_ket_thuc, thoi_luong_khung_phut, so_luot_toi_da_moi_khung, dang_mo_dat_lich) |
| 8 | 1. Đặt lịch | Nhận phiếu hẹn có mã và QR | Người bệnh | `lich_hen.ma_lich_hen`, `lich_hen.ma_qr` |
| 9 | 1. Đặt lịch | Theo dõi, hủy lịch; nhận thông báo | Người bệnh | `lich_hen.trang_thai`; `lich_su_trang_thai_lich_hen`; `thong_bao` (nguoi_nhan_id, loai_thong_bao, da_doc) |
| 10 | 2. Gợi ý chuyên khoa | Gợi ý khoa từ mô tả triệu chứng (rule-based) | Hệ thống, Người bệnh | `tu_khoa_trieu_chung` (tu_khoa_chuan_hoa, trong_so, la_dau_hieu_nang, khoa_id); `goi_y_chuyen_khoa` (mo_ta_trieu_chung, khoa_goi_y_id, ly_do_goi_y, co_dau_hieu_nang) |
| 11 | 2. Gợi ý chuyên khoa | Lưu phương thức gợi ý (từ khóa hoặc LLM) | Hệ thống | `goi_y_chuyen_khoa.phuong_thuc_goi_y` |
| 12 | 2. Gợi ý chuyên khoa | Lưu khoa gợi ý, khoa người bệnh chọn, khoa y tá xác nhận | Hệ thống, Y tá | `goi_y_chuyen_khoa.khoa_goi_y_id`, `goi_y_chuyen_khoa.khoa_nguoi_benh_chon_id`, `goi_y_chuyen_khoa.khoa_y_ta_xac_nhan_id` |
| 13 | 2. Gợi ý chuyên khoa | Thống kê tỷ lệ chấp nhận gợi ý | Cán bộ quản lý | so sánh ba cột khoa trong `goi_y_chuyen_khoa`, nhóm theo `goi_y_chuyen_khoa.phuong_thuc_goi_y`; không đọc nội dung mô tả |
| 14 | 3. Duyệt lịch | Y tá duyệt: xác nhận, đổi khoa, từ chối | Y tá | `lich_hen.trang_thai`, `lich_hen.khoa_id`; `lich_su_trang_thai_lich_hen` (trang_thai_cu, trang_thai_moi, khoa_cu_id, khoa_moi_id, ly_do) |
| 15 | 3. Duyệt lịch | Chuyển lên bác sĩ khi ca nặng hoặc khó xác định khoa; bác sĩ quyết định | Y tá, Bác sĩ | `lich_hen.trang_thai` (CHO_BAC_SI_XEM_XET); `goi_y_chuyen_khoa.co_dau_hieu_nang`, `goi_y_chuyen_khoa.khoa_goi_y_id` rỗng khi khó xác định |
| 16 | 3. Duyệt lịch | Lưu ai duyệt, lúc nào, lý do, lịch sử đổi trạng thái | Y tá, Bác sĩ | `lich_su_trang_thai_lich_hen` (nhan_vien_thuc_hien_id, nguoi_benh_thuc_hien_id, tao_luc, ly_do) |
| 17 | 4. Tiếp nhận | Quét QR CCCD, tra cứu hồ sơ; tạo hồ sơ mới | Y tá | `nguoi_benh.ma_cccd` (tra cứu theo số CCCD đọc từ QR, không lưu ảnh QR), `nguoi_benh.nguon_tao` |
| 18 | 4. Tiếp nhận | Cấp số thứ tự | Y tá | `so_thu_tu` (lich_hen_id, phong_kham_id, ngay_kham, so_thu_tu, nhan_vien_cap_id) |
| 19 | 4. Tiếp nhận | In phiếu hướng dẫn | Y tá | `so_thu_tu.in_phieu_luc`; `phong_kham.vi_tri`; `danh_muc_can_lam_sang.huong_dan_chuan_bi` |
| 20 | 5. Y tế nền | Ghi nhận đồng ý và thu hồi chia sẻ dữ liệu | Người bệnh | `dong_y_chia_se_du_lieu` (pham_vi_du_lieu, dong_y_luc, thu_hoi_luc, nguoi_dong_y_id) |
| 21 | 5. Y tế nền | Lưu nhóm máu, tiền sử phẫu thuật, dị ứng thuốc và nguồn dữ liệu | Người bệnh, Y tá, Bác sĩ | `thong_tin_y_te_nen` (loai_thong_tin, ten_muc, nguon_du_lieu, dong_y_id) |
| 22 | 5. Y tế nền | Cảnh báo dị ứng khi kê đơn | Bác sĩ | đối chiếu `thong_tin_y_te_nen.ten_muc` (DI_UNG_THUOC) với `danh_muc_thuoc.hoat_chat` |
| 23 | 6. Khám bệnh | Mở lần khám, ghi bệnh sử, khám lâm sàng, lời dặn | Bác sĩ | `lan_kham` (bac_si_id, benh_su, kham_lam_sang, loi_dan, trang_thai) |
| 24 | 6. Khám bệnh | Chẩn đoán | Bác sĩ | `chan_doan` (loai_chan_doan, ma_icd10, ten_chan_doan) |
| 25 | 6. Khám bệnh | Chỉ định cận lâm sàng và theo dõi thực hiện | Bác sĩ, Kỹ thuật viên | `chi_dinh_can_lam_sang` (danh_muc_can_lam_sang_id, trang_thai, ky_thuat_vien_id); `danh_muc_can_lam_sang` |
| 26 | 6. Khám bệnh | Nhận kết quả cận lâm sàng | Kỹ thuật viên, Bác sĩ | `ket_qua_can_lam_sang` (ket_qua_chi_tiet, ket_luan, tep_dinh_kem_url, ky_thuat_vien_id) |
| 27 | 6. Khám bệnh | Hẹn tái khám | Bác sĩ | `hen_tai_kham` (ngay_tai_kham, lich_hen_tai_kham_id); nhắc qua `thong_bao` |
| 28 | 6. Khám bệnh | Cấp giấy chuyển tuyến | Bác sĩ | `giay_to_benh_vien` (loai_giay_to, noi_chuyen_den, trang_thai) |
| 29 | 7. Đơn thuốc QR | Kê đơn thuốc | Bác sĩ | `don_thuoc`, `chi_tiet_don_thuoc` (danh_muc_thuoc_id, so_luong, lieu_dung, cach_dung, so_ngay_dung); `danh_muc_thuoc` |
| 30 | 7. Đơn thuốc QR | Sinh mã QR đơn thuốc | Hệ thống | `don_thuoc.ma_qr`, `don_thuoc.ma_don_thuoc` |
| 31 | 7. Đơn thuốc QR | Xác thực QR: hợp lệ, đã thanh toán, chưa phát, còn hạn | Phát thuốc | `don_thuoc.ma_qr`, `don_thuoc.trang_thai`, `don_thuoc.han_nhan_den`; `phat_thuoc.don_thuoc_id` (chưa có dòng nghĩa là chưa phát) |
| 32 | 7. Đơn thuốc QR | Ghi nhận phát thuốc | Phát thuốc | `phat_thuoc` (don_thuoc_id, nhan_vien_phat_id, tao_luc) |
| 33 | 7. Đơn thuốc QR | Không quản lý tồn kho thuốc | (không có) | không có cột số lượng tồn, lô, hạn dùng ở `danh_muc_thuoc` |
| 34 | 8. Thanh toán | Lập hóa đơn nhiều khoản (khám, cận lâm sàng, thuốc) | Hệ thống, Thu ngân | `hoa_don`, `chi_tiet_hoa_don` (loai_khoan, thanh_tien) |
| 35 | 8. Thanh toán | Tách phần BHYT chi trả và phần người bệnh trả | Hệ thống | `hoa_don.muc_huong_ap_dung`, `hoa_don.tong_bhyt_chi_tra`, `hoa_don.tong_nguoi_benh_tra`; `chi_tiet_hoa_don.bhyt_chi_tra`, `chi_tiet_hoa_don.nguoi_benh_tra`; `bao_hiem_y_te.muc_huong` |
| 36 | 8. Thanh toán | Thanh toán mô phỏng và trạng thái thanh toán | Người bệnh, Thu ngân | `thanh_toan` (so_tien, phuong_thuc, trang_thai, nhan_vien_thu_id, nguoi_tra_id, thanh_toan_luc); `hoa_don.trang_thai` |
| 37 | 9. Bác sĩ của tôi | Gom hồ sơ theo từng lần khám, mỗi lần một bác sĩ | Người bệnh | `lan_kham.nguoi_benh_id`, `lan_kham.bac_si_id` (nhóm theo bác sĩ) |
| 38 | 10. Giấy tờ | Yêu cầu, duyệt, tải hoặc in giấy tờ | Người bệnh, Y tá, Bác sĩ | `giay_to_benh_vien` (loai_giay_to, trang_thai, nguoi_yeu_cau_id, nhan_vien_duyet_id, duyet_luc, tep_giay_to_url) |
| 39 | 11. Quản trị | Cấp tài khoản, vô hiệu, đổi mật khẩu | Quản trị viên | `tai_khoan` (ten_dang_nhap, mat_khau_bam, trang_thai, so_lan_dang_nhap_sai); `nhan_vien` |
| 40 | 11. Quản trị | Phân quyền theo vai trò | Quản trị viên | `vai_tro.ma_vai_tro`, `tai_khoan.vai_tro_id`; quyền cố định trong mã nguồn (Phần 6) |
| 41 | 11. Quản trị | Quản lý danh mục khoa, phòng, bác sĩ, dịch vụ | Quản trị viên | `khoa`, `phong_kham`, `bac_si`, `dich_vu_kham`, `danh_muc_can_lam_sang`, `danh_muc_thuoc`, `tu_khoa_trieu_chung` |
| 42 | 11. Quản trị | Xếp lịch làm việc của bác sĩ | Cán bộ quản lý | `lich_lam_viec` |
| 43 | 11. Quản trị | Soạn nội dung trang chủ | Cán bộ quản lý, Quản trị viên | `tin_tuc` (tac_gia_id, trang_thai, xuat_ban_luc) |
| 44 | 11. Quản trị | Thống kê, báo cáo | Cán bộ quản lý | truy vấn tổng hợp trên `lich_hen`, `goi_y_chuyen_khoa`, `lan_kham`, `hoa_don`, `thanh_toan`, `don_thuoc`; không đọc nội dung y tế |
| 45 | 11. Quản trị | Nhật ký thao tác (audit log) | Quản trị viên, Cán bộ quản lý | `nhat_ky_thao_tac` (nhan_vien_id, hanh_dong, ten_bang, nguoi_benh_id, du_lieu_truoc, du_lieu_sau, dia_chi_ip) |

Kết quả đối chiếu: 45 chức năng, và cả 35 bảng đều xuất hiện ở ít nhất một chức năng; không có chức năng nào thiếu bảng hoặc cột phục vụ. Nghiệp vụ “không quản lý tồn kho” được thể hiện bằng việc cố ý không có cột tồn kho.

### 8.2 Kết quả kiểm tra chéo trong tài liệu

Các kiểm tra sau chạy bằng chương trình trên chính tệp này (không chỉ đọc bằng mắt):

- 35 bảng ở Phần 1.7 khớp 35 mục ở Phần 2, đủ 35 hàng ở ma trận Phần 6, đủ 35 bảng ở Phần 5.1 và đủ 35 thực thể trong các sơ đồ ERD ở Phần 4.4.
- Mọi bảng đều có khóa chính `id` kiểu UUID; mọi cột khóa ngoại đều kiểu UUID, cùng kiểu với khóa chính được tham chiếu.
- Có 68 cột khóa ngoại ở Phần 2 và 68 dòng quan hệ ở Phần 4.2; hai danh sách trùng khớp từng cặp (bảng con, cột khóa ngoại), mọi bảng cha được nêu đều tồn tại.
- 30 cột ENUM ở Phần 2 đều có kiểu ENUM tương ứng ở Phần 3; các giá trị mặc định của cột ENUM đều thuộc danh sách giá trị cho phép (hai cột mặc định NULL là cột cho phép rỗng).
- Đã có sơ đồ chuyển trạng thái cho lịch hẹn, đơn thuốc và hóa đơn (Phần 3.7); đã có đủ các nhóm ENUM bắt buộc: trạng thái lịch hẹn, loại hình khám, trạng thái đơn thuốc, trạng thái hóa đơn, trạng thái và loại giấy tờ, vai trò, nguồn dữ liệu y tế nền, phương thức gợi ý, quan hệ thân nhân, giới tính, nhóm máu.
- Dữ liệu mẫu ở Phần 7 được chạy qua bộ kiểm tra riêng: khóa ngoại, định dạng mã, phép tính hóa đơn, trạng thái đơn thuốc so với hóa đơn và thứ tự thời gian đều đạt.

Một điểm không nhất quán được phát hiện và đã sửa trong lần kiểm tra này: bốn ô ràng buộc ở Phần 2 gọi vai trò bằng ba mã không có ở Phần 3.1.1, gồm `KTV_CAN_LAM_SANG` (ở `chi_dinh_can_lam_sang.ky_thuat_vien_id` và `ket_qua_can_lam_sang.ky_thuat_vien_id`), `NV_PHAT_THUOC` (ở `phat_thuoc.nhan_vien_phat_id`) và `NV_THU_NGAN` (ở `thanh_toan.nhan_vien_thu_id`). Phần 3.1.1 định nghĩa các mã này là `KY_THUAT_VIEN`, `PHAT_THUOC` và `THU_NGAN`; đã đổi bốn ô đó về đúng mã ở Phần 3.1.1.

Những mục sau chưa kiểm tra được bằng chương trình và cần người đọc rà lại bằng mắt: nội dung mô tả bằng lời của quy tắc nghiệp vụ ở từng bảng so với quy tắc ở Phần 3, và việc các sơ đồ Mermaid dán vào công cụ vẽ có hiển thị đúng hay không.

### 8.3 Danh sách giả định đã đặt ra

Danh tính và tài khoản

- Người bệnh đăng nhập bằng CCCD (mô phỏng VNeID), không có mật khẩu và không có dòng ở `tai_khoan`; hồ sơ `nguoi_benh` được tạo ở lần đăng nhập đầu nếu chưa có.
- Thân nhân luôn là một hồ sơ `nguoi_benh` riêng; `than_nhan` chỉ lưu quan hệ có hướng. Thân nhân dưới 14 tuổi có thể chưa có CCCD (`ma_cccd` để NULL).
- Quyền gắn cố định với 9 vai trò trong mã nguồn, không có bảng phân quyền; quản trị viên chỉ gán vai trò cho tài khoản.
- Quét QR CCCD chỉ để đọc số CCCD rồi tra cứu `nguoi_benh.ma_cccd`; hệ thống không lưu ảnh hay nội dung QR.

Đặt lịch, gợi ý, duyệt

- Người bệnh chọn khoa, không chọn bác sĩ; bác sĩ được gán khi mở lần khám (`lan_kham.bac_si_id`), nên số chỗ trống tính theo khoa, cộng từ các ca đang mở trong `lich_lam_viec`.
- Lịch làm việc nhập theo ngày cụ thể, không có mẫu lặp hằng tuần.
- Gợi ý chuyên khoa chạy nội bộ bằng `tu_khoa_trieu_chung`; phương thức LLM chỉ dùng khi trình diễn với dữ liệu giả, không gửi mô tả của người bệnh thật ra dịch vụ ngoài.
- Thông tin người duyệt, thời điểm và lý do không lưu ở `lich_hen` mà lấy từ dòng mới nhất của `lich_su_trang_thai_lich_hen`.
- `goi_y_chuyen_khoa.khoa_y_ta_xac_nhan_id` lưu khoa cuối cùng sau duyệt, kể cả khi người quyết định cuối là bác sĩ (ca chuyển lên).
- Mã QR phiếu hẹn và đơn thuốc là token ngẫu nhiên 256 bit, không chứa dữ liệu bệnh.

Dữ liệu y tế

- Dữ liệu y tế nền lấy từ VNeID chỉ khi có đồng ý còn hiệu lực theo từng phạm vi; thu hồi thì ẩn dữ liệu VNeID khỏi lần khám mới nhưng không xóa.
- Mỗi mục y tế nền một dòng; mỗi người bệnh tối đa một dòng nhóm máu.
- Mã và tên chẩn đoán nhập trực tiếp (có mã ICD-10 tùy chọn); không có bảng danh mục ICD-10 vì ngoài phạm vi.
- Kết quả cận lâm sàng lưu JSONB có cấu trúc thay đổi theo loại xét nghiệm, kèm tệp đính kèm tùy chọn.
- Mỗi lịch hẹn sinh tối đa một lần khám; mỗi lần khám tối đa một đơn thuốc và một hóa đơn còn hiệu lực.

Đơn thuốc và tài chính

- Hạn nhận thuốc mặc định bằng ngày kê cộng 7 ngày, cấu hình được; quá hạn không có trạng thái riêng mà bị từ chối khi quét.
- Hóa đơn gộp khám, cận lâm sàng và thuốc theo lần khám; thanh toán một lần đủ phần người bệnh trả; không hoàn tiền và không trả từng phần.
- Phần BHYT chi trả = làm tròn(thành tiền × mức hưởng / 100) cho khoản được BHYT, với lần khám loại BHYT; không giám định với cơ quan bảo hiểm.
- Thanh toán hoàn toàn mô phỏng: không kết nối cổng thanh toán, không lưu số thẻ hay tài khoản thật.
- Đơn giá trong danh mục không sửa khi đã dùng; đổi giá thì tạo mục mới, và hóa đơn sao chép đơn giá lúc lập.

Giấy tờ, lưu giữ, bảo vệ

- Giấy chuyển tuyến và giấy nghỉ ốm do bác sĩ duyệt; giấy được duyệt sinh tệp PDF lưu nơi không công khai, tải qua đường dẫn ký có thời hạn ngắn.
- Thời hạn lưu giữ ở Phần 5.5 (hồ sơ y tế 10 năm, nhật ký 5 năm, thông báo 90 ngày hoặc 1 năm) là đề xuất cấu hình cho đồ án, chưa xác minh với quy định pháp lý.
- Mã hóa tầng ứng dụng (AES-256-GCM) chỉ áp dụng cho văn bản tự do không cần lọc hay thống kê; cột dùng để lọc và thống kê lưu rõ trong ổ đĩa mã hóa.
- Thời điểm lưu UTC (TIMESTAMPTZ); ngày giờ hẹn khám lưu theo giờ địa phương của bệnh viện (DATE và TIME).

### 8.4 Câu hỏi cần xác nhận

| STT | Câu hỏi | Hiện đang giả định |
|---|---|---|
| 1 | Bác sĩ được xem hồ sơ y tế nền và lịch sử khám của ai: chỉ người bệnh đang khám với mình, hay cả người bệnh của bác sĩ khác cùng khoa? | Ma trận Phần 6 đang giới hạn ở người bệnh đang khám với mình (ký hiệu ²). |
| 2 | Khi ca được chuyển lên bác sĩ và bác sĩ quyết định khoa, có ghi vào `khoa_y_ta_xac_nhan_id` không, hay thêm một cột riêng cho khoa do bác sĩ xác nhận? | Đang giả định ghi vào cột hiện có để thống kê chung. |
| 3 | Thời hạn lưu hồ sơ bệnh án ngoại trú, nhật ký thao tác và thông báo theo quy chế của bệnh viện hoặc quy định hiện hành là bao lâu? | Phần 5.5 đang đặt 10 năm, 5 năm, 90 ngày hoặc 1 năm để làm mặc định. |
| 4 | Phần ngày (yyMMdd) trong `ma_lich_hen` lấy theo ngày hẹn hay ngày đặt? | Ví dụ ở Phần 2 và dữ liệu mẫu Phần 7 đang dùng ngày hẹn; các mã khác (`ma_lan_kham`, `ma_don_thuoc`, `ma_hoa_don`) dùng ngày phát sinh. |
| 5 | Người trưởng thành được người khác liên kết làm thân nhân có cần xác nhận của chính họ trước khi người kia xem hồ sơ, đặt lịch hộ không? | Hiện chỉ cần người quản lý tạo liên kết; chưa có bước xác nhận hai phía. |
| 6 | Người bệnh có cần chọn bác sĩ cụ thể khi đặt lịch không? | Đang giả định chỉ chọn khoa; nếu cần chọn bác sĩ thì thêm `lich_hen.bac_si_id` và tính chỗ trống theo bác sĩ. |
| 7 | Có cần lịch làm việc lặp hằng tuần (ca mẫu) thay vì nhập từng ngày không? | Đang nhập theo ngày cụ thể cho đơn giản. |
| 8 | Có cần hoàn tiền, hủy thanh toán đã thành công hoặc thanh toán từng phần trong phạm vi đồ án không? | Đang loại ra khỏi phạm vi. |
| 9 | Y tá tiếp nhận mở lịch hẹn bằng cách quét QR CCCD hay quét thêm QR phiếu hẹn (`lich_hen.ma_qr`)? | Dữ liệu đã hỗ trợ cả hai; cần chốt luồng để làm giao diện. |
| 10 | Có cần lưu lịch sử thay đổi cho `nguoi_benh` (đổi số điện thoại, địa chỉ) ngoài nhật ký thao tác nhân viên không? | Đang chỉ ghi nhật ký khi nhân viên sửa; người bệnh tự sửa chưa có nhật ký. |
