# Guideline gán nhãn — v1.0 (bản thống nhất nhóm) — Nhóm T025

- Phiên bản: v1.0
- Ngày: 2026-11-16
- Người soạn: cả nhóm T025. 
- Dựa trên: `guideline/guideline_v0_starter.md`, các bản guideline cá nhân và buổi họp nhóm.
- Taxonomy đi kèm: `submission/taxonomy.json` v1.0 (12 lớp). Nhãn CVAT: `cvat/labels_generated.json`.

---

## 0. Tóm tắt một trang: đọc trước khi vẽ

**Với mỗi vật trong ảnh, hỏi lần lượt:**

1. **Có phải người hoặc phương tiện không?** Không phải → bỏ qua (cây, cột, biển, đồ vật, thú bông, nón, cọc giao thông…).
2. **Có phải vật thật không?**
   - Tượng (đá, đồng, kim loại, kể cả tượng xe) → `statue`
   - Xe đồ chơi, mô hình, xe thật nhưng chỉ đứng trưng bày trong bảo tàng/tủ kính → `display_vehicle`
   - Ma-nơ-canh, người/xe in trên poster/billboard, ảnh phản chiếu trong gương/kính → `non_real`
3. **Có quá nhỏ không?** Cao dưới **20 px** (khoảng 1/30 chiều cao ảnh) → không vẽ.
4. **Có nằm trong khối dày đặc không tách được không?** → một box `ignore_region` phủ cả khối. Vật nào **còn tách ra được** thì vẫn vẽ riêng.
5. **Còn lại: vẽ box ôm sát phần nhìn thấy**, chọn lớp (bảng mục 2), rồi điền 3 thuộc tính:
   - `occluded_level`: 0 (che < 10%), 1 (10–50%), 2 (50–90%), 3 (> 90% hoặc không chắc là gì)
   - `truncated`: `yes` nếu vật bị mép ảnh cắt mất một phần
   - `can_review`: tick khi không chắc

**Năm lỗi hay gặp nhất:**

| Lỗi | Đúng là |
|---|---|
| Vẽ person cho người ngồi trong ô tô, dưới mui tuk-tuk | **Không vẽ.** Chỉ vẽ người đi bộ hoặc người cưỡi ngoài thân xe (xe máy, xe đạp, xích lô). |
| Một box ôm cả người lẫn xe máy | **Hai box riêng:** `person` và `motorcycle`. |
| Pickup/bán tải gán `car` | Pickup, bán tải, xe tải nhỏ có thùng → `truck`. |
| Box rộng hơn vật (thừa nền) hoặc cắt mất bánh xe, chân người | Ôm sát từ điểm trên cùng tới điểm dưới cùng của phần nhìn thấy. |
| Phủ `ignore_region` lên cả đám đông dù phía trước còn thấy rõ từng người | Vẽ riêng từng người thấy rõ; `ignore_region` chỉ phủ phần phía sau không tách được. |

---

## 1. Mục tiêu

Nhãn dùng để huấn luyện mô hình **phát hiện người và phương tiện** trên ảnh đường phố (camera giao thông, xe tự lái).

Lỗi nguy hiểm nhất:
- **Bỏ sót người/xe thật** → xe tự lái không tránh.
- **Gán vật không thật thành vật thật** (tượng xe tải → `truck`, ma-nơ-canh → `person`) → mô hình phanh hoặc né vô cớ.

Vì vậy nhóm **không bỏ trống** vật không thật mà gán vào lớp riêng (`statue`, `display_vehicle`, `non_real`), để mô hình học được cả "cái gì **không** phải người/xe thật".

## 2. Lớp và thuộc tính

### 2.1. Lớp (mỗi vật đúng một lớp)

