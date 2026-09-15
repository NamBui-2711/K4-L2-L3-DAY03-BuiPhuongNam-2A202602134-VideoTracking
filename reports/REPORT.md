# Báo cáo Ngày 3 — Tracking Annotation

Chép file này thành `reports/REPORT.md` rồi điền. Giữ nguyên các tiêu đề.

Họ tên / nhóm: `Bùi Phương Nam`
Ngày: `15/09/2026`

---

## 1. Quá trình gán nhãn

| Mục | Giá trị |
| --- | --- |
| Công cụ | CVAT / khác: `CVAT` |
| Thời gian gán `clip_02` (warm-up) | `~60` phút |
| Thời gian gán `clip_01` | `~150` phút |
| Số track đã vẽ trong `clip_01` | `8 tracks` |
| Số keyframe trung bình mỗi track | `phần này e chưa tìm được chỗ để xem dữ liệu` |

Ba tình huống khó nhất khi gán clip này, và bạn xử lý thế nào:

1. `xe bị che khuất 1 phần bởi vật thể khác: giữ nguyên track id và chỉ vẽ box cho phần vật nhìn thấy `
2. `xe đứng yên không di chuyển( chỉ di chuyển theo camera quay): giữ nguyên track id và vẽ box quanh vật thể, di chuyển box nếu vật thể di chuyển theo camera`
3. `khi xe biến mất khỏi khung hình: đánh dấu outside khi xe biến mất `

## 2. Tự kiểm và kiểm chéo

Ba lượt tua bắt được gì (lượt 1 nhìn ID, lượt 2 frame đầu/cuối, lượt 3 frame giữa):

- Lượt 1: `Kiểm tra tính liên tục của track_id , đặc biệt tại các đoạn xe bị che khuất.`
- Lượt 2: `Kiểm tra frame bắt đầu và kết thúc của từng track, tránh gán nhãn khi xe chưa xuất hiện hoặc đã ra khỏi khung hình. `
- Lượt 3: `Kiểm tra các frame giữa, tập trung vào bbox và các đoạn xe di chuyển/che khuất.`

Kiểm chéo với: `Không thực hiện peer review; đối chiếu với bản Gold do Lab Coach cung cấp`. Chi tiết ở `reports/review_partner.md`.
Số lỗi bạn tìm được trong bản của bạn ấy: `N/A`. Số lỗi bạn ấy tìm được trong bản của bạn: `N/A`.

Ca nào hai người quyết khác nhau, và luật nào còn thiếu trong `GUIDELINE_MINI.md`?

`Không có peer review nên không phát sinh trường hợp hai người có quyết định khác nhau. Bản gán nhãn được đối chiếu với kết quả Gold do Lab Coach cung cấp để kiểm tra chất lượng.`

## 3. Pre-gold lock và chấm trước/sau rework

| Evidence | Giá trị |
| --- | --- |
| SHA-256 từ `evidence/pre-gold/clip_01/manifest.json` | `81a3f3bc2c9767f2099336b548a3a0a907e7943168d1a59a80fe8b675bb20aa6` |
| Thời điểm khóa | `2026-09-15T05:48:36.872798+00:00` |
| Số row / frame / track trước khi mở reference | `613/190/8` |

| | HOTA | DetA | AssA | LocA | IDF1 | MOTA | MOTP | FP | FN | IDSW |
| --- | ---: | ---: | ---: | ---: | ---: | ---: | ---: | ---: | ---: | ---: |
| Bản pre-gold | | | | | | | | | | |
| Sau rework | | | | | | | | | | |
Em không tìm thấy dữ liệu trong google colabs. Có thể e bị miss mất một chỗ nào đó chưa làm. Nếu anh/chị Lab Coach nào biết e thiếu ở đâu thì mong anh chị có thể chia sẻ cho e.

Qua cổng (`IDF1 >= 0.80`, `MOTA >= 0.75`, `MOTP >= 0.70`): **có / chưa**

Sau khi đọc danh sách lỗi, bạn đã sửa cụ thể những gì? Ghi theo frame và ID:

| Loại lỗi | Frame | ID | Đã sửa thế nào |
| --- | --- | --- | --- |
| | | | |
| | | | |
| | | | |

## 4. Kết quả model: ByteTrack control vs ReID treatment

Cấu hình từ `outputs/model_run_config.json`:

| Mục | Giá trị |
| --- | --- |
| Python / ultralytics / torch / lap | `3.13.15/ 8.4.145/ 2.11.0+cu128/ 0.5.13` |
| weights / hai tracker | `yolo26n.pt` |
| conf / IoU / imgsz / classes | `0,25/0,7/960/[2,5,7]` |
| device | `0` |

| So sánh | HOTA | DetA | AssA | LocA | IDF1 | MOTA | MOTP | FP | FN | IDSW |
| --- | ---: | ---: | ---: | ---: | ---: | ---: | ---: | ---: | ---: | ---: |
| bạn vs gold | 0.808 | 0.789 | 0.828 | 0.880 | 0.943 | 0.881 | 0.872 | 54 | 14 | 0 |
| ByteTrack control vs gold |0.709 |0.649 |0.776 |0.846 |0.875 |0.749 |0.823 |88 |54 |2 |
| BoT-SORT + ReID vs gold |0.763 |0.711 |0.820 |0.872 |0.900 |0.792 |0.860 |91 |26 |2 |
| ReID vs bạn |0.786 |0.734 |0.842 |0.915 |0.887 |0.775 |0.908 |80 |55 |2 |

## 5. Phân tích — năm câu hỏi

