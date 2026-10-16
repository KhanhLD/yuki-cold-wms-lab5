# E08 · Lập tuyến giao hàng
<!-- lab5-epic: E08 -->

## E08 · Lập tuyến giao hàng
- Loại: epic
- Nhãn: type:epic
- Spec: SCR016, SCR018, TRANSPORT_DB, FR-SCH-01, FR-SCH-04, DR-ROUTE-01, IF-MAP-01
### Mô tả
Phần lập và theo dõi tuyến của module giao vận, không gồm luật giờ làm của tài xế (luật đó thuộc E09). Điều phối viên gom các đơn đã phân bổ theo ngày giao và dải nhiệt thành một chuyến, chọn xe đúng dải nhiệt, chọn tài xế, đặt giờ xuất phát, lấy ước tính thời gian chạy từ dịch vụ bản đồ có bản quyền, rồi quản lý vòng đời tuyến (nháp, đã kiểm, đã công bố, đang chạy, hoàn tất, huỷ) kèm lịch sử phiên bản. Tài xế mở bản tuyến công bố mới nhất trên PWA và thấy điểm khác so với bản trước. Cả SCR016 và SCR018 mới có wireframe, 6 trong 7 API của nhóm này chưa có hợp đồng nên mỗi issue làm màn hoặc API phải tự chi tiết hoá hợp đồng trong định nghĩa xong. Việc kiểm giờ lái, chặn công bố theo luật, kéo dài khẩn cấp, sự kiện bất ngờ và báo cáo kế hoạch / thực tế nằm ở E09; E08 chỉ cung cấp nền tuyến cho các việc đó.
### Chỗ hở
- GAP-53 cấu trúc phần tử của các cột jsonb của phiên bản tuyến chưa được LAB-4 chốt
- GAP-54 danh mục depot chưa có chủ và khoá depot ở môi trường thật đang để trống
- GAP-55 dịch vụ bản đồ: thông tin hợp đồng, giới hạn gọi và nơi lưu bộ nhớ đệm chưa chốt
- GAP-39 bảng khung giờ nhận và thời gian dừng của khách chưa có API hay màn ghi
- GAP-60 nhiều API của nhóm tuyến chưa có hợp đồng

## E08-I01 · Mô hình dữ liệu tuyến, phiên bản tuyến và đơn thuộc tuyến
- Loại: issue
- Cha: E08
- Vai: BE-1
- Nhãn: type:issue
- Spec: TRANSPORT_DB, DR-ROUTE-01
### Mô tả
Dựng phần tuyến của schema giao vận: bảng tuyến (bảng chiếu trạng thái), bảng phiên bản tuyến (sổ cái, bản đã công bố bất biến) và bảng đơn thuộc từng phiên bản. Chỉ lưu ID của module khác, không khoá ngoại xuyên schema. Phần luật giờ làm (phiên bản luật, giờ làm thực tế, yêu cầu kéo dài, sự kiện bất ngờ) thuộc E09; bảng ước tính di chuyển thuộc E08-I03. LAB-4 chỉ ghi các cột điểm dừng, chặng, đoạn ca kế hoạch là "mảng jsonb" nên issue này phải chốt thành hợp đồng viết ra.
### Định nghĩa xong
- [ ] Migration tạo ba bảng tuyến, phiên bản tuyến, đơn thuộc tuyến đúng cột, ràng buộc và chỉ mục của thiết kế TRANSPORT_DB; chạy lên và xuống không lỗi trên cơ sở dữ liệu trống
- [ ] Ràng buộc hình dạng trạng thái (đã công bố phải có số phiên bản công bố, đã huỷ phải có lý do, các trạng thái còn lại chưa có giờ thực tế) có test chứng minh dữ liệu sai bị chặn
- [ ] Phiên bản tuyến đã công bố và bảng đơn thuộc tuyến từ chối UPDATE, DELETE, TRUNCATE (trigger và quyền chỉ INSERT), có test
- [ ] Khoá lạc quan `version` của bảng tuyến chạy đúng: hai lệnh cùng `version`, một lệnh nhận lỗi xung đột
- [ ] Mã tuyến sinh từ sequence, đúng dạng tiền tố RT- cộng số
- [ ] Tài liệu hợp đồng cấu trúc phần tử của điểm dừng, chặng, đoạn ca kế hoạch và điểm nghỉ chèn (khoá, kiểu, đơn vị) nằm trong repo và được E08-I02, E09-I03 tham chiếu
- [ ] Hợp đồng giao diện đặt tên cho module giao hàng đọc chuyến đã công bố (mã chuyến, ngày, dải nhiệt, xe, phiên bản kế hoạch, giờ xuất phát kế hoạch, đơn theo thứ tự giao) và hai cổng nội bộ do giao vận khai báo (trạng thái xếp hàng và xuất phát của chuyến, tiến độ POD) được viết ra; phần hiện thực hai cổng ở E07
### Chỗ hở
- GAP-53 cấu trúc phần tử của các cột jsonb của phiên bản tuyến chưa được LAB-4 chốt

