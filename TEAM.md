# Thông tin nhóm

- Tên nhóm: T025
- Mã thành viên (ba chữ số): 141, 145, 152
- Người điều phối: Thạch Trung Đức (2A202603141) — GitHub: ThachDuc123
- Người review bản cuối: Đinh Quang Thắng (2A202603152) — GitHub: quangthang311
- Repo bài nộp (URL): https://github.com/ThachDuc123/K5-L23-Day07-T025-Guideline-Taxonomy-QC
- Commit chốt bài (điền ở kênh nộp sau khi commit):
- Số thành viên: 3 → số người gán mỗi ảnh (K): 2 (nhóm 3 người → script chia 2 người/ảnh)

| STT | MSHV | Họ và tên | Lớp (L2 / L3 / khác) | Tài khoản GitHub |
|---|---|---|---|---|
| 1 | 2A202603141 | THẠCH TRUNG ĐỨC | L2A | ThachDuc123 |
| 2 | 2A202603145 | LÊ VĂN TRUNG TÍNH | L2A | tinhne |
| 3 | 2A202603152 | ĐINH QUANG THẮNG | L2A | quangthang311 |

Danh sách trên phải khớp dòng tiêu đề của `README.md` (đã đổi theo [SUBMISSION.md](SUBMISSION.md)) và `--members` đã dùng ở `scripts/split_assign.py`.

## Phân công (mỗi người làm gì ngoài phần gán nhãn)

| MSHV | Phụ trách thêm |
|---|---|
| 2A202603141 (Đức) | Trưởng nhóm + Admin CVAT: fork repo + TEAM.md + README; chạy `split_assign.py`; tạo Project/Task từ `cvat/labels_generated.json`; chạy `prepare_calibration.py` + sửa nhãn ảnh bị cờ trong CVAT (Pha 5) |
| 2A202603145 (Tính) | Owner guideline & taxonomy: soạn `guideline_v1.0_agreed.md` + sửa `taxonomy.json` + chạy `make_cvat_labels.py`; chạy `qc_compare.py` / `viz_disagreement.py` (Pha 4); ghi `guideline_v1.1_calibrated.md` + chạy `build_final.py` (Pha 5) |
| 2A202603152 (Thắng) | Owner QC & nộp bài: chạy `qc_sample.py` + điền `qc_review.csv` (người kiểm chính); viết `QC_REPORT.md`; chạy `check_submission.py`; nộp link LMS |

Chi tiết A→Z từng pha: [PHAN_CONG.md](PHAN_CONG.md)

## Kết quả tóm tắt (điền cuối bài)

- Lượt gán hoàn tất / được giao: … / 80 (40 ảnh × 2 người)
- IAA trước calibration (kèm mẫu số): IoU trung bình … , đồng thuận lớp … % (mẫu số: …)
- Số ảnh phải calibration: … / 40
- Vòng QC và số ảnh đã duyệt / cả lô: …
- Tỉ lệ lỗi: … % theo ảnh (…/…), … % theo đối tượng (…/…)
- **Quyết định** (Release / Hold / Rework) và lý do một dòng: …
