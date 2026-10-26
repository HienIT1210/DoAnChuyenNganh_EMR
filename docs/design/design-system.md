### B2. Thương hiệu và hướng thiết kế

Nhận diện:
- Logo: dùng đúng ảnh đính kèm. Không vẽ lại, không đổi màu, không kéo giãn. Đặt trên nền trắng, cao 44px trên desktop và 36px trên mobile, chừa khoảng trống quanh logo bằng nửa chiều cao logo.
- Khẩu hiệu "Năng động - Thân thiện - Phát triển" chỉ xuất hiện ở chân trang và trang đăng nhập.
- Họa tiết: dấu cộng bo tròn lấy từ biểu tượng chữ thập trên logo, độ mờ 6% đến 8%. Chỉ đặt trên nền xanh nhạt ở vùng hero, trang đăng nhập và khung rỗng. Không đặt sau khối chữ dài.
- Giọng văn: thân thiện, ngắn, rõ, như điều dưỡng giải thích cho người nhà bệnh nhân.

Sáu nguyên tắc thiết kế (áp dụng cho mọi màn hình):
1. Một việc chính mỗi màn hình, và chỉ một nút đỏ đặc cho việc đó.
2. Chữ lớn, khoảng trắng rộng. Phân nhóm bằng khoảng cách và nền xanh nhạt, hạn chế khung viền và đường kẻ.
3. Luôn cho người dùng biết đang ở đâu và bước tiếp theo là gì (ví dụ "Bước 2/5: Chọn loại hình khám").
4. Dùng lời nói thường ngày. Thuật ngữ y khoa phải đổi theo bảng ở B6.
5. Không phụ thuộc vào màu, hover hay cử chỉ ẩn. Mọi trạng thái có nhãn chữ.
6. Quyền riêng tư thấy được: biểu tượng khóa kèm một câu giải thích ngay tại chỗ dữ liệu được dùng.

Tham chiếu và khác biệt: website bệnh viện Bạch Mai chỉ là tham chiếu về mức độ rõ ràng của thông tin. Không sao chép bố cục, hệ thống menu, bảng màu, ảnh, biểu tượng hay câu chữ của website đó hoặc của bất kỳ bệnh viện nào. Bản của bạn phải khác ở năm điểm có chủ đích:
1. Trang chủ theo tác vụ: bốn ô việc lớn ("Đặt lịch khám", "Xem lịch hẹn và kết quả", "Tìm bác sĩ", "Hướng dẫn đi khám"). Tin tức chỉ có 3 thẻ ở phần dưới.
2. Thẻ "Bước tiếp theo của bạn" ở đầu trang chủ người bệnh (ví dụ "Thanh toán viện phí cho lần khám hôm nay").
3. Lộ trình khám dạng đường thời gian dọc, mỗi mốc có nhãn chữ.
4. Nút chỉnh cỡ chữ (A, A+, A++) trên mọi trang.
5. Mỗi thẻ trạng thái đi kèm một câu "Điều này nghĩa là gì".
Không dùng băng rôn tự chạy, không dùng lưới tin dày đặc, không dùng ảnh stock, không dùng gradient nhiều màu.

### B3. Hệ thống màu

Hai màu chủ đạo: xanh #008dc9 (nhận diện, điều hướng, thông tin) và đỏ #e02519 (hành động). Các token dưới đây là bắt buộc. Tỷ lệ tương phản đã tính theo công thức WCAG 2.2, hãy kiểm tra lại bằng công cụ khi dựng.