## E08-I02 · Lập tuyến từ đơn đã phân bổ
- Loại: issue
- Cha: E08
- Nhãn: type:issue
- Spec: SCR016, API-RTE-05, API-RTE-06, FR-SCH-01
- Phụ thuộc: E08-I01
### Mô tả
Điều phối viên chọn ngày giao và dải nhiệt, thấy các đơn xếp được và các đơn chưa xếp kèm lý do, chọn đơn vào một chuyến, chọn xe, tài xế và giờ xuất phát rồi tạo tuyến bản nháp. SCR016 chỉ có wireframe và hai API chưa có hợp đồng, nên cả hợp đồng lẫn màn được làm trong issue này, chia hai phần BE và FE chạy song song.
### Chỗ hở
- GAP-54 danh mục depot chưa có chủ và khoá depot ở môi trường thật đang để trống
- GAP-39 bảng khung giờ nhận và thời gian dừng của khách chưa có API hay màn ghi
- GAP-56 số điểm nghỉ hiện lúc tạo tuyến nhưng bộ chèn nghỉ nằm ở bộ kiểm giờ lái, chưa rõ ai tính lúc tạo
- GAP-60 nhiều API của nhóm tuyến chưa có hợp đồng

## E08-I02-S01 · API đơn chưa xếp tuyến và API tạo tuyến (BE)
- Loại: sub-issue
- Cha: E08-I02
- Vai: BE-1
- Nhãn: type:sub-issue
- Spec: API-RTE-05, API-RTE-06, FR-SCH-01, JP-18
- Phụ thuộc: E08-I01, E03-I04-S01
### Mô tả
Hai API của SCR016. API đọc trả các đơn đã phân bổ của một ngày giao và dải nhiệt chưa nằm trong tuyến nào, mỗi đơn kèm khung giờ nhận và thời gian phục vụ của điểm giao (lấy từ dữ liệu khách của module master) và lý do chưa xếp. API ghi tạo tuyến bản nháp từ các đơn đã chọn: kiểm xe, tài xế, giờ xuất phát, dựng lịch giờ cho từng điểm dừng, ghi tuyến, phiên bản đầu và đơn thuộc tuyến trong một giao dịch. Khung giờ dịch vụ ở đây chỉ là kiểm lúc nhập giờ xuất phát; việc chặn công bố theo khung giờ là của E09-I04-S01.
### Định nghĩa xong
- [ ] Tài liệu hợp đồng hai API (tham số, phản hồi, mã lỗi dùng lại từ bảng lỗi chung của LAB-4) viết xong và được đối chiếu với wireframe SCR016 và dòng api-list
- [ ] API đọc phân loại đúng ba nhóm lý do chưa xếp: chưa phân bổ xong, không còn xe cùng dải nhiệt trong ngày, xếp được nhưng chưa xếp
- [ ] API ghi chặn xe sai dải nhiệt, xe hoặc tài xế đã thuộc tuyến khác cùng ngày, đơn không hợp lệ (sai ngày, đã nằm tuyến khác, chưa phân bổ đủ); mỗi trường hợp có test
- [ ] Giờ xuất phát ngoài khung dịch vụ bị từ chối khi nhập, đúng ranh khung vẫn nhận (tạm tính giờ đúng ranh là trong khung, khung lấy từ phiên bản luật, cho tới khi khách trả lời ở gói làm rõ E09-I09; xem GAP-66 ở bảng chỗ hở của bản phân rã)
- [ ] Lịch giờ từng điểm dừng (đến, chờ, phục vụ, đi, giờ về depot) do một hàm dùng chung dựng ra, E09-I03-S01 gọi lại hàm này, không viết hai lần
- [ ] Gửi lại cùng khoá idempotency không tạo tuyến thứ hai
- [ ] Khung giờ nhận và thời gian phục vụ của khách đọc qua giao diện đặt tên của module master (bảng do E03 sở hữu); khách thiếu dữ liệu này trả mã lỗi dành riêng (YCL-RTE-027)
### Chỗ hở
- GAP-56 số điểm nghỉ hiện lúc tạo tuyến nhưng bộ chèn nghỉ nằm ở bộ kiểm giờ lái, chưa rõ ai tính lúc tạo
- GAP-39 bảng khung giờ nhận và thời gian dừng của khách chưa có API hay màn ghi
- GAP-66 chặn công bố theo khung giờ dịch vụ là cách hiểu nhà thầu; biên khung chưa rõ; khung nằm ở hai nơi

