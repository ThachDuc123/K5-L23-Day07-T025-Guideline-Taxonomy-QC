# Guideline gán nhãn (bản cá nhân) — mã 145

> Bản viết lại cá nhân từ `guideline/guideline_v0_starter.md`. ĐỪNG sửa file này sau cuộc họp Pha 2 —
> bản thống nhất nằm ở `submission/guidelines/guideline_v1.0_agreed.md`.

- Phiên bản: Cá nhân (145)
- Ngày: 2026-11-16
- Người viết: Lê Văn Trung Tính (mã 145)
- Loại nhãn: bounding box (Rectangle) trên CVAT, xuất COCO 1.0

## 1. Mục tiêu

Nhãn dùng để huấn luyện **mô hình phát hiện vật 2D** (người + phương tiện) trên ảnh đường phố, phục vụ camera giao thông / hỗ trợ lái xe.

- Mô hình phải trả lời: _ở đâu có người hoặc phương tiện **thật, đang tham gia giao thông** và đó là loại gì?_
- **Sai nguy hiểm nhất:** bỏ sót người (`person`) và xe máy ở gần camera → hệ thống không phanh/cảnh báo. Vì vậy khi phân vân "có gán không" với người/xe **thật**, ưu tiên **gán + tick `can_review`** thay vì bỏ.
- **Sai gây nhiễu:** gán tượng, ma-nơ-canh, poster, phản chiếu, đồ chơi thành `person`/`car` → mô hình báo động giả. Những vật này **không gán** (xem R-05).

## 2. Lớp và thuộc tính

### Lớp

Phép thử: _"Mô hình có phải đoán ra thông tin này khi suy luận không?"_ — có → là lớp; không → là thuộc tính.

| Lớp             | Định nghĩa (một câu)                                                                                               | Ví dụ                                                                                                                       | Phản ví dụ / dễ nhầm với                                                    |
| --------------- | ------------------------------------------------------------------------------------------------------------------ | --------------------------------------------------------------------------------------------------------------------------- | --------------------------------------------------------------------------- |
| `car`           | Ô tô con ≤ 9 chỗ thân liền (sedan, hatchback, SUV, taxi), kể cả xe tự chế có biển số đang chạy trên đường.         | img_015 (xe đỏ, xe bạc), img_023 (Civic đen), img_040 (taxi xanh), img_019 (xe diễu hành có biển số)                        | Xe trong tủ kính/đồ chơi img_038; xe trưng bày bảo tàng img_024 → không gán |
| `truck`         | Xe có cabin + **thùng hàng tách rời** (xe tải, bán tải pickup, xe chở ô tô, kei truck).                            | img_001 (tải nhỏ), img_002 (xe chở ô tô), img_007 (Isuzu thùng kín), img_012 & img_007 (bán tải), img_036 (tải nhỏ mui bạt) | Tượng bán tải img_027 → không gán; minivan thùng liền img_005 → `van`       |
| `van`           | Xe thùng kín **liền khối** cabin–khoang (minivan, xe 16 chỗ cỡ nhỏ, xe giao hàng thân liền).                       | img_005 (van bạc 51LD), img_016 (van trắng bên phải)                                                                        | Bán tải gắn nắp thùng cao img_037 → vẫn `truck` (cabin & thùng tách)        |
| `bus`           | Xe khách / bus ≥ khoảng 16 chỗ, thân dài, nhiều cửa sổ hành khách.                                                 | img_009 (xe khách nhỏ), img_031 (bus vàng xa), img_037 (bus xanh bên trái), img_040 (bus vàng phía sau)                     | Van 16 chỗ thân ngắn → `van` (nếu phân vân: `bus` + `can_review`)           |
| `tuk_tuk`       | Xe 3 bánh **có động cơ** chở khách/hàng có mui (tuk-tuk, auto-rickshaw, xe lam, ba gác máy).                       | img_004 (2 xe), img_016 (2 xe), img_035 (xe vàng), img_040 (tuk-tuk vàng)                                                   | Xích lô đạp img_023 → `bicycle`; xe máy sidecar img_022 → `motorcycle`      |
| `motorcycle`    | Xe 2 bánh có động cơ (xe máy, xe tay ga, mô tô), kể cả xe máy kèm sidecar (1 box chung).                           | img_006, img_008, img_021, img_026, img_034, img_022 (sidecar)                                                              | Xe đạp img_006 (bên phải), img_029 → `bicycle`                              |
| `bicycle`       | Xe không động cơ do người đạp: xe đạp 2 bánh, xích lô 3 bánh.                                                      | img_003, img_006 (xe đạp trắng), img_023 (xích lô), img_029 (xe đạp chở ống)                                                | Xe đạp điện có bàn đạp nhưng dáng tay ga → `motorcycle`                     |
| `person`        | Người thật nhìn thấy được thân thể: đi bộ, đứng, ngồi, người lái / người ngồi sau xe hở (xe máy, xe đạp, xích lô). | img_003, img_014, img_034, img_005 (người đi xe máy), img_019 (người lái xe diễu hành, mui hở)                              | Ma-nơ-canh img_033, tượng img_017, người trên billboard img_018 → không gán |
| `ignore_region` | **Vùng** (không phải vật) chứa đám đông hoặc bãi xe dày đặc không tách được từng vật, hoặc cụm vật cao < 20 px.    | img_019 (đám đông sau rào), img_021 (dòng xe máy phía xa), img_030 (bãi xe máy dày), img_032 (hàng người chờ xa)            | Không dùng ignore để che vật lớn rõ ràng mà mình lười vẽ                    |

