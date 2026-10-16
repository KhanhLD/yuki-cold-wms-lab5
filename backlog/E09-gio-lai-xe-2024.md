# E09 · Ràng buộc giờ lái xe tải 2024 (拘束時間)
<!-- lab5-epic: E09 -->

## E09 · Ràng buộc giờ lái xe tải 2024 (拘束時間)
- Loại: epic
- Nhãn: type:epic, constraint:jp-2024-driver-hours
- Spec: JP-11, JP-12, JP-13, JP-14, JP-18, ADR-010, RTE_DRIVER_DUTY_CHECK, TRANSPORT_DB, SCR017, FR-SCH-02, FR-SCH-03, FR-SCH-05, OPS-LAW-01
### Mô tả
Toàn bộ phần hệ thống hiện thực quy định giờ làm của tài xế xe tải theo bản sửa năm 2024 của 改善基準告示, đúng như thiết kế LAB-4 đã đóng băng. Luật được giữ như dữ liệu có phiên bản và ngày hiệu lực (không viết cứng trong mã); mỗi tuyến được bộ kiểm chấm theo tám ràng buộc riêng (làm thêm năm, 拘束時間 năm, tháng, ngày, nghỉ giữa hai ca, lái bình quân hai ngày, hai tuần, lái liên tục) cộng khung giờ dịch vụ, mỗi ràng buộc một dòng kết quả; tuyến còn dòng chặn thì không công bố được, và tuyến đã công bố giữ phiên bản luật lúc công bố, không áp hồi tố. Vượt trần ngày của kế hoạch thường chỉ được qua yêu cầu kéo dài khẩn cấp có lý do và bằng chứng, người duyệt khác người xin. Sự kiện bất ngờ có bằng chứng được ghi bất biến và báo cáo tách khỏi lịch thường. Epic gồm cả phần ngoại vi cắt từ màn khác: ô trong SCR033, khối ghi giờ làm trong SCR036, ô chờ duyệt ở SCR002, liên kết ở SCR016 và SCR018.

Rủi ro lớn nhất của epic là pháp lý, không phải kỹ thuật: các trần 拘束時間, JP-13 và JP-14 trong LAB-4 lấy từ bản tóm tắt của RFP và cách hiểu của nhà thầu, chưa đối chiếu với văn bản chính thức. Vì vậy mọi giá trị phải là dữ liệu của phiên bản luật, phiên bản dùng ở môi trường thật chỉ được duyệt khi có hồ sơ bằng chứng, và E09-I09 gom việc làm rõ và hồ sơ bằng chứng. Backlog này không đặt giá trị pháp lý nào ngoài các giá trị LAB-4 đã ghi.
### Chỗ hở
- GAP-61 các trần 拘束時間, JP-13, JP-14 chưa đối chiếu với 改善基準告示; mốc kiểm lại trước khi đóng băng thiết kế đã trôi qua
- GAP-62 hai điểm của bản 2024 mà RFP không tóm (giới hạn số ngày dài, nâng trần nhờ thoả thuận lao động) chưa có tham số
- GAP-63 các cách hiểu và định nghĩa của JP-12, JP-13, JP-14 còn mở (làm thêm, cắt ngày 24 giờ, bình quân hai tuần, mức nghỉ, kỳ năm)
- GAP-64 nguồn giờ làm thực tế của tài xế chưa chốt
- GAP-66 chặn công bố theo khung giờ dịch vụ là cách hiểu nhà thầu; biên khung chưa rõ; khung nằm ở hai nơi
- GAP-67 luồng kéo dài khẩn cấp và vai xin, duyệt chưa khách xác nhận
- GAP-68 D-12 chưa chốt làm ở Phase 1 và cách đánh dấu sự kiện chưa trả lời
- GAP-70 chưa có mục đích tải tệp cho bằng chứng của phiên bản luật

## E09-I01 · Phiên bản luật tài xế: dữ liệu, tra cứu hiệu lực, đề xuất, duyệt và bằng chứng
- Loại: issue
- Cha: E09
- Nhãn: type:issue, constraint:jp-2024-driver-hours
- Spec: ADR-010, TRANSPORT_DB, OPS-LAW-01
### Mô tả
Nền của cả epic: bộ tham số luật tài xế là bản ghi có kiểu, có ngày hiệu lực, có vòng đời nháp, đã duyệt, bị thay thế, đổi luật là tạo phiên bản mới và không sửa phiên bản đã duyệt. Phiên bản chỉ được duyệt ở môi trường thật khi có bằng chứng đã đối chiếu văn bản chính thức; người đề xuất khác người duyệt. Chia ba phần: bảng và tra cứu (BE), ba API đề xuất, duyệt, xem (BE), phần quy tắc giờ lái của màn SCR033 (FE). SCR033 và ba API đều chưa có hợp đồng hay spec.
### Chỗ hở
- GAP-61 các trần 拘束時間, JP-13, JP-14 chưa đối chiếu với 改善基準告示; mốc kiểm lại trước khi đóng băng thiết kế đã trôi qua
- GAP-69 wireframe SCR033 chưa vẽ đủ tham số, bước duyệt và bằng chứng
- GAP-70 chưa có mục đích tải tệp cho bằng chứng của phiên bản luật
- GAP-71 thời điểm chuyển trạng thái bị thay thế chưa quy định
- GAP-66 chặn công bố theo khung giờ dịch vụ là cách hiểu nhà thầu; biên khung chưa rõ; khung nằm ở hai nơi

## E09-I01-S01 · Bảng phiên bản luật tài xế và tra cứu phiên bản hiệu lực (BE)
- Loại: sub-issue
- Cha: E09-I01
- Vai: BE-1
- Nhãn: type:sub-issue, constraint:jp-2024-driver-hours
- Spec: ADR-010, TRANSPORT_DB, FR-SCH-02, OPS-LAW-01
- Phụ thuộc: E01-I14
### Mô tả
Dựng bảng phiên bản luật với các tham số có kiểu thay cho JSON tự do (để tham số gõ sai hoặc sai đơn vị bị bắt lúc lưu, không bị bộ kiểm lặng lẽ bỏ qua), vòng đời một chiều, ràng buộc bốn mắt và hàm tìm phiên bản có hiệu lực. Phiên bản khởi tạo nguồn "RFP tóm tắt" nạp đúng giá trị JP-11 đến JP-14, JP-18 của LAB-4 cho môi trường dev, test, UAT với nhãn cần xác thực; không có giá trị nào khác được đặt.
### Định nghĩa xong
- [ ] Migration tạo bảng với đủ tham số có kiểu (trần ngày thường và kéo dài, trần tháng, trần năm, nghỉ mục tiêu và tối thiểu, giờ lái bình quân hai ngày và hai tuần, lái liên tục tối đa, hai tham số nghỉ của JP-13, trần làm thêm năm, giờ làm chuẩn mỗi ca, ngày bắt đầu năm, khung giờ dịch vụ) và các ràng buộc ck của thiết kế; mỗi ràng buộc có test dữ liệu sai bị từ chối (giá trị không dương, trần kéo dài nhỏ hơn trần thường, nghỉ tối thiểu lớn hơn mục tiêu, khung giờ đảo)
- [ ] Trạng thái chỉ đi một chiều nháp, đã duyệt, bị thay thế; bản không còn là nháp không sửa được cột tham số (trigger), có test
- [ ] Mỗi ngày chỉ có một bản đã duyệt (ràng buộc duy nhất một phần bỏ bản nháp); hàm tra cứu trả bản đã duyệt có ngày hiệu lực lớn nhất không muộn hơn ngày nghiệp vụ, không có thì trả kết quả "không có" để bộ kiểm trả mã YCL-RTE-054
- [ ] Bản đã duyệt có nguồn khác "RFP tóm tắt" bắt buộc có tham chiếu bằng chứng; ngày hiệu lực chỉ đặt từ ngày nghiệp vụ hiện tại trở đi (kiểm ở service), không hồi tố
- [ ] Người duyệt khác người đề xuất, kiểm cả ở service lẫn ràng buộc cơ sở dữ liệu; có test
- [ ] Hai mốc nghiệp vụ và mốc ghi theo đồng hồ nghiệp vụ, không lùi trong cùng bản ghi; có test luật không lùi
- [ ] Phiên bản mẫu nguồn "RFP tóm tắt" được nạp bằng script dữ liệu mẫu chỉ chạy ở dev, test, UAT, kèm test xác nhận khớp từng giá trị với danh mục JP của LAB-4
### Chỗ hở
- GAP-71 thời điểm chuyển trạng thái bị thay thế chưa quy định
- GAP-66 chặn công bố theo khung giờ dịch vụ là cách hiểu nhà thầu; biên khung chưa rõ; khung nằm ở hai nơi
- GAP-72 thời hạn lưu phiên bản luật và ước tính di chuyển chưa chốt (nội bộ)

