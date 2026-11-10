# Báo cáo cá nhân — Ngày 2 Object Detection

## 1. Thông tin học viên

- Họ tên: Đỗ Phương Mạnh
- MSSV: 2A202603148
- Email: 26ai.manhdp@vinuni.edu.vn
- Lớp: K5 – L2A
- Link repo bài làm: https://github.com/manhtotbung/K5-L2A-DAY02-DoPhuongManh-2A202603148-ObjectDetection
- Link Colab đã chạy: <!-- TODO sau CP6 -->

## 2. Schema và mini guideline

Chi tiết tại [GUIDELINE_MINI.md](../../GUIDELINE_MINI.md). Schema 4 lớp cố định: `car`=0, `truck`=1, `bus`=2, `van`=3. Lớp mô tả loại xe, không mô tả trạng thái (xe đỗ, bị che vẫn gán theo loại).

Các ca mơ hồ đã quyết định:

- `drive_003` — xe trắng thân hộp, model pre-label gán `truck` → sửa thành `van` vì thân liền khối, không có cabin tách rời thùng hàng, ngắn hơn bus.
- `drive_002` — xe trắng thân cao có giá nóc (SUV hay minivan) → `van` vì đầu xe rất ngắn, thân hộp liền khối.
- `drive_001` — SUV bị cột điện che một dải dọc → `car`, một box trùm qua cột vì xe lộ rõ ở hai bên.
- `drive_001` — khối trắng hình hộp sau SUV, bị che gần hết → không gán vì không xác định được lớp; theo quy tắc không tự đoán.

Áp dụng schema: taxi, pickup, SUV → `car`; `truck` chỉ khi có cabin tách rời thùng hàng; minivan/xe thân hộp liền khối → `van`. Xe bị che hoặc bị rìa ảnh cắt vẫn gán nếu nhận ra lớp, box theo phần nhìn thấy.

Cần lab coach làm rõ: ranh giới SUV cỡ lớn / MPV giữa `car` và `van` khi nhìn từ trên cao, ảnh độ phân giải thấp; mức nhỏ tối thiểu để gán xe ở xa trong đoàn xe dày đặc.

## 3. Annotation và self-QC

- Số file nhãn train: 50 (`dataset/labels/train/`), tổng 1249 box: car 1086, bus 101, van 41, truck 21.
- Kết quả checker và lỗi đã xử lý: `python tools/check_yolo_labels.py` → `ĐẠT cấu trúc: 50/50 file nhãn hợp lệ`. 
- Danh sách ít nhất 10 ảnh đã rà bằng mắt: `drive_001`, `drive_002`, `drive_003`, `drive_008`, `drive_019`, `drive_021`, `drive_024`, `drive_027`, `drive_030`, `drive_040`
- Lỗi bỏ sót/box/class phát hiện khi rà và cách sửa:
  - Box trùng: một xe có 2–3 box (do chạy auto-label nhiều lần, hoặc model gán cùng xe là cả `car` và `truck`) → giữ một box.
  - Sai lớp: taxi, SUV bị model gán `truck` → `car`; minivan bị gán `truck`/`car` → `van` (model COCO không có lớp `van`).
  - Bỏ sót: xe nhỏ ở xa và đoàn xe nối đuôi trong ảnh đông (`drive_002`, `drive_003`) → thêm box.
  - Box lỏng / gộp nhiều xe: kéo sát mép xe, tách thành box riêng.
  - Box gộp SUV với vật thể phía sau (`drive_001`) → tách, SUV gán `car`.
  - Box sai sau pre-label: xe con đỗ sau bụi cây bị gán `van` (`drive_001`) → `car`; box nhỏ ở hàng rào không có xe (`drive_001`) và box trúng người đi xe đạp (`drive_003`) → xóa.
- Commit khóa nhãn ban đầu: `32a8396`

## 4. Gold QC và rework

