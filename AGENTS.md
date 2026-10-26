# AGENTS.md — ĐỌC FILE NÀY ĐẦU TIÊN

Dự án: website quản lý hồ sơ bệnh án điện tử (EMR) và đặt lịch khám ngoại trú tích hợp gợi ý chuyên khoa, Bệnh viện Nguyễn Tri Phương.
Một website duy nhất, có phần công khai và phần chức năng theo vai trò. Chủ dự án làm một mình, nên mọi thứ phải để lại dấu vết trong repo: agent sau không biết gì ngoài những gì có trong repo, và có thể hết token bất cứ lúc nào, không báo trước.

## 1. Quy trình khi bắt đầu (bắt buộc, theo thứ tự)

1. Đọc file này, rồi `STATUS.md`. Chỉ đọc thêm tài liệu cần cho việc đang làm, không đọc hết `docs/`.
2. Chạy `git status` và `git log -10` để biết agent trước làm tới đâu.
3. Chạy dự án và test (lệnh ở mục 6). Nếu đang hỏng, sửa trước và ghi vào `STATUS.md`.
4. Nhận việc: chỉ làm "Bước tiếp theo" trong `STATUS.md`, hoặc việc người dùng giao trong phiên này.
5. Ghi tên công cụ, module, nhánh và giờ bắt đầu vào mục KHÓA trong `STATUS.md`. Nếu mục KHÓA đã có agent khác đang làm module đó, dừng lại và báo người dùng.

## 2. Trong khi làm

- Sau mỗi bước nhỏ (xong một màn hình, một API, một lần sửa lỗi) phải commit và cập nhật `STATUS.md` ngay. Không để dồn đến cuối phiên.
- Mỗi commit làm một việc và để dự án ở trạng thái chạy được. Mẫu: `M04: thêm bước chọn người đăng ký (PB-02)`.
- Mỗi module một nhánh: `mXX-ten-ngan`. Mỗi lúc chỉ một agent làm một module.
- Chỉ làm module đang được giao. Không làm trước module khác dù thấy dễ.

## 3. Khi dừng hoặc sắp hết token

1. Commit phần đang làm. Nếu dở và chưa chạy được, dùng tiền tố `wip:` và ghi rõ chỗ chưa chạy.
2. Cập nhật `STATUS.md`: phần "Việc đang làm và bước tiếp theo" (ghi cụ thể tên file, hàm, màn hình) và thêm một mục vào "Nhật ký bàn giao" (mới nhất ở trên cùng).
3. Xóa dòng của mình khỏi mục KHÓA.
## 4. Nguồn sự thật

Chỉ đọc file liên quan đến việc đang làm. Không đọc hết `docs/design/`.

| Cần biết | Xem ở đâu |
|---|---|
| Bố cục từng màn hình (nguồn gốc giao diện) | Tra mã ở docs/design/stitch/INDEX.md. Mỗi thư mục có code.html và screen.png. Thư mục ghi “gộp” chứa nhiều màn hình trong cùng một file: xem screen.png để biết phần nào là màn nào. |
| Yêu cầu riêng từng màn hình, theo khu | `docs/design/screens-patient.md` (công khai và người bệnh: CK, PB), `docs/design/screens-staff.md` (nhân viên và quản trị: NV, BS, CLS, TN, PT, QT) |
| Khung chung, Demo Navigator, nối luồng, trạng thái biên | `docs/design/screens-global.md` |
| Màu, font, khoảng cách, thành phần, tiếp cận, đổi thuật ngữ | `docs/design/design-system.md` trước và `docs/design/stitch/DESIGN.md` sau|
| Quy tắc nghiệp vụ và nhãn trạng thái hiển thị | `docs/design/business-rules.md` |
| Dữ liệu mẫu cho mock (nhân vật, mã, hóa đơn, kịch bản) | `docs/design/sample-data.md` |
| Checklist nghiệm thu giao diện | `docs/design/acceptance-checklist.md` |
| Bảng, cột, kiểu dữ liệu, giá trị trạng thái trong CSDL | `docs/data-dictionary.md` |
| Kiểu dữ liệu, API dùng chung, bảng ghép "giá trị CSDL sang nhãn hiển thị" | `contracts/` |
| Quyết định đã chốt và phạm vi | `DECISIONS.md` |
| Việc đang làm, ai làm | `STATUS.md` |
| Chỗ chưa rõ đang chờ người dùng | `QUESTIONS.md` |
| Bản gốc prompt thiết kế (chỉ để tra cứu, không đọc mặc định) | `docs/design/Prompt_Prototype_NTP_EMR_v2.md` |

