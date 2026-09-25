# Báo cáo Lab Ngày 08: Học chủ động cho bộ phát hiện xe

Họ và tên: Nguyễn Đình Độ

Công cụ gán nhãn đã dùng: CVAT

## 1. Dữ liệu và cách chia tập

Camera đứng một chỗ, một chiếc xe nằm trong hình vài giây. Ảnh học (pool) và ảnh kiểm thử được
chia theo trục thời gian, có vùng đệm ở giữa, thay vì chia ngẫu nhiên. Nếu trộn ngẫu nhiên, cùng
một xe có thể vừa được AI học vừa được dùng để chấm. Điểm sẽ đẹp hơn sự thật vì mô hình đã “nhìn”
gần như cùng cảnh lúc train.

## 2. Mô hình khởi đầu lạnh (cold start)

Dòng vòng 0 từ `reports/rounds_table.md`: AP50 0.771, P@0.25 0.925, R@0.25 0.489, F1 0.640;
R small 0.182, R medium 0.547, R large 0.561. Nghĩa là xe ở xa / nhỏ bị bỏ sót nhiều hơn xe ở gần.
Trên `outputs/compare_round0.jpg`, cột cold start thường có box đỏ (phát hiện nhầm) hoặc box vàng
(bỏ sót) quanh xe tối sát mép và cụm đèn xa gần chân cầu. Nhãn dùng để chấm cũng do máy vẽ, chưa
có người xem từng khung, nên có thể nhãn chấm sai chứ không phải AI của mình sai.

## 3. Chiến lược chọn mẫu

Mỗi ảnh có điểm `score = 0.5·U + 0.3·A + 0.2·D`. Một nửa điểm là độ bất định (AI không chắc), ba
phần mười là số box còn lưỡng lự, hai phần mười là khoảng cách thời gian so với ảnh đã gán. Hai ảnh
trong cùng lô phải cách nhau ít nhất `MIN_GAP_S = 2` giây vì camera đứng yên, ảnh sát nhau gần như
giống hệt. Trong `SELECTION.md` tôi ưu tiên frame_0182.jpg, frame_0369.jpg, frame_0380.jpg; còn
frame_0372.jpg điểm 0.910 nhưng bị bỏ vì sát frame_0369.jpg. Điểm cao không có nghĩa sửa ảnh đó
sẽ làm AI giỏi hơn trên test.

## 4. Các vòng học chủ động (active learning)

Bảng từ `reports/rounds_table.md`:

| vòng | ảnh train | box train | AP50 | Δ AP50 so cold start | P@0.25 | R@0.25 | R small | R medium | R large |
| ---: | ---: | ---: | ---: | ---: | ---: | ---: | ---: | ---: | ---: |
| 0 | 0 | 0 | 0.771 | — | 0.925 | 0.489 | 0.182 | 0.547 | 0.561 |
| 1 | 12 | 341 | 0.426 | -0.345 | 1.000 | 0.012 | 0.000 | 0.010 | 0.049 |

Theo `outputs/round1_diff.md`: model đề xuất 169 box; sau khi sửa còn 341 box (accepted 140,
edited 15, deleted 14, added 186, accept rate 83%). Tôi thêm rất nhiều xe AI bỏ sót, xóa vài khung
không phải xe, và kéo lại khung lệch. AP50 vòng 1 giảm 0.345 so với cold start; recall gần như sụp
(0.012). Trên `compare_round0.jpg` và `compare_round1.jpg`, sau fine-tune mô hình khoanh ít hơn
nhiều so với cold start dù precision cao. Ba việc khác nhau: mắt tôi đếm ~18 xe trên frame_0099
trong `BLIND_SCAN.md`; khung cụ thể tôi sửa ghi trong `REVIEW_LOG.csv` (thêm xe cắt mép, xóa vệt
đèn, kéo khung sát thân); còn AI sau khi học lại đo bằng metrics vòng 1. Ca khó theo guideline: xe
chỉ còn hai chấm đèn hoặc bị cắt mép — khoanh phần nhìn thấy, không khoanh vệt sáng trên đường.

## 5. Kết luận và giới hạn

Vòng 1 kém hơn cold start (AP50 0.426 so với 0.771). Tôi dừng, không làm vòng 2, vì điểm giảm
mạnh và cần xem lại cách gán trước khi cho AI học thêm. Hai chỗ còn yếu: xe xa chỉ còn hai chấm
đèn, và xe bị cắt mép ảnh. Sửa thêm tốn thời gian; không nên chọn hai ảnh sát nhau vì gần như một
cảnh. Tập chấm chỉ 20 ảnh, xe quá nhỏ không tính, nhãn chấm chưa được người kiểm — nên AP50 chỉ
là mức khớp với bộ tham chiếu máy, không chứng minh chất lượng thực địa. Vì AP50 giảm, trước khi
train thêm tôi sẽ đối chiếu lại nhãn đã sửa với guideline (có khoanh quá nhiều xe xa / vệt đèn
không), xem lại `round1_diff` và ảnh compare, rồi mới quyết định có làm vòng sau.
