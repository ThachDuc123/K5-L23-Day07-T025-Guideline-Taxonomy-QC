# Tóm tắt IAA (taxonomy v1.0)

- Ảnh được so sánh (có >=2 người gán): **40**; số người gán: 4
- Lượt gán đủ **80/80**.
- IoU trung bình và % cùng lớp **chỉ tính trên các cặp box đã ghép** (IoU >= 0.5, ghép 1-1 không phân biệt lớp). Box chưa ghép được thống kê riêng ở cột bỏ sót/thừa.
- Mẫu số: 283 cặp box ghép (tổng mọi cặp người gán, ảnh chung).

| Cặp | Ảnh | Cặp box ghép | IoU TB | Cùng lớp | Cùng occluded_level | Cùng truncated | Chưa ghép (A/B) | Bỏ qua do ignore | Đạt IAA |
|---|---|---|---|---|---|---|---|---|---|
| **TỔNG** | 40 | 283 | 0.789 | 96.8% | 70.7% | 92.2% | 92/85 | 12 | CHƯA ĐẠT |
| 135|141 | 6 | 28 | 0.792 | 100.0% | 85.7% | 89.3% | 6/7 | 0 | CHƯA ĐẠT |
| 135|145 | 9 | 62 | 0.829 | 96.8% | 79.0% | 95.2% | 37/10 | 5 | CHƯA ĐẠT |
| 135|152 | 5 | 45 | 0.827 | 100.0% | 55.6% | 95.6% | 20/19 | 1 | CHƯA ĐẠT |
| 141|145 | 5 | 36 | 0.788 | 100.0% | 61.1% | 86.1% | 9/9 | 0 | CHƯA ĐẠT |
| 141|152 | 9 | 49 | 0.729 | 87.8% | 69.4% | 89.8% | 15/29 | 1 | CHƯA ĐẠT |
| 145|152 | 6 | 63 | 0.767 | 98.4% | 73.0% | 93.7% | 5/11 | 5 | CHƯA ĐẠT |

Ngưỡng IAA: IoU TB >= 0.85, đồng thuận lớp >= 95%.

Số ca bất thường: 465 ({'co_can_review': 2, 'iou_thap': 156, 'sai_thuoc_tinh': 96, 'bat_dong_ignore': 62, 'bo_sot_thua': 140, 'sai_lop': 9}), trên 38 ảnh cần calibration.
