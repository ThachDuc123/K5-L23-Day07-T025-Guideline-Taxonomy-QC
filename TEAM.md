# Thông tin nhóm

- Tên nhóm: T025
- Mã thành viên (ba chữ số): 135, 141, 145, 152
- Người điều phối: Thạch Trung Đức (2A202603141) — GitHub: ThachDuc123
- Người review bản cuối: Đinh Quang Thắng (2A202603152) — GitHub: quangthang311
- Repo bài nộp (URL): https://github.com/ThachDuc123/K5-L23-Day07-T025-Guideline-Taxonomy-QC
- Commit chốt bài (điền ở kênh nộp sau khi commit):
- Số thành viên: 4 → số người gán mỗi ảnh (K): 2 (nhóm 4 người → script chia 2 người/ảnh, mỗi người 20 ảnh)

| STT | MSHV | Họ và tên | Lớp (L2 / L3 / khác) | Tài khoản GitHub |
|---|---|---|---|---|
| 1 | 2A202603141 | THẠCH TRUNG ĐỨC | L2A | ThachDuc123 |
| 2 | 2A202603145 | LÊ VĂN TRUNG TÍNH | L2A | tinhne |
| 3 | 2A202603152 | ĐINH QUANG THẮNG | L2A | quangthang311 |
| 4 | 2A202603135 | PHẠM HUỲNH | L2A | phamhuynhmk1-png |

Danh sách trên phải khớp dòng tiêu đề của `README.md` (đã đổi theo [SUBMISSION.md](SUBMISSION.md)) và `--members` đã dùng ở `scripts/split_assign.py`.

## Phân công (chia đều theo pha, không chia theo vai trò)

Mỗi người gán **20 ảnh** (K=2). Việc ngoài phần gán nhãn được chia đều, pha nào cũng có việc cho cả 4 người:

| Pha | 141 (Đức) | 145 (Tính) | 152 (Thắng) | 135 (Huỳnh) |
|---|---|---|---|---|
| 2. Thống nhất | Điều phối họp, lập danh sách bất đồng | Viết v1.0 mục 1–3 | `taxonomy.json` + `make_cvat_labels.py` | Viết v1.0 mục 4–5, rà chéo với taxonomy |
| 3. Gán nhãn | Tạo Project CVAT + gán 20 ảnh | Gán 20 ảnh | Gán 20 ảnh | Gán 20 ảnh |
| 4. IAA | Phân loại ½ ảnh bị cờ | `qc_compare.py` + `viz_disagreement.py` | Tóm tắt `iaa_summary.md` | Phân loại ½ ảnh bị cờ |
| 5. Calibration | `prepare_calibration.py` + sửa nhãn CVAT | `guideline_v1.1_calibrated.md` | `build_final.py` (+ taxonomy nếu cần) | Điền `calibration_decisions.csv` |
| 6. QC & nộp | `check_submission.py`, commit chốt, nộp LMS | Kiểm 10 ảnh QC | `qc_sample.py` + `qc_report.py` + kiểm 10 ảnh QC | Viết `QC_REPORT.md` |

Chi tiết A→Z từng pha: [PHAN_CONG.md](PHAN_CONG.md)

## Kết quả tóm tắt (điền cuối bài)

- Lượt gán hoàn tất / được giao: 80 / 80 (40 ảnh × 2 người = 4 người × 20 ảnh)
- IAA trước calibration (kèm mẫu số): IoU trung bình 0.789, đồng thuận lớp 96.8% (mẫu số: 283 cặp box ghép)
- Số ảnh phải calibration: 38 / 40 (465 ca trong `calibration_candidates.csv`)
- Vòng QC và số ảnh đã duyệt / cả lô: Vòng 1 — 40 / 40 ảnh
- Tỉ lệ lỗi: 7.5% theo ảnh (3/40), 0.7% theo đối tượng (3/458)
- **Quyết định** (Release / Hold / Rework) và lý do một dòng: **Rework** — tỉ lệ ảnh lỗi 3/40 = 7.5% vượt ngưỡng 2% (3 ảnh `img_003.jpg`, `img_010.jpg`, `img_029.jpg` còn lệch từ `consensus_draft`, cần sửa trên CVAT rồi duyệt vòng 2).
