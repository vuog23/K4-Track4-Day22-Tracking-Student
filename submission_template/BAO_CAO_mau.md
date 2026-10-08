# Báo cáo lab: chọn tracker cho 5 video

**Nhóm:** ………………………… **Thành viên:** …………………………

Detector cố định: `yolo26n.pt`, ảnh 640 px, Re-ID `osnet_x0_25_msmt17`. Không đổi các mục này trong bài nộp chính.

## 1. Cấu hình đã chọn

Các quan sát dưới đây dựa trên preview 150 frame để so sánh cấu hình; sau đó cấu hình được chọn đã chạy đủ số frame của từng video.

| Video | Tracker | conf | iou | Quan sát khi xem video | Đã thử nhưng loại |
|---|---|---:|---:|---|---|
| video_1 (quảng trường, tĩnh, ban ngày) | strongsort | 0.15 | 0.5 | Ngưỡng thấp giữ thêm người nhỏ ở xa. Trong các cấu hình đã chấm, cấu hình này có HOTA, MOTA và IDF1 cao nhất. | strongsort 0.15/0.7; bytetrack 0.3/0.5 |
| video_2 (phố đêm, tĩnh, rất đông) | strongsort | 0.15 | 0.5 | Ở cảnh tối và đông, ngưỡng 0.15 giữ được thêm người nhỏ hoặc tối; Re-ID cho nhiều track dài hơn ByteTrack trong 150 frame thử. | strongsort 0.5/0.5; bytetrack 0.3/0.5 |
| video_3 (camera di động, ảnh nhỏ) | strongsort | 0.15 | 0.5 | Khi camera di chuyển, StrongSORT giữ nhiều track dài hơn ByteTrack trong đoạn thử; ngưỡng thấp cũng giữ thêm người ở rìa ảnh. | strongsort 0.5/0.5; bytetrack 0.3/0.5 |
| video_4 (trong nhà, camera di chuyển) | strongsort | 0.5 | 0.5 | Người ở gần và sáng vẫn có hộp; ngưỡng cao giảm các hộp yếu, ngắn quanh vùng kính phản chiếu. | strongsort 0.15/0.5; bytetrack 0.3/0.5 |
| video_5 (trên xe bus, giao lộ đông) | strongsort | 0.15 | 0.5 | Ngưỡng thấp bắt được thêm người đi bộ nhỏ ở xa. Re-ID hỗ trợ giữ ID khi góc nhìn và vị trí người thay đổi theo chuyển động xe. | strongsort 0.5/0.5; bytetrack 0.3/0.5 |

## 2. Số liệu video_1

Kết quả TrackEval của `runs/nop_bai/video_1.txt`:

| HOTA | MOTA | IDF1 |
|---:|---:|---:|
| 29.190 | 19.924 | 32.585 |

## 3. Phân tích

**video_2:** ByteTrack và StrongSORT đều theo được những người lớn, rõ trong cảnh. Ở 150 frame đầu, StrongSORT tạo 14 track dài ít nhất 30 frame, so với 10 của ByteTrack; ngưỡng `conf=0.15` cũng giữ được thêm các hộp người tối hoặc nhỏ. Không có nhãn cho video này nên số track dài chỉ là dấu hiệu để xem lại preview, không phải điểm chính xác danh tính. Cấu hình nộp dùng StrongSORT `conf=0.15`, `iou=0.5`.

**video_3:** Cảnh có camera di chuyển và người ở gần che khuất nhau. Trong đoạn thử, StrongSORT có 9 track dài ít nhất 30 frame, so với 4 của ByteTrack ở cùng ngưỡng `0.3/0.5`; đặc trưng ngoại hình giúp nối người qua chuyển động của camera. Hạ `conf` xuống `0.15` giữ thêm người ở rìa ảnh, còn `0.5` bỏ nhiều hộp hơn. Vì vậy nhóm chọn StrongSORT `conf=0.15`, `iou=0.5` và xem video để kiểm tra các lần ID đổi khi người che nhau.

**video_4:** Các người ở gần được phát hiện rõ, nhưng vùng kính và phản chiếu có thể làm xuất hiện hộp không ổn định. Với `conf=0.15`, số ID và hộp ngắn tăng rõ trong đoạn thử; `conf=0.5` giữ các track chính ổn định hơn và giảm các hộp yếu. Nhóm chọn StrongSORT `conf=0.5`, `iou=0.5` để ưu tiên track chắc chắn trong cảnh trong nhà này.

**video_5:** Ở góc nhìn trên xe bus, người đi bộ nhỏ và xa dễ bị bỏ sót khi `conf` cao. `conf=0.15` tạo 16 track dài ít nhất 30 frame trong đoạn thử, so với 11 ở `conf=0.3` và 6 ở `conf=0.5`. Nhóm chọn StrongSORT `conf=0.15`, `iou=0.5`; các số đếm chỉ giúp so sánh cấu hình vì video này không có nhãn.

## 4. Nếu có thêm thời gian

Thử các mức `conf` quanh 0.15–0.25 cho video tối và video trên xe bus, đồng thời xem lại các đoạn che khuất dài để tìm ID bị đổi hoặc hộp giả. Có nhãn cho các video còn lại sẽ giúp xác nhận liệu các track dài hơn có thật sự giữ đúng danh tính hay không.
