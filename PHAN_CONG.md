# Phân công chi tiết — Nhóm T025 (3 người)

> Trọng tâm bài này là **guideline và độ đồng thuận**, không phải số lượng box. Cả 3 cùng tham gia
> các buổi họp chung (Pha 2, 4, 5) và đều phải gán nhãn phần ảnh của mình (Pha 3).
> Vai trò chính dưới đây là **người chịu trách nhiệm cuối** cho sản phẩm của pha đó — người kia vẫn hỗ trợ.

## Thành viên & vai trò

| Người | MSHV | Vai trò chính |
|---|---|---|
| **A — Thạch Trung Đức** | (mã 141) | Trưởng nhóm + Admin CVAT |
| **B — Lê Văn Trung Tính** | (mã 145) | Owner guideline & taxonomy |
| **C — Đinh Quang Thắng** | (mã 152) | Owner QC & nộp bài |

Quy ước tên file cá nhân (không dấu viết liền):
`(mã 141)_ThachTrungDuc` · `(mã 145)_LeVanTrungTinh` · `(mã 152)_DinhQuangThang`

Lệnh chia ảnh (chạy đúng 1 lần, seed mặc định 42 — mọi người phải ra cùng một bảng chia):

```bash
python scripts/split_assign.py --members 141,145,152 --copy-images
```

Nhóm 3 người → **mỗi ảnh được 2 người gán độc lập** (K=2).

---

## PHẦN A — Thạch Trung Đức (Trưởng nhóm + Admin CVAT)

### A1 · Pha 0 — Chuẩn bị ✅ (đã setup sẵn)
- [x] Fork repo, điền `TEAM.md`, đổi tiêu đề `README.md` (đã làm trong setup)
- [x] Mời 2 bạn làm collaborator; mỗi người clone + `git config user.name/email`
- [x] Cài `pip install -r requirements.txt` (venv sẵn, pillow OK)

### A2 · Pha 1 — Guideline cá nhân (~35')
- [ ] Xem hết 40 ảnh `data/images/`
- [ ] Viết xong `submission/guidelines/individual/guideline_(mã 141)_ThachTrungDuc.md`
- [ ] Bắt đầu điền `submission/individual_notes/(mã 141)_ThachTrungDuc.md` (mục Pha 1)
- [ ] Commit + push file của mình

### A3 · Pha 2 — Họp thống nhất (cùng cả nhóm, A điều phối)
- [ ] Điều phối cuộc họp: lập danh sách chỗ 3 người quyết khác nhau
- [ ] Đóng góp quyết định cho từng ca mơ hồ

### A4 · Pha 3 — Chia ảnh & admin CVAT (60')
- [ ] Chạy lệnh `split_assign.py` ở trên, commit `submission/qc/` (báo nhóm pull)
- [ ] Tạo **Project CVAT** với nhãn từ `cvat/labels_generated.json` (theo `docs/CVAT_SETUP.md`)
- [ ] Hỗ trợ B, C tạo Task + upload đúng ảnh của `submission/qc/assignments/<MSHV>.txt`
- [ ] Gán nhãn xong ảnh của mình → export COCO → `submission/labels/141.json`
- [ ] Chạy `python scripts/validate_labels.py --all` sạch rồi mới push

### A5 · Pha 4 — Đo IAA (cùng cả nhóm)
- [ ] Cùng đọc `iaa_summary.md` + `work/viz/*.png`

### A6 · Pha 5 — Calibration (họp cùng cả nhóm; A phụ CVAT)
- [ ] Cùng chốt quyết định từng ca, điền `calibration_decisions.csv`
- [ ] Chạy `python scripts/prepare_calibration.py`
- [ ] Tạo Task mới trong CVAT từ ảnh bị cờ, nạp nhãn nháp, sửa theo quyết định nhóm
- [ ] Export COCO → `submission/qc/labels_calibrated_flagged.json`

---

## PHẦN B — Lê Văn Trung Tính (Owner guideline & taxonomy)

### B1 · Pha 0 — Chuẩn bị ✅
- [x] Clone repo, cài venv/pillow (đã làm trong setup)
- [ ] `git config user.name "Le Van Trung Tinh"` + email GitHub

### B2 · Pha 1 — Guideline cá nhân (~35')
- [ ] Xem hết 40 ảnh `data/images/`
- [ ] Viết xong `submission/guidelines/individual/guideline_(mã 145)_LeVanTrungTinh.md`
- [ ] Bắt đầu điền `submission/individual_notes/(mã 145)_LeVanTrungTinh.md`
- [ ] Commit + push file của mình

### B3 · Pha 2 — Soạn guideline v1.0 (sau cuộc họp, ~30')
- [ ] Ghi biên các chỗ 3 người quyết khác nhau vào buổi họp
- [ ] Viết `submission/guidelines/guideline_v1.0_agreed.md` đủ **5 phần** (mục tiêu, lớp + thuộc tính, quy tắc, ca khó, QC + phiên bản) + bảng lịch sử thay đổi
- [ ] Sửa `submission/taxonomy.json` cho khớp v1.0 (phép thử: *"mô hình có phải đoán ra điều này không?"*)
- [ ] Chạy `python scripts/make_cvat_labels.py` → `cvat/labels_generated.json` mới
- [ ] Commit cả 3 file (A hỗ trợ review)

