# Mini guideline - bài làm cá nhân | ngày: 2026-09-16

> Điền file này **trong lúc** gán nhãn, không phải sau khi xong. Mỗi lần bạn dừng lại
> hơn 10 giây để phân vân, đó là một dòng phải ghi vào đây.

## 1. Luật bắt buộc (đã thống nhất cả lớp - không sửa)

- Bộ 17 điểm COCO, đúng tên, đúng thứ tự. Lấy từ file `.SVG` chung.
- Mọi người trong ảnh đều có **đủ 17 điểm**. Điểm không dùng được thì gắn cờ, không xoá.
- Trái/phải tính theo **cơ thể người**, không theo bức ảnh.
- Bị che, còn trong khung -> `v = 1`, **vẫn đặt chấm** ở vị trí ước lượng.
- Ra ngoài mép ảnh -> `v = 0`, **không** đặt chấm.
- Không dùng `Hidden` (`h`) - nó không được lưu vào file.

## 2. Quy tắc áp dụng cho bài làm này

| Tình huống                            | Quy tắc áp dụng                                                            | Vì sao                                       |
| ------------------------------------- | -------------------------------------------------------------------------- | -------------------------------------------- |
| Hông của người mặc quần áo dài        | Ước lượng theo trục vai, thân và chân; vẫn đặt điểm nếu còn trong ảnh.     | Giữ đủ 17 điểm.                              |
| Tai bị tóc hoặc mũ che một phần       | Đặt tại vị trí giải phẫu ước lượng và dùng `v = 1`.                        | Bị che không đồng nghĩa với ra ngoài ảnh.    |
| Người bị cắt ở mép ảnh                | Chỉ dùng `v = 0` cho khớp thực sự nằm ngoài ảnh.                           | Không đoán điểm đã ra ngoài khung.           |
| Cổ tay nằm sau tay lái hoặc thân mình | Đặt điểm ước lượng và dùng `v = 1` nếu vẫn trong khung.                    | Không xóa khớp bị che.                       |
| Hai người chồng lên nhau              | Hoàn thành từng người riêng, đối chiếu box và limb trước khi chuyển người. | Tránh gán nhầm keypoint sang người bên cạnh. |
| Người nhỏ trong ảnh                   | Vẫn gán nếu người đủ nhận diện; bộ dữ liệu này không loại người nhỏ.       | Đồng nhất với route 20 ảnh.                  |

Với mỗi luật, chèn **một ảnh mẫu** (screenshot từ CVAT) thay vì chỉ viết một câu.
Slide 12 nói rõ: khớp không có bề mặt nhìn thấy được thì phải có ảnh mẫu, không phải
một câu văn chung chung.

## 3. Ba ca mơ hồ đã gặp (bắt buộc, ghi ít nhất 3)

### Ca 1 - ảnh `train_02`, người thứ `1`, khớp `left_hip` / `right_hip`

- Mơ hồ ở chỗ hai hông có cảnh báo ngược chiều so với hai mắt.
- Bạn quyết thế nào: giữ trái/phải theo cơ thể người, không theo vị trí trái/phải trên ảnh.
- Vì sao: quy tắc COCO xác định bên theo cơ thể; ảnh phủ `outputs/vis_train/train_02.jpg` dùng để kiểm tra đường nối.
- Nếu người khác quyết ngược lại thì model học sai cặp trái/phải và lỗi sẽ bị khuếch đại khi lật ảnh.

### Ca 2 - ảnh `train_04`, người thứ `2`, khớp `knee` / `ankle`

- Mơ hồ ở chỗ có bốn khớp `v = 0` trong khi người vẫn nằm trong khung.
- Bạn quyết thế nào: khớp còn trong ảnh nhưng bị che phải giữ chấm và dùng `v = 1`; chỉ dùng `v = 0` khi khớp ra ngoài mép.
- Vì sao: đây là quy tắc visibility của bài và được cảnh báo bởi `check_pose_labels.py`.
- Nếu người khác quyết ngược lại thì model học thiếu các khớp chân bị che.

### Ca 3 - ảnh `train_13`, người thứ `3`, khớp `nose` đến `ankle`

- Mơ hồ ở chỗ người nhỏ ở hậu cảnh dễ bị bỏ sót khi chuyển sang người tiếp theo.
- Bạn quyết thế nào: kiểm đủ số người trong ảnh trước khi kết thúc và bổ sung đủ 17 điểm cho người thứ 3.
- Vì sao: gold có 3 skeleton còn bản đầu chỉ có 2; sau khi bổ sung, `matched_people` đạt 29/29.
- Nếu người khác quyết ngược lại thì model học bỏ qua người nhỏ và giảm khả năng phát hiện nhiều người.

## 4. Tự kiểm sau khi hoàn thành

- Dataset status: 20 train images, 20 train label files, 29 skeletons.
- Visibility rule confirmed: use `v = 1` for an occluded joint inside the image; use `v = 0` only when the joint is outside the image.
- Review case 1: `train_02.txt` has a left/right hip direction warning; inspect hip labels against the visible body orientation.
- Review case 2: `train_04.txt` has four `v = 0` joints while the person is inside the frame; these are likely occlusion cases.
- Review case 3: `train_10.txt`, `train_11.txt`, and `train_13.txt` have the same inside-frame `v = 0` warning; inspect before changing labels.
- The generated overlay is in `outputs/vis_train`; structural validation completed with 20/20 files.
