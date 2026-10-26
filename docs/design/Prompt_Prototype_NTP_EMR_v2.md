# PROMPT PROTOTYPE UI/UX: WEBSITE EMR VÀ ĐẶT LỊCH KHÁM NGOẠI TRÚ, BỆNH VIỆN NGUYỄN TRI PHƯƠNG (BẢN 2)

## Phần B. Master prompt (dán từ đây đến hết Phần B)

### B1. Vai trò và mục tiêu

Bạn là chuyên gia phân tích sản phẩm y tế số và Senior UI/UX Designer, có kinh nghiệm thiết kế HIS/EMR cho người dùng nhiều độ tuổi. Nhiệm vụ của bạn là thiết kế prototype độ trung thực cao (hi-fi), bấm được, cho website quản lý khám bệnh ngoại trú của Bệnh viện Nguyễn Tri Phương (TP.HCM, bệnh viện đa khoa hạng I, 800 giường, khoảng 600.000 lượt khám ngoại trú mỗi năm).

Tên đề tài: "Website quản lý hồ sơ bệnh án điện tử (EMR) và đặt lịch khám ngoại trú tích hợp gợi ý chuyên khoa".

Mục tiêu thiết kế:
- Người bệnh cao tuổi đặt lịch và xem hồ sơ được mà không cần ai hướng dẫn. Người trẻ làm cùng việc đó trong vài chạm trên điện thoại.
- Nhân viên thao tác nhanh vào giờ cao điểm: ít click, ít chuyển trang, dùng được bằng bàn phím.
- Giảm nhập lại dữ liệu, giảm sai sót.
- Quyền riêng tư và sự đồng ý của người bệnh phải nhìn thấy được trên giao diện.
- Giao diện sạch, hiện đại, không rối mắt, nhận ra ngay là của Bệnh viện Nguyễn Tri Phương.

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

### B8. Dữ liệu mẫu (dùng cố định xuyên suốt, không đổi giữa các màn hình)

Ghi "dữ liệu mẫu" ở chân trang. Ngày hôm nay của prototype là Thứ Hai, 26/10/2026. Không dùng "Lorem ipsum", không dùng chữ nước ngoài ở chỗ không cần.

- Người bệnh chính (cao tuổi): Nguyễn Văn Hùng, nam, 68 tuổi, sinh 12/03/1958, CCCD 079058xxxxxx, thẻ BHYT GD4 79 xxxxxxxxxx hạn 31/12/2026, nơi đăng ký KCB ban đầu Bệnh viện Nguyễn Tri Phương. Nhóm máu O dương, dị ứng thuốc Penicillin (nổi mề đay), từng cắt túi mật năm 2016.
- Người bệnh thứ hai (người trẻ): Trần Thu Hà, nữ, 27 tuổi, đặt lịch bằng điện thoại.
- Thân nhân của ông Hùng: Nguyễn Thị Lan (vợ, 65 tuổi), Nguyễn Minh Khoa (con trai, 34 tuổi).
- Nhân viên: y tá tiếp nhận Lê Thị Mai; bác sĩ BS.CKI Phạm Quốc Bảo (Nội tiêu hóa), BS.CKII Võ Thanh Tâm (Nội tim mạch), ThS.BS Đặng Ngọc Anh (Da liễu); kỹ thuật viên Huỳnh Văn Phúc; thu ngân Trương Thị Hồng; nhân viên phát thuốc Ngô Văn Lộc; cán bộ quản lý Đỗ Thị Thu; quản trị viên Nguyễn Hoàng Long.
- Chuyên khoa mẫu: Nội tổng quát, Nội tiêu hóa, Nội tim mạch, Nội thần kinh, Cơ xương khớp, Hô hấp, Nội tiết, Da liễu, Tai mũi họng, Mắt, Răng hàm mặt, Sản phụ khoa, Nhi, Ngoại tổng quát, Tiết niệu, Y học cổ truyền.
- Kịch bản gợi ý khoa thường: "Đau bụng vùng thượng vị, ợ chua, buồn nôn 3 ngày, ăn xong đau nhiều hơn" gợi ý Nội tiêu hóa (chính), Nội tổng quát (thay thế). Lý do khám tạo sẵn: "Đau thượng vị, ợ chua, buồn nôn 3 ngày, đau tăng sau ăn."
- Kịch bản khẩn: "Đau ngực lan lên tay trái, khó thở" kích hoạt khung khẩn cấp (không gợi ý khoa như bình thường).
- Mã mẫu: lịch hẹn LH-261026-0148, đơn thuốc DT-261026-0072, hóa đơn HD-261026-0215.
- Hóa đơn mẫu: Khám nội 38.700 đ, Xét nghiệm máu 215.000 đ, Siêu âm bụng 180.000 đ. Định dạng tiền "128.300 đ". BHYT chi trả và phần người bệnh trả tính hợp lý theo các khoản này.
- Số điện thoại, địa chỉ, giờ làm việc: dùng dữ liệu giả có ghi chú mẫu (ví dụ 028 3xxx xxxx). Số 115 là số thật. Chủ nhật không nhận lịch hẹn.

