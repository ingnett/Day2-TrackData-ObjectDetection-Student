# Phiếu quy tắc gán nhãn — Ngày 2

**Họ và tên:** NGUYỄN KHẢI HƯNG<br>
**MSSV:** 2A202602258<br>
**Hình thức:** cá nhân<br>
**Mã cặp:** `SOLO`

## 1. Phạm vi

- Chỉ gán phương tiện thuộc bốn lớp bên dưới.
- Mỗi phương tiện là một hộp; không gộp nhiều xe.
- Không gán người, xe máy, xe đạp, biển báo hoặc phần phản chiếu.
- Vật thể quá nhỏ hoặc mờ đến mức không thể phân lớp có căn cứ: không đoán; ghi lý do vào nhật ký quyết định.

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

- Ảnh và mã vật thể: `drive_008` / một phương tiện
- Dấu hiệu nhìn thấy: kiểm tra chiều dài thân xe, số cửa sổ và dạng thân hộp trong ảnh.
- Quy tắc áp dụng: xe khách dài, có nhiều cửa sổ hoặc hàng ghế thì gán `bus`; xe thân hộp nhỏ thì gán `van`.
- Quyết định: chỉ chọn `bus` hoặc `van` khi dấu hiệu hình dạng đủ rõ; nếu không thì đánh dấu `needs_review`.
- Nếu vẫn thiếu bằng chứng, bạn sẽ làm gì? Không đoán lớp; ghi lại mã ảnh và hỏi Lab Coach.

### Tình huống B — xe tải hay xe van/ô tô con?

- Ảnh và mã vật thể: `drive_038` / một phương tiện
- Dấu hiệu nhìn thấy: kiểm tra phía sau cabin có thùng, ben hoặc sàn chở hàng tách biệt hay không.
- Quy tắc áp dụng: có khoang chở hàng tách biệt rõ thì gán `truck`; thân xe kín một khối thì cân nhắc `van`; xe nhỏ không có thùng hàng thì gán `car`.
- Quyết định: gán `truck` khi thùng hoặc khoang hàng nhìn thấy rõ; không dùng kích thước đơn thuần để đoán.
- Nếu vẫn thiếu bằng chứng, bạn sẽ làm gì? Đánh dấu `needs_review`, ghi lý do và xin hỗ trợ trước khi chốt lớp.

### Tình huống C — bị che, bị mép ảnh cắt hay không đủ bằng chứng?

- Ảnh và mã vật thể: `drive_022` / một phương tiện ở gần vật thể khác hoặc mép ảnh
- Dấu hiệu nhìn thấy khi phóng 100%: xác định phần thân xe nhìn thấy, phần bị vật thể khác che và phần có bị mép ảnh cắt hay không.
- Giá trị `visibility`: `occluded` nếu bị vật thể khác che; `unclear` nếu không đủ bằng chứng để phân lớp.
- Giá trị `boundary`: `truncated` nếu hộp bị mép ảnh cắt; nếu vật thể nằm trọn trong ảnh thì chọn `inside`.
- Trạng thái `review_state`: `needs_review` khi lớp hoặc ranh giới hộp chưa chắc chắn; ngược lại chọn `confident`.
- Lý do: `visibility` mô tả mức nhìn thấy, `boundary` mô tả quan hệ với mép ảnh, còn `review_state` mô tả mức chắc chắn của quyết định; ba thuộc tính không thay thế cho nhau.

## 6. Xác nhận tự kiểm tra

- [x] Đã rà đủ bốn ảnh.
- [x] Đã kiểm vật thể thiếu và trùng.
- [x] Đã kiểm lớp và hình học từng hộp.
- [x] Mỗi hộp có đủ ba thuộc tính.
- [x] Đã xử lý mọi hộp `needs_review`.
- [x] Đã hoàn thành ba tình huống trước khi xem nguồn đối chiếu.
- [ ] Nếu làm theo cặp, hai người đã xuất bài độc lập trước khi trao đổi.
- [x] Nếu làm cá nhân, bài riêng đã được kiểm trước khi nhận bộ tham chiếu.
- [x] Số vật thể thực tế: 72 — 40–60 là mục tiêu khối lượng, không phải điểm cắt.