## E09-I01-S02 · API xem, đề xuất và duyệt phiên bản luật (BE)
- Loại: sub-issue
- Cha: E09-I01
- Vai: BE-2
- Nhãn: type:sub-issue, constraint:jp-2024-driver-hours
- Spec: API-SYS-08, API-SYS-09, API-SYS-12, ADR-010, OPS-LAW-01
- Phụ thuộc: E09-I01-S01, E01-I16, E02-I03
### Mô tả
Ba API của SCR033 còn trống hợp đồng: danh sách phiên bản, đề xuất phiên bản mới (bản nháp có ngày hiệu lực) và quyết định duyệt hoặc từ chối. Bộ kiểm tuyến đọc phiên bản trong chính lệnh kiểm nên API danh sách không phải đường duy nhất.
### Định nghĩa xong
- [ ] Tài liệu hợp đồng ba API (tham số, phản hồi, mã lỗi dùng lại bảng lỗi chung) viết xong, đối chiếu với dòng api-list và vai trò quy định
- [ ] Đề xuất do trưởng phòng vận tải hoặc quản trị; duyệt chỉ trưởng phòng vận tải, và phải khác người đề xuất (kiểm ở lệnh duyệt, không chỉ ở giao diện)
- [ ] Duyệt phiên bản không phải nguồn "RFP tóm tắt" mà thiếu bằng chứng bị từ chối; ở môi trường thật chỉ duyệt được phiên bản có tham chiếu bằng chứng đã đối chiếu, phiên bản nguồn "RFP tóm tắt" chỉ duyệt ở dev, test, UAT
- [ ] Ngày hiệu lực trong quá khứ bị từ chối; từ chối bắt buộc lý do
- [ ] Duyệt ghi sự kiện audit; hai người duyệt cùng lúc chỉ một lệnh thành công (mẫu quyết một lần)
- [ ] Tuyến đã công bố giữ nguyên phiên bản luật khi có phiên bản mới được duyệt (test đọc lại tuyến cũ)
- [ ] Test phân quyền: vai điều phối, vai khác bị từ chối ở cả ba API
### Chỗ hở
- GAP-70 chưa có mục đích tải tệp cho bằng chứng của phiên bản luật

## E09-I01-S03 · Màn SCR033: phần quy tắc giờ lái, tạo và duyệt phiên bản (FE)
- Loại: sub-issue
- Cha: E09-I01
- Vai: FE-1
- Nhãn: type:sub-issue, constraint:jp-2024-driver-hours
- Spec: SCR033, FR-SCH-02, OPS-LAW-01
- Phụ thuộc: E09-I01-S02, E10-I01-S02, E01-I15-S02, E01-I18
### Mô tả
Phần quy tắc giờ lái của SCR033 (khối ngưỡng nhiệt của cùng màn thuộc E10, khung trang do E10-I01-S02 dựng, panel làm theo mẫu panel độc lập của E01-I15-S02). Wireframe mới vẽ danh sách với hai dòng trần tháng và năm cùng biểu mẫu tạo phiên bản mới; bước duyệt, đính kèm bằng chứng và các tham số còn lại chưa vẽ, nên issue này gồm cả việc bổ sung wireframe và xin duyệt thay đổi nhãn.
### Định nghĩa xong
- [ ] Bảng phiên bản hiện trạng thái (nháp, đã duyệt, bị thay thế), ngày hiệu lực, nguồn và cờ cần xác thực
- [ ] Hiện đủ nhóm tham số của phiên bản được chọn, không chỉ hai dòng trần tháng và năm của wireframe; wireframe bổ sung được chủ dự án duyệt
- [ ] Biểu mẫu tạo phiên bản có ngày hiệu lực không chọn được quá khứ, báo lỗi tại trường theo ràng buộc của server
- [ ] Nút duyệt và từ chối chỉ hiện cho trưởng phòng vận tải khác người đề xuất; từ chối yêu cầu lý do
- [ ] Đính kèm bằng chứng chỉ qua API xin URL ký sẵn của E01-I18 (không dựng đường tải riêng), dùng mục đích tải "bằng chứng tuyến" sẵn có, không thêm mục đích mới (xem GAP-70 ở bảng chỗ hở của bản phân rã); thiếu bằng chứng thì nút duyệt phiên bản không phải "RFP tóm tắt" bị khoá kèm giải thích
- [ ] Dòng ghi chú "kết quả đã ghi không được tính lại" hiện đúng chỗ
- [ ] Loading, lỗi mạng, lỗi quyền hiện đúng; nhãn tiếng Nhật theo wireframe đã duyệt
### Chỗ hở
- GAP-69 wireframe SCR033 chưa vẽ đủ tham số, bước duyệt và bằng chứng
- GAP-70 chưa có mục đích tải tệp cho bằng chứng của phiên bản luật

## E09-I02 · Giờ làm thực tế của tài xế làm đầu vào bộ kiểm
- Loại: issue
- Cha: E09
- Nhãn: type:issue, constraint:jp-2024-driver-hours
- Spec: TRANSPORT_DB, API-MST-22, SCR036, FR-SCH-02
### Mô tả
Trần tháng và năm của bộ kiểm cộng dồn giờ làm thật nên cần một nguồn lưu giờ làm của từng tài xế theo ngày, gồm cả dòng ngày nghỉ để cửa sổ 14 ngày của giờ lái bình quân đủ dữ liệu. Thiết kế đích có bảng giờ làm và API ghi cơ sở giờ làm ở màn xe và tài xế (SCR036, do E03 dựng khung). Nguồn dữ liệu thật (điều phối nhập tay, hệ thống chấm công của khách hay suy từ tuyến hoàn tất) chưa chốt, nên kết quả trần tháng và năm chỉ đúng khi nguồn này chốt xong.
### Chỗ hở
- GAP-64 nguồn giờ làm thực tế của tài xế chưa chốt
- GAP-65 vai được ghi giờ làm mâu thuẫn giữa danh mục API và màn SCR036

## E09-I02-S01 · Bảng giờ làm thực tế và API ghi cơ sở giờ làm (BE)
- Loại: sub-issue
- Cha: E09-I02
- Vai: BE-1
- Nhãn: type:sub-issue, constraint:jp-2024-driver-hours
- Spec: TRANSPORT_DB, API-MST-22, FR-SCH-02
- Phụ thuộc: E03-I01
### Mô tả
Dựng bảng giờ làm (một dòng mỗi tài xế mỗi ngày: ca làm hoặc ngày nghỉ) và chi tiết hoá rồi hiện thực API-MST-22 (ghi cơ sở giờ làm của tài xế), chưa có hợp đồng. Chỉ lưu định danh vận hành của tài xế, không họ tên hay số điện thoại. Danh sách giá trị cột nguồn nhập chưa chốt nên không đặt ràng buộc cứng.
### Định nghĩa xong
- [ ] Migration bảng giờ làm đúng thiết kế TRANSPORT_DB: duy nhất theo tài xế và ngày, loại ca làm hoặc ngày nghỉ, giờ bắt đầu và kết thúc rỗng khi nghỉ, số phút lái không âm, đoạn trong ca dạng mảng
- [ ] Tài liệu hợp đồng API-MST-22 viết xong: trường, vai được ghi (tạm chỉ quản trị ghi như danh mục API, cho tới khi khách trả lời ở gói làm rõ E09-I09; xem GAP-65), mã lỗi, khoá lạc quan khi sửa
- [ ] Ghi lại cùng tài xế cùng ngày cập nhật dòng cũ chứ không tạo dòng thứ hai; hai người sửa cùng lúc, một người nhận lỗi xung đột
- [ ] Ghi được dòng ngày nghỉ; test chứng minh cửa sổ 14 ngày đọc đủ dòng nghỉ lẫn dòng làm
- [ ] Bảng chỉ giữ định danh vận hành, không có cột cá nhân; không khoá ngoại sang bảng một năm của module master
- [ ] Cột nguồn nhập không có ràng buộc giá trị cứng cho tới khi khách trả lời nguồn giờ làm ở gói làm rõ E09-I09 (GAP-64); không có giá trị mẫu lọt vào môi trường thật
### Chỗ hở
- GAP-64 nguồn giờ làm thực tế của tài xế chưa chốt
- GAP-65 vai được ghi giờ làm mâu thuẫn giữa danh mục API và màn SCR036

## E09-I02-S02 · Khối ghi dữ liệu giờ làm trên màn xe và tài xế SCR036 (FE)
- Loại: sub-issue
- Cha: E09-I02
- Vai: FE-1
- Nhãn: type:sub-issue, constraint:jp-2024-driver-hours
- Spec: SCR036, API-MST-22, FR-SCH-02
- Phụ thuộc: E09-I02-S01, E03-I07-S02
### Mô tả
Thêm vào SCR036 (khung danh sách xe, tài xế do E03 dựng) khối ghi giờ làm của tài xế đang chọn: ngày làm việc, giờ lái, tổng thời gian ràng buộc trong ngày, nút ghi. Khối này là phần ngoại vi của luật 2024 cắt khỏi màn dữ liệu gốc.
### Định nghĩa xong
- [ ] Chọn một tài xế hiện khối ghi giờ làm với các trường theo wireframe; ghi thành công cập nhật danh sách giờ đã ghi
- [ ] Báo lỗi tại trường khi thiếu ngày, giờ lái âm hoặc lớn hơn tổng thời gian ràng buộc
- [ ] Ghi được ngày nghỉ không có giờ
- [ ] Khối ghi chỉ hiện với quản trị (tạm như danh mục API, cho tới khi khách trả lời ở gói làm rõ E09-I09; xem GAP-65); vai khác chỉ xem hoặc không thấy
- [ ] Hiện câu nhắc chỉ lưu định danh vận hành, không nhập thông tin cá nhân
- [ ] Loading, lỗi mạng, lỗi quyền hiện đúng
### Chỗ hở
- GAP-65 vai được ghi giờ làm mâu thuẫn giữa danh mục API và màn SCR036