## E08-I02-S02 · Màn SCR016: chọn đơn, xe, tài xế, giờ xuất phát và tạo tuyến (FE)
- Loại: sub-issue
- Cha: E08-I02
- Vai: FE-1
- Nhãn: type:sub-issue
- Spec: SCR016, FR-SCH-01
- Phụ thuộc: E08-I02-S01
### Mô tả
Dựng toàn bộ SCR016 theo wireframe: khối chọn ngày và dải nhiệt, bảng đơn xếp được có hộp chọn, khối chọn xe, tài xế, depot, giờ xuất phát, thông báo tạo tuyến thành công kèm liên kết kiểm giờ lái, và bảng đơn chưa xếp có lý do. Trạng thái xe sai dải nhiệt và ước tính di chuyển thiếu hoặc quá cũ phải hiện rõ. Liên kết sang SCR017 do E09-I07-S03 bổ sung.
### Định nghĩa xong
- [ ] Chọn ngày và dải nhiệt làm bảng đơn xếp được cập nhật; trạng thái rỗng, đang tải và lỗi mạng đều hiện
- [ ] Chọn xe không đúng dải nhiệt bị chặn tại trường và có câu lỗi theo bảng lỗi chung
- [ ] Ô giờ xuất phát báo lỗi tại trường khi ngoài khung dịch vụ
- [ ] Nút làm mới ước tính di chuyển gọi E08-I03; thông báo ước tính quá cũ hoặc thiếu chặng hiện đúng chỗ
- [ ] Tạo tuyến thành công hiện thông báo mã tuyến và giờ về dự kiến; bảng đơn chưa xếp cập nhật theo
- [ ] Màn chỉ vào được với vai điều phối, trưởng phòng vận tải, quản trị; vai khác nhận trang lỗi quyền
- [ ] Nhãn tiếng Nhật theo bản wireframe đã duyệt; khu vực depot dùng nguồn chốt ở E08-I07