| Chỉ số | Trước sửa | Sau sửa nếu có |
|---|---:|---:|
| TP | 476 | 484 |
| FP | 776 | 765 |
| FN | 177 | 169 |
| Precision | 0.3802 | 0.3875 |
| Recall | 0.7289 | 0.7412 |
| F1 | 0.4997 | 0.5089 |
| mean_iou_of_matches | 0.8435 | 0.8446 |
| passed | false | true |

Kết quả: [`outputs/annotation_evaluation.json`](../../outputs/annotation_evaluation.json). Đã rà lại các box trùng, box quá rộng và các trường hợp nhầm lớp, chỉnh theo vật thể trên ảnh. Kết quả đạt theo ngưỡng điều chỉnh.

## 5. Kết quả YOLO

<!-- TODO sau CP6 (Colab) -->

- Runtime, GPU/CPU, phiên bản Ultralytics và Torch:
- Các tham số thay đổi so với notebook nếu có và lý do:
- Đường dẫn best checkpoint trong phiên chạy:

| Chỉ số trên 10 ảnh eval | Giá trị |
|---|---:|
| Precision | |
| Recall | |
| mAP50 | |
| mAP50–95 | |

Ảnh dự đoán: [`assets/eval_predictions/`](assets/eval_predictions/) · log train và chỉ số: [`assets/yolo26_runs/`](assets/yolo26_runs/)

1. Lớp nào recall thấp nhất? Ảnh nào minh họa?
2. Nếu mAP50 cao nhưng mAP50–95 thấp, cần kiểm điều gì trên box?
3. Lỗi class nào có thể liên quan guideline? Dữ kiện nào hỗ trợ nhận xét?
4. Vì sao không dùng 10 ảnh eval để train? Vai trò validation của chúng trong notebook là gì?

## 6. Khó khăn và cách xử lý

| Bước/thời điểm | Lỗi quan sát | Cách đã thử | Kết quả/còn thiếu |
|---|---|---|---|
| CP2 – gán nhãn | Ảnh độ phân giải thấp, zoom sát bị vỡ hạt, khó thấy mép xe | Zoom vừa phải, đặt cạnh box ở giữa vùng chuyển màu | Box sát hơn; xe quá nhòe không xác định lớp thì bỏ |
| CP2 – gán nhãn | Đoàn xe nối đuôi dày đặc, model bỏ sót nhiều | Thêm box cho từng xe còn tách được, box theo phần nhìn thấy | Đã bổ sung; vùng xa nhất các xe dính nhau không gán |
| CP2 – pre-label | Model gán trùng box, sai lớp (taxi/SUV → truck), không có lớp `van` | Rà từng ảnh, xóa trùng, đổi lớp theo schema | Đã sửa; ranh giới SUV/minivan vẫn còn chủ quan |
| CP2 – export | Bản export đầu thiếu box `van` | Nguyên nhân: chưa bấm Save trước khi export; Save rồi export lại | Đã xử lý |

## 7. Tự đánh giá theo rubric

<!-- TODO sau CP5/CP6: đối chiếu RUBRIC.md -->

| Hạng mục | Trạng thái |
|---|---|
| Mini guideline và ca mơ hồ | Có định nghĩa 4 lớp, quy tắc box, 4 ca mơ hồ có lý do |
| Annotation + self-QC | 50/50 file, checker đạt |
| Gold-set QC | Đã chạy, đạt: F1 0.5089 và mean IoU 0.8446 (ngưỡng F1 điều chỉnh 0.50) |
| Pipeline YOLO và phân tích | Chưa chạy |

## 8. Công cụ AI và tài liệu tham khảo

- **YOLO v7**, threshold 0.30, ánh xạ car/bus/truck.
- **Tài liệu tham khảo ngoài repo:** tài liệu CVAT (https://docs.cvat.ai), tài liệu Ultralytics YOLO (https://docs.ultralytics.com).