## E09-I03 · Lõi bộ kiểm giờ làm: lệnh kiểm tuyến, dựng ca kế hoạch, lịch sử ca, chèn điểm nghỉ
- Loại: issue
- Cha: E09
- Nhãn: type:issue, constraint:jp-2024-driver-hours
- Spec: RTE_DRIVER_DUTY_CHECK, API-RTE-02, FR-SCH-02, FR-SCH-03
- Phụ thuộc: E09-I01, E09-I02
### Mô tả
Hạt nhân của bộ kiểm: một lệnh kiểm trên bản nháp của tuyến, chọn phiên bản luật, dựng ca kế hoạch từ lịch giờ của tuyến, ghép lịch sử ca của tài xế trong cả kỳ tháng và năm, chèn điểm nghỉ trước khi lái liên tục chạm mức, rồi chạy các luật ở E09-I04 và lưu kết quả cùng phiên bản luật vào bản nháp. Bộ kiểm là hàm thuần, không đọc đồng hồ thật. Chia hai phần BE chạy song song: lệnh kiểm với lịch sử ca, và bộ chèn nghỉ.
### Chỗ hở
- GAP-63 các cách hiểu và định nghĩa của JP-12, JP-13, JP-14 còn mở (làm thêm, cắt ngày 24 giờ, bình quân hai tuần, mức nghỉ, kỳ năm)

## E09-I03-S01 · API kiểm giờ lái tuyến, dựng ca kế hoạch và lịch sử ca (BE)
- Loại: sub-issue
- Cha: E09-I03
- Vai: BE-1
- Nhãn: type:sub-issue, constraint:jp-2024-driver-hours
- Spec: API-RTE-02, RTE_DRIVER_DUTY_CHECK, ADR-012, ADR-005
- Phụ thuộc: E09-I01-S01, E09-I02-S01, E08-I02-S01, E01-I13, E01-I14
### Mô tả
API-RTE-02 theo hợp đồng có sẵn cùng phần xử lý bên trong theo thiết kế kỹ thuật: khoá tuyến, chọn phiên bản luật, dựng ca kế hoạch (xếp điểm dừng theo khung giờ nhận, chờ khi đến sớm, phục vụ, về depot, tính thời gian xếp hàng và kết thúc tại depot vào thời gian ràng buộc), ghép lịch sử ca rồi giao cho các luật ở E09-I04 và bộ chèn nghỉ ở E09-I03-S02. Kết quả là lệnh trả 200 với từng dòng ràng buộc, kể cả dòng chặn, không phải lỗi HTTP. Các khoá cấu hình của thiết kế (thời gian xếp hàng và kết thúc tại depot, độ dài điểm nghỉ chèn, tuổi tối đa của ước tính) được đọc từ kho cấu hình của E01.
### Định nghĩa xong
- [ ] Endpoint theo hợp đồng API-RTE-02: khoá idempotency bắt buộc, vai điều phối, trưởng phòng vận tải, quản trị; lỗi xung đột version (YCL-RTE-001), tuyến không còn là nháp (YCL-RTE-007), thiếu khung giờ phục vụ của khách (YCL-RTE-027), thiếu ước tính chặng (YCL-RTE-028) đúng bảng mã
- [ ] Khoá dòng tuyến trước khi đọc bản nháp; không có phiên bản luật hiệu lực trả YCL-RTE-054, tham số thiếu hoặc sai schema trả lỗi hệ thống và ghi log, không đoán luật
- [ ] Ca kế hoạch dựng bằng hàm lịch giờ dùng chung của E08-I02-S01; thời gian xếp hàng và kết thúc tại depot tính vào thời gian ràng buộc; bốc dỡ và chờ không tính là nghỉ
- [ ] Lịch sử ca lấy giờ thực tế từ bảng giờ làm (thắng tuyến cùng ngày) và các tuyến khác của tài xế đã công bố, đang chạy, hoàn tất, trong cả kỳ tháng và năm, kể cả ca có ngày sau ngày kế hoạch
- [ ] Bộ kiểm là hàm thuần không đọc đồng hồ hệ thống, ngày theo đồng hồ nghiệp vụ; test chạy cùng đầu vào hai lần ra cùng kết quả
- [ ] Ghi kết quả kiểm, trạng thái tổng (đạt, cảnh báo, chặn), thời điểm kiểm, điểm nghỉ chèn và mã phiên bản luật vào bản nháp trong một giao dịch; tuyến sang trạng thái đã kiểm khi không còn dòng chặn; không phát sự kiện EV
- [ ] Mỗi luật một dòng kết quả gồm giá trị thực, ngưỡng, phần vượt và khoảng thời gian tính, thứ tự cố định, không gộp thành "không khả thi"
### Chỗ hở
- GAP-63 các cách hiểu và định nghĩa của JP-12, JP-13, JP-14 còn mở (làm thêm, cắt ngày 24 giờ, bình quân hai tuần, mức nghỉ, kỳ năm)
- GAP-64 nguồn giờ làm thực tế của tài xế chưa chốt

## E09-I03-S02 · Bộ chèn điểm nghỉ trước khi lái liên tục vượt mức (BE)
- Loại: sub-issue
- Cha: E09-I03
- Vai: BE-2
- Nhãn: type:sub-issue, constraint:jp-2024-driver-hours
- Spec: RTE_DRIVER_DUTY_CHECK, FR-SCH-03, JP-13
- Phụ thuộc: E08-I02-S01
### Mô tả
Hàm thuần chèn điểm nghỉ vào lịch ca: đi theo chuỗi lái liên tục, trước khi chuỗi chạm giới hạn của phiên bản luật thì chèn một lần nghỉ dài theo khoá cấu hình, kể cả giữa một chặng dài, rồi dời phần còn lại của ngày. Cách tính lần nghỉ (chỉ lần đủ ngắn nhất mới tính, tổng đủ mới ngắt chuỗi) là cách hiểu nhà thầu đọc từ phiên bản luật, không viết cứng. Việc chặn khi sau chèn vẫn vượt là của E09-I04-S02. Sau khi xong, nối hàm vào API tạo tuyến của E08-I02-S01 để thông báo số điểm nghỉ lúc tạo tuyến.
### Định nghĩa xong
- [ ] Hàm chèn một lần nghỉ trước khi chuỗi lái liên tục chạm giới hạn của phiên bản luật, kể cả giữa một chặng dài, và dời phần còn lại của ngày; độ dài lần nghỉ đọc từ khoá cấu hình
- [ ] Lần nghỉ ngắn hơn mức tối thiểu của phiên bản không ngắt chuỗi; nhiều lần nghỉ cộng đủ tổng của phiên bản thì ngắt chuỗi; có test cho ca nghỉ ngắn và ca ba lần nghỉ (ca biên 18 của thiết kế)
- [ ] Bốc dỡ và thời gian chờ không được tính là nghỉ; có test
- [ ] Trả danh sách điểm nghỉ chèn (vị trí, thời lượng) để hiện trong ca kế hoạch và lưu vào bản nháp
- [ ] Đổi hai tham số nghỉ trong phiên bản luật thì kết quả chèn đổi mà không sửa mã (test với hai phiên bản)
- [ ] Hàm được gọi cả từ lệnh kiểm E09-I03-S01 và từ API tạo tuyến E08-I02-S01; thông báo tạo tuyến hiện số điểm nghỉ
### Chỗ hở
- GAP-63 các cách hiểu và định nghĩa của JP-12, JP-13, JP-14 còn mở (làm thêm, cắt ngày 24 giờ, bình quân hai tuần, mức nghỉ, kỳ năm)

## E09-I04 · Các luật kiểm: 拘束時間 ngày, tháng, năm, làm thêm, nghỉ giữa ca, giờ lái, khung giờ dịch vụ
- Loại: issue
- Cha: E09
- Nhãn: type:issue, constraint:jp-2024-driver-hours
- Spec: RTE_DRIVER_DUTY_CHECK, JP-11, JP-12, JP-13
### Mô tả
Tám luật của bộ kiểm cộng một dòng khung giờ dịch vụ, mỗi luật một hàm riêng và một dòng kết quả riêng, chạy theo thứ tự cố định: làm thêm năm, 拘束時間 năm, tháng, ngày, nghỉ giữa ca, giờ lái bình quân hai ngày, hai tuần, lái liên tục. Mọi ngưỡng đọc từ phiên bản luật có hiệu lực. Chia hai phần BE theo nhóm luật chạy song song và một phần QA phủ bảng ca biên của thiết kế (20 ca) cùng các kịch bản R06-04 và ranh giới với kiểm trước xuất phát.
### Chỗ hở
- GAP-61 các trần 拘束時間, JP-13, JP-14 chưa đối chiếu với 改善基準告示; mốc kiểm lại trước khi đóng băng thiết kế đã trôi qua
- GAP-63 các cách hiểu và định nghĩa của JP-12, JP-13, JP-14 còn mở (làm thêm, cắt ngày 24 giờ, bình quân hai tuần, mức nghỉ, kỳ năm)
- GAP-66 chặn công bố theo khung giờ dịch vụ là cách hiểu nhà thầu; biên khung chưa rõ; khung nằm ở hai nơi