**Vật không thuộc lớp nào:** **không gán box** (không tạo lớp mới ở bản cá nhân). Gồm: tượng, đồ chơi/mô hình, xe trưng bày, ma-nơ-canh, poster/billboard/tranh tường, phản chiếu gương/kính, xe kéo tay/xe đẩy hàng, động vật thật. Nếu đó là thứ chiếm phần lớn ảnh mà nghĩ cần nhóm chốt → ghi vào `submission/qc/image_issues.csv`.

> Ghi chú so với `taxonomy.json` hiện có: taxonomy đang có thêm `statue`, `display_vehicle`, `non_real` và dùng tên `tuktuk`. Quan điểm của tôi: 3 lớp kia **không cần** (mô hình chỉ cần _không_ báo là người/xe), và tên nên là `tuk_tuk` như hướng dẫn lab. Mang ra họp Pha 2.

### Thuộc tính

| Thuộc tính       | Mức cố định                     | Ngưỡng / ý nghĩa                                                                                                                              |
| ---------------- | ------------------------------- | --------------------------------------------------------------------------------------------------------------------------------------------- |
| `occluded_level` | `0`                             | Thấy rõ, bị che **0 %** (hoặc < 1 %, ví dụ vài sợi dây điện).                                                                                 |
|                  | `1`                             | Bị **vật khác** che **1–30 %** diện tích ước lượng của vật đầy đủ.                                                                            |
|                  | `2`                             | Bị che **31–70 %**.                                                                                                                           |
|                  | `3`                             | Bị che **> 70 %** nhưng vẫn nhận dạng chắc chắn lớp. Nếu không nhận ra lớp → không gán.                                                       |
| `truncated`      | `no` / `yes`                    | `yes` khi vật **bị cắt bởi mép ảnh** (box chạm mép và rõ ràng vật còn tiếp ra ngoài). Mép ảnh **không** tính vào `occluded_level`.            |
| `can_review`     | tick / không (`false` mặc định) | Tick khi ≥ 1 trong: không chắc lớp; không chắc có nên gán; ranh giới box không rõ (mờ chuyển động, tối). Mỗi lần tick ghi ID/ảnh vào ghi chú. |

Cách ước lượng %: tưởng tượng box của vật đầy đủ (gồm phần bị che), so phần bị che với box đó. Ví dụ img_005: người phía sau bị người đội mũ nâu che khoảng nửa → `2`.

## 3. Quy tắc vẽ box