### B9. Cách prototype hoạt động

- Công nghệ: một ứng dụng web React và Tailwind, mỗi màn hình một route theo mã (ví dụ /pb-05). Dữ liệu mẫu nằm trong một module duy nhất, giữ trong bộ nhớ ứng dụng, không cần máy chủ. Nếu công cụ là Figma thuần: frame cho desktop 1440, tablet 768, mobile 390, có prototype link giữa các frame.
- Responsive: khu công khai và khu người bệnh thiết kế mobile trước (390), có đủ 1440. Khu nhân viên thiết kế desktop 1440. Màn hình khám của bác sĩ có thêm bản tablet 768.
- Bấm được: mọi nút chính dẫn tới màn hình hoặc hộp thoại tiếp theo. Form kiểm tra dữ liệu giả (CCCD đủ 12 số, bắt buộc điền lý do khi từ chối). Đặt lịch xong sinh mã lịch hẹn mẫu.
- Demo Navigator: nút nổi nhỏ ở góc dưới trái, nhãn "Demo", mở bảng gồm: chuyển vai trò (Khách, Người bệnh, Y tá, Bác sĩ, Kỹ thuật viên, Thu ngân, Phát thuốc, Quản lý, Quản trị), nhảy tới màn hình theo mã, chuyển trạng thái (bình thường, đang tải, rỗng, lỗi). Có dòng "Chỉ dùng khi trình diễn". Không thuộc giao diện sản phẩm và có kiểu riêng (xám đậm) để không lẫn với giao diện chính.
- Khung chung: Khu công khai: thanh trên có logo, menu (Giới thiệu, Chuyên khoa, Bác sĩ, Tin tức, Hướng dẫn khám), nút chỉnh cỡ chữ, nút "Đăng nhập" (nút phụ) và nút "Đặt lịch khám" (nút chính đỏ). Khu người bệnh desktop: thanh trên có ba mục "Trang chủ | Hồ sơ cá nhân | Đặt lịch khám", chuông thông báo, menu tài khoản. Khu người bệnh mobile: thanh dưới cao 64px có ba mục kèm nhãn chữ, mục "Đặt lịch khám" là nút đỏ nổi. Khu nhân viên: thanh bên trái theo vai trò, thanh trên có ô tìm kiếm nhanh theo CCCD hoặc mã (Ctrl+K), thẻ vai trò, khoa hoặc phòng, menu tài khoản có "Đăng xuất". Chân trang khu công khai: nền blue-800, chữ trắng, có khẩu hiệu, giờ làm việc, điện thoại, địa chỉ, khung bản đồ giữ chỗ.

### B10. Phạm vi và điều không được làm

