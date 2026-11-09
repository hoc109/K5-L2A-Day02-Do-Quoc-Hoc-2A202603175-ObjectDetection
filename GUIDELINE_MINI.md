# Mini guideline — bài làm cá nhân

> Điền nội dung này trước khi bắt đầu gán nhãn. Giữ lại các quyết định sau khi self-QC.

## Định nghĩa lớp

| Lớp | Định nghĩa của bạn | Ví dụ/điểm phân biệt |
| --- | --- | --- |
| `car` | Xe ô tô con chở người cỡ nhỏ đến trung bình (4–7 chỗ), bao gồm sedan, hatchback, coupe, SUV, crossover, taxi và xe bán tải (pickup) cabin nhỏ. | Có nắp capo và cốp sau thấp hoặc dáng SUV thể thao; phân biệt với `van` ở chỗ thân xe thấp hơn, mũi xe dài hơn và không có dạng hình hộp đứng vuông vức. |
| `truck` | Xe tải chở hàng chuyên dụng (thùng kín, thùng lửng, thùng khung mui/bạt), xe đầu kéo, xe bồn, xe cứu hộ giao thông có sàn/cần cẩu phía sau. | Có cabin đầu xe tách biệt rõ với phần thùng chở hàng lớn hoặc thiết bị chuyên dụng phía sau; không gộp xe bán tải cá nhân cỡ nhỏ (thuộc `car`). |
| `bus` | Xe buýt công cộng (1 tầng, 2 tầng, xe buýt khớp nối 2 toa) và xe khách/coach cỡ lớn hoặc minibus tuyến cố định chở nhiều hành khách. | Thân xe dài, cao, nhiều ô cửa kính liên tiếp dọc thân và kính chắn gió trước rộng thẳng đứng; lớn và dài hơn rõ rệt so với `van`. |
| `van` | Xe van và minivan/microvan dạng hình hộp kín dùng chở hàng nhẹ hoặc chở người (thường 7–12 chỗ, đầu xe ngắn/bằng, trần cao vừa phải, cửa lùa bên hông). | Thân hình hộp vuông vức, đầu xe ngắn chúc xuống, kích thước lớn hơn sedan (`car`) nhưng ngắn hơn nhiều so với xe buýt (`bus`) và không có thùng hàng tách rời như `truck`. |

## Quy tắc chung

- **Box bám sát vật thể nghĩa là:** Bốn cạnh của bounding box tiếp xúc sát với biên ngoài cùng của phần thân xe và bánh xe thực tế nhìn thấy được trong ảnh; tuyệt đối không lấy dư mặt đường, bóng đổ của xe, vùng lóa đèn pha phía trước hay khoảng trống nền xung quanh.
- **Vật thể bị che khuất:** Vẽ một bounding box riêng bao quanh đúng phần diện tích nhìn thấy được (visible part) của xe nếu phần lộ ra vẫn đủ đặc trưng để xác định chắc chắn một trong 4 lớp của schema; không tự suy đoán vẽ mở rộng ra phần bị che. Hai xe chồng lấn nhau phải tách thành hai box riêng biệt.
- **Vật thể bị cắt bởi rìa ảnh:** Cạnh bounding box dừng chính xác tại biên ảnh (tọa độ chuẩn hóa trong `[0, 1]`), ôm sát phần thân xe còn nằm trong khung hình nếu vẫn nhận diện rõ lớp; nếu chỉ lộ một mảnh quá nhỏ sát mép không đủ xác định loại xe thì không gán.
- **Vật thể rất nhỏ/mơ hồ:** Các xe ở quá xa phía chân trời/gầm cầu vượt bị nhòe thành đốm điểm ảnh không thể phân biệt rõ lớp (`car`/`van`/`truck`/`bus`), hoặc các phương tiện ngoài schema (xe máy, xe đạp, xe 3 bánh, người đi bộ) thì **không tự đoán và không gán nhãn**.

## Ghi lại quyết định cho ít nhất ba ca mơ hồ

| Ảnh | Tình huống | Quyết định cuối | Lý do theo schema |
| --- | --- | --- | --- |
| `drive_008.jpg` | Xuất hiện xe buýt khớp nối 2 toa (vàng-trắng-xanh), xe tải thùng đỏ chở đầy hàng ở giữa ngã tư, hai xe minivan trắng và người đi xe máy/xe đạp bên trái. | Gán 1 box `bus` (ID 2) ôm trọn xe buýt khớp nối, 1 box `truck` (ID 1) cho xe tải đỏ, `van` (ID 3) cho xe microvan hộp trắng; bỏ qua xe máy/xe đạp. | Mỗi phương tiện thuộc schema là một box riêng theo phần nhìn thấy; mô tô/xe đạp nằm ngoài 4 lớp của schema nên không gán. |
| `drive_014.jpg` | Xe cứu hộ giao thông (tow truck màu trắng có cần trục kéo phía sau) đỗ sát dải phân cách, xe van/cứu thương màu đỏ ở bên phải và 3 xe con bị cắt một phần ở sát mép dưới ảnh. | Gán xe cứu hộ là `truck` (ID 1), xe hộp đỏ bên phải là `van` (ID 3), và vẽ box chạm sát biên dưới (`y_max = 1.0`) cho 3 xe `car` (ID 0) bị cắt khung. | Xe cứu hộ có kết cấu cabin + sàn/cần cẩu tải chuyên dụng nên thuộc `truck`; các xe bị cắt khung ở mép dưới vẫn nhìn rõ trên 50% thân xe con nên phải gán đủ và dừng đúng rìa ảnh. |
| `drive_024.jpg` | Làn gần nhất có một phương tiện 3 bánh có mái che (xe ba gác/xe lam nhỏ) đi phía trước hai xe minivan/microvan thân hộp màu bạc-xám và hàng xe con/taxi. | Không gán phương tiện 3 bánh có mái che; gán hai xe thân hộp màu bạc-xám là `van` (ID 3) và các xe sedan/taxi xung quanh là `car` (ID 0). | Phương tiện 3 bánh có mái che thuộc nhóm mô tô/xe ba bánh không nằm trong 4 lớp ô tô của schema; hai xe màu bạc có dáng hộp đứng đầu ngắn đặc trưng của `van`. |
| `drive_033.jpg` | Làn giữa có xe tải thùng kín màu trắng dễ nhầm với `van`/`bus` khi nhìn từ trên cao; phía xa gần cầu vượt có nhiều đốm xe rất nhỏ và mờ. | Gán xe thùng kín màu trắng là `truck` (ID 1); chỉ gán các xe ở khoảng cách trung bình–gần còn nhận diện rõ hình khối và bỏ qua các đốm xe quá nhỏ phía cầu vượt. | Xe màu trắng có cabin đầu kéo tách biệt với thùng hàng vuông phía sau nên là `truck`; các xe quá xa ở chân trời không đủ chi tiết xác định lớp theo quy tắc không tự đoán. |
| `drive_042.jpg` | Xe tải chở các bao hàng xanh/trắng chất cao vượt quá nóc cabin đi cạnh xe buýt 2 tầng (bên phải) và xe buýt đỏ-trắng bị cắt khung ở góc dưới. | Gán `truck` (ID 1) bao gồm cả phần thân xe và khối hàng hóa chất trên thùng xe; gán cả hai xe buýt là `bus` (ID 2) bám sát phần nhìn thấy. | Hàng hóa chở trên thùng là một phần thể tích nhìn thấy gắn liền của xe tải (`truck`) trong cảnh giao thông; hai xe buýt (1 tầng và 2 tầng) đều thuộc lớp `bus`. |
