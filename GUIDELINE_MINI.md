# Mini guideline — bài làm cá nhân

> Điền nội dung này trước khi bắt đầu gán nhãn. Giữ lại các quyết định sau khi self-QC.

## Định nghĩa lớp

| Lớp | Định nghĩa của bạn | Ví dụ/điểm phân biệt |
| --- | --- | --- |
| `car` | Phương tiện giao thông đường bộ chở người, thường có 4 bánh và kích thước nhỏ hoặc trung bình | Sedan, SUV, hatchback, coupe. Không nhầm với van hoặc truck |
| `truck` | Phương tiện vận tải có kích thước lớn, được thiết kế chủ yếu để chở hàng hóa | Xe tải thùng, xe tải ben, xe đầu kéo |
| `bus` | Phương tiện giao thông được thiết kế để vận chuyển nhiều hành khách | Xe buýt công cộng, xe khách, xe buýt trường học |
| `van` | Phương tiện có khoang thân xe kín, thường có kích thước nhỏ hơn truck, dùng để chở người hoặc hàng hóa | Minivan, cargo van, xe van giao hàng |

## Quy tắc chung

- Box bám sát vật thể nghĩa là: Bounding Box phải bao trọn phần vật thể nhìn thấy được, sát các cạnh ngoài của phương tiện, hạn chế tối đa background và không cắt mất phần phương tiện nhìn thấy.
- Vật thể bị che khuất: Vẫn gán nhãn nếu có đủ đặc điểm để xác định loại phương tiện. Box chỉ bao quanh phần vật thể thực sự nhìn thấy, không tự suy đoán phần bị che khuất.
- Vật thể bị cắt bởi rìa ảnh:  Vẫn gán nhãn nếu có đủ thông tin để nhận diện. Box kéo đến rìa ảnh, không mở rộng ra ngoài phạm vi ảnh.
- Vật thể rất nhỏ/mơ hồ: Chỉ gán nhãn khi có thể xác định tương đối chắc chắn loại phương tiện. Nếu quá nhỏ, mờ hoặc không đủ thông tin để phân loại thì không tự suy đoán, ghi nhận để xem xét hoặc escalation.

## Ghi lại quyết định cho ít nhất ba ca mơ hồ

| Ảnh | Tình huống | Quyết định cuối | Lý do theo schema |
| --- | --- | --- | --- |
| `drive_004` | Ở phía trên góc trái của ảnh có rất nhiều vật thể bị che khuất lẫn nhau, chỉ có thể nhìn thấy một phần của vật thể, rất khó hình dung nó thuộc lớp nào | Không gán nhãn, ghi nhận ca mơ hồ để xem xét | Không đủ bằng chứng để xác định class, tránh gán theo suy đoán |
| `drive_005` | Ở hướng 9h có một phương tiện bị cắt bởi rìa ảnh, nhưng có thể nhận ra đây là một chiếc xe hơi | Gán nhãn car, Box kéo đến rìa ảnh | Loại phương tiện xác định được, Bounding Box vẫn nằm trong phạm vi ảnh |
| `drive_008` | Có một vài chiếc phương tiện đang bị che khuất một phần, nhưng vẫn nhận ra chúng là những chiếc xe hơi | Gãn nhãn car, Box bao quanh phần nhìn thấy | Vẫn đủ đặc điểm nhận nhận diện ra car, không suy đoán phần bị che khuất |
