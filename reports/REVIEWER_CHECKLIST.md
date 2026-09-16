# Self-review checklist - bài làm cá nhân

Người gán: cá nhân Người kiểm: tự kiểm Ngày: 2026-09-16

Chạy trước khi soi bằng mắt:

```bash
python3 tools/check_pose_labels.py --images dataset/images/train --labels dataset/labels/train
python3 tools/visualize_pose.py --images dataset/images/train --labels dataset/labels/train --out outputs/vis_train
python3 tools/visibility_report.py --labels dataset/labels/train --out outputs/visibility_report.json --markdown reports/visibility_report.md
```

|     | Mục kiểm                                                                | Đạt? | Ghi chú / ảnh nào                                                  |
| --- | ----------------------------------------------------------------------- | ---- | ------------------------------------------------------------------ |
| 1   | Mọi người trong ảnh đều có đủ 17 điểm, không ai bị thiếu                | ☐    |                                                                    |
| 2   | Bật đường nối: không có xương nào cắt chéo ở vai hoặc hông              | ☐    |                                                                    |
| 3   | Không có xương nào kéo dài sang một cơ thể khác                         | ☐    |                                                                    |
| 4   | Khớp bị che dùng `v = 1` **và có chấm**, không phải `v = 0`             | ☐    |                                                                    |
| 5   | `v = 0` chỉ xuất hiện ở khớp thật sự ra ngoài mép ảnh                   | ☐    |                                                                    |
| 6   | Không có dấu hiệu dùng `Hidden` (điểm `v = 2` nằm ở chỗ vô lý)          | ☐    |                                                                    |
| 7   | Export đúng **COCO Keypoints 1.0**: mảng `keypoints` có 51 số mỗi người | ☐    |                                                                    |
| 8   | Bản YOLO Pose: mỗi dòng 56 số, `kpt_shape: [17, 3]`                     | ☐    |                                                                    |
| 9   | Visibility report đã được tạo                                           | ☑    | `outputs/visibility_report.json` và `reports/visibility_report.md` |
| 10  | Mọi ca không rõ đều được ghi trong `GUIDELINE_MINI.md`                  | ☐    |                                                                    |
| 11  | `check_pose_labels.py` chạy 0 lỗi                                       | ☐    |                                                                    |

## Kết luận tự kiểm - 2026-09-16

- Structure check: PASS - 20/20 image labels parsed; 29 skeletons; no format errors.
- Visibility totals: `v=2` 343, `v=1` 115, `v=0` 35.
- Warnings reviewed in overlays: `train_02`, `train_04`, `train_10`, `train_11`, and `train_13`.
- Các lỗi tự kiểm chính là nhầm trái/phải ở hông, dùng `v=0` cho khớp bị che, và bỏ sót người nhỏ; đã ghi trong `GUIDELINE_MINI.md`.
