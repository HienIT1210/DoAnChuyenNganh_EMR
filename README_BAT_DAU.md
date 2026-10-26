# HƯỚNG DẪN SỬ DỤNG BỘ QUẢN LÝ AGENT (dành cho bạn, không phải cho agent)

## 1. Cài vào repo

1. Tạo repo Git cho dự án (hoặc dùng repo có sẵn).
2. Chép toàn bộ nội dung thư mục này vào thư mục gốc repo, giữ nguyên cấu trúc, kể cả thư mục ẩn `.github` và `.cursor`.
3. Chép thêm vào repo:
   - Ảnh Figma từng màn hình vào `docs/design/`, đặt tên theo mã trong sitemap (ví dụ `PB-05-desktop.png`, `PB-05-mobile.png`).
   - Data dictionary của bạn vào `docs/data-dictionary.md`.
4. Mở `DECISIONS.md` và `QUESTIONS.md`, chốt hai mục đang để TẠM (D-01 công nghệ, D-11 ngôn ngữ). Điền hạn báo cáo và hạn nộp vào `STATUS.md`.
5. Commit lần đầu: `chore: thêm bộ quản lý agent`.

## 2. Ba công cụ của bạn đọc luật như thế nào

Mọi luật nằm ở `AGENTS.md`. Ba file nhỏ chỉ trỏ về đó:
- Cursor: `.cursor/rules/project.mdc`
- GitHub Copilot trong VS Code: `.github/copilot-instructions.md`
- Antigravity: `GEMINI.md`

Công cụ có thể thay đổi cách đọc file luật theo phiên bản. Vì vậy ở lần đầu mở mỗi công cụ, hãy dán "Prompt bắt đầu" bên dưới thay vì tin vào việc nó tự đọc.

## 3. Prompt bắt đầu (dán khi mở agent mới ở bất kỳ công cụ nào)

```
Bạn làm việc trong repo này. Đọc AGENTS.md rồi STATUS.md, và làm đúng "Quy trình khi bắt đầu".
Việc của phiên này: <ghi việc cụ thể, ví dụ: tiếp tục từ "Bước tiếp theo" trong STATUS.md>.
Chỉ làm việc đó. Sau mỗi bước nhỏ, commit và cập nhật STATUS.md. Gặp chỗ chưa rõ thì ghi vào QUESTIONS.md, không đoán.
```

## 4. Prompt việc đầu tiên (M01)

```
Làm M01 trong STATUS.md: dựng khung dự án theo DECISIONS.md (D-01), cấu trúc thư mục trong AGENTS.md mục 5.
Gồm: apps/web, apps/api, contracts, lớp apps/web/src/api có mock, "vai trò hiện tại" chọn giả để thử, khung giao diện theo vai trò, màn hình đăng nhập (CK-06, CK-07) theo docs/design.
Khi xong, điền lệnh cài đặt, chạy, test vào AGENTS.md mục 6, viết docs/modules/M01.md theo mẫu, rồi dừng chờ tôi duyệt.
```

## 5. Prompt khi thấy sắp hết token hoặc muốn đổi công cụ

```
Dừng việc mới. Commit phần đang làm (dùng tiền tố wip: nếu chưa chạy được), cập nhật STATUS.md gồm "Việc đang làm và bước tiếp theo" thật cụ thể và thêm mục vào Nhật ký bàn giao, rồi xóa dòng của bạn ở mục KHÓA.
```

Lưu ý: agent không biết trước khi nào hết token, nên đừng chờ đến lúc đó mới dùng prompt này. Luật trong AGENTS.md đã bắt agent cập nhật sau mỗi bước nhỏ, prompt này chỉ để chắc chắn khi bạn chủ động đổi công cụ.

## 6. Việc của riêng bạn trong mỗi module

1. Đọc `docs/modules/Mxx.md` mà agent viết.
2. Chạy thử theo mục "Cách chạy thử".
3. Hỏi lại agent những chỗ chưa hiểu (câu hỏi này giúp bạn trả lời thầy).
4. Đổi cột "Người dùng duyệt" trong `STATUS.md` thành DA_DUYET rồi mới cho sang module sau.

## 7. Nên làm theo thứ tự

M01, M02, M03, M04, M05, M06, M07, M08 (nhóm A). Nhóm B (M09 đến M11) chỉ làm khi nhóm A xong và còn thời gian. Giữ tuần cuối cho kiểm thử và sửa lỗi.
