# Báo cáo — Ngày 2: phát hiện vật thể

**Họ và tên: NGUYỄN KHẢI HƯNG** <br>
**MSSV: 2A202602258**<br>
**Hình thức:** cá nhân<br>
**Mã cặp:** `SOLO`

## 1. Bài độc lập và nguồn dữ liệu

- Mã SHA-256 của ZIP ảnh được cấp: f7d99888f21440fb0374d84962b93213bd8c14e665d093cc8d37f4c61b71ed33
- Bốn mã ảnh: drive_022, drive_033, drive_038, drive_008 
- Số vật thể thực tế: 72
- Mã SHA-256 của gói YOLO của bạn: 256f64a26e5b3db01a0252536afebce85d553fed8d30f55ea9a56317e9216d87
- Mã SHA-256 của gói CVAT gốc của bạn: 3b11a35c3c48c7d2a75e9b68289eea1e1a938d29efeb9fdeea421a5cb80c7cd3

- Nguồn đối chiếu: bạn cùng cặp hoặc bộ tham chiếu do người hướng dẫn thực hành cấp: teaching_reference
- Mã SHA-256 của gói đối chiếu: `c8bbc767d8bb9a29f4ca5abf0c3516e5c2af94c58143a980b0148cfe0b500d2b`
- Nếu làm cá nhân, ghi mã lần phát và thời điểm nhận bộ tham chiếu: chưa có thông tin trong file output


Giải thích vì sao bài của bạn vẫn độc lập trước khi đối chiếu:

Tôi hoàn thành việc gán nhãn và tự kiểm tra bài của mình trước khi nhận bộ nhãn tham chiếu. Vì vậy bộ tham chiếu chỉ được dùng ở bước đối chiếu sau cùng, không dùng để quyết định các hộp ban đầu.

## 2. Quyết định phân lớp

| Ảnh/vật thể | Lớp | Dấu hiệu nhìn thấy | Quy tắc áp dụng |
| --- | --- | --- | --- |
| drive_022 / một phương tiện | car, truck, bus hoặc van theo hình dạng thân xe | Quan sát thân xe, thùng hàng, số cửa sổ và mục đích sử dụng thể hiện trong ảnh | Áp dụng bảng bốn lớp cố định trong phiếu quy tắc |
| drive_033 / một phương tiện | car | Thân xe nhỏ gọn, không có thùng hàng hoặc khoang xe buýt rõ ràng | Gán `car` cho sedan, hatchback, SUV hoặc taxi dùng như xe con |
| drive_038 / một phương tiện | truck | Có thùng hoặc khoang chở hàng tách biệt phía sau cabin | Gán `truck` khi thùng, ben hoặc sàn hàng được nhìn thấy rõ |
| drive_008 / một phương tiện | bus hoặc van theo bằng chứng hình dạng | Kiểm tra chiều dài thân xe, số cửa sổ và dạng thân hộp trước khi quyết định | Gán `bus` cho xe khách dài; gán `van` cho xe thân hộp nhỏ, không đoán khi thiếu bằng chứng |

Nêu một ví dụ cho thấy lớp và thuộc tính là hai loại thông tin khác nhau:

- Ví dụ, `car` là thông tin về loại phương tiện, còn `visibility=occluded` là thông tin về mức độ nhìn thấy hộp. Một chiếc xe vẫn có thể là `car` dù bị che một phần; lớp và thuộc tính mô tả hai khía cạnh khác nhau.

## 3. Tự kiểm tra và sửa nhãn

| Trước khi sửa | Loại lỗi | Cách phát hiện | Sau khi sửa và quy tắc |
| --- | --- | --- | --- |
| Các hộp chưa chắc về lớp hoặc sát mép ảnh | lớp/hình học/thuộc tính | Rà từng ảnh ở mức phóng to, kiểm số hộp, biên hộp, lớp và ba thuộc tính trong CVAT | Giữ hoặc sửa theo bằng chứng nhìn thấy; không suy đoán phần bị che |
| Một phương tiện bị thiếu hộp hoặc có hai hộp chồng lên cùng một phương tiện | phạm vi/hình học | Đếm vật thể theo từng ảnh và kiểm tra các hộp chồng lấn trong CVAT | Bổ sung hộp còn thiếu hoặc xóa hộp trùng; mỗi phương tiện chỉ có một hộp |
| Hộp đã bao đúng phương tiện nhưng chọn nhầm giữa `car`, `truck`, `bus` và `van` | lớp | Đối chiếu thân xe, thùng hàng, chiều dài xe và số cửa sổ với phiếu quy tắc | Sửa về lớp có bằng chứng rõ nhất; đánh dấu `needs_review` nếu chưa đủ căn cứ |
| Hộp không có đủ `visibility`, `boundary` hoặc `review_state` | thuộc tính | Kiểm từng box trong CVAT và đối chiếu danh sách ba thuộc tính bắt buộc | Bổ sung đúng một giá trị cho mỗi thuộc tính; không dùng file YOLO để thay thế thuộc tính CVAT |