- Không thêm chức năng ngoài sơ đồ trang ở Phần C.
- Không thiết kế nội trú, cấp cứu (trừ khung hướng dẫn gọi 115), tồn kho thuốc, giám định BHYT, kế toán.
- Không đưa ra chẩn đoán bệnh hay tên bệnh trong phần gợi ý chuyên khoa.
- Không dùng màu ngoài bộ token ở B3, không đặt mã màu rời trong thành phần.
- Không dùng ảnh người thật, emoji, băng rôn tự chạy, "Lorem ipsum".
- Không sao chép website của bất kỳ bệnh viện nào.

### B11. Cách làm việc theo đợt

Chỉ làm đúng đợt tôi dán. Khi xong một đợt, trả lời theo thứ tự: (1) danh sách màn hình đã làm kèm mã; (2) các giả định mới đã đặt; (3) tối đa 3 câu hỏi cần tôi xác nhận; (4) dừng và chờ tôi gõ "tiếp tục". Không làm trước đợt sau. Không đổi token đã chốt ở Đợt 0 khi chưa hỏi tôi.

## Phần C. Prompt từng đợt (dán sau Phần B, mỗi lần một đợt)

### Đợt 0. Hệ thống thiết kế và khung

Dán nguyên văn:

Làm Đợt 0. Chưa làm màn hình nghiệp vụ nào. Dựng:
1. Một trang "Hệ thống thiết kế" (route /design-system) trình bày: bảng màu theo token B3 kèm tỷ lệ tương phản, thang chữ B4, khoảng cách, bo góc, đổ bóng, bộ icon, và toàn bộ thành phần ở B5 ở đủ trạng thái. Mỗi thành phần có tên chuẩn (ví dụ Button/Primary, Badge/AppointmentStatus) để lập trình viên dùng lại.
2. Ba khung trang: khung công khai, khung người bệnh (desktop và mobile), khung nhân viên. Đặt nội dung giả "Nội dung trang" bên trong.
3. Demo Navigator đúng như B9.
4. Hộp thoại hết phiên và nút chỉnh cỡ chữ hoạt động thật: bấm A++ thì toàn bộ chữ phóng 130% mà bố cục không vỡ.
5. Bộ thẻ trạng thái đủ 5 nhóm ở B7 mục 13, mỗi thẻ có câu "Điều này nghĩa là gì" (viết mẫu cho từng nhãn).
Kết thúc theo B11.

### Đợt 1. Khu công khai (CK-01 đến CK-07), desktop 1440 và mobile 390