Thứ tự ưu tiên khi các nguồn lệch nhau:
1. Bản thiết kế đã chốt (Stitch, ảnh PNG).
2. `docs/design/screens-patient.md`, `screens-staff.md`, `screens-global.md`.
3. `docs/design/design-system.md` và `business-rules.md`.
4. `docs/design/Prompt_Prototype_NTP_EMR_v2.md`.
Cấu trúc dữ liệu thì `docs/data-dictionary.md` thắng giao diện: tên trường và giá trị trạng thái lấy từ đó, nhãn hiển thị lấy từ `business-rules.md`, hai thứ nối với nhau qua bảng ghép trong `contracts/`.

Quy tắc:
- Thiết kế là nguồn gốc. Không tự thêm, bớt hay đổi bố cục, chữ, luồng. Muốn lệch thì ghi vào `QUESTIONS.md` và hỏi.
- Không dùng màu hoặc mã màu ngoài token trong `design-system.md`.
- Màu, font, cỡ chữ lấy từ `docs/design/design-system.m` trước và `docs/design/stitch/DESIGN.md` sau. Hình dạng và bố cục thành phần tham chiếu từ ds_01 và các màn khác của Stitch. Khi mã màu trong code.html khác bảng token thì dùng bảng token.
- Tên trường, tên bảng, giá trị trạng thái lấy đúng từ `docs/data-dictionary.md`. Không tự đặt tên mới.
- `sample-data.md` chỉ dùng làm dữ liệu mock và seed, không dùng để suy ra cấu trúc bảng.
- Thiếu thông tin thì ghi `QUESTIONS.md` rồi làm phần khác. Không đoán.
## 5. Cấu trúc thư mục

```
apps/web/        frontend
apps/api/        backend
contracts/       kiểu dữ liệu và danh sách API dùng chung
docs/            design/, modules/, data-dictionary.md
AGENTS.md  STATUS.md  DECISIONS.md  QUESTIONS.md
```

## 6. Công nghệ và lệnh chạy

Công nghệ: xem `DECISIONS.md` mục D-01 (đang ghi là TẠM). Lệnh cài đặt, chạy, test, kiểm tra kiểu: điền vào đây sau khi dựng khung dự án ở module M01.

```
(chưa có)
```

## 7. Frontend trước, dữ liệu giả

- Frontend gọi dữ liệu chỉ qua lớp `apps/web/src/api/`. Dữ liệu giả nằm ở `apps/web/src/api/mock/` và phải đúng cấu trúc trong `contracts/`.
- Chuyển sang backend thật bằng cấu hình, không được sửa từng màn hình.
- "Vai trò hiện tại" phải có ngay từ đầu (chọn vai trò giả để thử). Menu và quyền hiển thị đi theo vai trò.
- Mọi kết nối ngoài (VNeID, thanh toán, quét CCCD, gợi ý khoa) đặt sau một giao diện riêng và đều là mô phỏng.

## 8. Định nghĩa "xong" cho một task hoặc module

- Chạy được, không lỗi ở console và terminal.
- Khớp thiết kế: chụp ảnh trang thật (Playwright) và so với ảnh trong `docs/design/`, liệt kê chỗ khác.
- Có đủ trạng thái: rỗng, đang tải, lỗi, thành công.
- Có test cơ bản cho phần logic.
- Cập nhật `STATUS.md` và commit.
- Hết module: viết `docs/modules/Mxx.md` theo `docs/modules/_TEMPLATE.md`, rồi DỪNG và chờ người dùng duyệt (cột "Người dùng duyệt" trong `STATUS.md`). Chưa được duyệt thì không sang module sau.
- Khu công khai và người bệnh: chụp ảnh ở 390 và liệt kê chỗ vi phạm checklist, vì không có thiết kế mobile để so.
## 9. Không được làm

- Thêm chức năng ngoài tài liệu, hoặc làm các mục ngoài phạm vi trong `DECISIONS.md`.
- Đổi quyết định đã chốt, sửa thiết kế gốc hay `docs/data-dictionary.md` khi chưa hỏi.
- Làm module khác khi agent khác đang giữ khóa.
- Commit mật khẩu, khóa API, tệp `.env`.
- Dùng dữ liệu thật của người thật. Chỉ dùng dữ liệu mẫu tự tạo.
- Gửi dữ liệu y tế (kể cả mô tả triệu chứng) tới dịch vụ bên ngoài. Gợi ý chuyên khoa mặc định chạy nội bộ.
- Xóa nhánh, ghi đè lịch sử git (`push --force`, `reset --hard`) khi chưa được người dùng cho phép.

## 10. Ngôn ngữ

Tài liệu, chú thích giải thích, nội dung giao diện và phần giải thích module viết bằng tiếng Việt. Tên biến, hàm, file, thư mục trong code viết bằng tiếng Anh. Tên bảng và cột CSDL theo đúng `docs/data-dictionary.md` (xem D-11 trong `DECISIONS.md`).
