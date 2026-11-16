# Guideline gán nhãn (bản cá nhân) — Thạch Trung Đức (mã 141)

> Bản viết lại cá nhân từ `guideline/guideline_v0_starter.md`. ĐỪNG sửa file này sau cuộc họp Pha 2 —
> bản thống nhất nằm ở `submission/guidelines/guideline_v1.0_agreed.md`.

- Phiên bản: v0-draft cá nhân
- Ngày: 2026-11-16
- Người viết: Thạch Trung Đức (mã 141)


## 1. Mục tiêu
Nhãn dùng cho mô hình phát hiện người và phương tiện để xe tự lái **tránh va chạm**. Điều quan trọng nhất là mô hình biết "ở đây có vật cần tránh và nó to cỡ nào", nên tôi ưu tiên **ít lớp, dễ gán đồng đều** hơn là chia nhỏ.
(Nhưng: v0 không nói vật không thật như tượng, ma-nơ-canh có cần gán không. Tôi đề xuất gom chung vào một lớp.)

## 2. Lớp và thuộc tính
### Lớp
| Lớp | Định nghĩa (một câu) | Ví dụ | Phản ví dụ / dễ nhầm với |
|---|---|---|---|
| `car` | Ô tô con chở người (sedan, SUV, taxi). | img_015, img_023, img_040 (taxi) | pickup → `truck` |
| `truck` | Mọi xe cỡ lớn hoặc có thùng: xe tải, pickup, **xe buýt, minibus**. | img_002, img_007, img_009 (minibus), img_037 | van nhỏ → `van` |
| `van` | Xe thùng kín cỡ nhỏ, minivan. | img_005, img_016 | minibus thân dài → `truck` |
| `motorcycle` | Xe máy, vespa; có sidecar hoặc thùng kéo thì **một box chung**. | img_006, img_022, img_025 | |
| `bicycle` | Xe đạp, xích lô. | img_003, img_023 | |
| `tuktuk` | Xe 3 bánh có động cơ chở khách. | img_004, img_016, img_035 | xích lô → `bicycle` |
| `person` | Người thật, kể cả người ngồi trong xe nếu nhìn thấy. | img_014, img_034 | tượng, ma-nơ-canh → `fake_object` |
| `fake_object` | **Một lớp chung** cho mọi hình người/xe không thật: tượng, xe đồ chơi, xe trưng bày, ma-nơ-canh, poster, phản chiếu. | img_017, img_024, img_027, img_033, img_038 | |
| `ignore_region` | Vùng đông người/xe. | img_021, img_030 | |

Vật **không thuộc lớp nào** (cây, biển, đồ vật): không gán.
(Nhưng: tách bus khỏi truck có thể hữu ích về sau, để nhóm bàn.)

### Thuộc tính
- `occluded_level`: đánh theo **bộ phận còn thấy** thay vì phần trăm (phần trăm khó ước lượng):
  - `0`: thấy toàn bộ
  - `1`: mất một phần nhỏ (người: mất bàn chân; xe: mất một bánh hoặc một góc)
  - `2`: chỉ thấy nửa vật (người: chỉ thân trên; xe: chỉ nửa đầu hoặc nửa đuôi)
  - `3`: chỉ thấy một mẩu (người: chỉ đầu; xe: chỉ nóc hoặc đèn)
- `truncated`: `yes` khi box chạm mép ảnh.
- `can_review`: tick khi không chắc.

## 3. Quy tắc vẽ box
- Box ôm sát phần nhìn thấy, không thừa nền.
- **Người ngồi trong ô tô/tuk-tuk** nhìn thấy qua kính hoặc dưới mui: **vẫn gán** `person`, `occluded_level` 2–3 (mô hình cần biết trong xe có người).
- **Đám đông, bãi xe dày**: phủ **một** `ignore_region` lên cả khối, không vẽ từng người. Như vậy đỡ tốn thời gian và tránh lệch giữa người gán.
- Vật **cao dưới 20 px**: không gán.
- Xe có sidecar, xe kéo thùng, tuk-tuk chở hàng trên nóc: **một box** ôm cả xe và phần dính liền.
- Xe chở xe: mỗi xe một box riêng.
(Nhưng: chưa rõ người đứng đầu đám đông thấy rõ có phải vẽ riêng không.)

## 4. Ca khó
| Ảnh | Tình huống | Quyết định | Lý do |
|---|---|---|---|
| img_002 | Xe đầu kéo chở 8 ô tô | `truck` + `car` từng chiếc | mỗi chiếc là một vật riêng |
| img_004 | Tài xế ngồi dưới mui tuk-tuk | `tuktuk` + `person` (`occluded_level` 2) | có người trong xe |
| img_009 | Minibus | `truck` | xe cỡ lớn, không cần lớp riêng |
| img_017 | Tượng kỵ sĩ | `fake_object` | không thật |
| img_021 | Đoàn xe máy dày đặc | một `ignore_region` phủ cả khối phía sau, vẽ riêng vài xe đầu tiên | quá đông |
| img_033 | Ma-nơ-canh, poster | `fake_object` | không thật |

Câu hỏi nào chưa chốt: gắn `can_review` và ghi lên trưởng nhóm. Không tự đoán.

## 5. QC và phiên bản
- Ngưỡng QC: IoU ghép box ≥ 0.5; ảnh nào hai người lệch lớp thì đem ra họp.
- Trưởng nhóm quyết ca mơ hồ sau khi nghe cả nhóm.

| Phiên bản | Thay đổi | Lý do (ca nào) |
|---|---|---|
| v0-draft (141) | Thêm `bicycle`, `tuktuk`, `fake_object`; gộp bus vào truck; mức che theo bộ phận | img_004, img_009, img_017, img_033 |
