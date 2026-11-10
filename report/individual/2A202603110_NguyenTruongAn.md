# Báo cáo cá nhân — Ngày 2 Object Detection

Đổi tên file theo `MSSV_HoTen.md`, ví dụ `20241234_NguyenVanAn.md`. Điền kết quả thật, xóa dòng hướng dẫn khi nộp. Xem [quy định nộp](../../SUBMISSION.md).

## 1. Thông tin học viên

- Họ tên: Nguyễn Trường An
- MSSV: 2A202603110
- Email: 26ai.annt5@vinuni.edu.vn
- Lớp: L2A
- Link repo bài làm: https://github.com/nta2112/K5-L2-DAY02-NguyenTruongAn-2A202603110-ObjectDetection
- Link Colab đã chạy: https://colab.research.google.com/drive/14OHA4azQZ34pULmECqWt0XkJnLErPeZ7?usp=sharing

## 2. Schema và mini guideline

Dẫn tới [GUIDELINE_MINI.md](../../GUIDELINE_MINI.md). Tóm tắt ba ca mơ hồ đã quyết định, cách áp dụng schema và phần cần giảng viên làm rõ.

## 3. Annotation và self-QC

- Số file nhãn train: 50 file nhãn .txt
- Kết quả checker và lỗi đã xử lý:Toàn bộ 50 file đều hợp lệ, không có lỗi định dạng hay box vượt biên ảnh. Lỗi đã xử lý: Xử lý các box có góc xoay thành bounding box thẳng bao trọn vật thể và cắt gọt box nếu chạm rìa ảnh để không bị lỗi.

- Danh sách ít nhất 10 ảnh đã rà bằng mắt: drive_001.jpg, drive_002.jpg, drive_003.jpg, drive_004.jpg, drive_005.jpg, drive_006.jpg, drive_007.jpg, drive_008.jpg, drive_009.jpg, drive_010.jpg.

- Lỗi bỏ sót/box/class phát hiện khi rà và cách sửa: Lỗi vẽ thừa: Ở các ảnh góc rộng, ban đầu gán cả các xe ở làn ngược chiều bên kia dải phân cách và xe đỗ xa. Cách sửa: Xóa bớt các box ngoài làn đường chính để tập trung vào vùng di chuyển cũng như các xe gần nhất. Lỗi nhầm Class: Một số xe minivan chỗ ban đầu bị gán nhầm sang bus. Cách sửa: Đối chiếu lại schema và chuyển về đúng nhãn, chỉ giữ nhãn bus cho xe buýt công cộng và xe khách cỡ lớn.

- Commit khóa nhãn ban đầu: 920595f

## 4. Gold QC và rework

| Chỉ số | Trước sửa | Sau sửa nếu có |
|---|---:|---:|
| TP | 404| 533|
| FP | 225| 125|
| FN | 249| 120|
| Precision | 0.6423| 0.81|
| Recall | 0.6187| 0.8162|
| F1 | 0.6303| 0.8131|
| mean_iou_of_matches | 0.8114| 0.8074|
| passed | flase| true|

Dẫn tới `outputs/annotation_evaluation.json` trong repo. Nếu rework, ghi ảnh, lỗi đã kiểm, quyết định sửa/giữ và lý do. Không suy nguyên nhân chỉ từ FP/FN. Không có rework thì ghi rõ.

## 5. Kết quả YOLO

- Runtime, GPU/CPU, phiên bản Ultralytics và Torch:
- Các tham số thay đổi so với notebook nếu có và lý do:
- Đường dẫn best checkpoint trong phiên chạy:

| Chỉ số trên 10 ảnh eval | Giá trị |
|---|---:|
| Precision | 0.3986|
| Recall | 0.3328|
| mAP50 |0.3956 |
| mAP50–95 | 23.54|

Đặt cả 2 folder `assets/eval_predictions/` và `assets/yolo26_runs/` (giải nén từ `day2_colab_outputs.zip`) cạnh báo cáo và dẫn link tương đối. Không chỉ ghi số; chỉ ra ít nhất một ảnh và vật thể cụ thể.

Trả lời bốn câu hỏi cuối notebook:

1. Lớp bus có recall thấp nhất với recall = 0.0. Ví dụ ảnh drive_052.jpg mô hình đã nhận diện nhầm bus thành car. Bởi số lượng giữa các nhãn bị chênh lệch quá nhiều (class imbalanced).

2. Nếu  mAP50 mAP50 cao nhưng mAP50–95 mAP50–95 thấp, cần kiểm tra: Độ ôm sát (Tightness) của bounding box: Box có bị vẽ quá rộng hoặc quá hẹp hay không.

3. Lỗi class liên quan trực tiếp đến guideline: Lỗi nhầm lẫn giữa van và bus. Guideline chưa phân định rõ ràng về số chỗ và kích thước giữa van/minivan khối hộp (9–16 chỗ) và minibus (≥24 chỗ). Việc thiếu định lượng cụ thể khiến người gán nhãn dễ gắn nhãn cảm tính, dẫn đến dữ liệu huấn luyện mâu thuẫn.

