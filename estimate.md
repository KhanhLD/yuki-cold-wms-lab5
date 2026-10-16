# Estimate Lab5 — Yuki Cold WMS

Báo cáo SWP đầy đủ (HTML, chỉ đọc): [docs/estimate-swp-khanhld-project-1-20261015-1710.html](docs/estimate-swp-khanhld-project-1-20261015-1710.html).

Skill `estimate-swp` đọc 279 item của GitHub Project: 13 epic, 61 issue có sub-issue (cộng 74 item cha, cha lấy tổng con) và 205 lá. Skill chấm trực tiếp từng lá bằng hai điểm Scope và Complexity, tra bảng ra Standard Work Point (SWP). Ở đầu báo cáo HTML có dòng `existingEstimateCheck: unavailable`: đó là bước skill kiểm "issue này đã có estimate chưa" nhờ công cụ KPI của công ty, và nó không chạy được vì dự án lab này không nằm trên công cụ đó. Skill vì vậy chấm tất cả 205 lá, không bỏ issue nào.

Tổng SWP trong HTML là 1057, trên Project là 1060: hai con số chênh đúng 3 SWP, do chủ dự án chỉnh tay một issue sau khi skill chạy (mục đầu bên dưới). Mọi bảng số của file này sinh bằng script từ ảnh chụp Project đọc ngược ngày 2026-10-16 và từ dữ liệu nhúng trong HTML, rồi được script kiểm lại sau khi dán.

## Năm issue mẫu và lời chủ dự án

Skill chọn năm issue mẫu theo quy tắc cố định (thấp nhất, trung vị dưới, cao nhất, ngoại lai, và nơi hai người chấm lệch nhau nhất) rồi dừng lại cho chủ dự án xem. Ở bước này có hai người chấm độc lập: người chấm chính cho điểm toàn bộ lá; người chấm thứ hai (một mô hình AI khác, không thấy kết quả của người thứ nhất) chấm lại mười lăm issue có độ tin thấp (mười bốn việc hỏi khách và một việc đào tạo). Người chấm thứ hai cho điểm cao hơn ở cả mười lăm issue, vì tính cả việc rà spec, chờ trả lời và cập nhật issue liên quan. Điểm chính thức vẫn là của người chấm chính; điểm thứ hai chỉ để lộ chỗ bất định. Giờ ước là giờ làm của một người, gồm tự kiểm, không tính thời gian chờ khách trả lời.

| Issue | Skill chấm (Scope × Complexity → SWP) | Chủ dự án chốt (SWP; giờ ước) | Lý do |
|---|---|---|---|
| #53 (E04-I06) — Làm rõ với khách về đối soát tổng dòng nhận với số báo (mẫu thấp nhất) | Scope 1 × Complexity 1 → 1 SWP; người chấm thứ hai: 3 SWP | Scope 1 × Complexity 1 → 1 SWP (giữ nguyên); 3 giờ | Độ khó nằm ở chờ khách trả lời, không ở công làm — SWP đo công làm nên giữ 1 |
| #131 (E13-I11) — Đào tạo người dùng bằng tiếng Nhật cho từng nhóm vai trò (mẫu trung vị dưới) | Scope 5 × Complexity 0 → 5 SWP; người chấm thứ hai: 6 SWP | Scope 8 × Complexity 0 → 8 SWP (nâng); 24 giờ | 7 bộ nội dung riêng cho 7 nhóm vai trò + làm bằng tiếng Nhật có người bản ngữ rà, cộng môi trường thử có dữ liệu che — rộng hơn một việc tài liệu thường; chỉ áp riêng issue này |
| #268 (E13-I02-S01) — Chiều vào: nhận file ERP, kiểm checksum – schema – khoá, nạp một giao dịch cho cả file (BE) (mẫu cao nhất) | Scope 5 × Complexity 3 → 10 SWP | Scope 8 × Complexity 3 → 16 SWP (nâng); 48 giờ | Tích hợp file với ERP thật của khách hay vỡ ở định dạng / phiên bản schema / dữ liệu bẩn, cộng nạp cả file một giao dịch và khoá chống trùng cần test kỹ; chỉ áp riêng issue này |
| #19 (E01-I06) — Pipeline CD: cổng duyệt, blue/green, rollback, bản ghi phát hành (mẫu ngoại lai) | Scope 5 × Complexity 3 → 10 SWP | Scope 5 × Complexity 3 → 10 SWP (giữ nguyên); 32 giờ | Blue/green + rollback là mẫu đội đã làm, 4 môi trường và diễn tập đã tính đủ; rủi ro vận hành có thật nhưng dịch vụ AWS quản lý sẵn giữ nó trong mức vừa |
| #74 (E06-I09) — Làm rõ với khách các điểm mở của FEFO và 日付逆転 (mẫu hai người chấm lệch nhất) | Scope 2 × Complexity 0 → 2 SWP; người chấm thứ hai: 5 SWP | Scope 2 × Complexity 0 → 2 SWP (giữ nguyên); 8 giờ | Rà spec FEFO là công của các issue #7 (E06) làm tính năng — tính vào đây là đếm trùng; độ khó nằm ở chờ khách trả lời, không ở công làm (giữ 2 dù người chấm thứ 2 cho 5) |