## E09-I04-S01 · Luật ngày, tháng, năm, làm thêm và khung giờ dịch vụ (BE)
- Loại: sub-issue
- Cha: E09-I04
- Vai: BE-1
- Nhãn: type:sub-issue, constraint:jp-2024-driver-hours
- Spec: JP-11, JP-14, JP-18, RTE_DRIVER_DUTY_CHECK, ADR-005
- Phụ thuộc: E09-I03-S01, E01-I14
### Mô tả
Năm dòng kết quả: 拘束時間 ngày (có xét yêu cầu kéo dài đã duyệt của chính tuyến), tháng, năm, làm thêm năm, và khung giờ dịch vụ. Kế hoạch thường lấy trần ngày thường làm trần cứng (khoá cho phép kéo dài trong kế hoạch thường để mặc định tắt); trần kéo dài chỉ áp khi tuyến có yêu cầu kéo dài đã duyệt, giới hạn ở số phút đã duyệt. Tháng và năm cộng dồn giờ thực của mọi ca trong kỳ; kỳ năm tính từ ngày bắt đầu năm của phiên bản, theo đồng hồ nghiệp vụ.
### Định nghĩa xong
- [ ] Dòng ngày: không có kéo dài đã duyệt thì vượt trần ngày thường là chặn (YCL-RTE-041); đúng bằng trần vẫn đạt, vượt một phút thì chặn (13 giờ theo JP-11 của phiên bản mẫu)
- [ ] Có kéo dài đã duyệt của chính tuyến: trần là số phút đã duyệt, vượt thì chặn (YCL-RTE-042); vượt trần kéo dài của phiên bản dù đã duyệt thì chặn (YCL-RTE-043); kéo dài của tuyến khác không có tác dụng
- [ ] Dòng tháng (YCL-RTE-049) và năm (YCL-RTE-048) cộng dồn giờ thực mọi ca trong kỳ, nêu phút vượt và kỳ tính; ngày 31/03 thuộc năm bắt đầu 01/04 của năm trước (ca biên 14); tuyến ngày 5 thấy tuyến đã công bố ngày 25 cùng tháng (ca biên 16)
- [ ] Dòng làm thêm năm (YCL-RTE-050) tính phần giờ làm vượt mức chuẩn mỗi ca của phiên bản, không tính thời gian nghỉ, cộng dồn năm; không giả định luôn có giờ làm thêm
- [ ] Dòng khung giờ dịch vụ (YCL-RTE-032) chặn khi điểm dừng hoặc giờ xuất phát nằm ngoài khung của phiên bản; ranh khung tạm tính là trong khung cho tới khi khách trả lời ở gói làm rõ E09-I09 (xem GAP-66)
- [ ] Đổi trần ngày trong phiên bản luật thì kết quả đổi mà không sửa mã (ca biên 12); không có hằng số ngưỡng nào trong mã nguồn
### Chỗ hở
- GAP-63 các cách hiểu và định nghĩa của JP-12, JP-13, JP-14 còn mở (làm thêm, cắt ngày 24 giờ, bình quân hai tuần, mức nghỉ, kỳ năm)
- GAP-66 chặn công bố theo khung giờ dịch vụ là cách hiểu nhà thầu; biên khung chưa rõ; khung nằm ở hai nơi

## E09-I04-S02 · Luật nghỉ giữa ca, giờ lái bình quân và lái liên tục (BE)
- Loại: sub-issue
- Cha: E09-I04
- Vai: BE-2
- Nhãn: type:sub-issue, constraint:jp-2024-driver-hours
- Spec: JP-12, JP-13, RTE_DRIVER_DUTY_CHECK
- Phụ thuộc: E09-I03-S02
### Mô tả
Bốn nhóm dòng kết quả: nghỉ giữa hai ca (kèm phát hiện ca trùng nhau), giờ lái bình quân hai ngày, bình quân hai tuần, lái liên tục sau khi bộ chèn nghỉ đã chạy. Cách tính bình quân hai ngày và hai tuần đúng theo thiết kế (hai ngày: chặn khi cả hai cặp ngày cùng vượt; hai tuần: cửa sổ 14 ngày kết thúc ở ngày kế hoạch).
### Định nghĩa xong
- [ ] Ca trùng ca khác của tài xế, kể cả qua nửa đêm, bị chặn (YCL-RTE-040)
- [ ] Nghỉ dưới mục tiêu nhưng không dưới mức tối thiểu của phiên bản cho cảnh báo (YCL-RTE-044) và vẫn công bố được; dưới mức tối thiểu thì chặn (YCL-RTE-045); đúng mức tối thiểu cảnh báo, kém một phút thì chặn
- [ ] Lái bình quân hai ngày chỉ chặn (YCL-RTE-046) khi cả hai cặp ngày (trước và kế hoạch, kế hoạch và sau) cùng vượt; một cặp vượt mà ngày còn lại chưa có kế hoạch hoặc dữ liệu thì cảnh báo (YCL-RTE-052, YCL-RTE-053)
- [ ] Lái bình quân hai tuần: cửa sổ 14 ngày kết thúc ở ngày kế hoạch vượt thì chặn (YCL-RTE-047); ngày nghỉ lấy từ dòng ngày nghỉ của E09-I02
- [ ] Lái liên tục vượt mức sau khi đã chèn nghỉ thì chặn (YCL-RTE-051)
- [ ] Mọi ngưỡng đọc từ phiên bản luật; test các ca biên 2, 3, 4 và 17 của thiết kế
### Chỗ hở
- GAP-63 các cách hiểu và định nghĩa của JP-12, JP-13, JP-14 còn mở (làm thêm, cắt ngày 24 giờ, bình quân hai tuần, mức nghỉ, kỳ năm)

## E09-I04-S03 · Bộ kiểm thử ca biên của bộ kiểm giờ làm và ranh giới với kiểm trước xuất phát (QA)
- Loại: sub-issue
- Cha: E09-I04
- Vai: QA-1
- Nhãn: type:sub-issue, constraint:jp-2024-driver-hours
- Spec: RTE_DRIVER_DUTY_CHECK, FR-SCH-02, FR-SCH-03, OUT_DEPARTURE_GATE
- Phụ thuộc: E09-I04-S01, E09-I04-S02, E09-I07-S01
### Mô tả
Chuyển bảng ca biên của thiết kế kỹ thuật (20 ca, rút từ ca của prototype) và kịch bản kiểm tuyến không khả thi (R06-04) thành bộ kiểm thử tự động chạy qua API. Thêm kiểm thử ranh giới: kiểm trước xuất phát của kho không kiểm giờ lái, còn khung giờ dịch vụ bị chặn ở hai nơi khác nhau (lúc công bố tuyến ở đây và lúc xuất phát ở E07).
### Định nghĩa xong
- [ ] 20 ca biên của bảng thiết kế chuyển thành test, mỗi test mang mã ca trong tên; ca nào chưa đủ dữ liệu nêu lý do
- [ ] Bảng đối chiếu ca biên, mã lỗi YCL-RTE và kết quả mong đợi (đạt, cảnh báo, chặn) nằm trong tài liệu, mọi dòng có test xanh; bảng viết để E13-I08 dùng lại cho cổng nghiệm thu lịch tài xế (ca R06-04 và R06-11) mà không viết lại luật
- [ ] Kịch bản R06-04 chạy từ lệnh kiểm tới lệnh công bố: lái liên tục hoặc nghỉ vi phạm thì công bố bị từ chối
- [ ] Test chứng minh kết quả không đổi khi chạy lại cùng đầu vào, thứ tự các dòng cố định, và không có đường "bỏ qua cảnh báo" để công bố tuyến còn dòng chặn
- [ ] Quét mã nguồn và bộ test xác nhận ngưỡng chỉ đến từ dữ liệu phiên bản luật mẫu, không có hằng số trần nằm cứng trong mã bộ kiểm
- [ ] Kiểm thử ranh giới với kiểm trước xuất phát: giờ xuất phát ngoài khung bị chặn ở hai nơi bằng hai mã lỗi khác nhau, kiểm trước xuất phát không bao giờ trả dòng giờ lái; biên bản gửi E07
### Chỗ hở
- GAP-66 chặn công bố theo khung giờ dịch vụ là cách hiểu nhà thầu; biên khung chưa rõ; khung nằm ở hai nơi

## E09-I05 · Màn SCR017: hiển thị kết quả kiểm giờ lái từng ràng buộc
- Loại: issue
- Cha: E09
- Nhãn: type:issue, constraint:jp-2024-driver-hours
- Spec: SCR017, API-RTE-01, API-RTE-02
- Phụ thuộc: E09-I03
### Mô tả
Màn Critical duy nhất của luật 2024: điều phối mở một tuyến, bấm kiểm, thấy từng ràng buộc một dòng kèm giá trị thực, ngưỡng, phần vượt, khoảng tính; ca kế hoạch có điểm nghỉ chèn; phiên bản luật và huy hiệu cần xác thực luôn hiện. Màn không tự tính ràng buộc nào, chỉ hiển thị kết quả server trả hoặc kết quả đã lưu. Khối xin kéo dài của cùng màn thuộc E09-I06. Chia hai phần: phần đọc của API-RTE-01 (BE) và màn (FE).
### Chỗ hở
- GAP-61 các trần 拘束時間, JP-13, JP-14 chưa đối chiếu với 改善基準告示; mốc kiểm lại trước khi đóng băng thiết kế đã trôi qua

