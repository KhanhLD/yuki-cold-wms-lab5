# Yuki Cold WMS — backlog LAB-5

Mã học viên: `KhanhLD` · Đề: Product B (hệ thống quản lý kho lạnh và giao hàng cho thực phẩm tươi sống).

Repo này là bài LAB-5: biến bộ thiết kế LAB-4 thành backlog trên GitHub Project, chấm điểm công việc bằng SWP, quy ra giờ và giả lập một sprint để lập báo cáo tiến độ. Sáu file markdown ở gốc repo là phần nộp; mỗi file trả lời một câu hỏi riêng (xem §4).

## 1. Tổng quan

- **Bản LAB-4 làm đầu vào:** bộ thiết kế LAB-4 đã nộp ngày 2026-10-14. Bộ này được giữ đóng băng trong suốt LAB-5: mọi chỗ backlog đi lệch khỏi thiết kế được ghi ở PREMISE.md, không sửa ngược vào LAB-4.
- **PREMISE có thay đổi:** có. Khi chia việc lộ ra chỗ thiết kế thiếu đường ghi, thiếu màn hoặc hai tài liệu nói hai cách, nên PREMISE.md ghi 15 dòng thêm / bỏ / sửa spec, 16 giả định bổ sung và 7 hướng đã chốt. Không có bản spec viết lại nào trong repo.
- Số epic: 13
- **Quy mô backlog:** 13 epic, 118 issue, 148 sub-issue (279 issue GitHub, 205 item lá được chấm điểm). Tổng 1060 SWP, 3180 giờ (bảng theo epic ở estimate.md).
- **Sprint giả lập đã chạy:** Sprint 1 (2026-09-28 → 2026-10-09), sprint dài 2 tuần, đội giả lập theo vai (BrSE, BE-1, BE-2, FE-1, QA-1, PM).
- Project hiện trạng thái cuối Sprint 1 (2026-10-09); ngày đóng issue mà GitHub ghi là ngày ghi kết quả giả lập lên Project, không phải ngày trong sprint, và Sprint 2 chưa được giả lập.
- Ngày của ba Iteration trên Project đúng như lịch giả lập, không phải dời ngày nào.

## 2. Link

- Repo: https://github.com/KhanhLD/yuki-cold-wms-lab5
- GitHub Project: https://github.com/users/KhanhLD/projects/1
- Báo cáo HTML do skill chấm SWP xuất ra, đường dẫn trong repo: [`docs/estimate-swp-khanhld-project-1-20261015-1710.html`](docs/estimate-swp-khanhld-project-1-20261015-1710.html)
- Cùng báo cáo đó mở bằng trình duyệt qua GitHub Pages: https://khanhld.github.io/yuki-cold-wms-lab5/estimate-swp-khanhld-project-1-20261015-1710.html (mở file `.html` trên github.com chỉ thấy mã nguồn, nên báo cáo được đưa lên Pages).
- Đã mở thử bằng cửa sổ ẩn danh (chưa đăng nhập GitHub) ngày 2026-10-16: repo, Project (cả 3 view) và báo cáo HTML qua Pages đều xem được; 6 file markdown và biểu đồ burndown hiển thị đúng trên github.com. Các view của Project chỉ hiện việc được chấm điểm trực tiếp (bộ lọc `no:sub-issues-progress`) để tổng SWP không cộng trùng epic và issue cha.

## 3. Cấu trúc repo

Mọi mục ở gốc repo:

| File / thư mục | Nội dung |
|---|---|
| `README.md` | File này: tổng quan, link, cấu trúc repo, bảng spec → epic |
| `PREMISE.md` | Backlog lệch khỏi thiết kế LAB-4 ở đâu: spec thêm / bỏ / sửa, giả định bổ sung, hướng đã chốt — mỗi dòng có lý do và issue bị ảnh hưởng |
| `task-breakdown.md` | Nguyên tắc chia epic → issue → sub-issue, bảng phủ đủ danh mục thiết kế LAB-4 sang issue, các ràng buộc đặc thù được tách nhãn riêng, và bảng chỗ hở / chỗ vênh của thiết kế cùng cách xử lý |
| `estimate.md` | Kết quả chấm SWP: issue mẫu đã kiểm và điểm chốt, cách quy ra giờ, tổng theo epic, so với estimate LAB-1 |
| `progress-report.md` | Báo cáo Sprint 1: trạng thái, burndown, rủi ro trễ và hành động |
| `AI-report.md` | Đã dùng AI thế nào: skill và ý đồ prompt cho từng việc, ai review ở bước nào, chỗ hiệu quả và chỗ chưa tốt |
| `docs/` | Báo cáo HTML mà skill chấm SWP xuất ra (giữ nguyên từng byte) và file `.nojekyll` để GitHub Pages phục vụ thư mục này |
| `backlog/` | 13 file markdown, mỗi epic một file (tên file bắt đầu bằng mã epic dùng trong backlog): nguồn backlog-as-code mà script đã kiểm rồi đăng thành issue. File nguồn còn mục "Chỗ hở" của từng item — phần này không đăng vào thân issue; bảng chỗ hở đầy đủ ở task-breakdown.md §4 |
| `.gitignore` | Chặn tệp tạm, ảnh, tệp môi trường khỏi repo |