## E08-I03 · Ước tính thời gian chạy qua dịch vụ bản đồ có bản quyền
- Loại: issue
- Cha: E08
- Vai: BE-2
- Nhãn: type:issue
- Spec: API-RTE-07, IF-MAP-01, TRANSPORT_DB, ADR-008
- Phụ thuộc: E08-I01, E08-I07, E13-I01-S01, E13-I01-S02
### Mô tả
Tuyến cần thời gian chạy giữa các điểm. Phía giao vận khai báo cổng nội bộ lấy ước tính thời gian; module tích hợp hiện thực cổng bằng một bộ nối gọi dịch vụ bản đồ có bản quyền theo lô chặng của một tuyến, có thời gian chờ tối đa và bộ nhớ đệm theo khoá cấu hình. Kết quả lưu ở bảng ước tính (mỗi cặp điểm một dòng, ghi đè theo cặp). Ước tính thiếu hoặc quá cũ thì tuyến không công bố được; cổng chặn đó nằm ở E09-I07-S01. Khung chạy và giám sát tích hợp chung (ADR-008) do E13 dựng, issue này viết bộ nối bản đồ cụ thể và phần giao vận. Bản đồ không trả lời thì không tự đoán thời gian.
### Định nghĩa xong
- [ ] Tài liệu hợp đồng API-RTE-07 (làm mới ước tính theo lô chặng của một tuyến) viết xong, có mã lỗi cho thiếu chặng và dịch vụ không phản hồi
- [ ] Cổng khai báo ở module giao vận, bộ nối ở module tích hợp; kiểm tra ranh giới module trong CI không cho giao vận gọi thẳng bộ nối
- [ ] Bảng ước tính ghi đè theo cặp điểm (thứ tự chữ cái), cập nhật mốc ước tính cũ nhất của phiên bản tuyến; có test ghi đè
- [ ] Lỗi, hết thời gian chờ hoặc thiếu một chặng trả lỗi rõ và không ghi ước tính giả; thời gian chờ và thời gian sống bộ nhớ đệm đọc từ khoá cấu hình tích hợp bản đồ
- [ ] Test với bản giả lập của dịch vụ bản đồ (chỉ trong test) phủ thành công, hết thời gian chờ và thiếu chặng
- [ ] Thông tin kết nối và giới hạn gọi của dịch vụ thật đọc từ cấu hình môi trường, không có trong mã
### Chỗ hở
- GAP-55 dịch vụ bản đồ: thông tin hợp đồng, giới hạn gọi và nơi lưu bộ nhớ đệm chưa chốt

## E08-I04 · Quản lý tuyến trên SCR018: danh sách, chi tiết, vòng đời, tiến độ, lịch sử phiên bản
- Loại: issue
- Cha: E08
- Nhãn: type:issue
- Spec: SCR018, API-RTE-01, API-RTE-10, FR-SCH-04
- Phụ thuộc: E08-I01
### Mô tả
Phần SCR018 phục vụ điều phối theo dõi tuyến: danh sách theo ngày và trạng thái, chi tiết một tuyến, đổi trạng thái chạy hoặc huỷ, tiến độ theo điểm POD (không theo dõi GPS liên tục) và lịch sử các phiên bản. Nút công bố, sửa sau công bố và các thông báo chặn theo luật giờ lái do E09-I07 gắn vào cùng màn. Màn mới có wireframe, API-RTE-01 đã có hợp đồng, API-RTE-10 chưa có.
### Chỗ hở
- GAP-57 chưa nói API nào trả chỉ số tuyến sửa sau công bố

## E08-I04-S01 · API đọc tuyến và đổi trạng thái tuyến (BE)
- Loại: sub-issue
- Cha: E08-I04
- Vai: BE-2
- Nhãn: type:sub-issue
- Spec: API-RTE-01, API-RTE-10, FR-SCH-04
- Phụ thuộc: E08-I01, E07-I06-S01
### Mô tả
Hiện thực API-RTE-01 theo hợp đồng có sẵn cho danh sách, chi tiết và tiến độ POD, và chi tiết hoá rồi hiện thực API-RTE-10 đổi trạng thái. Các khối kết quả kiểm giờ lái, yêu cầu kéo dài và sự kiện trả thêm ở E09-I05-S01. Tiến độ POD đọc qua cổng nội bộ do module giao hàng hiện thực, module giao vận không gọi thẳng.
### Định nghĩa xong
- [ ] Danh sách lọc theo ngày, trạng thái, tài xế; sắp xếp theo các khoá hợp đồng cho phép; phân trang theo khoá cỡ trang chung
- [ ] Chi tiết trả phiên bản kế hoạch, điểm dừng, phiên bản đã công bố và tiến độ theo điểm POD qua cổng nội bộ; không có điểm GPS
- [ ] Tài xế chỉ đọc tuyến của mình và chỉ bản đã công bố; test chứng minh tài xế khác bị từ chối
- [ ] Tài liệu hợp đồng API-RTE-10 viết xong; chuyển trạng thái sai thứ tự bị từ chối, huỷ bắt buộc lý do, bắt đầu và kết thúc ghi giờ thực tế
- [ ] Danh sách trả kèm chỉ số "đã công bố / đã sửa sau công bố" trong tháng (tạm để danh sách tuyến tự trả chỉ số này, chưa cần API báo cáo của một tuyến; xem GAP-57 ở bảng chỗ hở của bản phân rã)
### Chỗ hở
- GAP-57 chưa nói API nào trả chỉ số tuyến sửa sau công bố

