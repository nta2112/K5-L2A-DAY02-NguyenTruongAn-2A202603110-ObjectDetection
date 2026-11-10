# Mini guideline — bài làm cá nhân

> Điền nội dung này trước khi bắt đầu gán nhãn. Giữ lại các quyết định sau khi self-QC.

## Định nghĩa lớp

| Lớp | Định nghĩa của bạn | Ví dụ/điểm phân biệt |
| --- | --- | --- |
| `car` |Xe ô tô cỡ nhỏ chở người(4-8 chỗ) | xe taxi, xe con|
| `truck` |Xe cơ giới cỡ lớn chuyên dụng chở hàng hóa nặng. |Xe tải thùng, xe ben, xe đầu kéo container. Thùng hàng tách biệt cabin lái|
| `bus` |Xe vận tải hành khách công cộng hoặc du lịch cỡ lớn ( trên ≥2 4 chỗ)|Xe buýt đô thị, xe khách liên tỉnh giường nằm/ghế ngồi lớn. Thân dài, nhiều cửa sổ hành khách |
| `van` |Xe thân liền khối hộp chở hàng nhẹ hoặc chở khách tầm trung (9–16 chỗ). |Minivan gia đình, xe van giao hàng. Thấp/ngắn hơn bus, không có thùng rời như truck. |

## Quy tắc chung

- Box bám sát vật thể nghĩa là: Bounding box phải ôm khít các mép ngoài cùng của thân xe (cản trước/sau, nóc xe, bánh xe tiếp đất); không để thừa khoảng trống mặt đường, bầu trời hay vật thể bên cạnh.
- Vật thể bị che khuất: Chỉ vẽ bounding box bao quanh phần thân xe thực sự nhìn thấy được; tuyệt đối không vẽ đoán mò (hallucinate) phần bị che khuất bởi xe khác, cây cối hay biển báo.
- Vật thể bị cắt bởi rìa ảnh: Kéo cạnh của box chạm sát mép khung ảnh; không mở rộng box ra ngoài giới hạn ảnh và không đoán phần xe nằm ngoài khung hình.

- Vật thể rất nhỏ/mơ hồ: Nếu vật thể quá nhỏ (ở tít xa) hoặc bị che khuất quá 80% khiến không thể nhận diện chắc chắn loại xe (class) bằng mắt thường thì bỏ qua không gán, tránh tạo nhãn nhiễu cho mô hình.

## Ghi lại quyết định cho ít nhất ba ca mơ hồ

| Ảnh | Tình huống | Quyết định cuối | Lý do theo schema |
| --- | --- | --- | --- |
| `drive_001` |Xe ở làn ngược chiều tít xa bên kia dải phân cách và xe chỉ nhìn thấy một góc rất nhỏ. |Không gán box cho các xe làn ngược chiều ở xa ngoài tầm quan sát; chỉ gán các xe di chuyển trên luồng đường chính. |Các vật thể ngoài phạm vi quan sát chính hoặc không ảnh hưởng quỹ đạo di chuyển sẽ không gán để tránh tạo nhãn nhiễu.|
| `drive_003` |Dòng xe con san sát nhau, che khuất nhau |Vẫn đánh nhãn cho các xe dù bị khuất nhưng vẫn có thể định dạng được đó là xe gì |Quy tắc schema nêu rõ: vật thể bị che khuất một phần vẫn phải gán box riêng quanh phần nhìn thấy nếu còn đủ đặc trưng nhận diện lớp; không gộp hai xe vào một box|
| `drive_006` |Có rất nhiều xe, cả các xe ở rất xa, nhỏ và khó phân biệt, nhận dạng |Đánh nhãn nhiều xe có thể nhìn thấy nhất có thể. Bỏ qua các xe ở quá xa. |Tránh tạo nhãn nhiễu từ các vật thể quá xa và mờ.|