Repo không có thư mục `spec-changes/` (không dòng PREMISE nào cần bản spec viết lại; phần "Trước → sau" trong PREMISE.md và định nghĩa xong của issue đã mô tả đủ) và không có thư mục `skills/` (skill chấm SWP được dùng nguyên bản, không phải sửa).

## 4. Spec → epic

Cột Spec liệt kê các đơn vị chính của thiết kế LAB-4 mà epic phủ (màn, miền API, thiết kế xử lý, thiết kế dữ liệu, quyết định kiến trúc, nhóm yêu cầu, mã ràng buộc Nhật) và các dòng PREMISE chạm tới epic. Các nhóm dùng chung nhiều epic (luật nghiệp vụ BR, yêu cầu dữ liệu DR, yêu cầu phi chức năng NFR) không lặp lại ở đây; bảng đầy đủ từng đơn vị → issue ở task-breakdown.md §2.

| Spec (LAB-4 / dòng PREMISE) | Epic trên Project | Ghi chú |
|---|---|---|
| SCR033; API-ACS, API-SYS; PLATFORM_DB; ADR-001, ADR-002, ADR-003, ADR-005, ADR-008, ADR-009, ADR-012; nhóm OPS; JP-05, JP-18 · PREMISE PR-02, PR-03, GD-02, GD-03, GD-04, GD-11, GD-15, HC-02 | #1 — Nền tảng & hạ tầng | Nền cho mọi epic khác: cấu hình có duyệt, job lưu giữ dữ liệu gọi qua cổng của từng module, pipeline triển khai. Là phần chính của Sprint 1 |
| SCR001, SCR030; API-ACS; PLATFORM_DB; ADR-003, ADR-006; nhóm IF · PREMISE PR-04, PR-05, HC-01 | #3 — Đăng nhập, phân quyền, nhật ký | Thiếu đường cấp tài khoản lần đầu nên thêm công cụ vận hành; nhà cung cấp định danh còn chờ khách chọn |
| SCR026, SCR027, SCR028, SCR029, SCR036; API-MST; MASTER_DB, TEMPERATURE_DB; ADR-004 · PREMISE PR-01, PR-10 | #4 — Dữ liệu gốc | Thêm đường ghi cửa sổ giờ nhận và thời gian dừng theo khách, vì lập tuyến cần mà LAB-4 không có |
| SCR003, SCR004; API-MST, API-SYS, API-INB, API-INV; INB_RECEIVING_EXPIRY_CHECK; INVENTORY_DB; ADR-003; nhóm FR-REC; JP-01, JP-02, JP-03, JP-06, JP-07, JP-15, JP-19 · PREMISE PR-07, GD-01 | #5 — Nhập kho | Ghi hạn dùng và mã truy xuất gạo / bò lúc nhận; SCR003 thuộc giai đoạn 2 (nhãn `phase-2`, không xếp sprint) |
| SCR005, SCR006, SCR007, SCR008, SCR034; API-INV; INVENTORY_DB, MASTER_DB; ADR-003; nhóm FR-INV; JP-08 · PREMISE PR-08, PR-09, PR-10, PR-11, HC-06 | #6 — Tồn kho | Thêm giao diện đặt tên của kho cho module khác gọi (trừ tồn, chặn lô); SCR005 thuộc giai đoạn 2 |
| SCR009, SCR010, SCR011, SCR035, SCR037; API-MST, API-ALC; ALC_DATE_REVERSAL_CHECK, ALC_FEFO_ALLOCATION; ALLOCATION_DB; ADR-004; nhóm FR-OUT; JP-06 … JP-10, JP-16 · PREMISE PR-11, PR-12, GD-01, GD-04, GD-07 | #7 — Hạn dùng & phân bổ FEFO | Ràng buộc FEFO, hạn dùng và 日付逆転; chưa nạp xong lịch sử giao từ di trú thì chưa bật phân bổ thật |
| SCR012, SCR013, SCR014, SCR015; API-MST, API-SYS, API-ALC, API-OUT; OUT_DEPARTURE_GATE; SHIPPING_DB; ADR-007; nhóm FR-OUT; JP-01, JP-02, JP-03, JP-05, JP-06, JP-10, JP-15, JP-18 · PREMISE PR-11, GD-01, GD-04, HC-03 | #8 — Xuất kho & giao hàng | Cổng xuất phát kiểm nhiệt xe, seal, giờ và 日付逆転 trước khi xe rời kho; mang mã truy xuất gạo / bò theo hàng |
| SCR016, SCR018; API-RTE; TRANSPORT_DB; ADR-008; nhóm FR-SCH, nhóm IF; JP-18 · PREMISE PR-01, PR-13, GD-01, GD-08, GD-09, GD-10 | #9 — Lập tuyến giao hàng | Thêm khối cấp / thay seal trên SCR018; lập tuyến dùng bộ kiểm giờ lái của #10, không chứa luật giờ lái |
| SCR002, SCR016, SCR017, SCR018, SCR024, SCR033, SCR036; API-HOM, API-MST, API-SYS, API-RTE; OUT_DEPARTURE_GATE, RTE_DRIVER_DUTY_CHECK; TRANSPORT_DB; ADR-005, ADR-008, ADR-010, ADR-012; nhóm FR-SCH, FR-RPT, OPS; JP-11 … JP-14, JP-18 · PREMISE PR-14, GD-01, GD-06, GD-08, GD-11, GD-12, GD-16, HC-07 | #10 — Ràng buộc giờ lái xe tải 2024 (拘束時間) | Epic riêng cho luật giờ lái 2024, kể cả phần nằm trên màn của epic khác, để luật có đúng một chủ. Trần giờ chưa đối chiếu văn bản chính thức (PREMISE GD-16); làm trước các ràng buộc khác |
| SCR019, SCR020, SCR021, SCR033; API-MST, API-SYS, API-TMP; TMP_LOGGER_INGEST_DEVIATION, TMP_LOT_RELEASE_APPROVAL; TEMPERATURE_DB; ADR-008, ADR-011; nhóm FR-TEMP, nhóm IF; JP-01 … JP-05, JP-17 · PREMISE PR-11, GD-13, HC-05, HC-06 | #11 — Nhiệt độ & HACCP | Chuỗi đo → cảnh báo → tạm giữ → quyết định lô; ngưỡng là dữ liệu có phiên bản. Các điểm chạm ở epic khác mang nhãn `constraint:haccp-temp` |
| SCR022, SCR023; API-TRC; TRC_RECALL_CASE_LIFECYCLE, TRC_TRACEABILITY_SEARCH; TRACE_RECALL_DB; nhóm FR-TRC; JP-15 · PREMISE PR-11, PR-15, GD-15 | #12 — Truy xuất & thu hồi | Truy xuất gạo / bò và vòng đời case thu hồi; dấu chặn lô đặt qua giao diện của kho (#6). Phần thông báo thu hồi (FR-TRC-03) hoãn sang giai đoạn 2 nên không thành issue |
| SCR002, SCR024, SCR025; API-ACS, API-HOM, API-RPT; PLATFORM_DB; nhóm FR-RPT, nhóm IF · PREMISE PR-06, GD-05, GD-06 | #13 — Trang chủ & báo cáo | Thêm một màn xem báo cáo dùng chung vì LAB-4 không có wireframe cho màn này |
| SCR031, SCR032; API-SYS; TRC_TRACEABILITY_SEARCH; PLATFORM_DB, TRACE_RECALL_DB; ADR-008, ADR-012; nhóm IF, OPS, ACC; JP-15 · PREMISE PR-12, GD-01, GD-14, GD-15, HC-04 | #14 — Tích hợp, chuyển đổi dữ liệu & nghiệm thu | Tích hợp ERP, chuyển đổi dữ liệu cũ (có bước nạp lịch sử giao), nghiệm thu và đào tạo; các câu hỏi khách lớn nằm ở đây |