4. Chống rò rỉ dữ liệu (Data Leakage) và tránh hiện tượng Overfitting (học vẹt). Nếu đưa cả 10 ảnh eval vào train, mô hình chỉ ghi nhớ vị trí và hình ảnh cụ thể của 10 ảnh đó, khiến chúng ta không thể đo lường khách quan mô hình sẽ hoạt động như thế nào khi gặp các xe và cung đường mới ngoài thực tế. Vai trò của 10 ảnh eval trong notebook: Đánh giá khả năng tổng quát hóa của detector trên dữ liệu chưa từng thấy. Đồng thời kiểm tra trực tiếp xem chất lượng bộ nhãn ảnh train trước đó gán ảnh hưởng tốt hay xấu đến khả năng suy luận thực tế của mô hình.
## 6. Khó khăn và cách xử lý

| Bước/thời điểm | Lỗi quan sát | Cách đã thử | Kết quả/còn thiếu |
|---|---|---|---|
|Gán nhãn|Gán box chéo dẫn đến xuất kết quả ra .txt bị thiếu box (box lệch ko được tính) |Sửa lại các box bị lệch|Số lượng box tăng lên |
|Đánh giá Gold QC lần đầu (Chưa đạt) |Điểm F1 ban đầu chỉ đạt 0.6303 |Phân tích phân bố lỗi theo từng ảnh: Xóa bớt các box ở làn xe ngược chiều tít xa (drive_003); phóng to ảnh để bù các xe nối đuôi nhau ở dòng xe đông; chuẩn hóa lại các nhãn bị gán nhầm giữa bus, van, truck. |F1 tăng vọt từ 0.6303 lên 0.8131, Mean IoU đạt 0.8074, hệ thống chính thức thông qua điều kiện |
|Huấn luyện và Đánh giá mô hình trên Colab |Lớp bus có Recall đạt 0.0 |Kiểm tra dữ liệu tập train và phân bố nhãn trên tập eval; phân tích nguyên nhân do số lượng mẫu bus trong 50 ảnh train quá ít|Hiểu rõ hiện tượng mất cân bằng lớp (Class Imbalance) và ghi nhận hạn chế vào báo cáo phân tích mô hình. |
## 7. Tự đánh giá theo rubric


Đối chiếu [RUBRIC.md](../../RUBRIC.md): Đã hoàn thành đầy đủ tất cả các bước theo yêu cầu.

| Tiêu chí | Kết quả bài làm thực tế | Tự đánh giá |
| --- | --- | :---: |
| Hoàn thành và định dạng YOLO | 50 file nhãn `.txt` cùng basename, 5 cột normalized, 4 class ID đúng chuẩn, pass checker `check_yolo_labels.py` 100%. | 10/10 |
| Đầy đủ vật thể | Gán đủ xe thuộc schema, kể cả xe bị che khuất và cắt mép ảnh sau khi rework (giảm FN từ 249 xuống 120). | 18-20/20 |
| Độ bám sát và hình học box | Mean IoU đạt `0.8074`, box ôm sát thân xe, không lấn nền mặt đường. | 18-20/20 |
| Phân lớp nhất quán | Phân biệt đúng `car/truck/bus/van`, không gán vật thể ngoài scope (người, xe máy, biển báo). | 14-15/15 |
| Mini guideline và quyết định mơ hồ | Đã hoàn thành `GUIDELINE_MINI.md` với định nghĩa 4 lớp và 3 ca mơ hồ kèm lý do phân loại. | 10/10 |
| Gold-set annotation QC | Đạt F1 = `0.8131`  và Mean IoU = `0.8074`; F1 = 0.6303. | 15/15 |
| Pipeline YOLO và phân tích | Colab chạy GPU T4 hoàn tất, không dùng eval để train, có metrics/ảnh eval và trả lời chi tiết 4 câu hỏi phân tích. | 10/10 |
| **Tổng điểm tự đánh giá** | **Đã hoàn thành toàn bộ các yêu cầu, không còn hạng mục nào tồn đọng.** | **~95-100/100** |

## 8. Công cụ AI và tài liệu tham khảo

Ghi công cụ đã dùng, dùng ở bước nào và phần đã tự kiểm tra; không dùng thì ghi rõ. Liệt kê nguồn ngoài repo được sử dụng. Không ghi mật khẩu/token hoặc đính kèm gold train.
Phần tự thực hiện và kiểm tra: Tự tay thực hiện gán nhãn 50 ảnh trên nền tảng CVAT. Tự đối chiếu hình ảnh thực tế, rà soát lại các box bị vẽ thừa ở làn đối diện và bổ sung các box bị bỏ sót khi rework. Tự thiết lập môi trường runtime GPU T4 trên Google Colab, chạy trực tiếp  và kiểm chứng các biểu đồ, ảnh kết quả dự đoán trên tập eval.


