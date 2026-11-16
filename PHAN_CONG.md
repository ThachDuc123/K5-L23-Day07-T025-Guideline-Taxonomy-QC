# Phân công chi tiết — Nhóm T025 (4 người)

> Trọng tâm bài này là **guideline và độ đồng thuận**, không phải số lượng box.
> Việc được **chia đều theo từng pha**, không gán cố định một người một vai: pha nào cũng có việc cho cả 4 người,
> và việc nặng (guideline v1.0, gán nhãn, QC, báo cáo) được chia cho nhiều người cùng làm.
> Người được ghi tên là **người chịu trách nhiệm** việc đó; những người khác vẫn hỗ trợ khi cần.

## Thành viên

| Người | Mã | Tên file cá nhân |
|---|---|---|
| **A — Thạch Trung Đức** | 141 | `guideline_141.md`, `individual_notes/141.md` |
| **B — Lê Văn Trung Tính** | 145 | `guideline_145.md`, `individual_notes/145.md` |
| **C — Đinh Quang Thắng** | 152 | `guideline_152.md`, `individual_notes/152.md` |
| **D — Phạm Huỳnh** | 135 | `guideline_135.md`, `individual_notes/135.md` |

Lệnh chia ảnh (đã chạy, seed mặc định 42 — ai chạy lại cũng ra cùng một bảng chia):

```bash
python scripts/split_assign.py --members 135,141,145,152 --copy-images
```

Nhóm 4 người → **mỗi ảnh được 2 người gán độc lập** (K=2), **mỗi người đúng 20 ảnh**.

## Bảng tổng hợp: ai làm gì ở từng pha

| Pha | A — Đức (141) | B — Tính (145) | C — Thắng (152) | D — Huỳnh (135) |
|---|---|---|---|---|
| **0. Chuẩn bị** | ✅ Fork repo, TEAM.md, README; mời collaborator | Clone, cài môi trường | Clone, cài môi trường | Clone, cài môi trường |
| **1. Guideline cá nhân** | Guideline + ghi chú của mình | Guideline + ghi chú của mình | Guideline + ghi chú của mình | Guideline + ghi chú của mình |
| **2. Thống nhất** | Điều phối họp, lập danh sách chỗ 4 người khác nhau | Viết mục 1–3 của `guideline_v1.0_agreed.md` | Sửa `taxonomy.json` + chạy `make_cvat_labels.py` | Viết mục 4–5 của v1.0 (ca khó, QC + lịch sử); rà chéo guideline với taxonomy |
| **3. Gán nhãn** | Tạo Project CVAT; gán 20 ảnh | Gán 20 ảnh | Gán 20 ảnh | Gán 20 ảnh |
| **4. Đo IAA** | Phân loại một nửa số ảnh bị cờ | Chạy `qc_compare.py` + `viz_disagreement.py` | Tóm tắt `iaa_summary.md` cho cả nhóm | Phân loại nửa còn lại số ảnh bị cờ |
| **5. Calibration** | Chạy `prepare_calibration.py`, sửa nhãn ảnh bị cờ trong CVAT, export | Viết `guideline_v1.1_calibrated.md` | Sửa taxonomy nếu cần + chạy `build_final.py` | Thư ký họp: điền `calibration_decisions.csv` |
| **6. QC & nộp** | Chạy `check_submission.py`, điền kết quả TEAM.md, commit chốt, nộp LMS | Kiểm 10 ảnh QC mẫu (nửa đầu) | Chạy `qc_sample.py` + `qc_report.py`; kiểm 10 ảnh QC mẫu (nửa sau) | Viết `QC_REPORT.md` (7 mục + quyết định) |

---

## Pha 0 — Chuẩn bị (0:00 → 0:15)

- [x] **A:** Fork repo, điền `TEAM.md`, đổi tiêu đề `README.md`, mời collaborator
- [x] **A:** Cài `pip install -r requirements.txt`
- [ ] **B, C, D:** Clone repo, `pip install -r requirements.txt`, `git config user.name/email` theo tài khoản GitHub của mình

## Pha 1 — Guideline cá nhân (0:15 → 0:50) — cả 4 người, mỗi người tự làm

- [ ] Xem hết 40 ảnh trong `data/images/`
- [ ] Viết `submission/guidelines/individual/guideline_<mã>.md`
- [ ] Điền mục Pha 1 trong `submission/individual_notes/<mã>.md`
- [ ] Commit + push file của mình

## Pha 2 — Họp thống nhất + guideline v1.0 (0:50 → 1:20)

Cả 4 cùng họp. Sau buổi họp thì mỗi người làm phần của mình:

- [ ] **A:** Điều phối họp; lập danh sách các chỗ 4 bản guideline quyết định khác nhau, chốt từng chỗ
- [ ] **B:** Viết mục 1–3 của `submission/guidelines/guideline_v1.0_agreed.md` (mục tiêu, lớp + thuộc tính, quy tắc vẽ box)
- [ ] **D:** Viết mục 4–5 của v1.0 (ca khó, QC + bảng lịch sử thay đổi); rà chéo xem guideline có khớp `taxonomy.json` không
- [ ] **C:** Sửa `submission/taxonomy.json` cho khớp v1.0 (phép thử: *"mô hình có phải đoán ra điều này không?"*), chạy `python scripts/make_cvat_labels.py` → `cvat/labels_generated.json`
- [ ] Commit 3 file trên; người khác review trước khi chốt