- Số hộp `needs_review` trước và sau khi kiểm: chưa có thống kê riêng trong output đã lưu
- Một quyết định chưa đủ bằng chứng và cách bạn xin hỗ trợ: đánh dấu `needs_review`, ghi lại ảnh/vật thể và hỏi Lab Coach thay vì tự đoán lớp

## 4. Một dòng nhãn YOLO

- Dòng `class x_center y_center width height`: đọc trực tiếp một dòng từ file TXT trong `my_export_audit.json` khi nộp kèm minh chứng
- Tên lớp và tọa độ điểm ảnh `xyxy`: đối chiếu từ cùng record trong báo cáo audit; không chép số liệu khi chưa có file output
- Vì sao dòng đúng định dạng vẫn có thể sai lớp, phạm vi hoặc hình học?

Đúng định dạng chỉ có nghĩa là dòng có đủ năm trường và các giá trị nằm trong phạm vi hợp lệ. Dòng đó vẫn có thể gán sai `class_id`, bao phủ quá nhiều nền hoặc không bao sát vật thể.

## 5. Huấn luyện và dự đoán thử

- Ba mã ảnh huấn luyện: `drive_022`, `drive_033`, `drive_038`
- Mã ảnh thẩm định: `drive_008`
- Mô tả một dự đoán trong `detect_result.jpg`: cần ghi theo ảnh dự đoán thực tế đã lưu
- Dự đoán đó gợi ý cần kiểm lại quy tắc hoặc dữ liệu nào?: kiểm lại các hộp có lớp hoặc vị trí dự đoán khác với nhãn
- Minh chứng nào có thể bác bỏ nhận định của bạn?: ảnh gốc, nhãn CVAT, file audit và ảnh phủ hộp
- Vì sao kết quả trên bốn ảnh không phải phép đánh giá mô hình dùng thực tế?: tập dữ liệu quá nhỏ, chỉ có một ảnh thẩm định và mô hình chỉ được huấn luyện thử để chẩn đoán dữ liệu

Kết quả huấn luyện chỉ dùng để phát hiện dấu hiệu cần xem lại, không dùng làm kết luận chất lượng mô hình hay chất lượng người gán nhãn.

## 6. Đối chiếu nhãn

- Số hộp ghép được: cần đọc từ `comparison_summary.json`
- IoU trung bình và trung vị: cần đọc từ `comparison_summary.json`
- Mức đồng thuận lớp: cần đọc từ `comparison_summary.json`
- Số hộp phía bạn không ghép được: cần đọc từ `comparison_summary.json`
- Số hộp phía đối chiếu không ghép được: cần đọc từ `comparison_summary.json`
- Một điểm khác biệt cụ thể: đối chiếu một dòng trong `comparison_iou.csv` và ảnh `comparison_overlay.png`
- Quy tắc hoặc hành động sửa phát sinh: rà lại ranh giới giữa `car`, `truck`, `bus`, `van`; sửa trong CVAT nếu bằng chứng cho thấy nhãn hoặc hộp sai
- Vì sao mức đồng thuận cao không chứng minh mọi nhãn đều đúng?

Mức đồng thuận cao chỉ cho thấy hai lần gán nhãn gần nhau theo cách ghép đã chọn. Cả hai người vẫn có thể cùng áp dụng sai một quy tắc hoặc cùng bỏ sót một vật thể.

## 7. Kiểm tra kho GitHub cá nhân

- [x] Có phiếu quy tắc với ba tình huống mơ hồ.
- [x] Có kết quả kiểm hai gói xuất.
- [x] Có thông tin lần huấn luyện và ảnh dự đoán.
- [x] Có tóm tắt, bảng và ảnh phủ của bước đối chiếu.
- [ ] Không có gói xuất thô, bộ nhãn tham chiếu hoặc trọng số mô hình.
- [ ] Không có dữ liệu VinFast/khách hàng/ảnh cá nhân/mật khẩu/mã truy cập.

Minh chứng mạnh nhất trong bài và câu hỏi còn lại cho Lab Coach:

Minh chứng mạnh nhất là hai gói xuất từ cùng trạng thái CVAT, kết quả kiểm checksum và báo cáo đối chiếu. Câu hỏi còn lại cho Lab Coach là cách xử lý các phương tiện quá nhỏ hoặc bị che khiến lớp không đủ bằng chứng.
