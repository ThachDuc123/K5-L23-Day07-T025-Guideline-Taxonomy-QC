# Guideline gán nhãn — v1.0 (bản thống nhất nhóm) — Nhóm T025

- Phiên bản: v1.0
- Ngày: 2026-11-16
- Người soạn: cả nhóm T025 (Đức điều phối; Tính ghi biên; soạn thảo có dùng AI hỗ trợ — nhóm đã đọc, sửa và đồng ý từng quyết định; khai báo trong ghi chú cá nhân)
- Dựa trên: `guideline/guideline_v0_starter.md` + 3 bản cá nhân của thành viên + họp nhóm

## 1. Mục tiêu

Nhãn dùng để huấn luyện mô hình phát hiện **người và phương tiện** trên ảnh đường phố (camera giao thông / xe tự lái). Sai lớp hoặc gán vật không thật (tượng, đồ chơi, phản chiếu) là nguy hiểm nhất — mô hình sẽ học nhầm "xe đá trong nghĩa trang là xe tải". Vì vậy v1.0 tách riêng các lớp **không thật** thay vì bỏ trống, để mô hình học được cả "cái gì KHÔNG phải xe/người thật".

## 2. Lớp và thuộc tính

### Lớp (mỗi vật thuộc đúng một lớp)

| Lớp | Định nghĩa (một câu) | Ví dụ | Phản ví dụ / dễ nhầm với |
|---|---|---|---|
| `car` | Ô tô con, SUV, hatchback, sedan **chạy ngoài đường phố**. | sedan, SUV, taxi (img_034, 040) | xe diễu có biển số vẫn là car; xe trong bảo tàng → `display_vehicle` |
| `truck` | Xe có thùng hàng riêng: xe tải, **bán tải (pickup)**, kei truck. | img_004, 007, 012, 036, 037 | xe thùng kín nhỏ → `van`; xe tải đá/xe tải tượng → `statue` |
| `van` | Xe thùng kín cỡ nhỏ, minivan, nhiều chỗ dạng thùng kín nhỏ. | img_005, 006 (van taxi) | xe khách to → `bus` |
| `bus` | Xe khách/xe bus lớn nhiều chỗ. | img_009, 031, 037 | van lớn không phải bus |
| `motorcycle` | Xe máy, mô tô, vespa; **có sidecar gán chung 1 box**. | img_002, 008, 022 (sidecar) | xe đạp máy (ebike đạp được) cân nhắc ca khó |
| `bicycle` | Xe đạp 2 bánh; **cyclo/xích lô đạp 3 bánh** xếp lớp này. | img_002, 023, 029 | tuk-tuk có động cơ → `tuktuk` |
| `tuktuk` | Xe ba bánh chở khách **có động cơ** (auto-rickshaw). | img_003, 006, 035, 040 | cyclo đạp tay → `bicycle` |
| `person` | **Người thật**: đi bộ, đứng, ngồi, lái xe, hành khách. | img_015, 032, 034 | tượng → `statue`; ma-nơ-canh → `non_real`; người in poster → `non_real` |
| `statue` | **Tượng** người/động vật/xe bằng bất kỳ chất liệu. | img_011, 017, 027, 028 (ngựa tượng) | xe thật chở tượng → xe gán riêng, tượng gán `statue` |
| `display_vehicle` | Xe đồ chơi / mô hình / xe **trưng bày** trong bảo tàng, triển lãm, tủ kính — kể cả xe thật nhưng chỉ trưng bày. | img_024 (bảo tàng + xe đồ chơi trên nóc), img_038 | xe chạy ngoài phố → lớp xe thật |
| `non_real` | Hình ảnh không thật: **ma-nơ-canh, người/vật in trên poster–billboard–mural, phản chiếu gương/kính** (gộp chung một lớp). | img_020, 026, 033 | vật thật bị phản chiếu vẫn gán bình thường |
| `ignore_region` | **Vùng** bỏ qua (không phải vật): đám đông/bãi xe dày đặc không đếm được từng vật. | img_019 (đám đông), 030 (bãi xe máy), 032 (đám đông) | bỏ sót vật trong vùng này không tính lỗi |

Phép thử lớp/thuộc tính: *"mô hình có phải đoán ra điều này không?"* Có → lớp riêng. Không (chỉ để lọc/QC) → thuộc tính.

### Thuộc tính