## E09-I05-S01 · API-RTE-01: khối kết quả đã lưu, phiên bản luật, yêu cầu kéo dài và sự kiện (BE)
- Loại: sub-issue
- Cha: E09-I05
- Vai: BE-2
- Nhãn: type:sub-issue, constraint:jp-2024-driver-hours
- Spec: API-RTE-01, ADR-010, FR-SCH-02
- Phụ thuộc: E08-I04-S01, E09-I03-S01
### Mô tả
Mở rộng chế độ chi tiết của API-RTE-01 (phần cơ bản do E08-I04-S01 làm) để màn SCR017 nạp được: kết quả kiểm đã lưu theo phiên bản luật đã gắn, trạng thái tổng, thời điểm kiểm, phiên bản luật đang hiệu lực cùng bốn tham số cách hiểu nhà thầu, danh sách yêu cầu kéo dài và sự kiện của tuyến. API không tự kiểm lại.
### Định nghĩa xong
- [ ] Chi tiết trả kết quả kiểm đã lưu, trạng thái tổng, thời điểm kiểm và mã phiên bản luật đã gắn; không tính lại
- [ ] Với tuyến chưa kiểm, trả phiên bản luật đang hiệu lực tại ngày nghiệp vụ kèm bốn tham số cách hiểu nhà thầu để màn hiện khung giải thích
- [ ] Trả danh sách yêu cầu kéo dài và sự kiện của tuyến; vai tài xế không nhận các khối này (test)
- [ ] Tuyến đã công bố trả kết quả theo phiên bản luật lúc công bố; tạo phiên bản luật mới sau đó không đổi phản hồi (test)
- [ ] Mỗi trường mà màn SCR017 dùng trong bảng ánh xạ dữ liệu của spec đều có trong phản hồi; vai ngoài ma trận bị từ chối

## E09-I05-S02 · Màn SCR017: kết quả từng ràng buộc, ca kế hoạch, điểm nghỉ chèn, phiên bản luật (FE)
- Loại: sub-issue
- Cha: E09-I05
- Vai: FE-1
- Nhãn: type:sub-issue, constraint:jp-2024-driver-hours
- Spec: SCR017, FR-SCH-02, FR-SCH-03
- Phụ thuộc: E09-I05-S01
### Mô tả
Dựng SCR017 theo spec có sẵn (29 kịch bản chấp nhận, wireframe và ảnh chú thích): phần đầu (liên kết về tuyến, thông tin tuyến, thời điểm kiểm và phiên bản luật, khung cách hiểu nhà thầu), nút kiểm, bảng ràng buộc, các banner kết quả, bảng ca kế hoạch. Khối xin kéo dài (mục 20 đến 28) do E09-I06-S02. Màn không tính ràng buộc nào ở phía client.
### Định nghĩa xong
- [ ] Mở màn hiện thông tin tuyến, thời điểm kiểm, mã phiên bản luật; huy hiệu cần xác thực luôn hiện cùng bốn tham số cách hiểu nhà thầu lấy từ phiên bản, không có số gõ cứng
- [ ] Nút kiểm gọi API-RTE-02 với khoá idempotency mới mỗi lần bấm, giữ nguyên khoá khi gửi lại sau lỗi mạng, vô hiệu trong lúc gọi, ẩn với tuyến không còn là nháp
- [ ] Bảng ràng buộc mỗi dòng một ràng buộc theo thứ tự server trả; nhãn chặn, cảnh báo, đạt có chữ chứ không chỉ màu; dòng ngày, nghỉ, lái liên tục, khung giờ hiện giá trị thực, ngưỡng, phần vượt, khoảng tính
- [ ] Bốn trạng thái tổng hiện đúng: còn dòng chặn (kèm số dòng), chỉ có cảnh báo (công bố được), tất cả đạt, chưa kiểm
- [ ] Bảng ca kế hoạch hiện các điểm nghỉ chèn
- [ ] Lỗi xung đột version, tuyến không còn nháp, không có phiên bản luật, thiếu khung giờ hoặc ước tính chặng hiện đúng câu theo mã; lỗi quyền, không tìm thấy, lỗi hệ thống (kèm mã tương quan) hiện đúng
- [ ] Giờ hiển thị theo giờ Nhật, phút hiển thị dạng giờ:phút; 18 kịch bản chấp nhận đầu của spec (hiển thị và các ràng buộc) chạy xanh ở mức màn bằng kiểm thử tự động, kèm ảnh chụp đối chiếu wireframe

## E09-I06 · Kéo dài 拘束時間 khẩn cấp: xin, duyệt, hiện ở trang chủ
- Loại: issue
- Cha: E09
- Nhãn: type:issue, constraint:jp-2024-driver-hours
- Spec: API-RTE-03, API-RTE-04, SCR017, TRANSPORT_DB
- Phụ thuộc: E09-I03
### Mô tả
Đường duy nhất để một tuyến vượt trần ngày của kế hoạch thường: điều phối hoặc trưởng phòng vận tải xin kéo dài có lý do và ảnh bằng chứng, trưởng phòng vận tải khác người xin duyệt hoặc từ chối, kết quả quyết định một lần. Gồm bảng yêu cầu và hai API (BE), khối xin ở SCR017 cùng danh sách chờ duyệt ở SCR018 (FE), và dòng đếm yêu cầu chờ duyệt ở trang chủ SCR002 (FE, phần cắt từ E12). Luồng này là đề xuất của nhà thầu chưa khách xác nhận.
### Chỗ hở
- GAP-67 luồng kéo dài khẩn cấp và vai xin, duyệt chưa khách xác nhận

## E09-I06-S01 · Bảng yêu cầu kéo dài và API xin, duyệt kéo dài (BE)
- Loại: sub-issue
- Cha: E09-I06
- Vai: BE-1
- Nhãn: type:sub-issue, constraint:jp-2024-driver-hours
- Spec: API-RTE-03, API-RTE-04, TRANSPORT_DB, FR-SCH-02
- Phụ thuộc: E09-I03-S01, E01-I16, E02-I03
### Mô tả
Dựng bảng yêu cầu kéo dài (mỗi tuyến một yêu cầu chờ, quyết định đúng một lần, người quyết khác người xin) và hiện thực hai API đã có hợp đồng. Duyệt xong phát sự kiện EV-RTE-03; lần kiểm sau của tuyến áp trần kéo dài đúng số phút đã duyệt (dòng ngày ở E09-I04-S01).
### Định nghĩa xong
- [ ] Bảng đúng thiết kế: ràng buộc duy nhất một yêu cầu chờ mỗi tuyến, trigger chỉ cho chuyển từ chờ sang duyệt hoặc từ chối một lần, người quyết khác người xin, đường dẫn bằng chứng duy nhất, ghi chú lý do giới hạn độ dài; test từng ràng buộc
- [ ] API xin: vai điều phối hoặc trưởng phòng vận tải (quản trị không xin); số phút phải lớn hơn trần thường và không quá trần kéo dài của phiên bản (YCL-RTE-029); lý do một đến 500 ký tự; bằng chứng phải là tệp đã tải lên (YCL-SYS-003); đã có yêu cầu chờ thì chặn (YCL-CMN-015); tuyến đã huỷ không xin được
- [ ] API duyệt: vai theo khoá vai duyệt (mặc định trưởng phòng vận tải) và khác người xin (YCL-CMN-004); đã quyết thì chặn (YCL-CMN-017); từ chối bắt buộc lý do
- [ ] Hai người duyệt cùng lúc chỉ một lệnh thành công; hai lệnh xin cùng khoá idempotency không tạo yêu cầu thứ hai
- [ ] Duyệt phát sự kiện EV-RTE-03 qua hộp thư đi và ghi audit trong cùng giao dịch
- [ ] Yêu cầu có thể trỏ sự kiện bất ngờ của chính tuyến, trỏ sự kiện tuyến khác bị từ chối
- [ ] Kiểm lại tuyến sau khi duyệt thì dòng ngày áp đúng số phút đã duyệt (test cùng E09-I04-S01)
### Chỗ hở
- GAP-67 luồng kéo dài khẩn cấp và vai xin, duyệt chưa khách xác nhận

## E09-I06-S02 · Khối xin kéo dài ở SCR017 và danh sách chờ duyệt ở SCR018 (FE)
- Loại: sub-issue
- Cha: E09-I06
- Vai: FE-1
- Nhãn: type:sub-issue, constraint:jp-2024-driver-hours
- Spec: SCR017, SCR018, API-RTE-03, API-RTE-04
- Phụ thuộc: E09-I06-S01, E09-I05-S02, E01-I18
### Mô tả
Phần xin trên SCR017 (mục 20 đến 28 của spec) và phần duyệt trên SCR018 (danh sách yêu cầu chờ duyệt có nút duyệt, từ chối; bước duyệt thuộc SCR018 theo quyết định đã đóng của LAB-4). Ảnh bằng chứng tải lên qua URL ký sẵn của cơ chế tải tệp chung.
### Định nghĩa xong
- [ ] Khối xin chỉ hiện khi tuyến còn vi phạm trần ngày thường và vai được xin; có ô số phút xin, chọn sự kiện bất ngờ tuỳ chọn, ô lý do, chọn ảnh bằng chứng, nút gửi
- [ ] Báo lỗi tại trường cho số phút ngoài khoảng, thiếu lý do, thiếu ảnh; server từ chối lần nữa thì hiện câu theo mã lỗi
- [ ] Đang chờ duyệt thì hiện trạng thái đã gửi thay cho khối xin; đã duyệt hiện mức kéo dài được duyệt; gửi lại sau lỗi mạng giữ khoá idempotency, không tạo yêu cầu thứ hai
- [ ] Danh sách chờ duyệt ở SCR018 có nút duyệt và từ chối chỉ với trưởng phòng vận tải khác người xin; từ chối bắt buộc nhập lý do
- [ ] Quản trị kiểm giờ lái được nhưng không thấy khối xin; vai ngoài ma trận không vào được màn
- [ ] Các kịch bản chấp nhận về kéo dài của spec (xin, ngoài khoảng, một yêu cầu chờ, tuyến huỷ, người xin không tự duyệt, liên kết sự kiện) chạy xanh ở mức màn