| Token | Mã màu | Dùng cho | Tương phản |
|---|---|---|---|
| ink-900 | #0B2533 | Chữ chính | 15.8:1 trên trắng |
| ink-700 | #46596A | Chữ phụ | 7.3:1 trên trắng |
| ink-500 | #5E7080 | Chú thích, placeholder (không nhỏ hơn 16px) | 5.1:1 trên trắng |
| line-300 | #D3DEE6 | Đường phân vùng, viền thẻ | Trang trí |
| line-500 | #7B8E9D | Viền ô nhập, checkbox, radio | 3.4:1 trên trắng |
| bg-page | #F4F8FA | Nền trang | |
| bg-surface | #FFFFFF | Nền thẻ, hộp thoại | |
| blue-500 | #008DC9 | Thương hiệu: icon lớn, thanh tiến trình, viền chọn, vòng focus, biểu đồ, chữ số lớn từ 24px đậm | 3.7:1 trên trắng, chỉ dùng cho đối tượng lớn hoặc không phải chữ |
| blue-600 | #006E9B | Nền khối xanh có chữ trắng, liên kết trong đoạn văn | 5.7:1 với chữ trắng |
| blue-800 | #00587C | Tiêu đề H1 và H2, nền chân trang, chữ xanh trên nền nhạt | 7.8:1 trên trắng, 7.1:1 trên blue-50 |
| blue-100 | #D6EDF8 | Nền chip và thẻ đang được chọn, avatar chữ cái | |
| blue-50 | #EAF6FB | Nền vùng nhấn, hero, khối thông tin | |
| red-500 | #E02519 | Nút hành động chính (chữ trắng) | 4.7:1 |
| red-600 | #BF1E14 | Nút hành động khi hover | 6.2:1 với chữ trắng |
| red-700 | #9E170F | Nút khi nhấn, chữ đỏ trên nền nhạt | 7.2:1 trên red-50 |
| red-50 | #FDEEEC | Nền khung lỗi, thẻ "Từ chối" | |
| green-700 trên green-50 | #1B6E3A trên #E7F5EC | Thẻ trạng thái thành công | 5.6:1 |
| amber-800 trên amber-50 | #7A4B00 trên #FFF3D6 | Thẻ trạng thái cảnh báo, khối dị ứng thuốc | 6.7:1 |

Quy tắc dùng màu:
- Tỷ lệ diện tích tham chiếu: 70% trắng và xám nhạt, 20% xanh và các sắc nhạt của xanh, tối đa 5% đỏ, phần còn lại là xanh lá và vàng trong thẻ trạng thái.
- Xanh dùng cho: logo, điều hướng, mục đang chọn, liên kết, tiêu đề, icon, thanh tiến trình, biểu đồ, nền nhạt, chân trang (blue-800).
- Đỏ chỉ dùng cho ba việc: (1) nút hành động chính, tối đa một nút đỏ đặc trên mỗi màn hình hoặc mỗi hộp thoại; (2) nút hành động không hoàn tác dạng viền đỏ (Hủy lịch, Từ chối) và nút xác nhận cuối trong hộp thoại hủy; (3) khung lỗi và cảnh báo chặn, luôn có biểu tượng và chữ. Không dùng đỏ cho tiêu đề, nền lớn, trang trí hay liên kết.
- Chữ đỏ trên nền nhạt dùng red-700. Không đặt chữ red-500 lên red-50 (4.2:1, không đạt).
- Không đặt chữ trắng cỡ nhỏ lên blue-500. Muốn nền xanh đặc có chữ, dùng blue-600 hoặc blue-800.
- Hàm ý của màu không đứng một mình: thẻ trạng thái, cảnh báo, kết quả xét nghiệm bất thường đều có biểu tượng và nhãn chữ.
- Không có chế độ tối ở giai đoạn này.

### B4. Chữ, khoảng cách, hình khối

- Font: Be Vietnam Pro (Google Fonts), trọng số 400, 500, 600, 700. Dự phòng system-ui. Bật số tabular cho tiền, mã và số thứ tự.
- Màu chữ: tiêu đề H1 và H2 dùng blue-800, H3 và nội dung dùng ink-900, chữ phụ dùng ink-700.

| Cấp | Desktop | Mobile | Trọng số |
|---|---|---|---|
| Display (số thứ tự, mã lớn) | 72/80 | 56/64 | 700 |
| H1 | 36/44 | 28/36 | 700 |
| H2 | 28/36 | 24/32 | 700 |
| H3 | 22/30 | 20/28 | 600 |
| Nội dung chính khu người bệnh | 18/28 | 18/28 | 400 |
| Chú thích khu người bệnh (nhỏ nhất) | 16/24 | 16/24 | 400 |
| Nút | 18/24 | 18/24 | 600 |
| Nhãn ô nhập | 16/24 | 16/24 | 600 |
| Khu nhân viên: nội dung và bảng | 16/24, bảng tối thiểu 15/22 | | 400 |