Chủ dự án giữ nguyên điểm của #53 (E04-I06) và #74 (E06-I09) vì độ khó của hai issue này nằm ở chờ khách chứ không ở công làm. Chủ dự án giữ nguyên #19 (E01-I06) vì đội đã làm mẫu triển khai blue/green này. Chủ dự án nâng #131 (E13-I11) từ 5 lên 8 và #268 (E13-I02-S01) từ 10 lên 16, với lý do ghi trong bảng.

**Vì sao #131 và #268 "chỉ áp riêng issue này", và vì sao #102 (E10-I03) lại được chỉnh tay.** Khi chủ dự án sửa điểm một issue mẫu, hiệu chỉnh đó có thể được lưu thành quy tắc áp cho cả nhóm issue cùng đặc điểm, hoặc được đánh dấu `issue-specific` để chỉ áp riêng issue đó; skill không có chế độ chọn một phần của nhóm: hiệu chỉnh lan theo nguyên nhóm hoặc không lan. Với #131 (E13-I11) và #268 (E13-I02-S01), chủ dự án chọn "chỉ riêng" vì lý do nâng điểm gắn với riêng từng issue. #102 (E10-I03) (xử lý file từ S3 rồi nạp vào hệ thống) thuộc cùng nhóm tích hợp với #268 (E13-I02-S01), nên chủ dự án muốn nó nâng theo; nhưng #123 (E13-I03) (mail relay, chỉ gửi thông báo, đơn giản hơn) cũng nằm trong nhóm đó và chủ dự án muốn giữ nguyên. Vì skill không lan chọn lọc được, chủ dự án để skill chạy với "chỉ riêng" rồi tự sửa #102 (E10-I03) từ 5 lên 8 SWP ngay trên Project. HTML của skill vẫn ghi giá trị gốc 5; script kiểm số cộng chỉnh tay này vào báo cáo trước khi so với Project, nên con số 1057 (HTML) và 1060 (Project) khớp nhau sau chỉnh tay.

## Từ SWP ra giờ: một hệ số k cho cả dự án

**Cách đã chọn: giờ = SWP × k.** SWP đo độ lớn tương đối của việc, không đo giờ; muốn có giờ thì cần một cầu nối cố định. Một hệ số chung cho mọi issue dễ kiểm lại (mọi lá đều chia được cho cùng một số) và không để giờ của một epic trôi riêng so với SWP của nó.

Quy tắc cụ thể:

