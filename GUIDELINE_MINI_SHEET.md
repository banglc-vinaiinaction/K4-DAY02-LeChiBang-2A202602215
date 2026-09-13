# Phiếu quy tắc gán nhãn — Ngày 2

**Họ và tên:** Lê Chí Bằng<br>
**MSSV:** 2A202602215<br>
**Hình thức:** cá nhân<br>
**Mã cặp:** SOLO

## 1. Phạm vi

- Chỉ gán phương tiện thuộc bốn lớp bên dưới.
- Mỗi phương tiện là một hộp; không gộp nhiều xe.
- Không gán người, xe máy, xe đạp, biển báo hoặc phần phản chiếu.
- Vật thể quá nhỏ hoặc mờ đến mức không thể phân lớp có căn cứ: không đoán; ghi lý do vào nhật ký quyết định.

> **Khó khăn thực tế khi gán nhãn:**
> 1. **Thiếu định nghĩa cho nhiều đối tượng ngoài đường:** Quy định chỉ cho gán 4 loại ô tô, nhưng thực tế có thêm cả suv.
> 2. **Ảnh bị méo và co ép:** Ảnh gốc bị nén ép tỷ lệ nên nhìn xe bị biến dạng, mờ và rất khó nhìn rõ chi tiết để vẽ khung cho chuẩn.
> 3. **Đặc thù ảnh 3 (drive_033.jpg) - Che khuất diện rộng & Góc mù chân cầu:** Ảnh 3 có mật độ xe bị che khuất (occlusion) cực kỳ dày đặc. Đặc biệt, cụm phương tiện đỗ hoặc di chuyển ở khu vực tối dưới gầm cầu phía xa gần như bị hòa vào nền, không đủ bằng chứng hình ảnh (pixels) để phân biệt rõ ràng bằng mắt thường đó là xe tải, xe van hay xe buýt.
> 4. **Các khung vẽ đè lên nhau quá nhiều:** Vì các xe đứng quá gần và che nhau nên các khung vẽ bị chồng lấn diện tích rất lớn (IoU nội bộ cao), dễ vẽ sót hoặc dễ làm thuật toán NMS lọc nhầm, xóa nhầm xe liền kề.

## 2. Bốn lớp cố định

| Mã | Lớp | Gán khi nhìn thấy | Không gán vào lớp này |
| ---: | --- | --- | --- |
| 0 | `car` (ô tô con) | sedan, hatchback, SUV, taxi, xe bán tải dùng như xe con | xe có thùng/ben rõ; thân xe buýt; xe van thân hộp |
| 1 | `truck` (xe tải) | thùng, ben, sàn hàng hoặc thiết bị công vụ rõ ràng | ô tô con; thân xe buýt; xe van kín một khối |
| 2 | `bus` (xe buýt) | thân xe khách dài, nhiều cửa sổ hoặc hàng ghế | xe van nhỏ; xe tải; ô tô con |
| 3 | `van` (xe van) | thân hộp nhỏ, kín, dùng chở người hoặc hàng | thân xe buýt; khoang hàng tách biệt như xe tải |

Thứ tự lớp là cố định: `0 car, 1 truck, 2 bus, 3 van`.

## 3. Hộp giới hạn

- Vẽ sát phần vật thể nhìn thấy.
- Không ước lượng phần bị xe khác che.
- Vật thể chạm mép ảnh vẫn được gán nếu đủ bằng chứng phân lớp.
- Không để hộp chứa nhiều nền hoặc nhiều phương tiện.

## 4. Ba thuộc tính

| Thuộc tính | Giá trị | Ý nghĩa |
| --- | --- | --- |
| `visibility` (mức nhìn thấy) | `clear` (rõ), `occluded` (bị che), `unclear` (không rõ) | mức bằng chứng nhìn thấy |
| `boundary` (quan hệ mép ảnh) | `inside` (trong ảnh), `truncated` (bị cắt) | vật thể có bị mép ảnh cắt hay không |
| `review_state` (trạng thái xem lại) | `confident` (tự tin), `needs_review` (cần xem lại) | đánh dấu quyết định cần quay lại |

YOLO không lưu ba thuộc tính này. Vì vậy phải xuất thêm `CVAT for images 1.1` từ cùng công việc.

## 5. Ba tình huống mơ hồ

Hoàn thành trước khi xem bài của người khác hoặc bộ nhãn tham chiếu.

### Tình huống A — xe buýt hay xe van?

- Ảnh và mã vật thể: drive_033.png
- Dấu hiệu nhìn thấy: Xe bus nối (articulating bus)
- Quy tắc áp dụng: bus
- Quyết định: 2
- Nếu vẫn thiếu bằng chứng, bạn sẽ làm gì? Đã đủ bằng chứng quyết định, không cần escalate.

### Tình huống B — xe tải hay xe van/ô tô con?

- Ảnh và mã vật thể: drive_033.png
- Dấu hiệu nhìn thấy: xe tải có thùng 
- Quy tắc áp dụng: truck
- Quyết định: 1
- Nếu vẫn thiếu bằng chứng, bạn sẽ làm gì? Đã đủ bằng chứng quyết định, không cần escalate.

### Tình huống C — bị che, bị mép ảnh cắt hay không đủ bằng chứng?

- Ảnh và mã vật thể: drive_008.png
- Dấu hiệu nhìn thấy khi phóng 100%: car
- Giá trị `visibility`: unclear 
- Giá trị `boundary`: inside 
- Trạng thái `review_state`: needs_review
- Lý do: estimate giống với car

## 6. Xác nhận tự kiểm tra

- [x] Đã rà đủ bốn ảnh.
- [x] Đã kiểm vật thể thiếu và trùng.
- [x] Đã kiểm lớp và hình học từng hộp.
- [x] Mỗi hộp có đủ ba thuộc tính.
- [x] Đã xử lý mọi hộp `needs_review`.
- [x] Đã hoàn thành ba tình huống trước khi xem nguồn đối chiếu.
- [x] Nếu làm theo cặp, hai người đã xuất bài độc lập trước khi trao đổi.
- [x] Nếu làm cá nhân, bài riêng đã được kiểm trước khi nhận bộ tham chiếu.
- [x] Số vật thể thực tế: 82 — 40–60 là mục tiêu khối lượng, không phải điểm cắt.
