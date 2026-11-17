# Báo cáo QC — Nhóm T025

## 1. Mục đích, dữ liệu và phiên bản

- Mục đích và giới hạn sử dụng: Thực hành đặc tả và QC nhãn 2D trong Day 07 (phát hiện người và phương tiện trên 40 ảnh đường phố). Tập ảnh tuyển chọn để luyện ca biên; độ đại diện cho dữ liệu vận hành thực tế chưa được đánh giá.
- Nguồn và phép sử dụng dữ liệu: Đầy đủ thông tin tác giả, giấy phép và URL nguồn cho 40/40 ảnh trong `data/ATTRIBUTION.csv` và được băm SHA-256 trong `submission/qc/batch_manifest.json`.
- Manifest và checksum nhãn cuối: `submission/qc/batch_manifest.json`, nhãn cuối `submission/qc/labels_calibrated.json` (SHA-256: `bba0a2375fca86c67f115cc4acc77175a26037091ecb3da289a054da26429c78`, 40 ảnh, 469 annotation gồm 458 đối tượng và 11 vùng `ignore_region`).
- Guideline và taxonomy khi gán / khi bàn giao: Gán độc lập theo `guideline_v1.0_agreed.md` + `taxonomy.json` v1.0 (12 lớp, 3 thuộc tính). Bàn giao theo `guideline_v1.1_calibrated.md` + `guideline_change.json` (`kind: editorial`, bổ sung mã quy tắc `R-01`..`R-08`, giữ nguyên schema `taxonomy.json` v1.0).

## 2. Phân công và đồng thuận trước calibration