1. **Neo.** Chủ dự án ước giờ cho sáu issue: năm issue mẫu của skill và một issue dải vừa (6–12 SWP) ngoài năm mẫu. Ba trong năm issue mẫu là việc hỏi khách hoặc đào tạo, không phải việc kỹ thuật, nên neo thứ sáu chọn một issue kỹ thuật (#155 (E04-I03-S01), 8 SWP) để k không lệch về phía việc hỏi khách. Với neo thứ sáu chủ dự án giữ điểm 8 SWP của skill và ước khoảng ba ngày làm việc của một BE, gồm unit test và tự kiểm.
2. **Loại neo kém tin cậy.** Issue nào skill khuyên tách (`splitRecommended`) bị bỏ khỏi phép tính k vì nó là điểm neo kém. Ở lần chấm cuối không có issue nào như vậy (0 trên 205 lá), nên sáu neo đều dùng được.
3. **k là trung vị.** Mỗi neo cho một tỉ lệ giờ / SWP; k là trung vị của sáu tỉ lệ.
4. **Giờ của mỗi lá = SWP × k, làm tròn half-up tới 0,5.** Với k = 3,00 mọi giờ đều là bội số của 3 nên bước làm tròn không đổi số nào; quy tắc vẫn được giữ để đổi k sau này không phải đổi cách tính.
5. **Cha = tổng con.** Issue cha và epic không được ước riêng: SWP và giờ của chúng là tổng của các lá bên dưới, và cha không có người phụ trách.
6. **Áp nhất quán.** Script ghi giờ lên Project theo công thức trên cho mọi lá, rồi đọc ngược. Kết quả: 205 trên 205 lá có giờ = 3 × SWP, tổng 1060 SWP ứng với 3180 giờ.

| Neo | Vai trò của neo | SWP chốt | Giờ chủ dự án ước | Giờ / SWP | Giờ trên Project (SWP × k) | Lệch (giờ) |
|---|---|---:|---:|---:|---:|---:|
| #53 (E04-I06) | mẫu thấp nhất | 1 | 3 | 3,0 | 3 | 0 |
| #131 (E13-I11) | mẫu trung vị dưới | 8 | 24 | 3,0 | 24 | 0 |
| #268 (E13-I02-S01) | mẫu cao nhất | 16 | 48 | 3,0 | 48 | 0 |
| #19 (E01-I06) | mẫu ngoại lai | 10 | 32 | 3,2 | 30 | +2 |
| #74 (E06-I09) | mẫu hai người chấm lệch nhất | 2 | 8 | 4,0 | 6 | +2 |
| #155 (E04-I03-S01) | neo thứ sáu, dải vừa (không phải issue mẫu) | 8 | 24 | 3,0 | 24 | 0 |

Sáu tỉ lệ: 3,0 · 3,0 · 3,0 · 3,2 · 4,0 · 3,0. Sắp xếp lại: 3,0 · 3,0 · 3,0 · 3,0 · 3,2 · 4,0. Hai giá trị ở giữa đều là 3,0, trung vị là 3,0, vậy **k = 3,00 giờ / SWP**. Tỉ lệ lệch xa nhất là #74 (E06-I09) ở mức 4,0 giờ / SWP.

**Vì sao giờ chủ dự án ước cho issue mẫu có thể khác SWP × k trên Project.** Trên Project có một quy ước duy nhất cho cả 205 lá; giờ chủ dự án ước là cảm nhận riêng từng issue. Hai chỗ khác nhau: #19 (E01-I06) chủ dự án ước 32 giờ còn Project ghi 30 giờ, #74 (E06-I09) chủ dự án ước 8 giờ còn Project ghi 6 giờ; bốn neo còn lại khớp đúng. Issue nhỏ như #74 (E06-I09) (2 SWP) có phần việc cố định (soạn, gửi, theo dõi, ghi kết quả) không co lại theo điểm, nên một k chung cho nó thấp hơn cảm nhận của chủ dự án. Chủ dự án chấp nhận chênh này thay vì đặt k riêng theo cỡ issue, vì hệ số riêng làm hai issue cùng SWP có giờ khác nhau và không còn kiểm tổng bằng một phép nhân được.

## Tổng theo epic

Mỗi hàng là một epic ở trạng thái ghi trên Project. "Số issue" là các issue cấp hai dưới epic, "Sub-issue" đếm riêng các sub-issue của chúng. Tổng SWP và giờ cộng từ các lá. Bốn sub-issue `phase-2` có SWP và giờ, nhưng không xếp sprint.

| Epic | Số issue | Sub-issue | Tổng SWP | Tổng giờ | trong đó Phase 2 (SWP) | trong đó Phase 2 (giờ) | Ghi chú |
|---|---:|---:|---:|---:|---:|---:|---|
| E01 (#1) — Nền tảng & hạ tầng | 20 | 8 | 139 | 417 | 0 | 0 | Nền tảng dùng chung; tổng SWP lớn nhất vì mọi epic đứng trên nó |
| E02 (#3) — Đăng nhập, phân quyền, nhật ký | 6 | 6 | 47 | 141 | 0 | 0 |  |
| E03 (#4) — Dữ liệu gốc | 8 | 9 | 58 | 174 | 0 | 0 |  |
| E04 (#5) — Nhập kho | 8 | 9 | 60 | 180 | 10 | 30 | Phần Phase 2: lịch hẹn nhập hàng (nhãn `phase-2`, không xếp sprint) |
| E05 (#6) — Tồn kho | 10 | 14 | 93 | 279 | 12 | 36 | Phần Phase 2: sức chứa và gợi ý vị trí lưu kho (nhãn `phase-2`, không xếp sprint) |
| E06 (#7) — Hạn dùng & phân bổ FEFO | 9 | 14 | 86 | 258 | 0 | 0 |  |
| E07 (#8) — Xuất kho & giao hàng | 9 | 12 | 80 | 240 | 0 | 0 |  |
| E08 (#9) — Lập tuyến giao hàng | 7 | 8 | 44 | 132 | 0 | 0 |  |
| E09 (#10) — Ràng buộc giờ lái xe tải 2024 (拘束時間) | 9 | 25 | 118 | 354 | 0 | 0 | Lõi của dự án: làm đầu tiên ở Sprint 1 |
| E10 (#11) — Nhiệt độ & HACCP | 8 | 17 | 113 | 339 | 0 | 0 | Gồm issue #102 (E10-I03) chỉnh tay 5 → 8 SWP sau khi skill chạy |
| E11 (#12) — Truy xuất & thu hồi | 7 | 6 | 62 | 186 | 0 | 0 |  |
| E12 (#13) — Trang chủ & báo cáo | 6 | 6 | 42 | 126 | 0 | 0 |  |
| E13 (#14) — Tích hợp, chuyển đổi dữ liệu & nghiệm thu | 11 | 14 | 118 | 354 | 0 | 0 | Gồm đào tạo #131 (E13-I11) và go-live #130 (E13-I10); #268 (E13-I02-S01) được nâng 10 → 16 SWP ở vòng hiệu chỉnh |
| **Tổng** | 118 | 148 | 1060 | 3180 | 22 | 66 | |

## So với estimate LAB-1

**Nguồn LAB-1.** Chỉ estimate v1 (2026-09-11, đơn vị người-ngày MD) có số effort. Bản v2 (2026-10-05) đổi phạm vi (feature 44 lên 57, function 69 lên 156, màn hình 33 lên 37) nhưng changelog của v2 ghi rõ chưa đồng bộ lại file estimate, nên không có MD của v2. Vì thế cột LAB-1 dùng v1; v2 chỉ xuất hiện trong cột "Vì sao" để giải thích phạm vi.

**Quy đổi.** File estimate v1 không ghi một MD bằng bao nhiêu giờ (số "20 ngày công / tháng" trong cấu hình chỉ để đổi đơn giá từ tháng sang ngày), nên bảng giả định 1 MD = 8 giờ ở cả hai phía và so sánh bằng giờ.

**Ánh xạ cùng phạm vi.** v1 chia theo domain D1–D8, LAB-5 chia theo 13 epic. Mỗi dòng v1 được xếp vào epic LAB-5 chứa việc đó (ví dụ task "kiểm tra giờ lái" vào #10 (E09), tích hợp mapping vào #9 (E08)). Phase 1 so với Phase 1; Phase 2 so với các mục Phase 2 của v1 (D1-P2, D2-P2, D4-P2, D5-P2, D7-P2).

| Phần | LAB-1 | LAB-5 | Chênh | Vì sao |
|---|---|---|---|---|
| Phase 1 · E01 (#1) — Nền tảng & hạ tầng | v1 (2026-09-11): 63 MD (504 giờ) | 417 giờ (52,13 MD) | −87 giờ (−17,3 %) | v1 tính hạ tầng theo từng dịch vụ AWS, nhiều dòng nhân 2,8 cho bốn môi trường (một vai Infra riêng); LAB-5 dựng bằng một bộ module Terraform dùng chung, chỉ khác theo biến môi trường (#15 (E01-I02)), nên không nhân lên theo môi trường. Đổi lại epic này có thêm khung ứng dụng, kho cấu hình, sổ audit, đường tải tệp và khung giao diện mà v1 không có dòng riêng (PR-02, PR-03, GD-02, GD-03). |
| Phase 1 · E02 (#3) — Đăng nhập, phân quyền, nhật ký | v1 (2026-09-11): 6 MD (48 giờ) | 141 giờ (17,63 MD) | +93 giờ (+193,8 %) | v1 chỉ có hai dòng (SSO qua OIDC và RBAC, mỗi dòng 3 MD). LAB-5 thêm đường cấp tài khoản lần đầu và luật "còn ít nhất một quản trị" (PR-04, PR-05) và việc làm rõ nhà cung cấp danh tính với khách (HC-01, #39 (E02-I06)). |
| Phase 1 · E03 (#4) — Dữ liệu gốc | v1 (2026-09-11): 7 MD (56 giờ) | 174 giờ (21,75 MD) | +118 giờ (+210,7 %) | v1 có hai dòng (từ điển dữ liệu, audit thay đổi). v2 (2026-10-05) thêm danh mục xe và tài xế; LAB-5 thêm đường ghi cho bảng hồ sơ điểm dừng và cột vị trí (PR-01, PR-10). |
| Phase 1 · E04 (#5) — Nhập kho | v1 (2026-09-11): 10 MD (80 giờ) | 150 giờ (18,75 MD) | +70 giờ (+87,5 %) | v1 có ba task (10 MD). LAB-5 thêm điểm vào luồng sửa dòng nhận (PR-07), viết hợp đồng API ngay trong issue (GD-01) và hai issue làm rõ với khách (#53 (E04-I06), #54 (E04-I07)). |
| Phase 1 · E05 (#6) — Tồn kho | v1 (2026-09-11): 17 MD (136 giờ) | 243 giờ (30,38 MD) | +107 giờ (+78,7 %) | v1 có năm task (17 MD). v2 (2026-10-05) thêm phiếu chuyển dải nhiệt tạm thời; LAB-5 thêm ô lọc và vùng hiển thị trên màn lô (PR-08, PR-09), giao diện đặt tên của kho cho module khác (PR-11), chốt nơi quyết định giải phóng lô (HC-06). |
| Phase 1 · E06 (#7) — Hạn dùng & phân bổ FEFO | v1 (2026-09-11): 12 MD (96 giờ) | 258 giờ (32,25 MD) | +162 giờ (+168,8 %) | v1 có ba task (12 MD, mỗi task 4 MD). v2 (2026-10-05) thêm tra cứu lịch sử giao, duyệt ngoại lệ phân bổ, màn cấu hình luật cửa sổ giao và luật 日付逆転 theo hạn lớn nhất đã được khách nhận; LAB-5 thêm bước nạp lịch sử giao khi di trú (PR-12) và quy ước loại hạn theo phiên bản (GD-07). |
| Phase 1 · E07 (#8) — Xuất kho & giao hàng | v1 (2026-09-11): 17 MD (136 giờ) | 240 giờ (30 MD) | +104 giờ (+76,5 %) | v1 có năm task (17 MD). LAB-5 chốt ứng dụng tài xế là PWA có hàng đợi offline mã hoá (HC-03), thêm giao diện của kho (PR-11) và v2 (2026-10-05) kiểm lại 日付逆転 lúc xuất xe. |
| Phase 1 · E08 (#9) — Lập tuyến giao hàng | v1 (2026-09-11): 9 MD (72 giờ) | 132 giờ (16,5 MD) | +60 giờ (+83,3 %) | v1 chỉ có tạo tuyến, publish tuyến và dịch vụ bản đồ (9 MD sau khi tách luật giờ lái). LAB-5 thêm khối cấp / thay seal trên màn tuyến (PR-13), số tuyến đã công bố (GD-09), màn tuyến của tài xế chạy trực tuyến (GD-10). |
| Phase 1 · E09 (#10) — Ràng buộc giờ lái xe tải 2024 (拘束時間) | v1 (2026-09-11): 4 MD (32 giờ) | 354 giờ (44,25 MD) | +322 giờ (+1006,3 %) | v1 chỉ có một task "kiểm tra giờ lái" (4 MD). v2 (2026-10-05) tách thành ba function (năm / tháng, ngày kèm nghỉ, lái trung bình) và LAB-4 viết riêng một bộ spec; LAB-5 thêm khối đủ 16 tham số của phiên bản luật với duyệt bốn mắt (PR-14), một hàm chèn điểm nghỉ dùng chung (GD-08), bằng chứng pháp lý (GD-11, GD-16) và bản tối thiểu của sự kiện bất ngờ trên tuyến (HC-07). |
| Phase 1 · E10 (#11) — Nhiệt độ & HACCP | v1 (2026-09-11): 15 MD (120 giờ) | 339 giờ (42,38 MD) | +219 giờ (+182,5 %) | v1 có hai khối (giám sát nhiệt 12 MD, tải file logger 3 MD). v2 (2026-10-05) thêm tự tạm giữ lô khi có cảnh báo và đo lại trong 15 phút; LAB-5 thêm phiên bản ngưỡng nhiệt chỉ tạo bản mới (GD-13, HC-05) và việc làm rõ hạn nhắc ca thiếu file (#107 (E10-I08)). |
| Phase 1 · E11 (#12) — Truy xuất & thu hồi | v1 (2026-09-11): 8 MD (64 giờ) | 186 giờ (23,25 MD) | +122 giờ (+190,6 %) | v1 có hai task (8 MD). v2 (2026-10-05) thêm mã bò 10 chữ số, truy xuất ba năm trong 60 giây, đối chiếu số lượng thu hồi và điểm dừng chuỗi. Phần thông báo thu hồi (FR-TRC-03) được hoãn, không có issue (PR-15). |
| Phase 1 · E12 (#13) — Trang chủ & báo cáo | v1 (2026-09-11): 9 MD (72 giờ) | 126 giờ (15,75 MD) | +54 giờ (+75 %) | v1 có cổng báo cáo, xuất bằng chứng audit và trang chủ (9 MD). LAB-5 thêm một màn xem báo cáo dùng chung vì màn đó không có wireframe (PR-06) và tự định nghĩa mười hai báo cáo (GD-05, GD-06). |
| Phase 1 · E13 (#14) — Tích hợp, chuyển đổi dữ liệu & nghiệm thu | v1 (2026-09-11): 29 MD (232 giờ) | 354 giờ (44,25 MD) | +122 giờ (+52,6 %) | v1 có ERP, mail, archive, di trú, đào tạo và bốn task kiểm thử (29 MD). LAB-5 thêm bước nạp lịch sử giao khi di trú (PR-12), kênh outbox kèm bộ phân tích theo phiên bản schema (HC-04), tám mã nghiệm thu thành issue (GD-14), bộ dữ liệu đo hiệu năng (GD-15) và đào tạo tiếng Nhật đủ bảy nhóm vai trò (#131 (E13-I11)). |
| **Phase 1 · cộng 13 epic** | v1 (2026-09-11): 206 MD (1648 giờ) | 3114 giờ (389,25 MD) | +1466 giờ (+89 %) | Tổng của 13 dòng trên. Nguyên nhân chung: v1 là estimate thầu ở mức user story, làm ngày 2026-09-11 khi LAB-4 chưa có; LAB-5 chấm lại từng issue theo spec chi tiết của LAB-4. Phạm vi cũng đã nở: ở v2 (2026-10-05) feature từ 44 lên 57, function từ 69 lên 156, màn hình từ 33 lên 37 (v1: 2026-09-10). Chiều ngược lại, v1 đã gồm 20 % đệm trong từng task còn LAB-5 không có đệm. |
| Phase 2 · D1-P2 — Lịch hẹn nhập hàng (SCR003) | v1 (2026-09-11): 3 MD (24 giờ) | 30 giờ (3,75 MD) | +6 giờ (+25 %) | Cùng phạm vi: lịch hẹn nhập hàng. LAB-5 có wireframe SCR003 nên làm được hai sub-issue BE / FE (#162 (E04-I08-S01), #163 (E04-I08-S02)). |
| Phase 2 · D2-P2 — Sức chứa, gợi ý vị trí (SCR005) | v1 (2026-09-11): 6 MD (48 giờ) | 36 giờ (4,5 MD) | −12 giờ (−25 %) | Cùng phạm vi: sức chứa vị trí và gợi ý vị trí. LAB-5 có wireframe SCR005 nên làm được hai sub-issue (#176 (E05-I09-S01), #177 (E05-I09-S02)), kèm migration thêm cột sức chứa (PR-10). |
| Phase 2 · D4-P2 — Gộp nhóm picking, trả hàng / khiếu nại | v1 (2026-09-11): 6 MD (48 giờ) | 0 giờ (0 MD) | −48 giờ (−100 %) | Không có wireframe trong LAB-4 cho gộp nhóm picking và trả hàng (FR-OUT-03, FR-OUT-07) nên không thành issue; lý do ghi ở task-breakdown.md. |
| Phase 2 · D5-P2 — Sự cố, hỏng xe trên tuyến | v1 (2026-09-11): 3 MD (24 giờ) | 0 giờ (0 MD) | −24 giờ (−100 %) | v1 xếp sự cố trên tuyến ở Phase 2, nhưng v2 (2026-10-05) kéo bản tối thiểu của FR-SCH-05 lên Phase 1 (HC-07), nên phần này đã nằm trong #10 (E09) (Phase 1), không còn dòng Phase 2. |
| Phase 2 · D7-P2 — Thông báo thu hồi | v1 (2026-09-11): 3 MD (24 giờ) | 0 giờ (0 MD) | −24 giờ (−100 %) | Thông báo thu hồi (FR-TRC-03) được hoãn tới khi khách duyệt giai đoạn 2, không dựng bảng hay issue nào (PR-15). |
| **Phase 2 · cộng 5 mục** | v1 (2026-09-11): 21 MD (168 giờ) | 66 giờ (8,25 MD) | −102 giờ (−60,7 %) | Bốn issue `phase-2` (#162, #163, #176, #177) có giờ nhưng không xếp sprint. |
| **Trong backlog · Phase 1 + Phase 2** | v1 (2026-09-11): 227 MD (1816 giờ) | 3180 giờ (397,5 MD) | +1364 giờ (+75,1 %) | So cùng phạm vi: 227 MD của v1 là phần có epic tương ứng trong LAB-5. |
| Ngoài backlog · quản lý dự án và BrSE | v1 (2026-09-11): 34 MD (272 giờ) | không ước lượng (0 giờ) | −272 giờ | v1 tính PM bằng 10 % và BrSE bằng 9 % tổng MD làm việc (task M.1, M.2), tức chi phí quản lý chứ không phải phần giao được. Chấm Scope × Complexity cho nó không có nghĩa và làm nhiễu tổng SWP, nên LAB-5 để ngoài backlog. Việc làm rõ với khách (việc của BrSE) vẫn có issue riêng nhãn `type:clarification` trong từng epic nghiệp vụ và đã nằm trong các tổng ở trên. |
| Ngoài backlog · UI/UX | v1 (2026-09-11): 10 MD (80 giờ) | không ước lượng (0 giờ) | −80 giờ | v1 tính design system và wireframe 31 màn. Wireframe đã làm xong ở LAB-4, nên backlog LAB-5 chỉ có việc dựng màn theo wireframe, không có issue thiết kế. |
| **Tổng v1 (cả phần ngoài backlog)** | v1 (2026-09-11): 271 MD (2168 giờ) | 3180 giờ (397,5 MD) | +1012 giờ (+46,7 %) | Dòng này chỉ để đối chiếu với con số 271 MD thường được trích từ v1; so sánh có nghĩa là dòng "Trong backlog" phía trên. |

**v1 tự lệch trong cách chia dev / non-dev.** Bảng "With vs Without AI" của v1 ghi dev 176 MD, non-dev 95 MD (chia theo task: phần hạ tầng và quản lý là non-dev). Bảng "Summary by Category" của chính file đó chia theo vai: FE + BE + QA + Design = 173 MD, PM + BrSE + Infra = 98 MD. Cả hai cộng ra 271 MD nhưng cho hai cặp số khác nhau (176 / 95 so với 173 + 98) vì hai cách phân loại khác nhau; file không giải thích. LAB-5 không dùng cách chia nào trong hai cách đó.

**Cách đọc kết quả.** Cùng phạm vi, LAB-5 lớn hơn v1 1,89 lần ở Phase 1 (riêng nền tảng nhỏ hơn). Đây không phải một sai số mà là hai độ chi tiết khác nhau: v1 là ước lượng thầu ở mức user story, còn LAB-5 là tổng của 205 lá chấm theo spec đã đóng băng. Vì vậy v1 nên đọc như một ước lượng sơ bộ ở mức thầu; con số theo backlog là 3180 giờ, chưa tính quản lý dự án và thiết kế màn.
