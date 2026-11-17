# Guideline gán nhãn (bản cá nhân) — Đinh Quang Thắng (mã 152)

> Bản viết lại cá nhân từ `guideline/guideline_v0_starter.md`. ĐỪNG sửa file này sau cuộc họp Pha 2 —
> bản thống nhất nằm ở `submission/guidelines/guideline_v1.0_agreed.md`.

- Phiên bản: v0
- Ngày: 16/11/2026
- Người viết: Đinh Quang Thắng (mã 152)


## 1. Mục tiêu
Mô hình phát hiện người và phương tiện **thật** trên đường. Giữ taxonomy gần v0 nhất có thể, chỉ thêm `bicycle`; vật không thật thì **không gán** để mô hình không học thêm lớp vô ích.
(Nhưng: v0 nói "gán mọi người và phương tiện" mà không rõ có tính tượng, đồ chơi không.)

## 2. Lớp và thuộc tính
### Lớp
| Lớp | Định nghĩa (một câu) | Ví dụ | Phản ví dụ / dễ nhầm với |
|---|---|---|---|
| `car` | Xe chở người có mui: ô tô con, taxi, **tuk-tuk**. | img_015, img_040 (taxi + tuk-tuk) | pickup → `truck` |
| `truck` | Xe tải, pickup, **xe buýt lớn**. | img_002, img_012, img_031 (xe buýt) | |
| `van` | Xe thùng kín cỡ nhỏ, minivan, **minibus**. | img_005, img_009 (minibus) | |
| `motorcycle` | Xe máy, vespa; box **chỉ ôm thân xe máy**. | img_006, img_026 | sidecar, thùng kéo nằm ngoài box |
| `bicycle` | Xe đạp, xích lô. | img_003, img_023, img_029 | |
| `person` | Người thật; người trong xe chỉ gán khi **thấy rõ đầu và vai** qua kính. | img_014, img_019 | |
| `ignore_region` | Vùng ảnh mờ/tối không nhìn ra vật. | img_013 | |

Vật không thật (tượng, xe đồ chơi, xe bảo tàng, ma-nơ-canh, poster, phản chiếu): **không gán gì**.
(Nhưng: tượng xe tải img_027 trông y như xe thật, không dạy thì mô hình có thể nhầm.)

### Thuộc tính
- `occluded_level`: `0` che < 25% · `1` che 25–50% · `2` che 50–75% · `3` che > 75%.
- `truncated`: `yes` khi box chạm mép ảnh.
- `can_review`: tick khi không chắc.

## 3. Quy tắc vẽ box
- Box ôm sát phần nhìn thấy.
- Đám đông: **gán từng người**, kể cả người chỉ lộ đầu (`occluded_level` 3); không dùng `ignore_region` cho người.
- Vật nhỏ: gán từ **10 px**.
- Xe có sidecar hoặc kéo thùng: box xe máy **chỉ ôm xe máy**, phần sidecar/thùng không gán.
(Nhưng: gán từng đầu người trong img_032 rất tốn thời gian.)

## 4. Ca khó
| Ảnh | Tình huống | Quyết định | Lý do |
|---|---|---|---|
| img_009 | Minibus | `van` | cỡ nhỏ hơn xe buýt |
| img_019 | Đám đông sau rào | gán từng `person` thấy đầu | không bỏ sót người |
| img_020 | Xe trong gương | không gán | không phải vật thật |
| img_022 | Xe máy sidecar | `motorcycle` chỉ ôm xe máy | sidecar không phải xe máy |
| img_024 | Bảo tàng xe | không gán | xe trưng bày không tham gia giao thông |
| img_040 | Taxi và tuk-tuk | cả hai `car` | đều chở khách, có mui |

Câu hỏi nào chưa chốt: gắn `can_review` và ghi lên trưởng nhóm. Không tự đoán.

## 5. QC và phiên bản
- Ngưỡng QC: tỉ lệ lỗi theo ảnh ≤ 5%; mẫu QC 20 ảnh.
- Người kiểm QC quyết ca mơ hồ.

| Phiên bản | Thay đổi | Lý do (ca nào) |
|---|---|---|
| v0-draft (152) | Thêm `bicycle`; tuk-tuk → `car`; minibus → `van`; không gán vật không thật | img_009, img_020, img_024, img_040 |