- **R-01 Box ôm sát phần nhìn thấy:** box chạm điểm ngoài cùng **nhìn thấy** của vật (gương, bánh xe, mũ bảo hiểm), không đoán phần bị che, không chừa khoảng trống > 2–3 px.
- **R-02 Người đi xe máy / xe đạp / xích lô:** **2 box riêng** — `person` cho mỗi người (lái + người ngồi sau, ôm cả phần chân thấy được) và 1 box `motorcycle`/`bicycle` cho xe. Box người được phép chồng lên box xe. (img_005, img_006, img_026, img_029, img_034, img_040)
- **R-03 Người trong khoang kín:** người ngồi trong ô tô, van, bus, cabin tải, dưới mui tuk-tuk → **không gán** `person` (chỉ gán xe). Ngoại lệ: xe mui trần/hở mà thấy rõ thân người từ ngực trở lên (img_019) → gán `person` + `can_review`.
- **R-04 Xe chở xe:** xe chở là 1 box `truck` ôm cả sàn chở; **mỗi xe được chở thấy rõ ≥ 50 % → 1 box `car` riêng** (img_002: các xe con trên 2 tầng). Xe trên nóc xe khác cũng áp dụng, trừ bối cảnh trưng bày.
- **R-05 Không phải vật thật → không gán:** tượng (img_011, img_017, img_027, con ngựa trên img_028), ma-nơ-canh (img_033), đồ chơi/mô hình trong tủ (img_038), xe trưng bày bảo tàng (img_024), người trên billboard/poster/tranh tường (img_018, tranh trên img_026), phản chiếu trong gương/kính (img_020).
- **R-06 Xe chủ (ego):** phần xe mang camera (nắp capô, gương, khung cửa — img_020) → không gán.
- **R-07 Hàng hóa chở theo:** box chỉ ôm **thân xe**, không ôm hàng chất vượt khỏi thân (bao tải trên nóc tuk-tuk img_035, ống dài trên img_029). Hàng nằm **trong thùng** (img_012) không cần xử lý vì đã nằm trong box xe.
- **R-08 Vật nhỏ:** vật có chiều cao box **< 20 px** → không vẽ riêng; nếu là cụm nhiều vật nhỏ → 1 `ignore_region` (img_021 phía xa, img_031 xa, img_039 phía xa).
- **R-09 Đám đông / bãi xe dày:** khi không tách được từng vật với ranh giới rõ → 1 `ignore_region` ôm cả cụm; vật **ở phía trước, tách rõ** vẫn vẽ box riêng (img_019, img_021, img_030, img_032).
- **R-10 Mờ chuyển động / thiếu sáng:** vẫn gán nếu nhận dạng được lớp; box ôm vùng **lõi** của vệt mờ (không ôm đuôi mờ), tick `can_review` (img_014, img_015, img_013).
- **R-11 Một vật nhiều phần:** sidecar + xe máy = 1 box `motorcycle` (img_022); rơ-moóc gắn liền xe tải = cùng box `truck` (img_002).

## 4. Ca khó