| Thuộc tính | Giá trị | Ngưỡng đo được |
|---|---|---|
| `occluded_level` | `0`–`3` | `0`: che <10% · `1`: che 10–50% · `2`: che 50–90% · `3`: che >90% hoặc không xác định được vật gì |
| `truncated` | `no` / `yes` | `yes` khi vật bị cắt mép ảnh (box đi tới mép) |
| `can_review` | tick | Tick khi không chắc, ghi chú mô tả ca; đem ra calibration |

Cờ `occluded` có sẵn của CVAT (phím `Q`) **không dùng** — chỉ dùng `occluded_level`.

## 3. Quy tắc vẽ box

1. Gán box cho **mọi người thật và phương tiện thật** trong ảnh, ở mọi vị trí (kể cả đậu, trong sân, trong hangar).
2. Box **ôm sát phần nhìn thấy được** của vật — không ôm rộng nguyên nền, không cắt ngang vật. Vật bị che: box theo phần nhìn thấy, đánh `occluded_level`.
3. Vật **cắt mép ảnh**: box kéo hết tới mép, `truncated: yes`.
4. Vật **quá nhỏ** (chiều cao < ~20px, ví dụ xe/người rất xa ở img_013, 039): **không gán**.
5. **Nhoè chuyển động** (img_014, 015): còn nhận ra là xe/người → gán; nhoè không còn biết là gì → bỏ.
6. **Xe chở xe** (img_001): gán box riêng **từng chiếc** trên palang, đánh `occluded_level` theo mức bị palang che.
7. **Người trên xe** (img_002, 005, 008, 021, 026): mỗi người một box `person` riêng, xe một box riêng; người quá nhỏ/che nhiều → theo ngưỡng ở trên. **Nguyên tắc khoang kín** (theo slide Day 07, trang 33/37): người **ngồi trong khoang kín** (dưới mui tuk-tuk, xe điện kín 4 bánh, trong ô tô) → **không gán person**; người **cưỡi ngoài thân xe** (xe máy, quad, xe điện kiểu cưỡi, người đánh xe bò ngồi ngoài) → **gán person**.
8. **Đám đông / bãi xe dày đặc** (img_019, 021, 030, 032): gán **từng vật thấy rõ hoặc thấy một phần rõ** (một box, `occluded_level` 2); chỉ vùng **chỉ lộ vài điểm, không đoán được là vật gì** mới vẽ `ignore_region` phủ. Không dùng `ignore_region` cho cả vùng khi còn đếm được từng vật.
8b. **Hàng cồng kềnh** (theo slide trang 34): hàng **vượt thân xe, chiếm chỗ** (xe ba bánh chở chồng cao, xe máy chở rơ-moóc) → **một box ôm cả xe và hàng** (hàng dính liền xe = một vật, mô hình phải tránh cả khối). Hàng **nằm gọn trong thùng** xe tải/pickup (img_004, 007, 012) → không gán riêng hàng, box xe như thường.
8c. **Xe chủ (ego)** và **gương chiếu hậu**: xe chủ (nếu có) không gán; gương chiếu hậu không tính vào box xe.
9. **Không gán** vào lớp người/xe thật: tượng (`statue`), xe đồ chơi/mô hình/xe trưng bày (`display_vehicle`), ma-nơ-canh/hình in/phản chiếu (`non_real`) — gán đúng lớp mới này, không để trống. Trong cảnh có nhiều đồ chơi (img_038): **chỉ gán xe đồ chơi** là `display_vehicle` từng chiếc; thú bông, vịt cao su, nhân vật hoạt hình và phản chiếu mờ trên mặt kính **bỏ qua hoàn toàn** (không phải người/xe, không thuộc mục tiêu của mô hình).
10. Vật **không thuộc lớp nào** và không rơi vào ca trên: tick `can_review`, không tự đoán.
11. Ảnh không có vật nào cần gán: để trống, vẫn tính là đã gán.

## 4. Ca khó