- Lượt hoàn thành / được giao: 80/80 lượt (40 ảnh × 2 người/ảnh, mỗi thành viên 135, 141, 145, 152 gán 20 ảnh theo `submission/qc/assignment.csv`).
- Số cặp box ghép, IoU TB, đồng thuận lớp và mẫu số: 283 cặp box ghép (trên 40 ảnh), IoU TB = 0.789 (< ngưỡng 0.85), đồng thuận lớp = 96.8% (>= ngưỡng 95%), cùng `occluded_level` = 70.7%, cùng `truncated` = 92.2% (mẫu số: 283 cặp box đã ghép với IoU >= 0.5).
- Box chưa ghép, loại do ignore, bất đồng vùng ignore: 92/85 box chưa ghép (A/B), 12 box loại do vùng ignore, 62 ca bất đồng vùng ignore (`submission/qc/iaa_summary.md`).
- Ca hai người có thể cùng sai sau khi đối chiếu tham chiếu: `img_025.jpg` (#92) và `img_031.jpg` (#136) có box `< 20px` (vi phạm `R-01`, đã loại bỏ ở bản calibrated); `img_033.jpg` (`C359`) phản chiếu xe tải trên tủ kính được chốt `non_real` (`R-05`).

## 3. Calibration và kiểm lại

- Ca đã xử lý, rule ID, bản sửa và người xác nhận: Đã chốt toàn bộ 465 ca (`C001`..`C465`) trên 38 ảnh bị cờ trong `submission/qc/calibration_decisions.csv`, gắn mã quy tắc `R-01`..`R-08` và người chốt (`135`, `141`, `145`, `152`). Phạm vi sửa lưu tại `submission/qc/labels_calibrated_86767b46dbf183cd.json` (đã loại 2 box `< 20px` và đóng 2 cờ `can_review`; tên file đổi từ `labels_calibrated_4251acd81445a913.json` vì `guideline_change.json` được cập nhật, nội dung nhãn vòng 1 giữ nguyên).
- Tác động v1.1 lên nhãn và ảnh: `kind: editorial` — giữ nguyên 12 lớp và 3 thuộc tính của `taxonomy.json` v1.0; phạm vi áp dụng `pham_vi=anh` cho 38 ảnh bị cờ (`img_014.jpg` và `img_027.jpg` giữ từ đồng thuận).
- Tham chiếu bằng chứng khi Rework: Vòng 1 phát hiện 3/40 ảnh (`img_003.jpg` #20, `img_010.jpg` #133, `img_029.jpg` #284) còn lệch lớp/vị trí từ `consensus_draft` (`submission/qc/reviews/round-01/`, quyết định Rework). Đã sửa 4 box trong `labels_calibrated_86767b46dbf183cd.json`: `img_003.jpg` #20 `motorcycle` hạ mép trên xuống tay lái, không lấn người ngồi (`R-02`, `occluded_level` 2); `img_010.jpg` #133 ôm sát xe (`R-06`); `img_029.jpg` #284 ôm sát người (`R-06`) và #291 đổi `bicycle` → `motorcycle` vì là xe máy (`C299`, `R-04`). Chạy lại `build_final.py`, duyệt lại toàn bộ lô ở vòng 2 (`submission/qc/reviews/round-02/`).

## 4. Kết quả duyệt

- Vòng QC: 2
- Người review và ngày: 135 (20 ảnh), 141 (14 ảnh), 145 (6 ảnh), đổi chéo không kiểm ảnh mình đã gán, ngày 2026-11-17
- Đã duyệt / cả lô: 40/40 (38 `ca_kho`, 2 `con_lai`)
- Ảnh lỗi / ảnh đã duyệt: 0/40 = 0.0% (0/38 ở `ca_kho`, 0/2 ở `con_lai`); vòng 1 trước đó 3/40 = 7.5%
- Annotation đối tượng hiện có bị lỗi / annotation đối tượng đã duyệt: 0/458 = 0.0% (vòng 1: 3/458 = 0.7%)
- Vật bỏ sót ghi riêng: 0
- Ca khó / phần còn lại, lỗi lặp và ví dụ: `submission/qc/reviews/round-02/qc_review.csv` — không còn lỗi. Lỗi vòng 1 (`round-01/qc_review.csv`: 2 `sai_vi_tri_kich_thuoc` ở `img_010.jpg` #133, `img_029.jpg` #284; 1 `sai_lop` ở `img_003.jpg` #20) đều đến từ box gộp của `consensus_draft` khi hai người gán lệch lớp; khi sửa phát hiện thêm `img_029.jpg` #291 sai lớp cùng gốc (`C299`).
- Một lỗi máy chặn được và một lỗi cần người đọc ảnh: Script máy (`validate_labels.py`, `qc_report.py`) chặn được 2 box `< 20px` và 2 cờ `can_review` còn sót; người đọc ảnh phát hiện được box ghép lệch `person`/`motorcycle` (#20 ở `img_003.jpg`) và box chưa ôm sát vật (#133 ở `img_010.jpg`, #284 ở `img_029.jpg`).

## 5. Quyết định bàn giao

- Quyết định: Release
- Lý do, người nhận và phạm vi sử dụng: Vòng 2 duyệt toàn bộ 40/40 ảnh, tỉ lệ ảnh lỗi 0/40 = 0.0% (≤ ngưỡng 2%, `error_rate_img_max = 0.02`). Bàn giao `labels_calibrated.json` cho coach Day 07 để chấm bài thực hành; chỉ dùng cho mục đích học tập, chưa dùng để huấn luyện mô hình vận hành vì tập ảnh tuyển chọn ca biên.
- Nếu còn việc: Không còn việc bắt buộc. Lưu ý IAA trước calibration chưa đạt (IoU TB 0.789 < 0.85), chủ yếu do ranh giới `ignore_region` và mức `occluded_level`; lô sau nên gán theo `R-07`, `R-08` của v1.1 ngay từ đầu.
- Kiểm ngược từ báo cáo tới ảnh, rule và nhãn: `C040` / `img_003.jpg` #20 → `submission/qc/calibration_decisions.csv` (`R-02`) → `submission/guidelines/guideline_v1.1_calibrated.md` (R-02 tách `person`/xe) → `submission/qc/reviews/round-01/qc_review.csv` (`sai_lop`, Rework) → sửa trong `labels_calibrated_86767b46dbf183cd.json` → `submission/qc/reviews/round-02/qc_review.csv` (`khong`) → ảnh kiểm tại `work/review/round-02/img_003.jpg.png`.