### B4 · Pha 3 — Gán nhãn (60')
- [ ] Upload đúng ảnh của mình vào Task CVAT, gán theo guideline v1.0, **không xem nhãn của ai**
- [ ] Export COCO → `submission/labels/145.json`
- [ ] `validate_labels.py --all` sạch rồi mới push

### B5 · Pha 4 — Chạy script IAA (~20')
- [ ] `python scripts/qc_compare.py`
- [ ] `python scripts/viz_disagreement.py --images flagged`
- [ ] Tóm tắt `iaa_summary.md` cho cả nhóm: IoU trung bình, % cùng lớp, số box không ghép được

### B6 · Pha 5 — Owner guideline v1.1 & gộp nhãn (40')
- [ ] Cùng chốt quyết định từng ca (họp cả nhóm)
- [ ] Ghi các **quy tắc mới** vào `submission/guidelines/guideline_v1.1_calibrated.md` (v1.0 + bổ sung + lịch sử; không có quy tắc mới thì vẫn tạo file và ghi rõ "chỉ là gán nhầm")
- [ ] Cần đổi taxonomy thì sửa `taxonomy.json` (tăng version)
- [ ] Chạy `python scripts/build_final.py` → `labels_calibrated.json`

---

## PHẦN C — Đinh Quang Thắng (Owner QC & nộp bài)

### C1 · Pha 0 — Chuẩn bị ✅
- [x] Clone repo, cài venv/pillow (đã làm trong setup)
- [ ] `git config user.name "Dinh Quang Thang"` + email GitHub

### C2 · Pha 1 — Guideline cá nhân (~35')
- [ ] Xem hết 40 ảnh `data/images/`
- [ ] Viết xong `submission/guidelines/individual/guideline_(mã 152)_DinhQuangThang.md`
- [ ] Bắt đầu điền `submission/individual_notes/(mã 152)_DinhQuangThang.md`
- [ ] Commit + push file của mình

### C3 · Pha 2 — Họp thống nhất (cùng cả nhóm)
- [ ] Đóng góp quyết định; giúp B rà soát taxonomy có khớp guideline không

### C4 · Pha 3 — Gán nhãn (60')
- [ ] Upload đúng ảnh của mình vào Task CVAT, gán theo guideline v1.0, **không xem nhãn của ai**
- [ ] Export COCO → `submission/labels/152.json`
- [ ] `validate_labels.py --all` sạch rồi mới push

### C5 · Pha 4 — Phân tích bất thường (cùng cả nhóm)
- [ ] Đọc `calibration_candidates.csv` + `flagged_images.txt`
- [ ] Cùng phân loại: **cả hai cùng lệch** (guideline mơ hồ) hay **chỉ một người lệch** (chưa nắm quy tắc)

### C6 · Pha 6 — QC mẫu + báo cáo + nộp bài (~30')
- [ ] Chạy `python scripts/qc_sample.py --n 20 --seed 42`
- [ ] Làm **người kiểm chính** (ít tham gia gán ảnh nào nhất thì kiểm ảnh đó): duyệt `work/review/`, điền `submission/qc/qc_review.csv` (mỗi lỗi 1 dòng, 5 loại; ảnh sạch ghi `khong`)
- [ ] Chạy `python scripts/qc_report.py` (thêm `--guideline-gap N` / `--data-problem N` nếu có)
- [ ] Copy `docs/QC_REPORT_TEMPLATE.md` → `submission/qc/QC_REPORT.md`, điền đủ 7 mục + quyết định **Release / Hold / Rework**
- [ ] Nếu Rework: sửa lỗi, chạy lại QC, ghi vòng sau vào báo cáo (Rework hợp lệ; Release khi số liệu không đạt mới bị trừ điểm)
- [ ] Chạy `python scripts/check_submission.py` — hết `LỖI`
- [ ] Điền "Kết quả tóm tắt" `TEAM.md` + commit chốt bài (hash vào `TEAM.md`)
- [ ] Nộp link repo lên LMS

---

## Mốc tự kiểm tra (từ lúc bắt đầu, tổng 240 phút)

| Giờ | Mốc | Ai xác nhận | Sản phẩm |
|---|---|---|---|
| 0:15 | CP0 | A | Repo + `TEAM.md` + cài xong |
| 0:50 | CP1 | Cả 3 (mỗi người) | Guideline cá nhân đã push |
| 1:20 | CP2 | **B** | `guideline_v1.0_agreed.md` + `taxonomy.json` + nhãn CVAT |
| 1:30 | Nghỉ | — | — |
| 2:30 | CP3 | Cả 3 | `assignment.csv` đúng K=2; nhãn đã export; `validate_labels` sạch |
| 2:50 | CP4 | **B** | `iaa_summary.md`, `work/viz/` |
| 3:30 | CP5 | A + B | `calibration_decisions.csv`, v1.1, `labels_calibrated.json` |
| 4:00 | CP6 | **C** | `QC_REPORT.md`, `check_submission.py` sạch, nộp link |

## Quy tắc làm việc chung

1. **Mỗi người chỉ sửa file có tên mình** + phần pha mình phụ trách → không xung đột.
2. Trước khi push: `git pull --rebase`. Mỗi người **ít nhất 1 commit** mang tên mình.
3. Pha 2 chốt xong → **không đổi guideline** tới hết Pha 3; ca mơ hồ thì tick `can_review` + ghi chú.
4. Pha 3 gán **độc lập**: không xem nhãn, không hỏi nhau về ảnh đang làm.
5. Không commit thư mục `work/`.