## Pha 3 — Gán nhãn (1:30 → 2:30) — cả 4 người, mỗi người 20 ảnh

- [ ] **A:** Tạo **Project CVAT** với nhãn từ `cvat/labels_generated.json` (theo `docs/CVAT_SETUP.md`), hướng dẫn cả nhóm tạo Task
- [ ] **Mỗi người:** pull bản mới nhất, upload đúng ảnh trong `submission/qc/assignments/<mã>.txt` (đã copy sẵn ở `work/<mã>/images/`)
- [ ] **Mỗi người:** gán theo guideline v1.0, **không xem nhãn của ai**; gặp ca mơ hồ thì tick `can_review`
- [ ] **Mỗi người:** export COCO → `submission/labels/<mã>.json`, chạy `python scripts/validate_labels.py --all` sạch rồi mới push

## Pha 4 — Đo IAA (2:30 → 2:50)

- [ ] **B:** Chạy `python scripts/qc_compare.py` và `python scripts/viz_disagreement.py --images flagged`
- [ ] **C:** Tóm tắt `iaa_summary.md` cho cả nhóm: IoU trung bình, % cùng lớp, số box không ghép được
- [ ] **A + D:** Chia đôi danh sách `flagged_images.txt` / `calibration_candidates.csv`, mỗi người phân loại một nửa: **cả hai người gán cùng lệch** (guideline mơ hồ) hay **chỉ một người lệch** (chưa nắm quy tắc)

## Pha 5 — Calibration (2:50 → 3:30)

Cả 4 cùng họp để chốt quyết định cho từng ca. Sau đó:

- [ ] **D:** Làm thư ký họp, điền `calibration_decisions.csv`
- [ ] **A:** Chạy `python scripts/prepare_calibration.py`; tạo Task CVAT từ ảnh bị cờ, sửa nhãn theo quyết định của nhóm; export COCO → `submission/qc/labels_calibrated_flagged.json`
- [ ] **B:** Viết `submission/guidelines/guideline_v1.1_calibrated.md` (v1.0 + quy tắc mới + lịch sử; nếu không có quy tắc mới vẫn tạo file và ghi rõ "chỉ là gán nhầm")
- [ ] **C:** Nếu cần đổi taxonomy thì sửa `taxonomy.json` (tăng version); chạy `python scripts/build_final.py` → `labels_calibrated.json`

## Pha 6 — QC mẫu, báo cáo, nộp bài (3:30 → 4:00)

- [ ] **C:** Chạy `python scripts/qc_sample.py --n 20 --seed 42`
- [ ] **B + C:** Mỗi người kiểm 10 ảnh trong `work/review/` (B nửa đầu, C nửa sau). **Không kiểm ảnh mình đã gán**: gặp ảnh của mình thì đổi cho A hoặc D. Điền `submission/qc/qc_review.csv`, mỗi lỗi 1 dòng (5 loại), ảnh sạch ghi `khong`
- [ ] **C:** Chạy `python scripts/qc_report.py` (thêm `--guideline-gap N` / `--data-problem N` nếu có)
- [ ] **D:** Copy `docs/QC_REPORT_TEMPLATE.md` → `submission/qc/QC_REPORT.md`, điền đủ 7 mục + quyết định **Release / Hold / Rework** (cả nhóm cùng chốt quyết định)
- [ ] Nếu Rework: cả nhóm sửa lỗi theo ảnh của mình, chạy lại QC, ghi vòng sau vào báo cáo
- [ ] **A:** Chạy `python scripts/check_submission.py` cho tới khi hết `LỖI`; điền "Kết quả tóm tắt" trong `TEAM.md`; commit chốt bài; nộp link repo lên LMS
- [ ] **Mỗi người:** điền nốt `submission/individual_notes/<mã>.md` (mục 2–6)

---

## Mốc tự kiểm tra (từ lúc bắt đầu, tổng 240 phút)

| Giờ | Mốc | Ai xác nhận | Sản phẩm |
|---|---|---|---|
| 0:15 | CP0 | A | Repo + `TEAM.md` + cả 4 cài xong |
| 0:50 | CP1 | Cả 4 (mỗi người) | Guideline cá nhân đã push |
| 1:20 | CP2 | B + C + D | `guideline_v1.0_agreed.md` + `taxonomy.json` + nhãn CVAT |
| 1:30 | Nghỉ | — | — |
| 2:30 | CP3 | Cả 4 | `assignment.csv` đúng K=2; 4 file nhãn đã export; `validate_labels` sạch |
| 2:50 | CP4 | B + C | `iaa_summary.md`, `work/viz/` |
| 3:30 | CP5 | A + B + C + D | `calibration_decisions.csv`, v1.1, `labels_calibrated.json` |
| 4:00 | CP6 | A + D | `QC_REPORT.md`, `check_submission.py` sạch, đã nộp link |

## Quy tắc làm việc chung

1. **Mỗi người chỉ sửa file có tên mình** + file của việc mình phụ trách → không xung đột.
2. Trước khi push: `git pull --rebase`. Mỗi người **ít nhất 1 commit** mang tên mình.
3. Chốt xong Pha 2 thì **không đổi guideline** cho tới hết Pha 3; ca mơ hồ thì tick `can_review` + ghi chú.
4. Pha 3 gán **độc lập**: không xem nhãn, không hỏi nhau về ảnh đang làm.
5. Không commit thư mục `work/`.
