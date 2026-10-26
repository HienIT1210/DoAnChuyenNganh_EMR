# STATUS — Bảng điều khiển tiến độ (agent cập nhật sau MỖI bước nhỏ)

Cập nhật lần cuối: 2026-10-26 — chưa có agent nào làm việc

## KHÓA (agent đang làm — chỉ một agent cho mỗi module)

| Công cụ | Module | Nhánh | Bắt đầu lúc |
|---|---|---|---|
| (trống) | | | |

Nhận việc: thêm một dòng. Dừng việc: xóa dòng của mình.

## Việc đang làm và bước tiếp theo

- Module đang làm: chưa có
- Đang làm dở: không có
- Bước tiếp theo (cụ thể: tên file, hàm, màn hình): Bắt đầu M01 — dựng khung dự án (apps/web, apps/api, contracts), cài đặt công cụ, điền lệnh chạy vào mục 6 của AGENTS.md
- Chỗ chưa chạy hoặc cần lưu ý: không có

## Bảng module

Ưu tiên: A = bắt buộc chạy thật, B = làm sau, làm nếu còn thời gian.
Trạng thái: CHUA_LAM, DANG_LAM, XONG, BI_CHAN.
"Người dùng duyệt": CHO_DUYET khi agent đã viết xong docs/modules/Mxx.md; người dùng đổi thành DA_DUYET.

| ID | Module | Ưu tiên | Màn hình | UI (dữ liệu giả) | Backend (nối thật) | Người dùng duyệt | Ghi chú |
|---|---|---|---|---|---|---|---|
| M01 | Nền tảng: khung dự án, đăng nhập, phân quyền, khung giao diện theo vai trò, trang chủ cơ bản | A | CK-01, CK-06, CK-07 | CHUA_LAM | CHUA_LAM | | |
| M02 | Danh mục: khoa, phòng khám, bác sĩ, dịch vụ | A | QT-03 | CHUA_LAM | CHUA_LAM | | |
| M03 | Hồ sơ người bệnh và thân nhân, thông tin y tế nền, đồng ý VNeID (mô phỏng), BHYT | A | PB-01, PB-10..PB-13, PB-20, PB-21 | CHUA_LAM | CHUA_LAM | | |
| M04 | Đặt lịch khám và gợi ý chuyên khoa | A | PB-02..PB-09 | CHUA_LAM | CHUA_LAM | | |
| M05 | Tiếp nhận: quét CCCD, duyệt lịch, cấp số, in phiếu | A | NV-01..NV-06 | CHUA_LAM | CHUA_LAM | | |
| M06 | Khám bệnh, chỉ định và kết quả cận lâm sàng, hẹn tái khám, xem hồ sơ khám | A | BS-01..BS-04, BS-06, CLS-01, CLS-02, PB-14, PB-15, PB-18 | CHUA_LAM | CHUA_LAM | | |
| M07 | Kê đơn, mã QR, phát thuốc | A | BS-05, PB-16, PT-01, PT-02 | CHUA_LAM | CHUA_LAM | | |
| M08 | Hóa đơn và thanh toán (mô phỏng) | A | PB-17, TN-01, TN-02 | CHUA_LAM | CHUA_LAM | | |
| M09 | Trang công khai đầy đủ (chuyên khoa, bác sĩ, tin tức, hướng dẫn) | B | CK-02..CK-05 | CHUA_LAM | CHUA_LAM | | |
| M10 | Giấy tờ bệnh viện và chuyển tuyến | B | PB-19, NV-07, BS-07 | CHUA_LAM | CHUA_LAM | | |
| M11 | Quản trị tài khoản, nội dung, thống kê, nhật ký thao tác | B | QT-01, QT-02, QT-04, QT-05 | CHUA_LAM | CHUA_LAM | | |

## Nhật ký bàn giao (mới nhất ở trên cùng; không xóa mục cũ)

Mẫu mỗi mục:

```
### YYYY-MM-DD HH:MM — <công cụ> — <module>
- Đã làm:
- Commit cuối:
- Dở dang:
- Bước tiếp theo:
- Vấn đề / câu hỏi (đã ghi QUESTIONS.md chưa):
```

### 2026-10-26 — người dùng và Claude — khởi tạo
- Đã làm: tạo bộ quản lý agent (AGENTS.md, STATUS.md, DECISIONS.md, QUESTIONS.md, mẫu tài liệu)
- Commit cuối: (chưa có)
- Dở dang: không có
- Bước tiếp theo: M01 — dựng khung dự án
- Vấn đề: công nghệ ghi TẠM ở D-01, cần người dùng chốt trước khi dựng khung