| Lớp | Định nghĩa | Ví dụ trong bộ ảnh | Dễ nhầm với |
|---|---|---|---|
| `car` | Ô tô con: sedan, SUV, hatchback, taxi, xe mui trần; xe điện kín 4 bánh; xe tự chế chạy trên đường và có biển số. | img_012 (Mazda), img_015, img_023 (Civic), img_034, img_040 (taxi), img_019 (xe diễu có biển số) | xe trưng bày → `display_vehicle`; pickup → `truck` |
| `truck` | Xe có **thùng hàng tách khỏi cabin**: xe tải, xe tải nhỏ, **bán tải/pickup** (kể cả pickup có nắp thùng/camper), kei truck, xe đầu kéo chở ô tô. | img_001, img_002 (xe chở ô tô), img_007 (Hilux + Isuzu), img_012, img_028, img_036, img_037 | tượng xe tải → `statue` |
| `van` | Xe thùng kín liền cabin, cỡ nhỏ: minivan, xe 7–16 chỗ dạng hộp (Hiace…). | img_005 (van trắng 51LD), img_016 (van trắng) | xe khách thân dài, cửa lên xuống kiểu bus → `bus` |
| `bus` | Xe khách, xe buýt, **minibus thân xe kiểu bus** (County 29 chỗ). | img_009 (minibus), img_031, img_037 (buýt xanh), img_040 (buýt phía sau) | van → `van` |
| `motorcycle` | Xe máy, mô tô, vespa, xe tay ga, quad. Xe có **sidecar** hoặc **thùng/rơ-moóc dính liền** → **một box chung**. | img_005, img_006, img_008, img_011 (xe máy làm từ sắt vụn), img_022 (sidecar), img_025 (xe kéo thùng), img_026 | xe đạp điện vẫn đạp được → coi như `motorcycle` nếu có thân xe máy, ngược lại `bicycle` |
| `bicycle` | Xe đạp; **xích lô/cyclo** (3 bánh, đạp chân); xe đạp chở hàng rong. | img_003, img_006, img_010 (xe đạp chở rau), img_023 (xích lô), img_029 | tuk-tuk có động cơ → `tuktuk` |
| `tuktuk` | Xe 3 bánh chở khách **có động cơ** (tuk-tuk, auto-rickshaw, xe lam). **Một box ôm cả thân và mui** (và hàng chất trên nóc). | img_004 (2 chiếc), img_016 (2 chiếc), img_035 (3 chiếc), img_040 | xích lô đạp → `bicycle` |
| `person` | Người thật: đi bộ, đứng, ngồi ngoài trời, **người cưỡi ngoài thân xe** (xe máy, xe đạp, xích lô, sidecar, xe mui trần). | img_001, img_003, img_014, img_032, img_034 | tượng → `statue`; ma-nơ-canh, người trên billboard → `non_real` |
| `statue` | Tượng người, động vật, xe bằng bất kỳ chất liệu; kể cả mô hình điêu khắc cỡ lớn ngoài trời. | img_011 (tượng Predator), img_017 (tượng kỵ sĩ), img_027 (bia mộ hình pickup), img_028 (ngựa tượng) | xe thật chở tượng → xe một box, tượng một box riêng |
| `display_vehicle` | Xe đồ chơi, mô hình, **xe thật nhưng đang trưng bày** (bảo tàng, phòng triển lãm, tủ kính), không tham gia giao thông. | img_024 (bảo tàng xe, kể cả xe vàng trên nóc), img_038 (xe đồ chơi trên kệ) | xe đỗ ngoài đường → lớp xe thật |
| `non_real` | Hình người/xe **không thật**: ma-nơ-canh, người/xe in trên poster/billboard, ảnh phản chiếu trong gương hoặc kính. | img_018 (người trên billboard), img_020 (xe trong gương chiếu hậu), img_033 (ma-nơ-canh, poster, phản chiếu trên kính), img_019 (mascot chuột) | xe thật ở phía sau tấm kính (nhìn xuyên kính, không phải phản chiếu) → lớp xe thật |
| `ignore_region` | **Vùng** (không phải vật) chứa người/xe dày đặc **không tách được từng cái**. Không có thuộc tính. | img_021 (đoàn xe máy phía xa), img_030 (bãi xe phía sau), img_032 (giữa hàng người), img_019 (đám đông phía sau rào) | còn tách được → vẽ riêng từng vật |

Phép thử lớp hay thuộc tính: *"mô hình có phải tự nhận ra điều này từ ảnh không?"* Có → lớp riêng. Không (chỉ để lọc dữ liệu/QC) → thuộc tính.

### 2.2. Thuộc tính (mọi lớp trừ `ignore_region`)

