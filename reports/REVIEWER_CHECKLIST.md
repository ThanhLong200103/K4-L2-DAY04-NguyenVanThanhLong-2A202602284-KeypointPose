# Reviewer checklist - điền khi kiểm bài người khác

Người gán: **\_\_** Người kiểm: **\_\_** Ngày: **\_\_**

Chạy trước khi soi bằng mắt:

```bash
python3 tools/check_pose_labels.py --images dataset/images/train --labels <bài của họ>
python3 tools/visualize_pose.py --images dataset/images/train --labels <bài của họ> --out /tmp/vis_review
python3 tools/visibility_report.py --labels dataset/labels/train --compare <bài của họ>
```

|     | Mục kiểm                                                                | Đạt? | Ghi chú / ảnh nào |
| --- | ----------------------------------------------------------------------- | ---- | ----------------- |
| 1   | Mọi người trong ảnh đều có đủ 17 điểm, không ai bị thiếu                | ☐    |                   |
| 2   | Bật đường nối: không có xương nào cắt chéo ở vai hoặc hông              | ☐    |                   |
| 3   | Không có xương nào kéo dài sang một cơ thể khác                         | ☐    |                   |
| 4   | Khớp bị che dùng `v = 1` **và có chấm**, không phải `v = 0`             | ☐    |                   |
| 5   | `v = 0` chỉ xuất hiện ở khớp thật sự ra ngoài mép ảnh                   | ☐    |                   |
| 6   | Không có dấu hiệu dùng `Hidden` (điểm `v = 2` nằm ở chỗ vô lý)          | ☐    |                   |
| 7   | Export đúng **COCO Keypoints 1.0**: mảng `keypoints` có 51 số mỗi người | ☐    |                   |
| 8   | Bản YOLO Pose: mỗi dòng 56 số, `kpt_shape: [17, 3]`                     | ☐    |                   |
| 9   | Visibility report đã nộp, và hai bảng đã được đặt cạnh nhau             | ☐    |                   |
| 10  | Mọi ca không rõ đều được ghi trong `GUIDELINE_MINI.md`                  | ☐    |                   |
| 11  | `check_pose_labels.py` chạy 0 lỗi                                       | ☐    |                   |

## Lỗi tìm được

Chép sang `reports/review_partner.md`. Mỗi dòng một lỗi, đủ bốn cột - người sửa phải
mở đúng chỗ đó được mà không cần hỏi lại.

| Ảnh | Người thứ | Khớp | Lỗi gì | Sửa thế nào |
| --- | --------: | ---- | ------ | ----------- |
|     |           |      |        |             |
|     |           |      |        |             |
|     |           |      |        |             |

## Hai câu kết luận

- Lỗi lặp đi lặp lại nhiều nhất của bài này:
- Nó là lỗi **thao tác** hay lỗi **guideline chưa rõ**?

## Self-review record - 2026-09-16

- Structure check: PASS - 20/20 image labels parsed; 28 skeletons; no format errors.
- Visibility totals: `v=2` 332, `v=1` 115, `v=0` 29.
- Warnings reviewed in overlays: `train_02`, `train_04`, `train_10`, `train_11`, and `train_13`.
- No partner comparison was completed. The comparison command requires a real partner label directory, which is not available in this workspace.
- Do not claim the partner review is complete until `reports/visibility_compare.md` is generated from that directory.
