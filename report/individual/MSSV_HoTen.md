# Báo cáo cá nhân — Ngày 2 Object Detection

Đổi tên file theo `MSSV_HoTen.md`, ví dụ `20241234_NguyenVanAn.md`. Điền kết quả thật, xóa dòng hướng dẫn khi nộp. Xem [quy định nộp](../../SUBMISSION.md).

## 1. Thông tin học viên

- Họ tên: Bùi Quang Huy
- MSSV: 2A202603201
- Email: bqhuy2211@gmail.com
- Lớp: L2A
- Link repo bài làm: https://github.com/qhuyyy/K5-L2-Day02-BuiQuangHuy-2A202603201-ObjectDetection
- Link Colab đã chạy: https://colab.research.google.com/drive/1Zabvl1q7O_HwODKC0_bM5XhwKycYP1OH?hl=vi

## 2. Schema và mini guideline

Dẫn tới [GUIDELINE_MINI.md](../../GUIDELINE_MINI.md). Đã áp dụng schema định nghĩa lớp `car`, `truck`, `bus`, `van` và quy tắc box sát vật thể, giữ nguyên khi có che khuất hoặc cắt rìa ảnh.

- `drive_004`: nhiều vật thể chồng lấp ở góc trái, chỉ nhìn thấy một phần. Quyết định: không gán nhãn, ghi nhận ca mơ hồ vì không đủ bằng chứng để xác định class.
- `drive_005`: xe bị cắt ở rìa ảnh nhưng vẫn nhận diện là `car`; gán nhãn `car`, box kéo sát rìa ảnh.
- `drive_008`: xe che khuất một phần nhưng vẫn đủ đặc điểm là `car`; gán `car` và box chỉ bao quanh phần nhìn thấy.

## 3. Annotation và self-QC

- Số file nhãn train: 50 file labels train (`dataset/labels/train/`), tương ứng 50 ảnh train.
- Kết quả checker và lỗi đã xử lý: đã rà lại khu vực đông xe, xe bị che/đứt rìa ảnh, và giữ nguyên nhãn khi đủ bằng chứng; lỗi chủ yếu là các trường hợp mơ hồ và xe nhỏ/xa bị nhầm class hoặc thiếu box; các quyết định được ghi lại trong mini guideline.
- Danh sách ít nhất 10 ảnh đã rà bằng mắt: `drive_001`, `drive_003`, `drive_005`, `drive_006`, `drive_008`, `drive_013`, `drive_020`, `drive_024`, `drive_026`, `drive_028`, `drive_036`, `drive_038`.
- Lỗi bỏ sót/box/class phát hiện khi rà và cách sửa:
  - `drive_004`: không gán nhãn cho vật thể quá mơ hồ để tránh suy đoán.
  - `drive_005`: gán `car` cho xe cắt rìa ảnh và kéo box đến mép hình.
  - `drive_008`: giữ `car` cho các xe che khuất nhưng còn phần nhìn thấy rõ; box chỉ bao quanh phần thấy được.
  - Một số box ở ảnh đông xe bị quá rộng hoặc lệch khỏi phần thực thể; sửa bằng cách giữ box sát thân xe và không bao gồm nền/
  - Một số class `bus`/`van` ở ảnh xa bị nhầm sang `car`; sửa bằng cách dựa vào độ dài thân xe và hình dạng cửa kính, không suy đoán mẫu đã che.
- Commit khóa nhãn ban đầu: `5132448` (`1st assignment`) là commit gần nhất trong repo hiện tại cho bài làm; nhãn đã được kiểm và giữ nguyên trong branch làm việc.

## 4. Gold QC và rework

| Chỉ số | Trước sửa | Sau sửa nếu có |
|---|---:|---:|
| TP | 435 | - |
| FP | 363 | - |
| FN | 218 | - |
| Precision | 0.5451 | - |
| Recall | 0.6662 | - |
| F1 | 0.5996 | - |
| mean_iou_of_matches | 0.8543 | - |
| passed | false | - |

Dẫn tới [outputs/annotation_evaluation.json](../../outputs/annotation_evaluation.json). Không có rework thêm trong repo hiện tại để cải thiện F1; vì vậy kết luận là chưa đạt ngưỡng gold-set yêu cầu (`F1 >= 0.80`), dù mean IoU khớp còn tốt (`0.8543`), cho thấy phần lớn box đã bám sát nhưng số lượng bỏ sót và nhầm class vẫn còn nhiều.

## 5. Kết quả YOLO

- Runtime, GPU/CPU, phiên bản Ultralytics và Torch: notebook chạy trên Colab với `torch.cuda.is_available() == True`, tức dùng GPU; model dùng `YOLO('yolo26n.pt')` theo notebook chuẩn. Repo không lưu version exact của Ultralytics/Torch trong file báo cáo; với môi trường Colab thì thông số thực tế được lấy từ notebook và log chạy, không phải bằng tay cài đặt ở local.
- Các tham số thay đổi so với notebook nếu có và lý do: giữ nguyên cấu hình notebook chuẩn: `epochs=50`, `imgsz=640`, `batch=-1`, `seed=20260909`; chỉ thay `REPO_URL` và `DEVICE` theo môi trường chạy. Không đổi split `val` sang train để tránh làm đẹp metric.
- Đường dẫn best checkpoint trong phiên chạy: `outputs/yolo26_runs/vehicle_annotation/weights/best.pt` (đi kèm trong folder nộp: [best.pt](./assets/day2_colab_outputs/yolo26_runs/vehicle_annotation/weights/best.pt)).