| Thuộc tính | Giá trị | Cách quyết định |
|---|---|---|
| `occluded_level` | `0` | Thấy gần trọn vật, bị che **dưới 10%** |
| | `1` | Bị che **10–50%** (ví dụ xe bị một người đứng trước che phần đầu xe) |
| | `2` | Bị che **50–90%**: chỉ thấy một nửa hoặc ít hơn, nhưng vẫn chắc chắn là vật gì |
| | `3` | Bị che **trên 90%**, hoặc chỉ thấy một mẩu mà không chắc là vật gì (tick thêm `can_review`) |
| `truncated` | `no` / `yes` | `yes` khi **mép ảnh cắt mất** một phần vật (box chạm mép ảnh). Phần bị cắt mất **không** tính vào `occluded_level`. |
| `can_review` | tick / không | Tick khi không chắc lớp hoặc mức che. Ghi lại số ảnh và lý do để đem ra họp calibration. |

Quy ước khi ước lượng mức che:
- So phần **bị vật khác che** với hình dung **toàn bộ vật**.
- **Người đang cưỡi** xe máy/xe đạp **không tính** là che chính chiếc xe đó (luôn có, tính vào sẽ làm mọi xe máy lệch mức).
- Hàng rào song thưa, lưới (img_018) → ước lượng phần bị che thật sự; rào thưa nhìn rõ xe phía sau → mức `1`.
- Cờ `occluded` có sẵn của CVAT (phím `Q`) **không dùng**; chỉ dùng `occluded_level`.

## 3. Quy tắc vẽ box

**Cách vẽ**

1. Box **ôm sát phần nhìn thấy**: cạnh trên chạm điểm cao nhất, cạnh dưới chạm điểm thấp nhất (bánh xe, bàn chân), không thừa nền, không cắt mất bộ phận.
2. Vật bị che → box chỉ ôm **phần nhìn thấy**, không đoán phần bị che. Nếu vật bị che làm hai mảnh rời → **một box** ôm cả hai mảnh.
3. Vật bị cắt mép ảnh → box kéo **tới sát mép**, `truncated: yes`.
4. Gương chiếu hậu, ăng-ten, cần gạt **có** trong box xe (là một phần của xe). Đồ người cầm/gánh/vác (quang gánh img_003, ống dài img_029, túi xách) **không** tính vào box người.

**Vẽ cái gì, bỏ cái gì**

5. Vẽ **mọi** người và phương tiện thật **cao từ 20 px trở lên**, dù đang chạy, đỗ, trong sân hay trong gara.
6. **Nhoè chuyển động** (img_014, img_015): còn nhận ra là xe/người → vẽ, box ôm phần nhoè nhìn thấy rõ hình dạng; nhoè thành vệt không nhận ra → bỏ.
7. **Xe chủ** (xe người chụp đang ngồi, ví dụ thân xe và gương ở img_020, kính lái ở img_018) → **không vẽ**.
8. Người/xe **in trên tranh tường, biển hiệu, tranh hoạt hình** chỉ gán `non_real` khi nhận ra rõ là người hoặc xe và cao ≥ 20 px. Tàu, nhà máy, nhân vật hoạt hình không phải người (img_026, img_031) → bỏ.
9. Trong cảnh đồ chơi (img_038): chỉ vẽ **xe đồ chơi** (`display_vehicle`, mỗi chiếc một box). Vịt cao su, thú bông, búp bê → bỏ.

**Người và xe**

10. **Người cưỡi xe**: người một box `person`, xe một box riêng. Xe máy chở 2–3 người → mỗi người một box.
11. **Khoang kín**: người ngồi **trong** ô tô, dưới mui tuk-tuk, trong cabin xe tải/bus → **không vẽ person**. Người cưỡi **ngoài thân xe** (xe máy, xe đạp, xích lô, sidecar, quad, ghế xe mui trần như img_019) → **vẽ person**.
12. **Xe chở xe** (img_002): xe đầu kéo `truck` một box, **mỗi ô tô trên giá** một box `car`, mức che theo phần bị khung giá che.
13. **Xe chở tượng** (img_028): xe một box theo lớp xe thật (`truck`), tượng một box `statue`, hai box được phép chồng nhau.
14. **Hàng cồng kềnh dính liền xe**: hàng chất vượt thân xe (tuk-tuk chở bao trên nóc img_035, xe máy kéo thùng img_025) → **một box ôm cả xe và hàng**. Hàng nằm gọn trong thùng xe tải/pickup (img_001, img_012) → box xe như thường.
15. **Ống/hàng dài do người cầm, nối giữa hai xe** (img_029) → không thuộc xe nào, không đưa vào box.