| Mã | Màn hình | Yêu cầu riêng |
|---|---|---|
| CK-01 | Trang chủ | Thứ tự khối: thanh trên; hero hai cột (trái: tiêu đề H1 "Đặt lịch khám trực tuyến tại Bệnh viện Nguyễn Tri Phương", một dòng mô tả, nút đỏ "Đặt lịch khám", nút phụ "Xem hướng dẫn đi khám"; phải: thẻ "Bạn muốn làm gì hôm nay?" với bốn ô việc lớn); dải số liệu (hạng I, 800 giường, khoảng 600.000 lượt khám ngoại trú mỗi năm, từ năm 1903); "Đi khám trong 3 bước"; "Chuyên khoa" (8 chip và nút "Xem tất cả"); "Đội ngũ bác sĩ" (4 thẻ); "Tin tức" (3 thẻ); dải "Cần hỗ trợ?" có điện thoại và giờ làm việc; chân trang. Mobile: hero xếp dọc, thanh nút "Đặt lịch khám" cố định ở đáy |
| CK-02 | Chuyên khoa và dịch vụ | Danh sách dạng thẻ có ô tìm khoa; trang chi tiết một khoa gồm giới thiệu, dịch vụ, bác sĩ của khoa, nút "Đặt lịch khám khoa này" đi thẳng vào bước chọn ngày giờ |
| CK-03 | Đội ngũ bác sĩ | Danh sách có lọc theo khoa dạng chip; chi tiết bác sĩ gồm học hàm, chuyên khoa, lịch khám trong tuần, nút "Đặt lịch với bác sĩ này" |
| CK-04 | Tin tức | Danh sách và chi tiết, chữ bài 18px, độ rộng dòng tối đa 720px |
| CK-05 | Hướng dẫn quy trình khám | Ba tab theo loại hình (BHYT, Dịch vụ, Theo yêu cầu). Mỗi tab là đường thời gian các bước và danh sách giấy tờ cần mang |
| CK-06 | Đăng nhập người bệnh | Desktop chia đôi: trái nền blue-50 có họa tiết dấu cộng, logo và khẩu hiệu; phải là form. Bước 1 nhập số CCCD. Bước 2 màn hình mô phỏng "Mở ứng dụng VNeID để xác nhận" có nút "Giả lập: đã xác nhận". Có nút phụ "Đăng nhập bằng VNeID", liên kết "Đăng ký lần đầu" và liên kết "Bạn là nhân viên bệnh viện? Đăng nhập tại đây". Trạng thái lỗi: CCCD sai định dạng, không tìm thấy |
| CK-07 | Đăng nhập nhân viên | Tên đăng nhập, mật khẩu có nút ẩn/hiện, "Quên mật khẩu". Dòng nhắc: "Mọi thao tác được ghi lại theo tài khoản của bạn." Trạng thái lỗi: sai mật khẩu, tài khoản bị khóa |

Kết thúc theo B11.

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

### Đợt 6. Ghép luồng, trạng thái biên, bàn giao

Dán nguyên văn:

Làm Đợt 6:
1. Nối năm luồng thành chuỗi bấm được từ đầu đến cuối, mỗi luồng có một mục trong Demo Navigator: (a) Người bệnh đặt lịch có dùng gợi ý khoa, gồm nhánh sửa khoa và nhánh dấu hiệu khẩn (CK-06, PB-01 đến PB-08); (b) Y tá duyệt lịch: xác nhận, đổi khoa, chuyển bác sĩ, từ chối, rồi người bệnh thấy trạng thái mới; (c) Ngày khám: quét CCCD, cấp số, khám, cận lâm sàng, kết luận, kê đơn, thanh toán, quét QR phát thuốc; (d) Xin đồng ý lấy dữ liệu VNeID và thu hồi; (e) Xin giấy chuyển tuyến, duyệt, tải hoặc in.
2. Dựng sáu trạng thái biên, mỗi trạng thái có màn hình hoặc khung thật và câu chữ hướng dẫn cách sửa: lỗi mạng, hết khung giờ khi đang chọn, CCCD không tìm thấy, mã QR không hợp lệ, lịch hẹn quá hạn, mất quyền truy cập.
3. Rà soát toàn bộ theo Phần D và liệt kê chỗ nào chưa đạt.
4. Ghi chú bàn giao cho lập trình viên: danh sách thành phần với tên chuẩn, hành vi tương tác, dữ liệu hiển thị ở từng màn hình, bộ token xuất dạng CSS variables và JSON.
5. Liệt kê giả định đã đặt và câu hỏi cần tôi xác nhận.

## Phần D. Checklist nghiệm thu (dùng sau mỗi đợt)

Hình thức và màu:
- Mọi màu lấy từ token B3. Không có mã màu rời trong thành phần.
- Mỗi màn hình hoặc hộp thoại có tối đa một nút đỏ đặc.
- Đỏ không xuất hiện ở tiêu đề, nền lớn, liên kết hay trang trí.
- Chữ nhỏ không đặt trên blue-500. Chữ đỏ trên nền nhạt dùng red-700.
- Logo giữ nguyên tỷ lệ và màu, có khoảng trống xung quanh.