**1. MOTA của bạn cao hơn hay thấp hơn IDF1? Nếu MOTA cao mà IDF1 thấp thì điều đó nói gì, và vì sao MOTA không phạt nặng lỗi ID?**

`MOTA của em thấp hơn IDF1. MOTA cao nhưng IDF1 thấp có thể cho thấy model phát hiện đối tượng khá tốt nhưng vẫn gặp vấn đề trong việc duy trì đúng identity. MOTA không phạt nặng lỗi ID vì ID switch chỉ là một thành phần của MOTA cùng với FP và FN, trong khi IDF1 tập trung trực tiếp vào việc duy trì đúng ID của đối tượng.`

**2. ByteTrack control và BoT-SORT + ReID treatment khác nhau thế nào ở IDF1, AssA và IDSW? Dẫn một frame sequence để giải thích treatment tốt hơn, tệ hơn hoặc không đổi đáng kể. Nhắc rõ đây không cô lập causal effect của ReID vì hai tracker implementation khác.**

`BoT-SORT + ReID treatment có IDF1 = 0.900 và AssA = 0.820, cao hơn ByteTrack control với IDF1 = 0.875 và AssA = 0.776. Tuy nhiên, IDSW của cả hai đều bằng 2 nên số lần đổi ID không thay đổi đáng kể. Ở frame 87, ReID có một ID switch khi track gold 5 chuyển từ ID 17 sang ID 18; trong khi ByteTrack có một ID switch khác ở frame 94, khi track gold 5 chuyển từ ID 23 sang ID 32. Điều này cho thấy cả hai tracker vẫn có lỗi association, nhưng ReID có kết quả tổng thể tốt hơn về IDF1 và AssA. Kết quả này không cô lập causal effect của ReID vì ByteTrack và BoT-SORT là hai tracker implementation khác nhau.`

**3. DetA, FP và FN đổi thế nào? Lỗi còn lại là detector hay association?**

`DetA tăng từ 0.649 lên 0.711. FP tăng nhẹ từ 88 lên 91, trong khi FN giảm đáng kể từ 54 xuống 26. Điều này cho thấy treatment cải thiện khả năng phát hiện và giảm bỏ sót đối tượng, nhưng vẫn còn một số lỗi detector thể hiện qua FP và FN. Đồng thời, vẫn còn lỗi association vì IDSW của ReID vẫn là 2 và có các trường hợp một track bị tách thành nhiều ID. Vì vậy, lỗi còn lại đến từ cả detector và association, trong đó FN đã được cải thiện rõ rệt sau treatment.`

**4. Một chỗ bạn đúng và ReID sai (frame, ID, vì sao):**

`Frame 87, gold track 5: ReID bị ID switch, chuyển từ ID 17 sang ID 18 trong khi đối tượng vẫn thuộc cùng một track. Do đó ReID sai ở bước duy trì identity`

**5. Một chỗ ReID làm bạn xem lại annotation (frame, ID, vì sao), hoặc lý do evidence cho thấy model sai:**

`Frame 118, track 6: ReID có bbox khác đáng kể so với annotation của em (IoU ≈ 0.54), nên em xem lại vị trí bbox và đối chiếu với hình ảnh gốc. Evidence chỉ cho thấy sự khác biệt giữa model và annotation, chưa đủ để kết luận annotation sai, vì vậy không thay đổi nhãn chỉ dựa trên kết quả của model.`

## 6. Nếu phải gán thêm 10 clip nữa

Bạn sẽ sửa gì trong `GUIDELINE_MINI.md`, và đổi gì trong quy trình làm việc của mình?

`Em sẽ bổ sung vào các ví dụ cụ thể về những trường hợp dễ nhầm như hai xe cắt nhau/chồng lên nhau, xe bị che khuất và xe rời khung hình rồi xuất hiện lại. em cũng sẽ ghi rõ hơn cách xử lý ID và bbox trong các trường hợp này. Về quy trình, em sẽ xem nhanh toàn bộ clip trước khi gán để xác định các đoạn khó, sau đó tập trung kiểm tra ID continuity và bbox tại các đoạn có occlusion hoặc xe giao nhau. Sau khi gán xong, em sẽ chạy validator và visualization để kiểm tra trước khi export/nộp. Khi đối chiếu với model, em sẽ dùng kết quả model làm evidence để xem lại annotation chứ không mặc định model là đúng.`


### ở phần cuối cùng của mục 6 trong google Colab, dựa theo kết quả thì em thấy khi thử nghiệm thêm với `appearance_thresh` = 0.7, 0.8 và 0.9 cho thấy kết quả tracking gần như không thay đổi trên clip_01. Cấu hình 0.7 và 0.8 cho cùng kết quả, trong khi 0.9 giảm nhẹ IDF1 và tăng FN. Vì vậy, trong thí nghiệm này chưa thấy lợi ích rõ ràng khi tăng ngưỡng lên 0.9.

## 7. Tệp đã nộp

- [x] `annotations/clip_01/gt.txt`
- [x] `annotations/clip_02/gt.txt`
- [x] `evidence/pre-gold/clip_01/gt.txt` và `manifest.json`
- [x] `GUIDELINE_MINI.md` đã điền
- [x] `outputs/eval_vs_gold.json`
- [x] `outputs/model_bytetrack_clip_01.txt`
- [x] `outputs/model_reid_clip_01.txt`
- [x] `outputs/model_run_config.json`
- [x] `outputs/eval_bytetrack_vs_gold.json`, `outputs/eval_reid_vs_gold.json`, `outputs/eval_reid_vs_me.json`
- [x] `reports/review_partner.md`
- [x] `reports/REPORT.md` (file này)
