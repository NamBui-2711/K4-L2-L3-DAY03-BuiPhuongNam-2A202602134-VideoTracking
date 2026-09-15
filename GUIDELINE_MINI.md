# Mini annotation guideline — Ngày 3 (tracking)

> Điền file này **trong lúc gán nhãn**, không phải sau khi xong. Mỗi lần bạn dừng
> lại nghĩ "cái này tính sao nhỉ?" thì đó là một dòng phải ghi vào đây.
>
> Đây là tài liệu mà người gán nhãn tiếp theo sẽ đọc để làm giống bạn. Nếu hai
> người trong nhóm gán khác nhau, gần như luôn là vì file này chưa nói rõ — chứ
> không phải vì ai kém.

Nhóm / tên: `Bùi Phương Nam`
Clip: `clip_01`, `clip_02`

---

## 1. Phạm vi: gán cái gì, không gán cái gì

Một lớp duy nhất: **`vehicle`** — xe bốn bánh (xe con, van, xe buýt, xe tải).

| Gán | Không gán |
| --- | --- |
| xe con, SUV, taxi, xe bán tải | người đi bộ |
| van, minivan | xe đạp |
| xe buýt, minibus | **xe máy / mô tô** |
| xe tải, xe đầu kéo | xe trong ảnh quảng cáo, trong gương, dưới bóng nước |

Bổ sung của nhóm (nếu có): `Không gán quầy bán đồ ăn`

## 2. Luật ID — phần quan trọng nhất

| Tình huống | Luật của nhóm | Vì sao |
| --- | --- | --- |
| Xe bị che một phần rồi hiện lại | giữ nguyên ID nếu bị che **dưới 25 frame** (mặc định của lab: 25 frame = 2 giây @ 12.5 fps) | `vẫn coi là cùng một xe nếu thời gian mất dấu ngắn` |
| Xe bị che lâu hơn ngưỡng trên | `tạo track mới khi xe xuất hiện lại` | `tránh gán nhầm identity sau thời gian mất dấu quá lâu ` |
| Xe rời khung hình rồi quay lại | mặc định: **track mới** | `không duy trì track khi xe đã hoàn toàn rời khỏi khung hình` |
| Hai xe cắt nhau / chồng lên nhau | `giữ ID dựa vào vị trí, hướng di chuyển và đặc điểm của từng xe; kiểm tra lại các frame sau khi chúng tách nhau` | `tránh ID switch giữa hai x` |

## 3. Luật bbox

| Tình huống | Luật của nhóm |
| --- | --- |
| Xe bị cắt bởi rìa ảnh | bbox chạm đúng rìa, không đoán phần ngoài ảnh |
| Xe bị xe khác che một phần | bbox ôm phần **nhìn thấy được** |
| Xe vừa xuất hiện, còn rất nhỏ / rất mờ | bắt đầu track từ frame đầu tiên xác định được là xe bốn bánh; ngưỡng nhóm chọn: `chỉ khi có đủ đặc điểm để xác định chắc chắn là xe` |
| Xe đang đỗ, không di chuyển | `vẫn gán bbox và giữ track khi xe còn nhìn thấy` |
| Keyframe đặt dày ở đâu | `đặt dày hơn ở đoạn xe đổi hướng, thay đổi tốc độ, bị che khuất hoặc bbox có nguy cơ lệch` |

## 4. Ít nhất ba ca mơ hồ đã gặp thật

Ghi **frame cụ thể** và **ID cụ thể**, không ghi chung chung.

### Ca 1
- Clip / frame / ID: `clip_01/87/track 5`
- Tình huống: `xe bị xe khác che mất một phần lớn`
- Quyết định: `gán bbox cho phần nhìn thấy của xe `
- Lý do: `dựa vào luật bbox`

### Ca 2
- Clip / frame / ID: `clip_01/0/track 3`
- Tình huống: `xe đứng yên không di chuyển trong toàn bộ clip ( chỉ di chuyển di camera quay hình)`
- Quyết định: `gán box và giữ track khi xe được nhìn thấy`
- Lý do: `dựa vào luật bbox và id`

### Ca 3
- Clip / frame / ID: `clip_01/132/track 8`
- Tình huống: `xe bắt đầu lọt vào khung hình nhưng chưa đủ căn cứ xác minh đó là xe gì ( có phải xe ô tô ko?)`
- Quyết định: `không gán bbox cho đến khi nhìn rõ và xác định được xe `
- Lý do: `dựa vào luật bbox`

## 5. Sửa gì sau khi chấm với gold và sau khi kiểm chéo

Luật nào trong file này hoá ra còn thiếu hoặc còn mơ hồ? Viết lại cho rõ:
- `Cần kiểm tra kỹ các đoạn hai xe cắt/chồng lên nhau và các đoạn occlusion vì đây là những vị trí dễ xảy ra ID switch.`