- Khoảng cách theo bội số 8px: 8, 16, 24, 32, 48, 64, 96. Khoảng giữa các khối lớn 64px (desktop) và 48px (mobile). Đệm trong thẻ 24px (desktop) và 16px (mobile).
- Bo góc: 12px cho nút, ô nhập, chip; 16px cho thẻ; 24px cho khối hero và bottom sheet; 999px cho thẻ trạng thái.
- Đổ bóng: s1 = 0 1px 2px rgba(11,37,51,0.08) cho thẻ có thể bấm; s2 = 0 8px 24px rgba(11,37,51,0.10) cho hộp thoại và thanh cố định. Thẻ thường chỉ có viền line-300, không đổ bóng.
- Lưới: desktop 1440 với khung nội dung 1200, 12 cột, rãnh 24px; tablet 768 với 8 cột, lề 24px; mobile 390 với 4 cột, lề 16px, rãnh 16px. Khu nhân viên: thanh bên 256px (thu gọn 72px), vùng nội dung co giãn, lề 32px.
- Icon: bộ Lucide, nét 2px, cỡ 24px (20px trong bảng dày). Icon điều hướng chính luôn kèm nhãn chữ. Chỉ các icon quen thuộc (đóng, mũi tên, in) được đứng một mình, và vẫn có nhãn truy cập.
- Chuyển động: 150 đến 200ms, ease-out, mờ dần cộng trượt 8px. Không có băng rôn tự chạy. Tôn trọng prefers-reduced-motion.
- Hình ảnh: không dùng ảnh người thật. Bác sĩ dùng avatar chữ cái đầu trên nền blue-100. Ảnh tin tức là khung nền blue-50, họa tiết dấu cộng và một icon. Minh họa nét đơn giản, chỉ dùng các sắc xanh và trắng. Không dùng emoji trong giao diện.
- Vòng focus: 3px blue-500, cách phần tử 2px bằng nền trắng, hiện trên mọi phần tử tương tác.

### B5. Thành phần giao diện (dùng chung toàn bộ prototype)