## E09-I06-S03 · Dòng "yêu cầu kéo dài chờ duyệt" ở trang chủ SCR002 (FE)
- Loại: sub-issue
- Cha: E09-I06
- Vai: FE-1
- Nhãn: type:sub-issue, constraint:jp-2024-driver-hours
- Spec: SCR002, API-HOM-01
- Phụ thuộc: E09-I06-S01, E12-I01-S01, E12-I01-S02
### Mô tả
Phần ngoại vi của luật 2024 cắt khỏi trang chủ: một loại dòng trong danh sách việc chờ của trang chủ, đếm yêu cầu kéo dài đang chờ và mở SCR018 khi bấm. Khung trang chủ, các loại dòng khác và việc cấp số đếm ở lớp báo cáo thuộc E12; issue này đăng ký loại việc "yêu cầu kéo dài" vào cơ chế đăng ký của E12-I01-S01 và làm loại dòng này hiện đúng.
### Định nghĩa xong
- [ ] Loại việc "yêu cầu kéo dài" được đăng ký vào cơ chế của E12-I01-S01 với mã loại, màn xử lý SCR018, vai đếm theo khoá vai duyệt và nguồn đếm là bảng yêu cầu kéo dài
- [ ] Dòng loại này hiện đúng vị trí cố định trong thứ tự các loại việc chờ, kèm số đếm và nhãn tiếng Nhật đã duyệt
- [ ] Chỉ vai được duyệt kéo dài (theo khoá vai duyệt) thấy dòng này; vai khác không thấy
- [ ] Bấm dòng mở SCR018 ở danh sách chờ duyệt kéo dài
- [ ] Khi chỉ có loại này thì khung việc chờ chỉ còn một dòng (kịch bản chấp nhận tương ứng của SCR002 chạy xanh)
- [ ] Số đếm khớp số yêu cầu đang chờ sau khi một yêu cầu mới được duyệt hoặc từ chối

## E09-I07 · Cổng công bố tuyến theo luật giờ lái và sửa tuyến sau công bố
- Loại: issue
- Cha: E09
- Nhãn: type:issue, constraint:jp-2024-driver-hours
- Spec: API-RTE-08, API-RTE-09, RTE_DRIVER_DUTY_CHECK, ADR-010, FR-SCH-02
### Mô tả
Điểm thực thi của luật: công bố tuyến kiểm lại toàn bộ với phiên bản luật có hiệu lực tại ngày công bố, tuần tự hoá hai tuyến cùng tài xế, chặn khi còn dòng chặn hoặc ước tính di chuyển thiếu hoặc quá cũ, rồi gắn cố định phiên bản luật vào tuyến và phát sự kiện công bố. Sửa sau công bố tạo phiên bản tuyến mới có lý do, phải kiểm và công bố lại. Cả hai API chưa có hợp đồng. Phần chung của vòng đời tuyến (danh sách, chi tiết, đổi trạng thái) ở E08-I04.
### Chỗ hở
- GAP-60 nhiều API của nhóm tuyến chưa có hợp đồng
- GAP-66 chặn công bố theo khung giờ dịch vụ là cách hiểu nhà thầu; biên khung chưa rõ; khung nằm ở hai nơi

## E09-I07-S01 · API công bố tuyến: kiểm lại theo phiên bản luật lúc công bố (BE)
- Loại: sub-issue
- Cha: E09-I07
- Vai: BE-1
- Nhãn: type:sub-issue, constraint:jp-2024-driver-hours
- Spec: API-RTE-08, RTE_DRIVER_DUTY_CHECK, ADR-010, ADR-008
- Phụ thuộc: E09-I04-S01, E09-I04-S02, E09-I06-S01, E08-I03
### Mô tả
Chi tiết hoá rồi hiện thực API-RTE-08 theo mục xử lý công bố của thiết kế kỹ thuật: khoá dòng tuyến, khoá advisory theo tài xế, kiểm lại bước dựng ca và các luật, kiểm tuổi ước tính, ghi phiên bản luật cố định, phát sự kiện. Bản đồ không trả lời hoặc ước tính quá cũ thì chặn công bố, không tự đoán thời gian chạy.
### Định nghĩa xong
- [ ] Tài liệu hợp đồng API-RTE-08 viết xong (tham số, phản hồi 201, bảng mã lỗi dưới đây), được đối chiếu với thiết kế kỹ thuật
- [ ] Khoá dòng tuyến rồi khoá advisory theo tài xế; hai tuyến cùng tài xế công bố đồng thời thì tuyến sau kiểm lại và thấy tuyến trước (ca biên 20)
- [ ] Kiểm lại với phiên bản luật hiệu lực tại ngày công bố: còn dòng chặn trả YCL-RTE-009, chỉ vượt trần ngày mà chưa có kéo dài đã duyệt trả YCL-RTE-010, chưa kiểm trả YCL-RTE-008, không có phiên bản luật hiệu lực trả YCL-RTE-054
- [ ] Lịch tài xế hoặc ước tính đổi sau lần kiểm thì trả xung đột YCL-RTE-011, YCL-RTE-012; ước tính quá tuổi tối đa trả YCL-RTE-013, thiếu một chặng trả YCL-RTE-028; dịch vụ bản đồ lỗi không làm mới được ước tính thì vẫn chặn công bố, không tự đoán thời gian
- [ ] Công bố thành công ghi thời điểm, người công bố, mã phiên bản luật cố định và số phiên bản công bố; phát sự kiện công bố kèm phiên bản luật qua hộp thư đi trong cùng giao dịch
- [ ] Phiên bản luật có hiệu lực sau đó không đổi kết quả hay mã phiên bản luật của tuyến đã công bố (test)
- [ ] Khoá idempotency bắt buộc; các ca biên 1, 19, 20 của thiết kế có test
### Chỗ hở
- GAP-66 chặn công bố theo khung giờ dịch vụ là cách hiểu nhà thầu; biên khung chưa rõ; khung nằm ở hai nơi

## E09-I07-S02 · API sửa tuyến sau công bố: phiên bản mới có lý do và kiểm lại (BE)
- Loại: sub-issue
- Cha: E09-I07
- Vai: BE-2
- Nhãn: type:sub-issue, constraint:jp-2024-driver-hours
- Spec: API-RTE-09, FR-SCH-02, ADR-010
- Phụ thuộc: E09-I07-S01, E07-I04-S02
### Mô tả
Chi tiết hoá rồi hiện thực API-RTE-09: sửa kế hoạch một tuyến tạo phiên bản tuyến mới, giữ nguyên các bản cũ bất biến; sửa tuyến đã công bố bắt buộc lý do. Bản mới là nháp phải kiểm lại và công bố lại với phiên bản luật có hiệu lực lúc công bố lại. Tuyến đã có sự kiện bất ngờ không sửa được giờ kế hoạch; chuyến đã xếp hàng không sửa được, kiểm qua cổng nội bộ do E07 hiện thực.
### Định nghĩa xong
- [ ] Tài liệu hợp đồng API-RTE-09 viết xong: lý do bắt buộc khi tuyến đã công bố, phản hồi trả số phiên bản mới
- [ ] Sửa tạo phiên bản mới và giữ nguyên các bản cũ; bản mới ở trạng thái nháp, chưa có kết quả kiểm
- [ ] Tuyến đã có sự kiện bất ngờ bị từ chối (YCL-RTE-004); chuyến đã bắt đầu xếp hàng bị từ chối qua cổng nội bộ (YCL-RTE-005); trạng thái không cho sửa trả YCL-RTE-006
- [ ] Phát sự kiện sửa tuyến kèm lý do; ghi audit
- [ ] Test: sửa một tuyến vi phạm thì không có đường nào công bố được mà không qua kiểm lại

## E09-I07-S03 · SCR018: nút công bố, sửa tuyến, thông báo chặn; liên kết sang SCR017 (FE)
- Loại: sub-issue
- Cha: E09-I07
- Vai: FE-1
- Nhãn: type:sub-issue, constraint:jp-2024-driver-hours
- Spec: SCR018, SCR016, SCR017, API-RTE-08, API-RTE-09
- Phụ thuộc: E09-I07-S01, E09-I07-S02, E08-I04-S02
### Mô tả
Gắn vào khung SCR018 của E08-I04-S02: nút công bố với thông báo chặn khi còn ràng buộc giờ lái hoặc ước tính quá cũ, biểu mẫu sửa tạo phiên bản mới có lý do, cột kết quả kiểm giờ lái trong bảng phiên bản; thêm liên kết "kiểm giờ lái" từ SCR016, từ danh sách và từ chi tiết sang SCR017. Không có nút bỏ qua cảnh báo.
### Định nghĩa xong
- [ ] Nút công bố không dùng được và hiện thông báo chặn khi còn ràng buộc giờ lái bị chặn; hiện yêu cầu làm mới khi ước tính quá 24 giờ
- [ ] Các mã lỗi công bố (008, 009, 010, 011, 012, 013, 054) hiện đúng câu theo bảng lỗi chung, có hướng dẫn làm lại
- [ ] Biểu mẫu sửa tuyến tạo phiên bản mới, lý do bắt buộc khi tuyến đã công bố và báo lỗi tại trường
- [ ] Liên kết kiểm giờ lái ở SCR016 (sau khi tạo tuyến), ở dòng danh sách SCR018 và ở chi tiết đều mở SCR017 đúng tuyến, chỉ hiện với vai mở được SCR017
- [ ] Cột kết quả kiểm trong bảng phiên bản hiện đạt hoặc chặn khớp bản đã lưu
- [ ] Test chứng minh không có nút hay đường giao diện nào bỏ qua dòng chặn

