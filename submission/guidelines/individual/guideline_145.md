# Guideline gán nhãn (bản cá nhân) — Lê Văn Trung Tính (mã 145)

> Bản viết lại cá nhân từ `guideline/guideline_v0_starter.md`. ĐỪNG sửa file này sau cuộc họp Pha 2 —
> bản thống nhất nằm ở `submission/guidelines/guideline_v1.0_agreed.md`.

- Phiên bản: v0-draft cá nhân
- Ngày: 2026-11-16
- Người viết: Lê Văn Trung Tính (mã 145)

## 1. Mục tiêu
Mô hình phát hiện người và phương tiện cho camera giao thông. Sai nguy hiểm nhất là **nhận nhầm vật không thật thành thật** (tượng xe tải, ma-nơ-canh), nên tôi tách riêng các lớp không thật. Ngược lại, các loại xe hai–ba bánh tôi gộp lại vì về mặt tránh va chạm chúng giống nhau.
(Nhưng: v0 không nói gì về tượng, xe đồ chơi, phản chiếu.)

## 2. Lớp và thuộc tính
### Lớp
| Lớp | Định nghĩa (một câu) | Ví dụ | Phản ví dụ / dễ nhầm với |
|---|---|---|---|
| `car` | Xe 4 bánh chở người cỡ nhỏ: sedan, SUV, taxi, **cả pickup/bán tải**. | img_012 (pickup + Mazda), img_037 (pickup camper) | xe tải thùng lớn → `truck` |
| `truck` | Xe tải thực thụ (cabin + thùng lớn). | img_002, img_007 (Isuzu), img_036 | pickup → `car` |
| `van` | Xe thùng kín cỡ nhỏ, minivan. | img_005, img_016 | |
| `bus` | Xe buýt, xe khách, minibus. | img_009, img_031, img_037 | van → `van` |
| `motorcycle` | **Mọi xe 2–3 bánh**: xe máy, xe đạp, xích lô, tuk-tuk. Sidecar/thùng kéo một box chung. | img_006, img_016 (tuk-tuk), img_023 (xích lô) | |
| `person` | Người thật đi bộ hoặc cưỡi ngoài thân xe. | img_014, img_032 | ma-nơ-canh → `non_real` |
| `statue` | Tượng người, động vật, xe. | img_017, img_027 | |
| `display_vehicle` | Xe đồ chơi, mô hình, xe trưng bày bảo tàng. | img_024, img_038 | |
| `non_real` | Ma-nơ-canh, người in poster/billboard, phản chiếu. | img_018, img_020, img_033 | |
| `ignore_region` | Phần đám đông không tách được. | img_021, img_032 | |

(Nhưng: gộp tuk-tuk với xe máy có thể làm box rất khác nhau về kích thước, để nhóm bàn.)

### Thuộc tính
- `occluded_level`: `0` che < 10% · `1` che 10–50% · `2` che 50–90% · `3` che > 90%.
- `truncated`: `yes` khi mép ảnh cắt mất một phần vật.
- `can_review`: tick khi không chắc, ghi lý do.

## 3. Quy tắc vẽ box
- Box ôm sát phần nhìn thấy.
- Người **ngồi trong** ô tô, cabin, dưới mui tuk-tuk: **không gán** (không nhìn rõ, mô hình không cần).
- Đám đông: **vẽ từng người còn tách được**, phần dày đặc phía sau mới phủ `ignore_region`.
- Vật nhỏ: gán từ **10 px** trở lên (camera giao thông cần thấy vật ở xa).
- Sidecar, thùng kéo: **một box chung** với xe.
(Nhưng: người cưỡi có tính là che chiếc xe không thì chưa rõ.)

## 4. Ca khó
| Ảnh | Tình huống | Quyết định | Lý do |
|---|---|---|---|
| img_012 | Pickup chở hàng | `car` | cỡ xe con |
| img_016 | Tuk-tuk xanh | `motorcycle` | xe 3 bánh |
| img_023 | Xích lô | `motorcycle` + `person` | gộp xe 2–3 bánh |
| img_027 | Bia mộ hình pickup | `statue` | không thật |
| img_032 | Hàng người chờ xe buýt | từng `person` tách được + `ignore_region` đoạn giữa | không bỏ sót người rõ |
| img_038 | Kệ xe đồ chơi | `display_vehicle` từng chiếc | đếm được |

Câu hỏi nào chưa chốt: gắn `can_review` và ghi lên trưởng nhóm. Không tự đoán.

## 5. QC và phiên bản
- Ngưỡng QC: IAA IoU ≥ 0.85, đồng thuận lớp ≥ 95%.
- Ca mơ hồ: cả nhóm biểu quyết ở buổi calibration.

| Phiên bản | Thay đổi | Lý do (ca nào) |
|---|---|---|
| v0-draft (145) | Thêm `bus`, `statue`, `display_vehicle`, `non_real`; gộp xe 2–3 bánh vào `motorcycle`; pickup → `car` | img_012, img_016, img_027, img_038 |
