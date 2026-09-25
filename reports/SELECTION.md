# Vì sao chọn lô này?

Trong 50 dòng đứng đầu `outputs/selection_round1.csv`, chọn năm frame bạn sẽ ưu tiên nếu chỉ có
ngân sách rà năm ảnh. Ghi tên, điểm, thời điểm, thứ tự và lý do; tối thiểu một quyết định phải xét
ảnh gần trùng hoặc trường hợp model không dự đoán được box: Nếu chỉ được sửa 5 ảnh, tôi chọn
frame_0182.jpg (hạng 1, điểm 0.959, giây 72.8), frame_0369.jpg (hạng 2, điểm 0.932, giây 147.6),
frame_0380.jpg (hạng 3, điểm 0.917, giây 152.0), frame_0326.jpg (hạng 4, điểm 0.916, giây 130.4)
và frame_0331.jpg (hạng 5, điểm 0.915, giây 132.4). Năm ảnh này đứng đầu danh sách và AI khoanh
nhiều box còn lưỡng lự. frame_0182.jpg và frame_0187.jpg cách nhau khoảng 2 giây (72.8 và 74.8),
cảnh gần giống nhau, nên nếu chỉ có 5 suất tôi không lấy cả hai; tôi giữ frame_0182.jpg vì điểm cao hơn.

Ba frame thuộc lô 12 ảnh model chọn và bằng chứng trong CSV/ảnh contact sheet: Tôi nhìn
frame_0182.jpg (điểm 0.959, 28 box, 18 box mơ hồ), frame_0099.jpg (điểm 0.906, 29 box, 14 box mơ hồ)
và frame_0107.jpg (điểm 0.888, 33 box, 15 box mơ hồ). Cả ba đều có điểm trên 0.88 và AI khoanh nhiều
xe nhưng còn nhiều khung chưa chắc, đúng với ảnh contact sheet `outputs/selection_round1.jpg`.

Một frame có điểm cao nhưng không chọn hoặc một frame có điểm thấp vẫn nên xem, và lý do:
frame_0372.jpg hạng 6, điểm 0.910, cao hơn vài ảnh đã được chọn (ví dụ frame_0392.jpg hạng 15,
điểm 0.887), nhưng AI bỏ qua vì nó sát giờ với frame_0369.jpg (147.6 giây và 148.8 giây, chỉ cách
khoảng 1.2 giây). Hai ảnh gần như cùng một cảnh, sửa cả hai thì tốn công mà ít học thêm được gì.

Điều phép chọn này chưa chứng minh về chất lượng mô hình: Điểm cao chỉ nghĩa là AI đang phân vân.
Nó chưa chứng minh sửa ảnh đó xong thì AI sẽ nhận xe tốt hơn trên tập kiểm thử.