## E09-I08 · Sự kiện bất ngờ có bằng chứng và báo cáo kế hoạch, thực tế, sự kiện
- Loại: issue
- Cha: E09
- Nhãn: type:issue, constraint:jp-2024-driver-hours
- Spec: API-RTE-11, API-RTE-14, FR-SCH-05, TRANSPORT_DB
### Mô tả
Khi tuyến gặp sự cố ngoài kế hoạch (tai nạn, xe hỏng, thiên tai, hoặc chậm giờ mà có chứng cứ độc lập), hệ thống lưu thành một bản ghi không sửa được của tuyến đó và khoá giờ kế hoạch của tuyến. Báo cáo tuân thủ chia làm ba phần là kế hoạch, thực tế và sự kiện, để không ai chỉnh giờ kế hoạch cho che được vi phạm. Phần vượt trần theo giờ thực tế vẫn hiện nguyên và gắn nhãn "có sự kiện có bằng chứng"; kết quả tuân thủ đã tính được giữ nguyên. Phase 1 theo thiết kế LAB-4 nhưng quyết định D-12 còn chờ khách; nếu khách chọn Phase 2 thì cả issue chuyển giai đoạn. Hai API chưa có hợp đồng, SCR018 mới có wireframe. Cổng báo cáo SCR024 còn một báo cáo riêng về tuân thủ giờ lái (báo cáo số 10 trong danh mục 12 loại) mà bản kiểm kê chưa tính vào luật 2024: LAB-4 chỉ có tên, nên issue này kèm một phần định nghĩa và đăng ký báo cáo đó vào khung của E12.
### Chỗ hở
- GAP-68 D-12 chưa chốt làm ở Phase 1 và cách đánh dấu sự kiện chưa trả lời
- GAP-60 nhiều API của nhóm tuyến chưa có hợp đồng
- GAP-21 báo cáo số 10 của cổng báo cáo (tuân thủ giờ lái) chưa được LAB-4 định nghĩa

## E09-I08-S01 · Bảng sự kiện bất ngờ và API ghi sự kiện (BE)
- Loại: sub-issue
- Cha: E09-I08
- Vai: BE-2
- Nhãn: type:sub-issue, constraint:jp-2024-driver-hours
- Spec: API-RTE-11, TRANSPORT_DB, FR-SCH-05
- Phụ thuộc: E08-I01, E01-I16
### Mô tả
Dựng bảng sự kiện bất ngờ (chỉ thêm, không sửa, không xoá) và chi tiết hoá rồi hiện thực API-RTE-11. Loại sự kiện hợp lệ là khoá cấu hình, không phải ràng buộc cứng; bằng chứng ảnh bắt buộc. Sau khi có sự kiện, kế hoạch của tuyến bị khoá (phối hợp với API-RTE-09 ở E09-I07-S02).
### Định nghĩa xong
- [ ] Bảng sự kiện đúng thiết kế, trigger và quyền chỉ INSERT từ chối UPDATE, DELETE, TRUNCATE; có test
- [ ] Tài liệu hợp đồng API-RTE-11 viết xong: loại sự kiện nằm trong khoá danh mục (ngoài danh mục trả YCL-RTE-030), thời điểm xảy ra ngoài ngày tuyến hoặc ở tương lai trả YCL-RTE-031, tuyến chưa công bố trả YCL-RTE-003, bằng chứng bắt buộc
- [ ] Ghi sự kiện phát sự kiện RouteIncidentRecorded qua hộp thư đi và ghi audit; khoá idempotency bắt buộc
- [ ] Sau khi có sự kiện, sửa giờ kế hoạch của tuyến bị từ chối (YCL-RTE-004); kế hoạch đã công bố giữ nguyên (ca biên 6)
- [ ] Yêu cầu kéo dài ở E09-I06-S01 chỉ trỏ được sự kiện của chính tuyến; test cả hai chiều
### Chỗ hở
- GAP-68 D-12 chưa chốt làm ở Phase 1 và cách đánh dấu sự kiện chưa trả lời

## E09-I08-S02 · API báo cáo kế hoạch, thực tế, sự kiện (BE)
- Loại: sub-issue
- Cha: E09-I08
- Vai: BE-1
- Nhãn: type:sub-issue, constraint:jp-2024-driver-hours
- Spec: API-RTE-14, FR-SCH-05, RTE_DRIVER_DUTY_CHECK
- Phụ thuộc: E09-I08-S01, E09-I03-S01
### Mô tả
Chi tiết hoá rồi hiện thực API-RTE-14: trả ba phần tách biệt của một tuyến. Kế hoạch là bản đã công bố cùng kết quả kiểm lúc công bố (không tính lại). Thực tế là giờ bắt đầu, kết thúc thực tế và bản ghi giờ làm, đo riêng, chưa có thì để trống. Sự kiện là danh sách sự kiện kèm yêu cầu kéo dài liên kết. Phần vượt trần theo thực tế hiện nguyên.
### Định nghĩa xong
- [ ] Tài liệu hợp đồng API-RTE-14 viết xong với ba khối tách bạch
- [ ] Khối kế hoạch lấy kết quả kiểm đã lưu lúc công bố, không tính lại sau khi có sự kiện hay có phiên bản luật mới
- [ ] Khối thực tế chưa có giờ thì để trống chứ không trả số 0 (ca biên 7)
- [ ] Phần vượt trần theo thực tế hiện nguyên kèm nhãn "có sự kiện có bằng chứng" khi tuyến có sự kiện và kéo dài đã duyệt gắn sự kiện; không xoá, không ghi đè kết quả tuân thủ đã tính; cách đánh dấu mặc định như prototype LAB-3 (một cờ theo tuyến) và đổi được theo câu trả lời của khách
- [ ] Tuyến thường cùng ngày không bị ảnh hưởng bởi tuyến có sự kiện (ca biên 5); test chứng minh
### Chỗ hở
- GAP-68 D-12 chưa chốt làm ở Phase 1 và cách đánh dấu sự kiện chưa trả lời

## E09-I08-S03 · SCR018: ghi sự kiện và báo cáo ba phần (FE)
- Loại: sub-issue
- Cha: E09-I08
- Vai: FE-1
- Nhãn: type:sub-issue, constraint:jp-2024-driver-hours
- Spec: SCR018, FR-SCH-05, API-RTE-11, API-RTE-14
- Phụ thuộc: E09-I08-S02, E08-I04-S02
### Mô tả
Gắn vào khung SCR018 của E08-I04-S02: biểu mẫu ghi sự kiện bất ngờ có ảnh bằng chứng bắt buộc và ô xác nhận đã hiểu sự kiện không sửa được và kế hoạch bị khoá, cùng báo cáo ba khối kế hoạch, thực tế, sự kiện. Tài xế không thấy các khối này.
### Định nghĩa xong
- [ ] Biểu mẫu có loại sự kiện (lấy từ khoá danh mục), thời điểm, ghi chú, ảnh bằng chứng bắt buộc, ô xác nhận; thiếu ảnh hoặc chưa xác nhận thì không gửi, báo lỗi tại trường
- [ ] Sau khi ghi hiện thông báo kế hoạch đã bị khoá; thử sửa kế hoạch hiện câu từ chối rõ
- [ ] Báo cáo có ba khối tách biệt; khối thực tế chưa có dữ liệu hiện trống rõ ràng, không hiện 0
- [ ] Nhãn "có sự kiện có bằng chứng" hiện ở phần vượt đúng theo API
- [ ] Vai tài xế không thấy biểu mẫu lẫn báo cáo; loading và lỗi mạng hiện đúng

## E09-I08-S04 · Báo cáo "tuân thủ giờ lái" của cổng báo cáo SCR024 (BE)
- Loại: sub-issue
- Cha: E09-I08
- Vai: BE-2
- Nhãn: type:sub-issue, constraint:jp-2024-driver-hours
- Spec: SCR024, FR-RPT-01, FR-SCH-02, RTE_DRIVER_DUTY_CHECK
- Phụ thuộc: E09-I08-S02, E12-I02-S01
### Mô tả
Danh mục 12 loại báo cáo của SCR024 có báo cáo số 10 về tuân thủ giờ lái, nhưng LAB-4 chỉ ghi tên nhãn, chưa có cột, công thức hay bộ lọc. Khung cổng báo cáo (danh mục, lọc, xuất CSV, phân quyền) do E12 dựng; issue này viết định nghĩa báo cáo số 10 rồi đăng ký vào khung đó. Báo cáo chỉ đọc kết quả kiểm đã lưu theo phiên bản luật đã gắn cho từng tuyến, không tính lại, và tách phần kế hoạch, thực tế, sự kiện như API-RTE-14.
### Định nghĩa xong
- [ ] Tài liệu định nghĩa báo cáo (cột, công thức, bộ lọc nào của danh mục chung có nghĩa với báo cáo này) viết xong và ghi rõ là giả định chờ khách
- [ ] Mỗi dòng báo cáo là một tuyến kèm phiên bản luật đã gắn, trạng thái tổng lúc công bố, số dòng chặn và cảnh báo, cờ có sự kiện có bằng chứng; không tính lại ràng buộc
- [ ] Báo cáo đăng ký vào khung đăng ký báo cáo của E12-I02-S01 bằng một dòng đăng ký gồm mã báo cáo, bộ lọc áp dụng, vai xem (trưởng phòng vận tải, quản trị)
- [ ] CSV khớp bộ lọc đang chọn và giữ thời hạn lưu của nguồn
- [ ] Số liệu khớp truy vấn trực tiếp trên bộ dữ liệu mẫu có cả tuyến thường và tuyến có sự kiện bất ngờ (R06-11)
### Chỗ hở
- GAP-21 báo cáo số 10 của cổng báo cáo (tuân thủ giờ lái) chưa được LAB-4 định nghĩa

