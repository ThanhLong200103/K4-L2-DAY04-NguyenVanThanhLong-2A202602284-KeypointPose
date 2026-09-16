# Bài thực hành Ngày 4 - Gán nhãn keypoint & fine-tune pose

> Bắt đầu bằng [lab-guide.html](lab-guide.html) nếu bạn mới dùng CVAT. Sau đó làm theo
> [GUIDE.md](GUIDE.md) để hoàn thành toàn bộ route 240 phút. Hướng dẫn HTML có ảnh CVAT thật,
> thao tác phóng to/đóng bằng bàn phím, và không yêu cầu kinh nghiệm lập trình.

Repo này chứa toàn bộ bài làm keypoint pose: 20 ảnh train, nhãn YOLO Pose 17 điểm,
asset skeleton/schema, notebook fine-tune YOLO26-Pose và các kết quả kiểm tra đã chạy.
Mỗi người có 17 khớp COCO, mỗi khớp gồm tọa độ và cờ visibility.

```text
20 ảnh -> CVAT Skeleton (17 điểm) -> YOLO Pose labels
  -> kiểm cấu trúc/visibility -> đánh giá với gold
  -> notebook fine-tune YOLO26-Pose -> outputs
```

## Phạm vi dữ liệu — đọc trước khi tạo task

Lab có **một route bắt buộc**: 20 ảnh `person` COCO-17. Ba bộ dưới đây có vai trò khác nhau;
không đổi chỗ cho nhau.

| Bộ dữ liệu | Ở đâu | Bạn làm gì | Có train / nộp? |
| --- | --- | --- | --- |
| **Core: 20 ảnh chưa nhãn** | `dataset/images/train/` | Tạo một task CVAT `person` 17 điểm, gán tất cả người trong ảnh, export và chuyển thành nhãn YOLO Pose | **Có** |
| **Test: 10 ảnh đã có nhãn** | `dataset/images/test/`, `dataset/labels/test/` | Chỉ dùng để đánh giá model trong notebook | **Không sửa, không train** |

Không có bài hand/face trong bản phát hành này. Đừng tự tạo skeleton thứ hai hoặc thêm thư mục
export thứ hai: repo chưa phát hành input và schema có thể kiểm chứng cho phần đó.

## Mục tiêu học tập

Sau lab, bạn có thể:

1. Dựng một **skeleton label 17 điểm COCO** trong CVAT và tái sử dụng nó bằng file `.SVG`.
2. Chọn đúng **v = 0 / 1 / 2** cho từng khớp, và giải thích được vì sao "bị che" khác
   "ra ngoài khung".
3. Export đúng **COCO Keypoints 1.0**, và biết đếm 51 (hoặc 56) số để phát hiện export sai.
4. Đọc **OKS** để tìm lỗi trong nhãn của chính mình, và gọi đúng tên bốn kiểu sai:
   lệch nhẹ, đảo trái/phải, nhầm người, trượt hẳn.
5. Dùng **visibility report** để phát hiện bất đồng về *guideline* trước khi đi soi từng pixel.
6. Fine-tune một model pose trên chính nhãn của mình và giải thích được con số thu được.

## Bài nộp

| Tệp | Nội dung |
| --- | --- |
| `dataset/labels/train/*.txt` | nhãn 20 ảnh train, định dạng Ultralytics YOLO Pose (56 số/dòng) |
| `annotations/coco_keypoints/person_keypoints_default.json` | đúng bản export **COCO Keypoints 1.0** từ CVAT |
| `reports/visibility_report.md`, `outputs/visibility_report.json` | bảng đếm cờ theo từng khớp |
| `GUIDELINE_MINI.md` | quy tắc gán nhãn và các ca mơ hồ của bài làm cá nhân |
| `outputs/eval_vs_gold.json` | kết quả chấm với gold (sau khi protected release mở) |
| `outputs/eval_model.json` | số liệu model trước/sau fine-tune, từ notebook |
| `reports/REPORT.md` | báo cáo, điền từ `reports/REPORT_TEMPLATE.md` |
| `reports/REVIEWER_CHECKLIST.md` | checklist tự kiểm và ghi chú lỗi đã phát hiện |

Đọc [GUIDE.md](GUIDE.md) theo thứ tự thao tác và đối chiếu [RUBRIC.md](RUBRIC.md) trước khi nộp.