**Đám đông và bãi xe**

16. Vẽ riêng **từng** người/xe còn tách được (thấy rõ đầu–thân hoặc thân xe, ≥ 20 px), kể cả khi bị che nhiều (`occluded_level` 2).
17. Phần phía sau **dày đặc, chồng chéo không tách được** → **một** box `ignore_region` phủ cả khối. Có thể có nhiều `ignore_region` nếu có nhiều khối tách rời.
18. **Không** dùng `ignore_region` để né việc: phía trước còn tách được thì phải vẽ từng cái.

**Khi không chắc**

19. Vật không rõ thuộc lớp nào → chọn lớp gần nhất **và tick `can_review`**, ghi số ảnh vào ghi chú cá nhân. Không tự đặt quy tắc mới.
20. Ảnh không có vật nào cần vẽ → để trống, vẫn tính là đã gán.

## 4. Ca khó: ghi chú cho từng ảnh

Mỗi người chỉ gán 20 ảnh trong `submission/qc/assignments/<mã>.txt`, nhưng đọc hết bảng để hiểu quy tắc. Bảng chỉ nêu điểm khó, **không** thay thế việc vẽ đủ mọi vật trong ảnh.

| Ảnh | Nội dung | Gán thế nào |
|---|---|---|
| img_001 | Pickup trắng đỗ cạnh quán bar, nhiều người ngồi uống | Xe → `truck` (thùng chở sắt nằm gọn trong thùng). Mỗi người thấy rõ một `person`, người bị bàn/người khác che → mức 1–2. Poster hot dog → bỏ. |
| img_002 | Xe đầu kéo chở 8 ô tô | `truck` + `car` cho từng ô tô trên giá (che theo khung giá). Người nhỏ bên phải: vẽ nếu ≥ 20 px. |
| img_003 | Phố Việt Nam, người gánh hàng, xe đạp, xe máy | Người gánh → `person` (không gồm quang gánh). Xe đạp gần → `bicycle`. Xe máy đỗ dày ở xa → vẽ chiếc tách được, khối dày phía sau → `ignore_region`. |
| img_004 | 2 tuk-tuk ban đêm | 2 box `tuktuk`. Tài xế ngồi dưới mui → **không** vẽ person. Cọc giao thông → bỏ. |
| img_005 | Cận cảnh người đi xe máy, van và ô tô phía sau | Mỗi người một `person` (nhiều người bị cắt mép → `truncated: yes`). Xe máy thấy được → `motorcycle`. Van trắng → `van`, ô tô bên trái → `car`. |
| img_006 | Dòng xe máy, xe đạp từ phía sau | Từng `motorcycle`, `bicycle`, `person` (người đi xe). Taxi trắng trái → `car`. Xe xa dưới 20 px → bỏ. |
| img_007 | Gara: pickup Hilux và xe tải Isuzu | Cả hai → `truck`. Người trong cabin → không vẽ. |
| img_008 | Phố đêm, xe máy đỗ dày, xe ôm áo cam | Người áo cam → `person`. Xe máy tách được → từng box; khối xe chồng chéo không tách được → `ignore_region`. Xe phía cuối phố: vẽ nếu ≥ 20 px. |
| img_009 | Ngã tư, minibus | Minibus → `bus`. Xe máy, người nhỏ → vẽ nếu ≥ 20 px. |
| img_010 | Chợ: xe máy, người bán rau với xe đạp | Xe máy → `motorcycle`, người ngồi trên xe → `person`. Xe đạp chở rau → `bicycle` (một box ôm cả xe và rổ rau dính liền). |
| img_011 | Tượng Predator, xe máy làm từ sắt vụn | Tượng Predator → `statue`. Từng xe máy làm từ sắt vụn → **`motorcycle`** (nhóm chốt: có hình dạng xe máy hoàn chỉnh thì gán `motorcycle`, không gán `statue`). Người, ô tô thật ở xa → lớp thật nếu ≥ 20 px; đám đông xa không tách được → `ignore_region`. |
| img_012 | Pickup chở hàng, SUV, xe tải phía sau | Pickup gần (bị cắt mép) → `truck`, `truncated: yes`. Mazda SUV → `car`. Xe tải phía sau → `truck`. |
| img_013 | Phố đêm, vật rất nhỏ | Hầu hết dưới 20 px → bỏ. Chỉ vẽ người/xe ≥ 20 px. Tượng ngựa vàng nhỏ: vẽ `statue` nếu ≥ 20 px. |
| img_014 | 2 người đi bộ, xe máy nhoè | 2 người rõ → `person`. Xe máy và người lái nhoè: còn nhận ra → vẽ; thành vệt → bỏ. |
| img_015 | Phố mưa, ô tô | Ô tô đỏ trái và bạc phải → `car`, `truncated: yes`. Ô tô đỗ phía sau → `car`. Ô tô nhoè: còn nhận ra → vẽ. |
| img_016 | Bangkok: tuk-tuk, vespa, van, cô gái phía trước | 2 `tuktuk`. Vespa → `motorcycle` + người lái `person`. Van trắng → `van`. Cô gái (bị cắt mép) → `person`, `truncated: yes`. |
| img_017 | Tượng kỵ sĩ đội nón giao thông | **Một** box `statue` ôm cả ngựa và người cưỡi (một khối tượng). |
| img_018 | Billboard ứng cử viên, bãi xe sau hàng rào | Người trên billboard → `non_real`. Ô tô sau rào → `car`, mức che theo rào. Kính xe chủ → bỏ. |
| img_019 | Xe diễu màu vàng, mascot chuột, đám đông | Xe có biển số → `car`. Người lái (mui trần) → `person`. Mascot chuột → `non_real`. Hàng người đầu sau rào → từng `person`; phía sau dày đặc → `ignore_region`. Đầu người mờ ở góc dưới → `person` nếu nhận ra. |
| img_020 | Gương chiếu hậu phản chiếu xe | Xe trong gương → `non_real`. Thân xe chủ, gương → bỏ. |
| img_021 | Đoàn xe máy dày đặc ở Sài Gòn | Phía trước: từng `motorcycle` + `person`. Phía xa không tách được → `ignore_region`. Ô tô bên trái → `car`. |
| img_022 | Ảnh đen trắng, xe máy có sidecar trong rừng | **Một** box `motorcycle` ôm cả xe và sidecar. Người lái → `person`. |
| img_023 | Honda Civic, xích lô | Civic → `car`. Xích lô → `bicycle`, người đạp → `person`. Xe máy, người phía xa → vẽ nếu ≥ 20 px. |
| img_024 | Bảo tàng xe cổ | **Mọi** xe → `display_vehicle`, kể cả xe vàng trên nóc. Xe sâu bên trong: vẽ nếu ≥ 20 px. |
| img_025 | Đường ven biển Pattaya | Người đi bộ → `person`. Ô tô → `car`. Xe máy kéo thùng → **một** box `motorcycle`. Người nhỏ trên bãi biển < 20 px → bỏ; khối dày không tách được → `ignore_region`. |
| img_026 | Xe máy chở 2 người trước tranh tường cảng | `motorcycle` + 2 `person`. Tranh tường (tàu, cảng) → bỏ. |
| img_027 | Bia mộ đá hình pickup | `statue`. |
| img_028 | Pickup cổ chở tượng ngựa, SUV đỏ phía sau | Pickup → `truck`. Ngựa → `statue`. SUV đỏ → `car` (bị pickup che một phần). |
| img_029 | Xe máy và xe đạp cùng khiêng ống dài | Xe máy → `motorcycle`, xe đạp → `bicycle`, 2 người → `person`. Ống dài → **không** đưa vào box. Người, xe đạp phía xa → vẽ nếu ≥ 20 px. |
| img_030 | Bãi xe máy dày đặc | Xe phía trước thấy rõ → từng `motorcycle` (mức che 1–2). Phía sau không tách được → `ignore_region`. Người áo xanh → `person`. |
| img_031 | Phố đêm Đài Bắc | Ô tô → `car`, xe buýt → `bus`, xe máy, người → vẽ nếu ≥ 20 px. Tranh hoạt hình trên tường → bỏ. |
| img_032 | Hàng người chờ xe buýt | Người tách được → từng `person`. Đoạn giữa chồng chéo không tách được → `ignore_region`. Civic xanh (bị cắt mép) → `car`, `truncated: yes`. Xe trong bãi phía xa → `car` nếu ≥ 20 px. |
| img_033 | Tủ kính: ma-nơ-canh, poster, phản chiếu xe tải | Ma-nơ-canh → `non_real`. Người trên poster bên trái → `non_real`. Phản chiếu xe tải trên kính → `non_real` + tick `can_review`. |
| img_034 | Người băng qua đường, ô tô, xe máy | Người → `person`. Ô tô đen → `car`. Xe máy + người lái → từng box; người bên phải bị cắt mép → `truncated: yes`. |
| img_035 | Ấn Độ: auto-rickshaw chở hàng trên nóc | Từng `tuktuk`; chiếc chở bao trên nóc → **một** box ôm cả xe và hàng. Xe máy + người lái → 2 box. Ô tô trắng → `car`. |
| img_036 | Xe tải nhỏ màu xanh có mui bạt | `truck`. Xe máy đỗ phía sau → tách được thì vẽ từng chiếc, dày đặc thì `ignore_region`. |
| img_037 | Pickup có camper, xe buýt, người đi bộ | Pickup → `truck`. Xe buýt → `bus` (từng chiếc). Người → `person`. |
| img_038 | Kệ xe đồ chơi | **Mỗi** xe đồ chơi một box `display_vehicle`. Vịt, thú bông, búp bê → bỏ. |
| img_039 | Phố Việt Nam, vật nhỏ | Người, xe máy ≥ 20 px → vẽ; nhỏ hơn → bỏ. Xe máy đỗ bên trái → `motorcycle`. |
| img_040 | Taxi, người đội mũ bảo hiểm, tuk-tuk, xe buýt | Taxi → `car` (`truncated: yes`). Người đội mũ (cắt mép) → `person`. Tuk-tuk → `tuktuk`. SUV đen → `car`. Xe buýt phía sau → `bus`. Xe máy phía trước: vẽ nếu thấy được thân xe. |