## E09-I09 · Làm rõ với khách và hồ sơ bằng chứng pháp lý của luật giờ lái
- Loại: issue
- Cha: E09
- Nhãn: type:issue, type:clarification, constraint:jp-2024-driver-hours
- Spec: OPS-LAW-01, ADR-010, JP-11
### Mô tả
Vì trần 拘束時間, JP-13 và JP-14 chưa đối chiếu văn bản chính thức, đây là issue giữ việc làm rõ với khách và dựng hồ sơ bằng chứng: gói câu hỏi pháp lý và vận tải, hồ sơ đối chiếu từng tham số, và kiểm thử bảo đảm không có ngưỡng nào viết cứng. Ba câu mở có rủi ro cao nhất: bản chính thức của văn bản tại ngày bắt đầu chạy thật (ADR-010, câu 1; SCR017, câu 6; TRANSPORT, T-09). Phải làm trước khi duyệt phiên bản luật dùng ở môi trường thật. Chặn E09-I01-S02 (duyệt phiên bản thật) và go-live.
### Chỗ hở
- GAP-61 các trần 拘束時間, JP-13, JP-14 chưa đối chiếu với 改善基準告示; mốc kiểm lại trước khi đóng băng thiết kế đã trôi qua
- GAP-62 hai điểm của bản 2024 mà RFP không tóm (giới hạn số ngày dài, nâng trần nhờ thoả thuận lao động) chưa có tham số
- GAP-63 các cách hiểu và định nghĩa của JP-12, JP-13, JP-14 còn mở (làm thêm, cắt ngày 24 giờ, bình quân hai tuần, mức nghỉ, kỳ năm)
- GAP-64 nguồn giờ làm thực tế của tài xế chưa chốt

## E09-I09-S01 · Gói câu hỏi pháp chế và vận tải gửi khách (BrSE)
- Loại: sub-issue
- Cha: E09-I09
- Vai: BrSE
- Nhãn: type:sub-issue, type:clarification, constraint:jp-2024-driver-hours
- Spec: OPS-LAW-01, ADR-010, JP-13, JP-14
### Mô tả
Một văn bản gửi khách (tiếng Nhật lịch sự) gom hai nhóm câu LAB-4 đã mở. Nhóm pháp lý, người trả lời là pháp chế và nhân sự khách: văn bản chính thức tại ngày chạy thật và từng trần JP-11 đến JP-14, kỳ năm theo thoả thuận 36協定, định nghĩa làm thêm, quy tắc một ngày tính từ lúc bắt đầu ca, bình quân hai tuần cố định hay trượt kèm mức nghỉ cảnh báo và chặn, hai điểm của bản 2024 mà RFP không tóm (giới hạn số ngày dài, khả năng nâng trần nhờ 労使協定). Nhóm vận tải, người trả lời là trưởng phòng vận tải và chủ dự án khách: nguồn giờ làm thực tế, chặn công bố hay chỉ chặn lúc xuất phát với khung giờ dịch vụ, cách đánh dấu sự kiện có bằng chứng, xác nhận luồng kéo dài khẩn cấp, quyết định D-12.
### Định nghĩa xong
- [ ] Mỗi câu có mã nguồn LAB-4 (ADR-010 câu 1 đến 7, RTE_DRIVER_DUTY_CHECK câu 1 đến 5, SCR017 câu 4 và 6, TRANSPORT T-02 và T-09), tham số bị ảnh hưởng, người trả lời, hạn trả lời, việc bị chặn nếu chậm
- [ ] Văn bản không đưa số liệu pháp lý nào ngoài các số LAB-4 đã ghi, và gắn nhãn rõ số nào là tóm tắt RFP, số nào là cách hiểu nhà thầu chưa xác nhận
- [ ] Văn bản được trưởng nhóm duyệt về tiếng Nhật lịch sự (keigo) trước khi gửi
- [ ] Mỗi câu trả lời của khách được ghi vào log quyết định kèm người trả lời và ngày; câu trả lời đổi giá trị thì sinh việc tạo phiên bản luật mới (E09-I01-S02), không sửa mã
- [ ] Các dòng chỗ hở GAP-61 đến GAP-68 được đóng hoặc chuyển trạng thái kèm dẫn chứng sau mỗi lần khách trả lời
### Chỗ hở
- GAP-61 các trần 拘束時間, JP-13, JP-14 chưa đối chiếu với 改善基準告示; mốc kiểm lại trước khi đóng băng thiết kế đã trôi qua

## E09-I09-S02 · Hồ sơ bằng chứng đối chiếu từng tham số của phiên bản luật với văn bản chính thức (BrSE)
- Loại: sub-issue
- Cha: E09-I09
- Vai: BrSE
- Nhãn: type:sub-issue, type:clarification, constraint:jp-2024-driver-hours
- Spec: OPS-LAW-01, ADR-010, JP-11, JP-12, JP-13
### Mô tả
Hồ sơ là tệp đính kèm làm bằng chứng cho phiên bản luật dùng ở môi trường thật, theo yêu cầu kiểm lại nguồn chính thức hai lần (trước khi đóng băng thiết kế và trước khi chạy thật). Mỗi dòng của hồ sơ là một tham số của phiên bản luật, gắn giá trị đang dùng, nguồn của giá trị, điều khoản đối chiếu, người và ngày đối chiếu. Hồ sơ phân ba nhóm: số lấy từ tóm tắt RFP cần xác thực, cách hiểu nhà thầu chưa khách xác nhận, và giá trị không phải luật lao động (khung giờ dịch vụ).
### Định nghĩa xong
- [ ] Mẫu hồ sơ liệt đủ mọi tham số của phiên bản luật theo bảng tham số của thiết kế, mỗi dòng đủ các cột nêu trên
- [ ] Ba nhóm giá trị được tách rõ trong hồ sơ; mỗi giá trị chưa có văn bản hoặc xác nhận khách ghi "chưa xác thực", không điền giá trị suy đoán
- [ ] Quy trình ghi rõ hồ sơ hoàn chỉnh được tải lên làm tham chiếu bằng chứng của phiên bản trước khi trưởng phòng vận tải duyệt, người duyệt khác người đề xuất
- [ ] Hai thời điểm đối chiếu (trước khi đóng băng thiết kế và trước khi chạy thật) được ghi vào kế hoạch, thời điểm trước đã trôi qua nên ghi là độ lệch đã biết (khai ở PREMISE), bước đối chiếu trước khi chạy thật vẫn bắt buộc
- [ ] Hồ sơ mẫu đã điền thử với phiên bản mẫu nguồn "RFP tóm tắt" cho thấy mọi dòng đang ở trạng thái chưa xác thực
### Chỗ hở
- GAP-61 các trần 拘束時間, JP-13, JP-14 chưa đối chiếu với 改善基準告示; mốc kiểm lại trước khi đóng băng thiết kế đã trôi qua

## E09-I09-S03 · Kiểm không viết cứng ngưỡng và đối chiếu dữ liệu mẫu với LAB-4 (QA)
- Loại: sub-issue
- Cha: E09-I09
- Vai: QA-1
- Nhãn: type:sub-issue, constraint:jp-2024-driver-hours
- Spec: ADR-010, JP-11, JP-12, JP-13, JP-14
- Phụ thuộc: E09-I04-S01, E09-I04-S02
### Mô tả
Kiểm tra độc lập bảo vệ nguyên tắc của ADR-010: đổi luật không cần phát hành mã. Chạy sau khi các luật kiểm xong và trước khi hồ sơ bằng chứng được dùng để duyệt phiên bản thật; ghi lại để chứng minh với người đối chiếu pháp lý rằng giá trị đang chạy đúng là giá trị trong phiên bản luật.
### Định nghĩa xong
- [ ] Quét mã nguồn bộ kiểm và API xác nhận không có hằng số ngưỡng (giờ, phút, ngày bắt đầu năm, khung giờ) nằm ngoài dữ liệu phiên bản luật; kết quả quét lưu trong tài liệu
- [ ] Đối chiếu từng tham số của phiên bản mẫu với danh mục JP của LAB-4, báo cáo khớp hoặc lệch từng dòng
- [ ] Test chạy bộ kiểm với hai phiên bản luật khác giá trị cho cùng tuyến, kết quả đổi theo phiên bản mà không sửa mã
- [ ] Test không hồi tố: tuyến đã công bố giữ nguyên kết quả khi phiên bản mới có hiệu lực
- [ ] Kết quả gửi cùng hồ sơ bằng chứng cho người duyệt phiên bản