## Cấu trúc thư mục

```text
Day4-Lab/
  dataset/images/train/   20 ảnh - BÀI CHÍNH, không có nhãn khi pull
  dataset/images/test/    10 ảnh - có nhãn sẵn, dùng để đánh giá model
  dataset/labels/train/   20 file nhãn YOLO Pose đã hoàn thành
  dataset/labels/test/    nhãn phát sẵn để đánh giá model
  gold/                   nhãn chuẩn dùng để đối chiếu OKS
  annotations/            bản export COCO Keypoints
  assets/                 hướng dẫn CVAT và schema skeleton
  tools/                  check / visibility / evaluate / visualize / convert
  notebooks/              notebook fine-tune YOLO26-Pose
  reports/                visibility report và checklist
  outputs/                kết quả đánh giá, overlay và training runs
  data.yaml               cấu hình dataset cho Ultralytics (kpt_shape [17, 3])
```

Không đổi tên ảnh, không sửa `dataset/labels/test/`, và không sửa gold sau khi nhận.

## Notebook và kết quả

Notebook chính là `notebooks/day4_pose_finetune_yolo26.ipynb`. Có thể mở trực tiếp
trong Jupyter/VS Code hoặc chạy trên Colab. Notebook sử dụng `data.yaml`, train labels
và test set; không dùng `gold/` để train.

Các kết quả hiện có trong `outputs/` gồm:

- `eval_vs_gold.json`: OKS trung bình `0.930`, ghép đủ `29/29` người.
- `eval_model.json`: kết quả đánh giá model.
- `visibility_report.json` và `vis_train/`: kiểm visibility và ảnh phủ skeleton.
- `runs/`: kết quả fine-tune và đánh giá model.

## Công cụ

Mọi script trong `tools/` **chỉ dùng thư viện chuẩn của Python** - chạy được ngay,
không cần cài gì (trừ `visualize_pose.py` cần Pillow). OKS và per-keypoint sigma lấy
đúng theo định nghĩa của COCO, xem `tools/poselib.py`.

```bash
# 1. Sau khi export từ CVAT: COCO Keypoints 1.0 -> nhãn để train
python3 tools/coco_kp_to_yolo_pose.py \
    --coco annotations/coco_keypoints/person_keypoints_default.json \
    --out dataset/labels/train

# 2. Kiểm định dạng - chạy trước khi nộp, không cần gold
python3 tools/check_pose_labels.py --images dataset/images/train --labels dataset/labels/train

# 3. Lượt hình dáng: bật đường nối lên và nhìn
python3 tools/visualize_pose.py --images dataset/images/train \
    --labels dataset/labels/train --out outputs/vis_train

# 4. Deliverable thứ ba: bảng đếm cờ visibility
python3 tools/visibility_report.py --labels dataset/labels/train \
    --out outputs/visibility_report.json --markdown reports/visibility_report.md

# 5. Chấm với gold
python3 tools/evaluate_pose_annotations.py --pred dataset/labels/train \
    --gold gold/labels/train --images dataset/images/train --out outputs/eval_vs_gold.json
```

## Một lưu ý về gold - đọc trước khi cãi nhau với điểm số

Gold của lab này lấy từ COCO. COCO dùng `v = 0` cho **cả hai** trường hợp: "ra ngoài
khung" *và* "người gán nhãn quyết định không gán khớp này". Luật của lớp mình chặt hơn:
khớp bị che mà còn trong khung thì phải là `v = 1` và vẫn đặt chấm.

Hệ quả, và nó là cố ý:

- Khớp nào gold để `v = 0` thì **bị loại khỏi phép tính OKS** - bạn không được và cũng
  không mất điểm ở khớp đó. Cứ theo luật của lớp, gắn `v = 1` và đặt chấm.
- Nên `%v = 1` trong visibility report của bạn sẽ **cao hơn của gold**. Đó không phải lỗi
  của bạn. Đó đúng là thứ slide 47 nói: hai bảng đếm lệch nhau = hai guideline khác nhau,
  không phải hai bức ảnh khác nhau.
- `tools/check_pose_labels.py` chạy trên chính gold cũng in ra cảnh báo vì lý do này.
  Lớp sẽ dùng nó làm ví dụ.
