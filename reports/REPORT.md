# Báo cáo Ngày 4 - Keypoint & Pose

Họ tên: Nguyễn Văn Thành Long  Ngày: 2026-09-16

## 1. Nhãn của tôi

| Chỉ số                       |                Giá trị |
| ---------------------------- | ---------------------: |
| Số ảnh đã gán                |                     20 |
| Số skeleton                  |                     29 |
| v=2 / v=1 / v=0              |         343 / 115 / 35 |
| Thời gian trung bình mỗi ảnh | Không ghi trong output |

Ba khớp có `%v=1` cao nhất theo `outputs/visibility_report.json`:

1. `left_ear`: 18/29 = 62%
2. `left_wrist`: 9/29 = 31%
3. `left_hip`: 9/29 = 31%

Tai trái có tỷ lệ bị che cao nhất. Cổ tay và hông cũng khó xác định vì thường bị vật thể, thân người hoặc quần áo che. Đây là khó khăn về visibility và vị trí giải phẫu, không phải lý do để xóa keypoint; quy tắc của bài yêu cầu đặt điểm và dùng `v=1` khi khớp còn trong ảnh.

## 2. Chấm với gold

| Chỉ số                | Trước rework | Sau rework |
| --------------------- | -----------: | ---------: |
| OKS trung bình        |        0.928 |     0.9305 |
| OKS@0.50              |        0.966 |      1.000 |
| OKS@0.75              |        0.931 |     0.9655 |
| Lỗi `dao_trai_phai`   |            0 |          0 |
| Lỗi `nham_nguoi`      |            0 |          0 |
| Lỗi `xoa_khop_bi_che` |            0 |          0 |

Bản đầu thiếu một người trong `train_13.jpg`. Sau khi đối chiếu gold, đã bổ sung người thứ 3 với đủ 17 điểm. Kết quả sau rework cho thấy `gold_people=29`, `matched_people=29`, `missing_people=0`, `extra_people=0`. Bản cuối còn 15 cảnh báo `lech_nhe`; 66 điểm khác cờ gold và 49 điểm khác gold về visibility không bị trừ OKS theo quy tắc của bài.

Lỗi đảo trái/phải của tôi: không có lỗi được evaluator phân loại là đảo trái/phải. `train_02.jpg` có cảnh báo hướng hai hông cần kiểm tra, nhưng kết quả cuối không ghi nhận lỗi `dao_trai_phai`.

## 3. Model

| Chỉ số         | yolo26n-pose gốc | Sau fine-tune |   Chênh |
| -------------- | ---------------: | ------------: | ------: |
| pose_mAP50     |           0.8450 |        0.8450 |  0.0000 |
| pose_mAP50-95  |           0.6853 |        0.6908 | +0.0055 |
| pose_precision |           0.9734 |        0.9792 | +0.0058 |
| pose_recall    |           0.8462 |        0.8462 |  0.0000 |
| box_mAP50-95   |           0.8119 |        0.8041 | -0.0078 |

### Trả lời năm câu hỏi ở cuối notebook

1. `pose_mAP50-95` tăng `0.0055`, từ `0.6853` lên `0.6908`. Tập 20 ảnh giúp model thích nghi nhẹ với các tư thế và điều kiện che khuất của bộ dữ liệu này; mức tăng nhỏ nên chưa đủ kết luận về khả năng tổng quát.

2. Sau fine-tune, `box_mAP50-95 = 0.8041` còn `pose_mAP50-95 = 0.6908`, chênh `0.1133`. Model tìm box người dễ hơn xác định chính xác từng khớp, đặc biệt ở người bị che hoặc có tư thế khó.

3. Overlay `outputs/runs/predictions/test/test_01.jpg` cho thấy model vẫn nhận đúng người đi xe máy nhưng các khớp chân bị che và khó quan sát. Đây là ví dụ gần với lỗi **lệch nhẹ** hơn là nhầm người; file JSON hiện tại không lưu nhãn lỗi per-image để định danh chắc hơn.

4. `eval_model.json` chỉ lưu metric tổng hợp, không lưu OKS theo từng ảnh. Vì vậy chưa thể kết luận ảnh nào có OKS thấp nhất giữa nhãn và model từ output hiện có. Có thể dùng các overlay trong `outputs/runs/predictions/test/` để kiểm tra trực quan; không suy ra ảnh thấp nhất từ metric tổng.

5. Ảnh gán tệ nhất theo gold là `train_02.jpg` với OKS `0.7272`. Output model hiện không có OKS per-image tương ứng, nên chưa thể xác nhận nó có phải ảnh model đoán tệ nhất hay không. Nếu trùng, đó sẽ là dấu hiệu ảnh có cấu trúc pose/visibility khó; nếu không trùng, lỗi nhãn và lỗi model đến từ hai nguyên nhân khác nhau.

## 4. Một rule evidence đã dùng

Trong `train_04.jpg`, người thứ 2 có các khớp chân nằm trong vùng ảnh nhưng bị che hoặc khó nhìn rõ. Căn cứ vào box người và phần thân/chân còn liên tục, các khớp này chưa ra ngoài mép ảnh. Vì vậy phải đặt vị trí ước lượng và dùng `v=1`, không dùng `v=0`. Quy tắc này giúp giữ đủ 17 điểm và tránh biến khớp bị che thành khớp không tồn tại.