## E08-I04-S02 · Màn SCR018: danh sách, chi tiết, vòng đời, tiến độ, lịch sử phiên bản (FE)
- Loại: sub-issue
- Cha: E08-I04
- Vai: FE-1
- Nhãn: type:sub-issue
- Spec: SCR018, FR-SCH-04
- Phụ thuộc: E08-I04-S01
### Mô tả
Dựng khung SCR018 theo wireframe: chỉ số tuyến sửa sau công bố, bảng tuyến theo ngày, khung chi tiết tuyến, khối thao tác đổi trạng thái, bảng phiên bản (giữ cả bản cũ) và bảng điểm giao kèm tiến độ POD. Các phần của luật giờ lái (nút công bố, sửa, báo chặn, danh sách chờ duyệt kéo dài, ghi sự kiện, báo cáo ba phần) được E09 gắn vào chính màn này nên khung phải chừa chỗ.
### Định nghĩa xong
- [ ] Bảng tuyến hiện đủ cột, trạng thái rỗng, chuyển sang chi tiết và đổi trạng thái hoạt động
- [ ] Bảng phiên bản liệt kê cả bản cũ kèm giờ xuất, giờ về, xe, tài xế, thời điểm công bố, lý do sửa
- [ ] Bảng điểm giao hiện giờ đến, thời gian chờ, giờ đi, cờ trễ khung giờ, trạng thái POD; có dòng giải thích không dùng GPS
- [ ] Nút đổi trạng thái chỉ hiện đúng với trạng thái và vai hiện tại; huỷ yêu cầu nhập lý do
- [ ] Chi tiết một tuyến mở được trực tiếp bằng đường dẫn có mã tuyến (màn kiểm trước xuất phát của E07 liên kết sang)
- [ ] Loading, lỗi mạng và lỗi quyền hiện đúng; vai tài xế không vào được phần quản lý này
- [ ] Vùng gắn nút công bố và khối của E09 có chỗ cố định, được liệt kê trong tài liệu bàn giao cho E09-I07-S03

## E08-I05 · Tuyến của tôi cho tài xế trên PWA
- Loại: issue
- Cha: E08
- Nhãn: type:issue
- Spec: SCR018, API-RTE-13, FR-SCH-04
- Phụ thuộc: E08-I04
### Mô tả
Tài xế mở PWA và luôn thấy bản tuyến công bố mới nhất của mình, kèm các điểm khác so với bản trước (giờ xuất phát, xe, tài xế). Đây là màn tài xế của SCR018, vẽ ở bề rộng điện thoại. Khung PWA, cài đặt và hàng đợi POD offline là việc của E07; issue này là bên dùng khung đó. Tài xế không thấy kết quả kiểm giờ lái, sự kiện hay yêu cầu kéo dài.
### Chỗ hở
- GAP-59 chưa nói tài xế có xem được tuyến khi mất mạng không

