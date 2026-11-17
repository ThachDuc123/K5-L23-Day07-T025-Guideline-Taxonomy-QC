# Báo cáo QC — Nhóm T025

## 1. Mục đích, dữ liệu và phiên bản

- Mục đích và giới hạn sử dụng: Thực hành đặc tả và QC nhãn 2D trong Day 07 (phát hiện người và phương tiện trên 40 ảnh đường phố). Tập ảnh tuyển chọn để luyện ca biên; độ đại diện cho dữ liệu vận hành thực tế chưa được đánh giá.
- Nguồn và phép sử dụng dữ liệu: Đầy đủ thông tin tác giả, giấy phép và URL nguồn cho 40/40 ảnh trong `data/ATTRIBUTION.csv` và được băm SHA-256 trong `submission/qc/batch_manifest.json`.
- Manifest và checksum nhãn cuối: `submission/qc/batch_manifest.json`, nhãn cuối `submission/qc/labels_calibrated.json` (SHA-256: `9caa29a7a96fbff524c081b0c607842af61417b3a27d42587232137626a7bf56`, 40 ảnh, 469 annotation gồm 458 đối tượng và 11 vùng `ignore_region`).
- Guideline và taxonomy khi gán / khi bàn giao: Gán độc lập theo `guideline_v1.0_agreed.md` + `taxonomy.json` v1.0 (12 lớp, 3 thuộc tính). Bàn giao theo `guideline_v1.1_calibrated.md` + `guideline_change.json` (`kind: editorial`, bổ sung mã quy tắc `R-01`..`R-08`, giữ nguyên schema `taxonomy.json` v1.0).

## 2. Phân công và đồng thuận trước calibration

- Lượt hoàn thành / được giao: 80/80 lượt (40 ảnh × 2 người/ảnh, mỗi thành viên 135, 141, 145, 152 gán 20 ảnh theo `submission/qc/assignment.csv`).
- Số cặp box ghép, IoU TB, đồng thuận lớp và mẫu số: 283 cặp box ghép (trên 40 ảnh), IoU TB = 0.789 (< ngưỡng 0.85), đồng thuận lớp = 96.8% (>= ngưỡng 95%), cùng `occluded_level` = 70.7%, cùng `truncated` = 92.2% (mẫu số: 283 cặp box đã ghép với IoU >= 0.5).
- Box chưa ghép, loại do ignore, bất đồng vùng ignore: 92/85 box chưa ghép (A/B), 12 box loại do vùng ignore, 62 ca bất đồng vùng ignore (`submission/qc/iaa_summary.md`).
- Ca hai người có thể cùng sai sau khi đối chiếu tham chiếu: `img_025.jpg` (#92) và `img_031.jpg` (#136) có box `< 20px` (vi phạm `R-01`, đã loại bỏ ở bản calibrated); `img_033.jpg` (`C359`) phản chiếu xe tải trên tủ kính được chốt `non_real` (`R-05`).

## 3. Calibration và kiểm lại

- Ca đã xử lý, rule ID, bản sửa và người xác nhận: Đã chốt toàn bộ 465 ca (`C001`..`C465`) trên 38 ảnh bị cờ trong `submission/qc/calibration_decisions.csv`, gắn mã quy tắc `R-01`..`R-08` và người chốt (`135`, `141`, `145`, `152`). Phạm vi sửa lưu tại `submission/qc/labels_calibrated_4251acd81445a913.json` (đã loại 2 box `< 20px` và đóng 2 cờ `can_review`).
- Tác động v1.1 lên nhãn và ảnh: `kind: editorial` — giữ nguyên 12 lớp và 3 thuộc tính của `taxonomy.json` v1.0; phạm vi áp dụng `pham_vi=anh` cho 38 ảnh bị cờ (`img_014.jpg` và `img_027.jpg` giữ từ đồng thuận).
- Tham chiếu bằng chứng khi Rework: Vòng 1 phát hiện 3/40 ảnh (`img_003.jpg` #20, `img_010.jpg` #133, `img_029.jpg` #284) còn lệch lớp/vị trí từ `consensus_draft`; cần mở lại CVAT sửa 3 ảnh này rồi chạy `--round 2`.

## 4. Kết quả duyệt

- Vòng QC: 1
- Người review và ngày: 135, 141, 145, 152 (đổi chéo không kiểm ảnh mình đã gán), ngày 2026-11-17
- Đã duyệt / cả lô: 40/40 (38 `ca_kho`, 2 `con_lai`)
- Ảnh lỗi / ảnh đã duyệt: 3/40 = 7.5% (3/38 = 7.9% ở `ca_kho`, 0/2 = 0.0% ở `con_lai`)
- Annotation đối tượng hiện có bị lỗi / annotation đối tượng đã duyệt: 3/458 = 0.7%
- Vật bỏ sót ghi riêng: 0
- Ca khó / phần còn lại, lỗi lặp và ví dụ: `submission/qc/reviews/round-01/qc_review.csv` — 2 lỗi `sai_vi_tri_kich_thuoc` (`img_010.jpg` #133, `img_029.jpg` #284) và 1 lỗi `sai_lop` (`img_003.jpg` #20).
- Một lỗi máy chặn được và một lỗi cần người đọc ảnh: Script máy (`validate_labels.py`, `qc_report.py`) chặn được 2 box `< 20px` và 2 cờ `can_review` còn sót; người đọc ảnh phát hiện được box ghép lệch `person`/`motorcycle` (#20 ở `img_003.jpg`) và box chưa ôm sát vật (#133 ở `img_010.jpg`, #284 ở `img_029.jpg`).

## 5. Quyết định bàn giao

- Quyết định: Rework
- Lý do, người nhận và phạm vi sử dụng: Tỉ lệ ảnh lỗi vòng 1 là 3/40 = 7.5%, vượt ngưỡng tối đa 2% của lớp (`error_rate_img_max = 0.02`). Chưa bàn giao chính thức cho mô hình; gửi trả nhóm T025 để sửa lại 3 ảnh lỗi trên CVAT và duyệt lại vòng 2.
- Nếu còn việc: Thành viên `141`/`152` sửa `img_003.jpg` (#20, `R-02`), `145`/`152` sửa `img_010.jpg` (#133, `R-06`), `141`/`152` sửa `img_029.jpg` (#284, `R-06`) trên CVAT; xuất lại bản sửa, chạy `build_final.py`, `qc_sample.py --round 2`, `qc_report.py --round 2` và `check_submission.py --round 2`.
- Kiểm ngược từ báo cáo tới ảnh, rule và nhãn: `C040` / `img_003.jpg` #20 → `submission/qc/calibration_decisions.csv` (`R-02`) → `submission/guidelines/guideline_v1.1_calibrated.md` (R-02 tách `person`/xe) → `submission/qc/reviews/round-01/qc_review.csv` (`sai_lop`) → ảnh kiểm tại `work/review/round-01/img_003.jpg.png`.
