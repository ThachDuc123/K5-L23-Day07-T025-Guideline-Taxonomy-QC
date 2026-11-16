# Guideline gán nhãn (bản cá nhân) — Phạm Huỳnh (mã 135)

> Bản viết lại cá nhân từ `guideline/guideline_v0_starter.md`. ĐỪNG sửa file này sau cuộc họp Pha 2 —
> bản thống nhất nằm ở `submission/guidelines/guideline_v1.0_agreed.md`.

- Phiên bản: v0-draft cá nhân
- Ngày: 2026-11-16
- Người viết: Phạm Huỳnh (mã 135)

## 1. Mục tiêu
Mô hình phát hiện người và phương tiện cho xe tự lái. Tôi chia lớp **theo hình dạng xe** để mô hình học được rõ (pickup, tuk-tuk, bus khác hẳn ô tô con), và tách riêng vật không thật để mô hình không phanh vì tượng hay ma-nơ-canh.
(Nhưng: v0 chỉ có 5 lớp, không đủ cho tuk-tuk, pickup, xe buýt trong bộ ảnh.)

## 2. Lớp và thuộc tính
### Lớp
| Lớp | Định nghĩa (một câu) | Ví dụ | Phản ví dụ / dễ nhầm với |
|---|---|---|---|
| `car` | Ô tô con: sedan, SUV, taxi. | img_015, img_034 | pickup → `pickup` |
| `pickup` | Xe bán tải: cabin + thùng hở phía sau (kể cả có nắp thùng). | img_001, img_012, img_028, img_037 | xe tải → `truck` |
| `truck` | Xe tải cabin + thùng lớn. | img_002, img_007 (Isuzu), img_036 | |
| `van` | Xe thùng kín cỡ nhỏ, minivan, **minibus**. | img_005, img_009 (minibus) | xe buýt lớn → `bus` |
| `bus` | Xe buýt lớn, xe khách. | img_031, img_037 | minibus → `van` |
| `motorcycle` | Xe máy, vespa; box **chỉ ôm xe máy**, sidecar/thùng kéo không tính. Xe máy làm từ sắt vụn có hình dạng xe máy hoàn chỉnh vẫn gán `motorcycle`. | img_006, img_011, img_026 | |
| `tuktuk` | Xe 3 bánh có động cơ chở khách. | img_004, img_035 | |
| `person` | Người thật đi bộ hoặc cưỡi ngoài thân xe. | img_014, img_034 | ma-nơ-canh → `non_real` |
| `statue` | Tượng người, động vật, xe. | img_017, img_027 | |
| `display_vehicle` | Xe đồ chơi, xe trưng bày. | img_024, img_038 | |
| `non_real` | Ma-nơ-canh, poster, phản chiếu. | img_020, img_033 | |
| `ignore_region` | Đám đông, bãi xe dày đặc. | img_030, img_032 | |

Xe đạp, xích lô: **không gán** (v0 không có lớp này; xe đạp chạy chậm, ít rủi ro hơn).
(Nhưng: xích lô ở img_023 chiếm nửa ảnh, bỏ có ổn không? Ghi `can_review`.)

### Thuộc tính
- `occluded_level`: `0` che < 10% · `1` che 10–50% · `2` che 50–90% · `3` che > 90%.
- `truncated`: `yes` khi mép ảnh cắt mất một phần vật.
- `can_review`: tick khi không chắc.

## 3. Quy tắc vẽ box
- Box ôm sát phần nhìn thấy.
- Người ngồi **trong** ô tô, dưới mui tuk-tuk: **không gán**.
- Đám đông, bãi xe dày: **một** `ignore_region` phủ cả khối.
- Vật nhỏ: chỉ gán từ **32 px** trở lên (nhỏ hơn thì người gán dễ lệch nhau).
- Sidecar, thùng kéo: **không** đưa vào box xe máy.
(Nhưng: ngưỡng 32 px có thể bỏ sót xe ở xa trong img_009, img_039.)

## 4. Ca khó
| Ảnh | Tình huống | Quyết định | Lý do |
|---|---|---|---|
| img_009 | Minibus | `van` | thân ngắn, cỡ nhỏ |
| img_011 | Xe máy làm từ sắt vụn | `motorcycle` | hình dạng xe máy hoàn chỉnh |
| img_023 | Xích lô | không gán + `can_review` | không có lớp xe đạp |
| img_025 | Xe máy kéo thùng | `motorcycle` chỉ ôm xe máy | thùng không phải xe máy |
| img_030 | Bãi xe máy dày | một `ignore_region` | không đếm nổi |
| img_037 | Pickup có camper | `pickup` | vẫn là bán tải |

Câu hỏi nào chưa chốt: gắn `can_review` và ghi lên trưởng nhóm. Không tự đoán.

## 5. QC và phiên bản
- Ngưỡng QC: IAA IoU ≥ 0.8; đồng thuận lớp ≥ 90%.
- Ca mơ hồ: đem ra họp nhóm, owner guideline ghi quyết định.

| Phiên bản | Thay đổi | Lý do (ca nào) |
|---|---|---|
| v0-draft (135) | Thêm `pickup`, `bus`, `tuktuk`, `statue`, `display_vehicle`, `non_real`; minibus → `van`; bỏ xe đạp; ngưỡng 32 px | img_009, img_011, img_023, img_037 |