| Ảnh | Tình huống | Quyết định | Lý do |
|---|---|---|---|
| img_001 | Xe tải chở 8 ô tô trên 2 tầng | `truck` + `car` từng chiếc, `occluded_level` theo palang | mô hình phải nhận ra từng chiếc |
| img_002, 005, 008, 021 | Xe máy chở 2 người, giao thông dày | `motorcycle` + `person` từng người | tránh va chạm cần biết có người |
| img_003, 006, 035, 040 | Tuk-tuk / auto-rickshaw | lớp mới `tuktuk`; 1 box ôm cả thân + mui; **người ngồi dưới mui không gán person** (người cưỡi ngoài thì gán) | khác xe máy, khác ô tô, đoán được; quy tắc khoang kín theo slide Day 07 |
| (chung) | Xe điện kín 4 bánh; quad / xe điện kiểu cưỡi | xe điện kín 4 bánh → `car` (người trong khoang không gán person); quad/xe điện cưỡi → `motorcycle` + person | slide trang 37: xét người ngồi trong khoang hay cưỡi ngoài thân xe |
| (chung) | Xe chở hàng cồng kềnh vượt thân 1,5m; xe máy kéo rơ-moóc | 1 box ôm cả xe và hàng; rơ-moóc dính liền = một vật (+ person người lái) | slide trang 34/45: hàng chiếm chỗ, xe tự lái phải tránh cả khối |
| img_009, 031, 037 | Bus / xe khách | lớp mới `bus` | kích thước & hình dạng riêng |
| img_011, 017, 027, 028 | Tượng (Predator, tượng ngựa, xe tải đá, ngựa tượng trên xe thật) | tượng → `statue`; **xe tải thật ở img_028 → `truck`**; ngựa tượng trên xe → `statue` | không dạy mô hình coi tượng là xe/người thật |
| img_024, 038 | Xe trong bảo tàng; mô hình xe trong tủ kính | `display_vehicle` **từng chiếc** thấy rõ (≥ ~20px, kể cả xe thật chỉ trưng bày); xe mô hình quá nhỏ/mờ sâu trong tủ → bỏ; thú bông/vịt đồ chơi → không gán; phản chiếu mờ trên mặt kính → bỏ | yêu cầu của nhóm: tách riêng để không học nhầm; đồ chơi đếm được thì đếm từng chiếc như quy tắc xe chở xe |
| img_019 | Xe diễu tự chế + mascot chuột + đám đông | xe → `car`; mascot → `non_real`; người thật lái → `person`; đám đông → `ignore_region` | xe chạy được có biển số là xe thật |
| img_020, 033 | Xe trong gương; phản chiếu xe trên kính tủ | `non_real` | phản chiếu không phải vật thật |
| img_033 | Ma-nơ-canh + người in poster | `non_real` (gộp chung) | cùng bản chất: hình người không thật |
| img_016, 026 | Người in billboard; tàu in mural | `non_real` | hình in |
| img_014, 015 | Xe máy nhoè chuyển động | gán nếu còn nhận ra; nhoè nặng bỏ | mô hình học được từ nhoè nhẹ |
| img_013, 039 | Vật xa quá nhỏ | bỏ nếu < ~20px | quá nhỏ không đủ tín hiệu học |
| img_030, 032 | Bãi xe máy dày đặc; đám đông xếp hàng | Gán từng xe/người thấy rõ hoặc thấy một phần rõ (`occluded_level` 2); **chỉ** vùng không đoán được mới `ignore_region` | slide trang 36: gán được tới đâu gán tới đó, bỏ sót trong ignore mới không tính lỗi |
| img_022, 023, 025 | Xe máy sidecar; cyclo; xe đạp | sidecar → 1 box `motorcycle`; cyclo → `bicycle` | nguyên tắc gộp |

## 5. QC và phiên bản

- Ngưỡng QC (giữ nguyên từ taxonomy `rules`, không đổi): IAA IoU ≥ 0.85, đồng thuận lớp ≥ 95%, tỉ lệ lỗi ảnh tối đa 2%.
- Ca mơ hồ khi gán: tick `can_review` + ghi chú — **không tự đoán**; đem ra calibration (Pha 5).
- Từ khi chốt v1.0 tới hết Pha 3 **không đổi guideline**.

Lịch sử thay đổi:

| Phiên bản | Thay đổi | Lý do (ca nào) |
|---|---|---|
| v0 | bản khởi đầu của giảng viên | — |
| v1.0 | Thêm lớp `bus`, `bicycle`, `tuktuk`, `statue`, `display_vehicle`, `non_real`; ngưỡng `occluded_level` đo được (<10/10–50/50–90/>90%); quy tắc ôm sát phần nhìn thấy; thêm 14 ca khó có ảnh cụ thể | img_001–040: tuk-tuk, bus, xe đạp, tượng, xe bảo tàng, ma-nơ-canh/poster/phản chiếu, đám đông dày, nhoè, quá nhỏ |