## E08-I05-S01 · API tuyến của tôi (BE)
- Loại: sub-issue
- Cha: E08-I05
- Vai: BE-2
- Nhãn: type:sub-issue
- Spec: API-RTE-13, FR-SCH-04
- Phụ thuộc: E08-I01
### Mô tả
Chi tiết hoá và hiện thực API-RTE-13: trả cho tài xế đang đăng nhập bản tuyến đã công bố mới nhất của họ kèm danh sách điểm khác với bản công bố liền trước. Chỉ dùng dữ liệu đã công bố.
### Định nghĩa xong
- [ ] Tài liệu hợp đồng API-RTE-13 viết xong, đối chiếu với trạng thái màn tài xế của wireframe SCR018
- [ ] Trả đúng bản công bố mới nhất của tài xế; có phiên bản mới thì lần gọi sau trả bản mới
- [ ] Danh sách khác biệt liệt kê giờ xuất phát, xe, tài xế đã đổi so với bản trước; bản đầu không có khác biệt
- [ ] Phản hồi không chứa kết quả kiểm giờ lái, sự kiện hay yêu cầu kéo dài; có test kiểm trường
- [ ] Tài xế không có tuyến công bố nhận phản hồi rỗng rõ nghĩa; chỉ định danh của chính mình đọc được, vai khác bị từ chối

## E08-I05-S02 · Màn tuyến của tôi trên PWA (FE)
- Loại: sub-issue
- Cha: E08-I05
- Vai: FE-1
- Nhãn: type:sub-issue
- Spec: SCR018, API-RTE-13
- Phụ thuộc: E08-I05-S01, E07-I07-S01
### Mô tả
Dựng màn tài xế bề rộng khoảng 390 px chạy trong khung PWA của E07: số phiên bản và thời điểm công bố, danh sách điểm khác với bản trước, trạng thái chưa có tuyến, và hiển thị điểm giao theo thứ tự.
### Định nghĩa xong
- [ ] Màn hiện số phiên bản, thời điểm công bố và các điểm đổi so với bản trước; bản đầu hiện thông báo không có thay đổi
- [ ] Trạng thái chưa có tuyến công bố hiện câu riêng
- [ ] Hiển thị không vỡ bố cục ở bề rộng điện thoại và không cuộn ngang
- [ ] Gọi API qua khung PWA của E07, mất mạng hiện lỗi rõ và không hiện dữ liệu cũ như dữ liệu mới
- [ ] Tài xế không thấy bất kỳ khối nào dành cho điều phối

## E08-I06 · Cấp và thay seal cho chuyến (API-RTE-12)
- Loại: issue
- Cha: E08
- Nhãn: type:issue
- Spec: API-RTE-12, SCR018
- Phụ thuộc: E08-I01, E07-I01
### Mô tả
Cấp và thay số seal của chuyến, được các màn xếp hàng và kiểm trước xuất phát đọc lại. Gồm hai phần: BE (lệnh ghi API-RTE-12) và FE (khối thao tác seal trên SCR018, chủ dự án đã chốt thêm vào màn theo dòng PREMISE sửa SCR018). Dữ liệu seal thuộc module giao hàng (bảng seal riêng, mỗi lần cấp hoặc thay là một dòng, seal hiện hành là dòng mới nhất) và thuộc phạm vi E07; API chưa có hợp đồng, và wireframe SCR018 của LAB-4 chưa vẽ khối seal nên phần FE gồm cả bản phác bố cục.
### Chỗ hở
- GAP-58 wireframe SCR018 chưa có khối thao tác seal dù màn xếp hàng nói seal được cấp ở đó

## E08-I06-S01 · API cấp và thay seal (BE)
- Loại: sub-issue
- Cha: E08-I06
- Vai: BE-1
- Nhãn: type:sub-issue
- Spec: API-RTE-12
- Phụ thuộc: E08-I01, E07-I01
### Mô tả
Lệnh ghi cấp và thay số seal của chuyến do SCR018 gọi. Chưa có hợp đồng nên viết hợp đồng rồi hiện thực; bảng seal do E07-I01 sở hữu.
### Định nghĩa xong
- [ ] Tài liệu hợp đồng API-RTE-12 viết xong, dùng lại các mã lỗi seal của bảng lỗi chung (sai định dạng, đã cấp cho chuyến khác, đã khoá, chuyến đã xuất phát)
- [ ] Số seal sai định dạng bị chặn, số đã cấp cho chuyến khác bị chặn, mỗi trường hợp có test
- [ ] Chuyến đã bắt đầu xếp hàng với seal hiện tại hoặc đã xuất phát thì không đổi seal được
- [ ] Lệnh khoá dòng chuyến trước khi đọc seal hiện hành, cùng thứ tự khoá với kiểm trước xuất phát của E07; test hai lệnh đồng thời
- [ ] Mỗi lần thay ghi thêm một dòng seal mới, không sửa dòng cũ