| Ảnh                                | Tình huống                                                 | Rule ID    | Quyết định                                                                                   | Lý do                                                                         |
| ---------------------------------- | ---------------------------------------------------------- | ---------- | -------------------------------------------------------------------------------------------- | ----------------------------------------------------------------------------- |
| img_002                            | Xe chở 8 ô tô con trên 2 tầng                              | R-04       | 1 `truck` + 1 `car` cho mỗi xe thấy ≥ 50 %                                                   | Xe con là vật thật, mô hình phải nhận ra; `occluded_level` theo khung sắt che |
| img_004, img_016, img_035, img_040 | Tuk-tuk 3 bánh, v0 không có lớp                            | R-02, R-03 | Lớp mới `tuk_tuk`; người dưới mui không gán                                                  | Hình dáng khác hẳn car/motorcycle, mô hình cần phân biệt                      |
| img_035                            | Tuk-tuk chất bao tải cao gấp đôi thân                      | R-07       | Box chỉ ôm thân + mui tuk-tuk                                                                | Hàng không phải phần của xe, box quá cao làm lệch hình dạng lớp               |
| img_037                            | Bán tải gắn nắp thùng cao (canopy)                         | —          | `truck` + `can_review`                                                                       | Cabin và thùng vẫn tách rời → định nghĩa truck; nhưng dễ nhầm van             |
| img_009                            | Xe khách cỡ nhỏ (~29 chỗ)                                  | —          | `bus`                                                                                        | Thân dài nhiều cửa sổ, không phải van                                         |
| img_017, img_027, img_011          | Tượng người cưỡi ngựa, tượng bán tải, tượng kim loại       | R-05       | Không gán                                                                                    | Không phải vật thật; img_017, img_027 có thể 0 annotation                     |
| img_028                            | Bán tải cổ thật trên đường + tượng ngựa trên thùng         | R-05       | 1 `truck` (+ `car` đỏ phía sau); ngựa không gán                                              | Xe có biển, đậu trên đường thật                                               |
| img_024                            | Xe bảo tàng, xe vàng trên nóc xe đen                       | R-05       | Không gán (bối cảnh trưng bày) + `image_issues.csv`                                          | Không tham gia giao thông; cần nhóm chốt                                      |
| img_038                            | Tủ kính xe mô hình                                         | R-05       | Không gán (0 annotation)                                                                     | Đồ chơi                                                                       |
| img_033                            | Ma-nơ-canh sau kính                                        | R-05       | Không gán                                                                                    | Không phải người thật                                                         |
| img_018                            | Người trên billboard + bãi xe sau rào                      | R-05, R-01 | Không gán người trên biển; gán `car` cho xe thật, `occluded_level` theo hàng rào             | Rào chắn mảnh che ~ 30–60 %                                                   |
| img_020                            | Phản chiếu trong gương chiếu hậu + xe ego                  | R-05, R-06 | Không gán (0 annotation)                                                                     | Phản chiếu + xe chủ                                                           |
| img_019                            | Xe diễu hành tự chế có biển số, người lái mui hở, đám đông | R-03, R-09 | `car` + `person` (`can_review`) + `ignore_region` cho đám đông; con chuột linh vật không gán | Xe chạy trên đường thật; linh vật là hình nộm                                 |
| img_022                            | Mô tô sidecar                                              | R-11       | 1 `motorcycle`                                                                               | Một phương tiện liền khối                                                     |
| img_023                            | Xích lô đạp                                                | R-02       | `bicycle` + `person` người đạp                                                               | Không động cơ                                                                 |
| img_029                            | Xe máy & xe đạp chở ống dài                                | R-02, R-07 | Box xe không ôm ống                                                                          | Hàng hóa vượt thân                                                            |
| img_014, img_015                   | Mờ chuyển động                                             | R-10       | Gán + `can_review`                                                                           | Vẫn nhận ra lớp                                                               |
| img_030                            | Bãi xe máy dày đặc                                         | R-09       | Xe phía trước tách rõ → box; phía sau → `ignore_region`                                      | Không tách được từng xe                                                       |
| img_003                            | Đòn gánh hàng rong                                         | R-01       | `person` ôm người, không ôm gánh hàng                                                        | Gánh là vật mang theo                                                         |

Câu hỏi nào chưa chốt: gắn `can_review` và ghi lên trưởng nhóm. Không tự đoán.

## 5. QC và phiên bản

- Ngưỡng QC (giữ đúng `rules` trong `taxonomy.json`): box cao tối thiểu 20 px; IoU đạt ≥ 0.85 (vật > 50 px) / ≥ 0.70 (vật nhỏ); IAA: IoU trung bình ≥ 0.85, đồng thuận lớp ≥ 95 %; ghép cặp IoU ≥ 0.5; tỉ lệ lỗi ảnh ≤ 2 %.
- Ca mơ hồ: người gán tick `can_review`; **người điều phối (141)** + 1 người không gán ảnh đó quyết trong họp calibration, ghi mã R-xx vào `calibration_decisions.csv`.
- Mọi thay đổi làm đổi nhãn → tăng phiên bản; đổi nghĩa quy tắc = `semantic` → kiểm lại cả lô.

| Phiên bản    | Thay đổi                                                                                                                            | Lý do (ca nào)                                                      |
| ------------ | ----------------------------------------------------------------------------------------------------------------------------------- | ------------------------------------------------------------------- |
| v0           | Bản khởi đầu của giảng viên                                                                                                         | —                                                                   |
| v0-draft-145 | Thêm lớp `bus`, `tuk_tuk`, `bicycle`; định nghĩa có ví dụ/phản ví dụ; ngưỡng % cho `occluded_level`; quy tắc R-01…R-11; bảng ca khó | img_002, 004, 009, 016, 017, 020, 023, 024, 027, 033, 035, 037, 038 |