| Chỉ số trên 10 ảnh eval | Giá trị |
|---|---:|
| Precision | 0.23296 |
| Recall | 0.31544 |
| mAP50 | 0.29841 |
| mAP50–95 | 0.20922 |

Folder đánh giá đã được lưu trong repo theo cấu trúc từ notebook:
- [eval_predictions](./assets/day2_colab_outputs/eval_predictions/)
- [yolo26_runs](./assets/day2_colab_outputs/yolo26_runs/)
- [model_eval_metrics.json](./assets/day2_colab_outputs/model_eval_metrics.json)

Ví dụ trực quan: trên ảnh [drive_057.jpg](./assets/day2_colab_outputs/eval_predictions/drive_057.jpg) model phát hiện rõ nhiều `car` ở giữa đường và hai vùng `bus`/`van` có xác suất thấp hơn; trên [drive_054.jpg](./assets/day2_colab_outputs/eval_predictions/drive_054.jpg) các box `car` ở khoảng xa càng cho thấy độ tin cậy giảm và có nhiều nhầm lớp giữa `car` và `bus`/`van`.

Trả lời bốn câu hỏi cuối notebook:

1. Lớp nào recall thấp nhất? Ảnh nào minh họa?
   - `bus` là lớp có recall thấp nhất trong biểu đồ PR/F1, với giá trị rất thấp so với `car`. Hình ảnh minh họa tốt nhất là `drive_058.jpg` và `drive_057.jpg`: các phương tiện lớn/xe buýt dễ bị model gán nhầm thành `car`, hoặc bỏ sót nếu chỉ thấy một nửa xe.
2. Nếu mAP50 cao nhưng mAP50–95 thấp, cần kiểm điều gì trên box?
   - Cần kiểm tra độ sát box, không chỉ phân lớp. Khi mAP50–95 thấp, box thường quá rộng, lệch khỏi vật thể, hoặc chụp không bám sát nền/khung. Đây là dấu hiệu của box localization yếu, đặc biệt ở xe ở xa, che khuất hoặc nằm sát rìa ảnh.
3. Lỗi class nào có thể liên quan guideline? Dữ kiện nào hỗ trợ nhận xét?
   - Lỗi quan trọng nhất là `bus`/`van` và `truck` bị nhầm sang `car`; điều này phù hợp với guideline vì những xe lớn/xe van thường chỉ nhìn thấy phần thân hoặc bị che phía sau, nên nếu không đủ dấu hiệu nhận dạng thì cần tránh suy đoán. Trường hợp `drive_004` và `drive_008` đã được ghi lại như chất liệu mơ hồ/che khuất.
4. Vì sao không dùng 10 ảnh eval để train? Vai trò validation của chúng trong notebook là gì?
   - Vì chúng là tập đánh giá đúng nghĩa để đo generalization và chọn checkpoint tốt nhất. Nếu dùng `eval` để train, metric sẽ bị “dọn” và không phản ánh thực tế. Trong notebook, 10 ảnh eval vẫn đóng vai trò validation để theo dõi learning curve và chọn `best.pt`, nhưng không được đưa vào train.

## 6. Khó khăn và cách xử lý

| Bước/thời điểm | Lỗi quan sát | Cách đã thử | Kết quả/còn thiếu |
|---|---|---|---|
| Gán nhãn train | Xe nhỏ, bị che khuất hoặc cắt rìa ảnh | Áp dụng quy tắc guideline: gán nếu đủ đặc điểm, box sát phần nhìn thấy, không suy đoán phần che | Một số trường hợp vẫn mơ hồ và cần cẩn thận khi dùng model, nhưng rubric đã ghi rõ quyết định |
| Gold QC | F1 annotation chưa đạt 0.80 | Kiểm lại ảnh đông xe, box bị rộng/nhầm lớp | Chưa đạt ngưỡng, cần rework nếu muốn đạt rubic gold-set |
| Model eval | `car` chiếm ưu thế; `bus`/`van` recall thấp | Xem confusion matrix và PR curve; so sánh với ảnh eval trực quan | Model có thể chủ động phát hiện `car` tốt hơn; class `bus`/`van` cần dữ liệu/box tốt hơn |

## 7. Tự đánh giá theo rubric

Đối chiếu với [RUBRIC.md](../../RUBRIC.md):

- Đã hoàn thành: folder nhãn train đúng chuẩn YOLO, mini guideline có ghi 3 case mơ hồ, đã tạo model và đánh giá trên 10 ảnh eval, có log/ảnh dự đoán trong folder `assets/day2_colab_outputs`.
- Chưa đạt/đang thiếu: Gold-set annotation QC chưa đạt vì `F1 = 0.5996 < 0.80`; kết quả model cũng còn thấp (`mAP50 = 0.2984`, `mAP50-95 = 0.2092`) nên không tự nhận là “hoàn thành” ở tiêu chí quality annotation và pipeline nâng cao.
- Kết luận trung thực: bài làm đã hoàn thành đúng quy trình triển khai, nhưng cả nhãn gold và model vẫn cần rework nếu mục tiêu là chất lượng sản phẩm cao.

## 8. Công cụ AI và tài liệu tham khảo

- Đã dùng: Copilot/AI hỗ trợ rà soát phrasing báo cáo, kiểm tra cách diễn đạt và đối chiếu với guideline; không dùng AI để thay đổi dữ liệu gold train hoặc nội dung nhãn từ repo.
- Không dùng: không dùng token, không đính kèm gold train, không tự động sửa nhãn bằng script ngoài repo.
- Tài liệu tham khảo ngoài repo: Không có