## E08-I06-S02 · Khối cấp và thay seal trên màn SCR018 (FE)
- Loại: sub-issue
- Cha: E08-I06
- Vai: FE-1
- Nhãn: type:sub-issue
- Spec: SCR018, API-RTE-12
- Phụ thuộc: E08-I06-S01, E08-I04-S02
### Mô tả
Thêm vào chi tiết tuyến của SCR018 một khối thao tác seal: hiện seal hiện hành, cấp seal lần đầu hoặc thay seal. Khối chưa có trong wireframe LAB-4 nên bản phác bố cục phải được BrSE duyệt trước khi dựng; khối gắn vào vùng chừa sẵn của khung SCR018 ở E08-I04-S02.
### Định nghĩa xong
- [ ] Bản phác bố cục khối seal được BrSE duyệt bằng văn bản trước khi bắt đầu dựng
- [ ] Khối hiện seal hiện hành của tuyến (hoặc trạng thái chưa cấp) và nút cấp hoặc thay seal chỉ với vai được phép
- [ ] Nhập số seal báo lỗi tại trường khi sai định dạng hoặc đã cấp cho chuyến khác, theo mã lỗi của server
- [ ] Chuyến đã xếp hàng với seal hiện tại hoặc đã xuất phát thì khối khoá nút và giải thích lý do
- [ ] Thay seal thành công cập nhật seal hiện hành; có trạng thái đang tải và lỗi mạng

## E08-I07 · Làm rõ depot và dịch vụ bản đồ với khách
- Loại: issue
- Cha: E08
- Vai: BrSE
- Nhãn: type:issue, type:clarification
- Spec: IF-MAP-01, TRANSPORT_DB
### Mô tả
Hai việc cần khách trả lời trước khi lập tuyến chạy được thật. Một là danh mục depot: LAB-4 chưa chốt depot là dữ liệu master hay khoá cấu hình, và khoá depot ở môi trường thật đang để trống nên hệ thống không khởi động được cho tới khi khách cấp. Hai là dịch vụ bản đồ: thông tin hợp đồng, giới hạn số lần gọi và nơi lưu bộ nhớ đệm ước tính. Thêm một câu về dữ liệu: khung giờ nhận hàng và thời gian dừng của từng khách là dữ liệu của khách nhưng LAB-4 không có API hay màn nào ghi bảng này. Chặn E08-I02 và E08-I03.
### Định nghĩa xong
- [ ] Văn bản câu hỏi gửi khách bằng tiếng Nhật lịch sự, nêu từng câu kèm mã nguồn LAB-4, việc bị chặn nếu chưa trả lời và người trả lời (CNTT khách, trưởng phòng vận tải)
- [ ] Câu trả lời của khách về depot được ghi vào log quyết định kèm hướng chốt: bảng master hay khoá cấu hình, danh sách depot thật
- [ ] Câu trả lời về dịch vụ bản đồ ghi rõ nhà cung cấp, cách cấp thông tin kết nối, giới hạn gọi; nơi lưu bộ nhớ đệm ước tính được chốt giữa giao vận và tích hợp
- [ ] Câu trả lời về nguồn dữ liệu khung giờ nhận và thời gian dừng của khách (khách cấp, nạp ban đầu hay sửa trên màn khách) được ghi vào log quyết định và chuyển cho E03-I04-S01 và E03-I04-S02
- [ ] E08-I02-S02 và E08-I03 cập nhật theo câu trả lời, các dòng chỗ hở đóng lại có dẫn chứng
### Chỗ hở
- GAP-54 danh mục depot chưa có chủ và khoá depot ở môi trường thật đang để trống
- GAP-55 dịch vụ bản đồ: thông tin hợp đồng, giới hạn gọi và nơi lưu bộ nhớ đệm chưa chốt
- GAP-39 bảng khung giờ nhận và thời gian dừng của khách chưa có API hay màn ghi
