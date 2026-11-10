# Mini guideline — bài làm cá nhân

> Điền nội dung này trước khi bắt đầu gán nhãn. Giữ lại các quyết định sau khi self-QC.

## Định nghĩa lớp

| Lớp | Định nghĩa của bạn | Ví dụ/điểm phân biệt |
| --- | --- | --- |
| `car` (0) | Xe con chở người cỡ nhỏ: sedan, hatchback, SUV, taxi, pickup (bán tải). | Thân thấp, ngắn, thường 4–5 chỗ. Pickup có thùng hở phía sau nhưng cabin nhỏ → vẫn là `car`, **không** phải `truck`. Không gán bus, truck, mô tô/xe máy vào lớp này. |
| `truck` (1) | Xe tải chở hàng: xe tải thùng, xe ben, đầu kéo container/sơ mi rơ moóc. | Cabin tách rời với thùng hàng/rơ moóc phía sau, thân cao và dài. Pickup hoặc xe cabin nhỏ → `car`. |
| `bus` (2) | Xe buýt, xe khách, minibus chở hành khách. | Thân hộp dài, nhiều cửa sổ hành khách chạy dọc thân, thường có cửa lên xuống ở giữa/đầu. Van chở hàng hoặc van chở người cỡ nhỏ → `van`. |
| `van` (3) | Xe van/minivan chở hàng hoặc chở người. | Thân hộp liền khối (cabin và khoang sau chung một khối), cao hơn `car` nhưng ngắn hơn hẳn `bus`. Xe buýt lớn → `bus`; xe con thông thường → `car`. |

Lớp mô tả **loại xe**, không mô tả trạng thái: xe đang đỗ, đang dừng đèn đỏ hay bị che vẫn gán theo loại xe.

## Quy tắc chung

- **Box bám sát vật thể nghĩa là:** bốn cạnh box chạm đúng mép ngoài cùng của phần thân xe nhìn thấy được (kể cả gương chiếu hậu nếu thấy rõ), không lấy thêm mặt đường, bóng đổ, trời hay xe bên cạnh. Mỗi xe một box riêng; không gộp hai xe vào một box.
- **Vật thể bị che khuất:** nếu phần nhìn thấy đủ để xác định lớp thì vẽ box quanh **phần nhìn thấy được**, không đoán phần bị che. Hai xe chồng lên nhau → hai box riêng, mỗi box theo phần nhìn thấy của từng xe.
- **Vật thể bị cắt bởi rìa ảnh:** box chạm đúng rìa ảnh, không kéo ra ngoài và không đoán phần nằm ngoài khung. Vẫn gán nếu nhận diện được lớp.
- **Vật thể rất nhỏ/mơ hồ:** nếu chỉ còn mảnh quá nhỏ, không xác định chắc lớp thì **không tự đoán**; ghi tên ảnh vào bảng ca mơ hồ bên dưới và hỏi giảng viên.
- **Không gán:** người đi bộ, xe đạp, mô tô/xe máy, biển báo, đèn giao thông, hình xe trên bảng quảng cáo, ảnh phản chiếu trong kính/gương.
- Ảnh không có xe thuộc schema vẫn có file `.txt` rỗng.

## Ghi lại quyết định cho ít nhất ba ca mơ hồ

| Ảnh | Tình huống | Quyết định cuối | Lý do theo schema |
| --- | --- | --- | --- |
| `drive_003` | Xe trắng thân hộp ở phía xa bên phải; model pre-label gán `truck` | `van` | Thân liền khối (cabin và khoang sau chung một khối), có hàng cửa sổ hông, không có cabin tách rời thùng hàng nên không phải `truck`; ngắn hơn hẳn xe buýt nên không phải `bus`. |
| `drive_002` | Xe trắng thân cao, có giá nóc, đầu xe ngắn — phân vân SUV (`car`) hay `van` | `van` | Đầu xe rất ngắn, thân hộp cao liền khối giống minivan hơn SUV (SUV có nắp capô nhô rõ). Schema xếp minivan vào `van`. |
| `drive_001` | Xe SUV trắng bị cột điện che một dải dọc ở giữa thân | `car`, một box duy nhất trùm qua cột | Cột là vật che mảnh, xe lộ rõ ở hai bên nên vẫn xác định được lớp; vẽ một box theo phần nhìn thấy, không tách thành hai box. |
| `drive_001` | Khối trắng hình hộp phía sau SUV trắng đỗ bên lề, bị SUV và cột che gần hết | Không gán | Phần nhìn thấy quá ít, không chắc là xe hay vật khác và không xác định được `van`/`truck`; theo quy tắc không tự đoán khi không chắc lớp. |