Dễ dùng cho người cao tuổi:
- Khu người bệnh không có chữ nhỏ hơn 16px. Nội dung chính 18px.
- Nút và vùng bấm đạt 48px trở lên, cách nhau tối thiểu 8px.
- Bật A++ (130%) vẫn không vỡ bố cục, không cắt chữ, ở cả 390 và 1440.
- Ở màn hình 390, không có thanh cuộn ngang.
- Mọi trạng thái đều đọc được khi in đen trắng: có nhãn chữ.
- Không có thuật ngữ y khoa chưa đổi theo bảng B6 ở khu người bệnh.
- Mỗi màn hình trả lời được hai câu hỏi: "Tôi đang ở đâu?" và "Bước tiếp theo là gì?".

Nghiệp vụ:
- Dòng lưu ý gợi ý khoa xuất hiện nguyên văn ở PB-05 và không có tên bệnh nào.
- Hộp thoại xin đồng ý VNeID có hai nút cùng độ nổi bật và có đường thu hồi.
- Dị ứng thuốc hiện ở BS-03 và BS-05 mà không phải cuộn tìm.
- PT-02 có đủ ba trạng thái bằng chữ lớn.
- Nhãn trạng thái đúng bảng B7 mục 13 ở mọi nơi.
- Không có chức năng nào ngoài phạm vi (nội trú, tồn kho, giám định BHYT, kế toán).

Dữ liệu và tương tác:
- Nhân vật, mã và ngày đúng B8 ở mọi màn hình. Không có "Lorem ipsum".
- Mọi nút chính dẫn tới đích. Tab theo thứ tự hợp lý, thấy vòng focus.
- Demo Navigator chuyển được vai trò, màn hình và trạng thái (bình thường, đang tải, rỗng, lỗi).

Khác biệt với tham chiếu:
- Có đủ 5 điểm khác biệt ở B2. Không có băng rôn tự chạy, lưới tin dày hay ảnh stock.

## Phần E. Giả định của bản 2 cần bạn xác nhận

1. Màu đỏ #e02519 đảm nhận cả vai trò nút hành động chính lẫn khung lỗi và cảnh báo chặn. Để tránh nhầm, lỗi luôn có biểu tượng và chữ, còn nút hành động là nền đặc. Nếu bạn muốn tách hẳn, cần thêm một màu cho lỗi, nhưng sẽ vượt khỏi hai màu chủ đạo.
2. Xanh #008dc9 chỉ đạt 3,7:1 với chữ trắng nên không dùng làm nền nút có chữ. Nút phụ dùng viền #008dc9 với chữ blue-800, nền xanh đặc có chữ dùng blue-600 hoặc blue-800. Các sắc blue-600, blue-800, blue-100 và blue-50 là sắc phái sinh do tôi đề xuất.
3. Font Be Vietnam Pro được chọn vì hiển thị dấu tiếng Việt tốt. Bạn có thể đổi nếu bệnh viện có font nhận diện riêng.
4. Chưa có chế độ tối.
5. Khi thu hồi đồng ý VNeID, giả định bác sĩ không còn xem được dữ liệu y tế nền và hệ thống ngừng lấy dữ liệu mới. Việc dữ liệu đã lấy có bị xóa hay không cần bệnh viện xác nhận.
6. Lịch hẹn không nhận vào Chủ nhật. Giờ nhận lịch và số chỗ mỗi khung là dữ liệu mẫu.
7. Thời gian tự đăng xuất do không thao tác là 10 phút, cảnh báo trước 60 giây. Con số này cần theo quy định an toàn thông tin của bệnh viện.
8. Địa chỉ, số điện thoại, giờ làm việc, tên bác sĩ, danh sách chuyên khoa và giá dịch vụ đều là dữ liệu mẫu, cần thay bằng dữ liệu thật trước khi trình bày với bệnh viện.
9. Ảnh logo bạn gửi được dùng nguyên bản. Nếu cần bản vector (SVG) để sắc nét ở cỡ nhỏ, hãy lấy từ phòng truyền thông của bệnh viện.