| Thành phần | Quy cách | Trạng thái cần dựng |
|---|---|---|
| Nút hành động chính | Nền red-500, chữ trắng, cao 52px (mobile 56px, nhân viên 48px), bo 12px, có icon bên trái khi cần | Mặc định, hover (red-600), nhấn (red-700), focus, vô hiệu (nền #E4EAEE, chữ ink-500), đang xử lý (vòng xoay, giữ nguyên nhãn) |
| Nút phụ | Nền trắng, viền 2px blue-500, chữ blue-800, cùng kích thước nút chính | Mặc định, hover (nền blue-50), nhấn, focus, vô hiệu |
| Nút văn bản | Chữ blue-800, gạch chân luôn hiện khi nằm trong đoạn văn | Mặc định, hover, focus |
| Nút hủy/từ chối | Nền trắng, viền 2px red-500, chữ red-700, có icon | Mặc định, hover (nền red-50), focus |
| Ô nhập | Cao 52px, viền line-500, nhãn luôn hiện phía trên (không dùng placeholder thay nhãn), dòng gợi ý phía dưới | Mặc định, focus, đã điền, lỗi (viền red-500, icon, câu hướng dẫn sửa), vô hiệu |
| Ô số CCCD | Tự nhóm 3-3-3-3, nút ẩn/hiện số, chỉ nhận số | Như ô nhập |
| Chọn ngày sinh | Ba ô chọn Ngày, Tháng, Năm (không dùng lịch bật lên cho ngày sinh) | Như ô nhập |
| Thẻ chọn (radio/checkbox dạng thẻ) | Cả thẻ bấm được, điều khiển 24px ở góc, viền 2px blue-500 và nền blue-100 khi chọn | Chưa chọn, hover, đã chọn, focus, vô hiệu |
| Công tắc đồng ý | 56x32px, kèm chữ "Đã đồng ý" hoặc "Chưa đồng ý" bên cạnh | Bật, tắt, focus, vô hiệu |
| Thẻ trạng thái (badge) | Dạng viên thuốc, icon và chữ, kèm câu "Điều này nghĩa là gì" ở nơi có chỗ | Theo bảng trạng thái ở B7 mục 13 |
| Thanh tiến trình đặt lịch | Desktop: 5 bước ngang có số và nhãn. Mobile: chữ "Bước 2/5: tên bước", thanh 6px blue-500, nút quay lại ở góc | Hoàn tất, hiện tại, chưa tới |
| Thẻ (card) | Nền trắng, viền line-300, bo 16px | Mặc định, hover (khi bấm được: viền blue-500 và s1), chọn |
| Bảng (nhân viên) | Hàng cao 56px, tiêu đề cố định, sắp xếp, lọc dạng chip, phân trang có chọn số dòng, hàng hover nền blue-50, hàng chọn có vạch trái 4px blue-500 | Có dữ liệu, rỗng, đang tải (skeleton), lỗi |
| Hộp thoại | Giữa màn hình rộng 480px (mobile: bottom sheet). Tiêu đề, mô tả ngắn, nút phụ bên trái, nút chính bên phải | Thường, nguy hiểm (có ô nhập lý do bắt buộc) |
| Toast | Góc trên bên phải (mobile: dưới, cách thanh điều hướng 16px), icon, chữ, tự tắt sau 6 giây, có nút đóng | Thành công, thông tin, lỗi |
| Khung cảnh báo | Thông tin (blue-50), cảnh báo (amber-50), lỗi (red-50, vạch trái 4px red-500), khẩn cấp (nền trắng, viền 2px red-500, icon lớn, nút "Gọi 115") | Đủ bốn loại |
| Khung rỗng | Minh họa nhỏ, một câu giải thích, một nút hành động | Có |
| Khung đang tải | Skeleton, không dùng vòng xoay toàn trang | Có |
| Khung quét QR/CCCD | Khung camera mô phỏng, góc dẫn hướng, ô nhập tay bên cạnh | Chờ quét, đang quét, thành công, không đọc được |
| Mã QR | QR cỡ 240px trên nền trắng, chừa lề 16px, mã chữ phía dưới, ghi "Mã QR mẫu" ở bản prototype | Có |
| Khung xem trước phiếu in | Khổ A5 hoặc A6, đen trắng, số thứ tự 96px, có QR | Có |
| Đường thời gian lộ trình khám | Dọc, mỗi mốc có icon và nhãn chữ: Đã xong, Đang thực hiện, Chưa tới | Có |
| Breadcrumb (nhân viên) và nút Quay lại có nhãn (người bệnh) | | Có |
| Nút chỉnh cỡ chữ | Ba mức A, A+, A++ tương ứng 100%, 115%, 130%, nhớ lựa chọn | Có |
| Hộp thoại hết phiên | Cảnh báo 60 giây trước khi tự đăng xuất do không thao tác 10 phút, có nút "Tiếp tục sử dụng" | Có |

### B6. Khả năng tiếp cận và ngôn ngữ

Tiếp cận (mức tối thiểu WCAG 2.2 AA, chữ nội dung khu người bệnh đạt 7:1 khi có thể):
- Chữ ở khu người bệnh không nhỏ hơn 16px, nội dung chính 18px.
- Vùng bấm tối thiểu 48x48px ở khu người bệnh, 40x40px ở khu nhân viên. Khoảng cách giữa hai vùng bấm kề nhau tối thiểu 8px.
- Giao diện không vỡ, không cắt chữ khi bật cỡ chữ 130% và khi phóng to trình duyệt 200%.
- Mọi ô nhập có nhãn hiện sẵn. Lỗi có tóm tắt ở đầu form và dòng lỗi tại từng ô, nêu rõ cách sửa. Thứ tự Tab theo thứ tự đọc. Có liên kết "Bỏ qua đến nội dung chính".
- Không dùng thao tác vuốt, kéo thả hay nhấn giữ là cách duy nhất để hoàn thành việc. Không có nội dung chỉ hiện khi rê chuột.
- Tên ô nhập có thuộc tính autocomplete phù hợp (họ tên, ngày sinh, điện thoại).

Ngôn ngữ cho người bệnh: xưng "bạn", câu ngắn, mỗi nút bắt đầu bằng động từ ("Đặt lịch khám", "Tiếp tục", "Quay lại", "Xác nhận đặt lịch"). Dùng bảng đổi thuật ngữ sau ở khu người bệnh. Khu nhân viên dùng thuật ngữ chuyên môn.

| Thuật ngữ y khoa | Ghi ở khu người bệnh |
|---|---|
| Cận lâm sàng | Xét nghiệm và chẩn đoán hình ảnh |
| Chỉ định cận lâm sàng | Yêu cầu làm xét nghiệm |
| Tiền sử | Bệnh đã mắc và phẫu thuật trước đây |
| Chẩn đoán | Kết luận của bác sĩ |
| Tái khám | Hẹn khám lại |
| Chuyển tuyến | Chuyển sang bệnh viện khác |
| Lý do khám | Bạn đến khám vì lý do gì |
| BHYT | Bảo hiểm y tế (BHYT), lần đầu ghi đủ chữ |