## 5. QC và phiên bản

- Ngưỡng QC (theo `rules` trong `taxonomy.json`, không đổi):
  - IAA: IoU trung bình ≥ 0.85, đồng thuận lớp ≥ 95%
  - Ghép box: IoU ≥ 0.5; ngưỡng box lớn (≥ 50 px) 0.85, box nhỏ 0.7
  - Box nhỏ nhất: cao 20 px
  - Tỉ lệ lỗi theo ảnh tối đa 2%
- Ca mơ hồ khi gán: tick `can_review`, ghi số ảnh và lý do. **Không tự đoán**, đem ra calibration (Pha 5).
- Từ khi chốt v1.0 tới hết Pha 3 **không đổi guideline**. Quy tắc mới sau calibration ghi vào `guideline_v1.1_calibrated.md`.

Lịch sử thay đổi:

| Phiên bản | Thay đổi | Lý do (ca nào) |
|---|---|---|
| v0 | Bản khởi đầu của giảng viên | — |
| v1.0 | Thêm lớp `bus`, `bicycle`, `tuktuk`, `statue`, `display_vehicle`, `non_real`; ngưỡng `occluded_level` đo được (<10 / 10–50 / 50–90 / >90%); quy tắc ôm sát phần nhìn thấy, khoang kín, đám đông, hàng cồng kềnh | Các ca tuk-tuk, bus, xích lô, tượng, bảo tàng xe, ma-nơ-canh/poster/phản chiếu, đám đông dày, nhoè, vật quá nhỏ |
| v1.0 (soát lại trước Pha 3) | Sửa toàn bộ số ảnh trong bảng ca khó cho khớp `data/images/` (bản trước bị lệch); thống nhất quy tắc đám đông (vẽ từng vật tách được, `ignore_region` chỉ cho phần không tách được); thêm trang tóm tắt, quy ước "người cưỡi không tính là che xe", người trên xe mui trần được gán `person`, tượng kỵ sĩ một box, xe máy sắt vụn img_011 gán `motorcycle`, phản chiếu img_033 gán `non_real` + `can_review`, quy tắc xe chủ, đồ cầm tay, ống dài nối hai xe, tranh tường; ghi chú cho đủ 40 ảnh. **Không đổi lớp/thuộc tính** → nhãn CVAT giữ nguyên. | Soát 40 ảnh trước khi gán |
