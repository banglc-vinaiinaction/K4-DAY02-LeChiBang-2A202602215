# Báo cáo — Ngày 2: phát hiện vật thể

**Họ và tên:** LÊ CHí BÀNG <br>
**MSSV:** 2A202602215<br>
**Hình thức:** cá nhân<br>
**Mã cặp:** SOLO

## 1. Bài độc lập và nguồn dữ liệu

- Mã SHA-256 của ZIP ảnh được cấp: f7d99888f21440fb0374d84962b93213bd8c14e665d093cc8d37f4c61b71ed33
- Bốn mã ảnh: drive_008.jpg, drive_022.jpg, drive_033.jpg, drive_038.jpg
- Số vật thể thực tế: 82
- Mã SHA-256 của gói YOLO của bạn: 713faadb3e453c7abed916805b633519edd855a730132dcfce14758716fc00ac
- Mã SHA-256 của gói CVAT gốc của bạn: 38eec44faead5fd30b376048a534c1937faf4330b383898d417e96db5464e23f
- Nguồn đối chiếu: bạn cùng cặp hoặc bộ tham chiếu do người hướng dẫn thực hành cấp: Do người hướng dẫn thực hành cấp
- Mã SHA-256 của gói đối chiếu: c8bbc767d8bb9a29f4ca5abf0c3516e5c2af94c58143a980b0148cfe0b500d2b
- Nếu làm cá nhân, ghi mã lần phát và thời điểm nhận bộ tham chiếu: 15:03

Giải thích vì sao bài của bạn vẫn độc lập trước khi đối chiếu: 

làm độc lập ở giai đoạn này dễ hơn

## 2. Quyết định phân lớp

| Ảnh/vật thể | Lớp | Dấu hiệu nhìn thấy | Quy tắc áp dụng |
| --- | --- | --- | --- |
| drive_033 / xe tải góc trái | truck | có thùng chứa hàng rõ ràng | chọn truck nếu có thùng/ben rõ |

Nêu một ví dụ cho thấy lớp và thuộc tính là hai loại thông tin khác nhau:

Lớp (class) xác định bản chất vật thể là gì (ví dụ: car, truck), trong khi thuộc tính (attribute) mô tả trạng thái hiện tại của nó. Một chiếc ô tô dù bị che khuất 50% (visibility = occluded) thì bản chất vẫn là lớp ô tô (car).

## 3. Tự kiểm tra và sửa nhãn

| Trước khi sửa | Loại lỗi | Cách phát hiện | Sau khi sửa và quy tắc |
| --- | --- | --- | --- |
| Gán nhầm xe SUV thành Van | lớp | Xem lại phiếu quy tắc | Đổi thành Car (quy tắc: SUV thuộc lớp car) |

- Số hộp `needs_review` trước và sau khi kiểm: Trước: 3, Sau: 0
- Một quyết định chưa đủ bằng chứng và cách bạn xin hỗ trợ: Vật thể ở quá xa, hình dáng mờ không rõ là xe gì. Tôi đánh dấu `needs_review` và hỏi ý kiến Lab Coach.

## 4. Một dòng nhãn YOLO

- Dòng `class x_center y_center width height`: `0 0.986750 0.476812 0.026500 0.121313`
- Tên lớp và tọa độ điểm ảnh `xyxy`: Lớp `car` (0). Tọa độ sẽ được tính bằng cách nhân các giá trị tỷ lệ (center, width, height) với kích thước gốc (width, height) của ảnh.
- Vì sao dòng đúng định dạng vẫn có thể sai lớp, phạm vi hoặc hình học?

YOLO chỉ kiểm tra đúng cấu trúc số (1 số nguyên và 4 số thực từ 0-1), nhưng không biết người vẽ có chọn sai lớp (ví dụ nhầm xe tải thành xe con) hoặc vẽ khung hộp to hơn thực tế hay không.

## 5. Huấn luyện và dự đoán thử

- Ba mã ảnh huấn luyện: drive_008.jpg, drive_022.jpg, drive_033.jpg
- Mã ảnh thẩm định: drive_038.jpg
- Mô tả một dự đoán trong `detect_result.jpg`: Mô hình nhận diện được các xe to, rõ ràng nhưng bỏ sót các xe bị che khuất một phần.
- Dự đoán đó gợi ý cần kiểm lại quy tắc hoặc dữ liệu nào? Gợi ý cần xem lại tập huấn luyện xem đã đủ các mẫu xe bị che khuất chưa, hoặc cần thu thập thêm.
- Minh chứng nào có thể bác bỏ nhận định của bạn? Nếu test trên một tập ảnh khác lớn hơn mà mô hình vẫn nhận diện tốt các xe bị che khuất thì nhận định trên không chính xác.
- Vì sao kết quả trên bốn ảnh không phải phép đánh giá mô hình dùng thực tế?

Vì 4 ảnh quá ít để đại diện cho sự đa dạng về điều kiện ánh sáng, thời tiết, góc máy trong thực tế. Đánh giá thực tế cần một tập validation đủ lớn và phân phối đa dạng.

## 6. Đối chiếu nhãn

- Số hộp ghép được: 48
- IoU trung bình và trung vị: 0.7136 và 0.7275
- Mức đồng thuận lớp: 72.92%
- Số hộp phía bạn không ghép được: 34
- Số hộp phía đối chiếu không ghép được: 2
- Một điểm khác biệt cụ thể: Tôi bỏ qua một chiếc xe ở rất xa, nhưng trong bộ tham chiếu chiếc xe đó được gán nhãn `car`.
- Quy tắc hoặc hành động sửa phát sinh: Cần làm rõ quy tắc về ngưỡng kích thước tối thiểu (đơn vị pixel) để gán nhãn xe ở xa.
- Vì sao mức đồng thuận cao không chứng minh mọi nhãn đều đúng?

Vì cả 2 người gán nhãn có thể cùng mắc chung một sai lầm hệ thống (ví dụ, cả hai cùng gán nhầm xe SUV thành Van).

## 7. Kiểm tra kho GitHub cá nhân

 - [x] Có phiếu quy tắc với ba tình huống mơ hồ.
 - [x] Có kết quả kiểm hai gói xuất.
 - [x] Có thông tin lần huấn luyện và ảnh dự đoán.
 - [x] Có tóm tắt, bảng và ảnh phủ của bước đối chiếu.
 - [x] Không có gói xuất thô, bộ nhãn tham chiếu hoặc trọng số mô hình.
 - [x] Không có dữ liệu VinFast/khách hàng/ảnh cá nhân/mật khẩu/mã truy cập.

Minh chứng mạnh nhất trong bài và câu hỏi còn lại cho Lab Coach:

Minh chứng mạnh nhất là file `comparison_iou.csv` cho thấy mức đồng thuận IoU khá tốt. Câu hỏi còn lại: Làm thế nào để xác định ngưỡng IoU phù hợp nhất trong một dự án thực tế?
