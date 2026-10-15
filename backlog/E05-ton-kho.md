# E05 · Tồn kho
<!-- lab5-epic: E05 -->

## E05 · Tồn kho
- Loại: epic
- Nhãn: type:epic
- Spec: INVENTORY_DB, SCR005, SCR006, SCR007, SCR008, SCR034
### Mô tả
Phần tồn kho sau khi hàng đã vào kho: tìm lô và xem chi tiết, dịch chuyển cả lô giữa các vị trí, đổi trạng thái lô theo máy trạng thái (khả dụng, cách ly, tạm giữ, giải phóng hoặc huỷ), kiểm kho mù, phiếu chuyển dải nhiệt tạm, và bản đồ vị trí kho của giai đoạn 2. Issue đầu tiên dựng lược đồ INVENTORY_DB dùng chung cho cả nhập kho; các issue khác dựng trên đó. Phần luật người duyệt giải phóng sau cảnh báo nhiệt (thiết kế kỹ thuật phê duyệt giải phóng) thuộc epic nhiệt độ, epic này chỉ giữ điểm cuối và màn. Việc đảo dòng nhận nằm ở epic nhập kho. Một issue làm rõ gom các câu hỏi khách chặn hoặc đổi thiết kế.

## E05-I01 · Lược đồ INVENTORY_DB và sổ cái bất biến
- Loại: issue
- Cha: E05
- Vai: BE-1
- Nhãn: type:issue
- Spec: INVENTORY_DB, ADR-003, DR-LOT-01
### Mô tả
Tạo toàn bộ bảng của module tồn kho: lô (bảng chiếu có phiên bản), dòng nhận, sổ di chuyển, đề nghị đổi trạng thái, sự kiện trạng thái, dấu chặn lô, phiên kiểm kho và dòng kiểm, phiếu dải nhiệt tạm. Các bảng sổ cái chỉ được thêm, không sửa, không xoá. Dấu chặn lô là nơi chung để module nhiệt độ và module truy xuất đánh dấu lô, nên hai lệnh đặt và gỡ dấu cần ổn định sớm vì nhiều epic dựa vào. Các issue của E04 và E05 đều phụ thuộc issue này.
### Định nghĩa xong
- [ ] Migration tạo đủ chín bảng, chạy sạch trên cơ sở dữ liệu trống, có kiểm thử khởi tạo lại
- [ ] Tài khoản ứng dụng chỉ có quyền thêm và đọc trên các bảng sổ cái; thử sửa, xoá hoặc làm rỗng bị từ chối, có kiểm thử
- [ ] Ràng buộc dữ liệu theo thiết kế: mã bò đúng 10 chữ số, lô và vị trí rỗng khi và chỉ khi dòng bị từ chối, người duyệt khác người đếm, trạng thái đề nghị trong tập cho phép
- [ ] Lệnh đặt và gỡ dấu chặn lô dùng chung cho nhiệt độ và thu hồi có kiểm thử, hợp đồng lệnh được gửi cho chủ epic nhiệt độ và truy xuất
- [ ] Chỉ mục cho truy vấn danh sách lô theo SKU, hạn, vị trí, trạng thái; ghi số đo trên dữ liệu mẫu
- [ ] Bộ dữ liệu mẫu nhỏ cho các issue sau, không dữ liệu thật
### Chỗ hở
- GAP-37 chưa có nhóm lưu giữ cho tám bảng, chưa có bảng đơn vị tồn kho, chưa có nguồn định danh thiết bị kho, chưa quyết trạng thái "đang thu hồi" của lô

## E05-I02 · Tìm lô và chi tiết lô (API-INV-01 và phần đọc của SCR006)
- Loại: issue
- Cha: E05
- Nhãn: type:issue
- Spec: SCR006, API-INV-01, FR-INV-06
- Phụ thuộc: E05-I01
### Mô tả
Người dùng kho, QC và kinh doanh tìm lô theo tổ hợp tiêu chí (SKU, mã lô, nhà cung cấp, trạng thái, khoảng hạn, dải nhiệt, vị trí) rồi mở chi tiết lô kèm lịch sử chuyển động. Kinh doanh chỉ xem. Danh sách luôn sắp theo thứ tự hạn sớm trước nhưng đó chỉ là thứ tự đọc, không phải kết quả phân bổ. Cùng điểm cuối được màn cách ly dùng.
### Định nghĩa xong
- [ ] Tìm nhiều tiêu chí rồi mở chi tiết một lô và đọc lịch sử chạy được trên môi trường thử
- [ ] Vai kinh doanh chỉ xem, không thấy khối dịch chuyển

## E05-I02-S01 · API đọc lô: danh sách, chi tiết, lịch sử (BE)
- Loại: sub-issue
- Cha: E05-I02
- Vai: BE-2
- Nhãn: type:sub-issue
- Spec: API-INV-01, FR-INV-06, JP-08, INVENTORY_DB
- Phụ thuộc: E05-I01
### Mô tả
Một điểm cuối hai chế độ: danh sách có bộ lọc kết hợp AND và chi tiết một lô kèm lịch sử nhận, di chuyển, sự kiện trạng thái, dấu chặn đang mở và danh sách bước chuyển hợp lệ theo vai của người gọi. Bộ lọc sai không bao giờ bị bỏ qua, và kết quả không bao giờ bị cắt im lặng.
### Định nghĩa xong
- [ ] Bộ lọc kết hợp AND; bộ lọc sai (ngày sai khuôn, khoảng ngày ngược, mã lô sai khuôn) trả lỗi tại ô, không bị bỏ qua
- [ ] Thứ tự mặc định hạn sớm trước, cùng hạn thì giờ nhận sớm trước, rồi mã lô; điểm cuối không đánh giá chuỗi loại trừ phân bổ
- [ ] Giới hạn dòng theo khoá cấu hình; vượt giới hạn trả cờ và đọc trang vượt giới hạn báo lỗi, không cắt im lặng
- [ ] Chi tiết trả lịch sử chuyển động, sự kiện trạng thái, dấu chặn đang mở và các bước chuyển hợp lệ theo vai
- [ ] Lọc được nhiều trạng thái trong một lần đọc và kèm sự kiện trạng thái mới nhất khi yêu cầu
- [ ] Vai kinh doanh chỉ đọc và thấy cả lô cách ly theo ma trận đề xuất; kiểm thử quyền cho từng vai
- [ ] Đo thời gian đọc trên dữ liệu mẫu và đối chiếu ngưỡng hiệu năng chung
### Chỗ hở
- GAP-12 ma trận quyền xem của kinh doanh với lô cách ly và tạm giữ chưa khách xác nhận

## E05-I02-S02 · Màn SCR006 phần tìm lô và chi tiết lô (FE)
- Loại: sub-issue
- Cha: E05-I02
- Vai: FE-1
- Nhãn: type:sub-issue
- Spec: SCR006, API-INV-01
- Phụ thuộc: E05-I02-S01, E03-I02
### Mô tả
Dựng khối tìm (tám tiêu chí), bảng kết quả, chi tiết lô và lịch sử chuyển động theo wireframe. Phân trang dùng thanh chuẩn dưới bảng theo cỡ trang cấu hình, không thêm mục wireframe. Wireframe chưa có chỗ hiện lý do, thời điểm và người cách ly, dấu chặn đang mở, mã truy xuất, số lượng và nhiệt độ từng dòng chuyển động; các phần này cần phác bổ sung và BrSE duyệt trước khi dựng.
### Định nghĩa xong
- [ ] Khối tìm, bảng kết quả, chi tiết lô và lịch sử chuyển động chạy theo wireframe với dữ liệu từ API thật
- [ ] Bản phác bổ sung (lý do cách ly, dấu chặn, mã truy xuất, số lượng và nhiệt độ từng dòng chuyển động) được BrSE duyệt trước khi dựng
- [ ] Phân trang chuẩn dưới bảng theo cỡ trang cấu hình
- [ ] Thông báo vượt giới hạn dòng, không có kết quả, vai chỉ xem hiển thị đúng
- [ ] Từ màn kiểm nhận mở thẳng được chi tiết lô vừa tạo
- [ ] Kiểm thử đầu-cuối cho các tình huống tìm, mở chi tiết, vai chỉ xem, phiên hết hạn
### Chỗ hở
- GAP-28 wireframe chưa có chỗ hiển thị lý do cách ly, dấu chặn, mã truy xuất, số lượng và nhiệt độ từng dòng chuyển động

## E05-I03 · Dịch chuyển cả lô (API-INV-03 và khối dịch chuyển của SCR006)
- Loại: issue
- Cha: E05
- Nhãn: type:issue, constraint:haccp-temp
- Spec: SCR006, API-INV-03, BR-TEMP-02, FR-INV-03
- Phụ thuộc: E05-I02
### Mô tả
Người kho hoặc QC chuyển cả lô sang vị trí khác. Server chặn không ngoại lệ khi dải vị trí đích khác dải SKU hoặc nhiệt độ đo ngoài ngưỡng, không bao giờ để tồn âm, và giữ quy tắc khu cách ly. Thiết kế hiện chỉ cho chuyển cả lô; ô số lượng của wireframe chỉ hiển thị tồn khả dụng. Di chuyển một phần lô thuộc giai đoạn 2 và đang chờ khách.
### Định nghĩa xong
- [ ] Chuyển một lô sang vị trí cùng dải thành công và thấy dòng mới trong lịch sử chuyển động
- [ ] Chuyển sang vị trí khác dải bị chặn, lô ở nguyên chỗ, người dùng thấy lý do

## E05-I03-S01 · API dịch chuyển cả lô (BE)
- Loại: sub-issue
- Cha: E05-I03
- Vai: BE-1
- Nhãn: type:sub-issue, constraint:haccp-temp
- Spec: API-INV-03, BR-TEMP-02, FR-INV-03, ADR-003
- Phụ thuộc: E05-I01, E01-I14, E10-I01-S01
### Mô tả
Điểm cuối chuyển cả lô, ghi một dòng di chuyển và đổi vị trí lô trong một giao dịch. Mọi chặn đều ghi sự kiện để module nhiệt độ mở case sai lệch, phản hồi không chứa mã case.
### Định nghĩa xong
- [ ] Số lượng phải bằng tồn khả dụng của lô; khi tồn đã giảm sau lúc đọc, lỗi trả kèm tồn thực để màn tải lại
- [ ] Dải vị trí đích khác dải SKU hoặc nhiệt độ đo ngoài ngưỡng hiệu lực bị từ chối, lô ở nguyên chỗ, sự kiện chặn được ghi; biên tính là đạt
- [ ] Nhiệt độ đo bắt buộc, tối đa một chữ số thập phân; dải không có ngưỡng hiệu lực trả mã lỗi dữ liệu chủ
- [ ] Lô cách ly và tạm giữ chỉ vào khu cách ly của dải mình; lô khả dụng không vào khu cách ly; lô đã huỷ không di chuyển
- [ ] Khoá lạc quan theo phiên bản lô, khoá idempotency, thời điểm do server tính và không lùi, có ca kiểm riêng cho luật không lùi
- [ ] Chỉ vai kho và QC gọi được; kiểm thử cho từng nhánh chặn trên
- [ ] Sự kiện dịch chuyển mang lane, mã truy xuất và thời điểm nghiệp vụ cho bên nghe truy xuất
- [ ] Ca đối chiếu với hành vi của prototype ghi ở bảng khác prototype của spec có kiểm thử riêng
### Chỗ hở
- GAP-29 thiết kế chỉ chuyển cả lô, di chuyển một phần là cách đọc của nhà thầu chưa khách xác nhận

## E05-I03-S02 · Khối dịch chuyển lô trên SCR006 (FE)
- Loại: sub-issue
- Cha: E05-I03
- Vai: FE-1
- Nhãn: type:sub-issue, constraint:haccp-temp
- Spec: SCR006, API-INV-03
- Phụ thuộc: E05-I03-S01, E05-I02-S02
### Mô tả
Dựng khối chọn vị trí đích, nhiệt độ đo, ô số lượng chỉ đọc hiển thị tồn khả dụng, nút dịch chuyển và các thông báo chặn.
### Định nghĩa xong
- [ ] Khối hiện đúng wireframe; ô số lượng chỉ đọc và gửi đúng giá trị tồn khả dụng
- [ ] Thông báo chặn lệch dải hoặc nhiệt độ ngoài ngưỡng hiện đúng câu và không nêu mã case; thông báo chặn tồn âm kèm tải lại chi tiết
- [ ] Xung đột phiên bản: tải lại chi tiết và không tự gửi lại; gửi lại sau lỗi mạng giữ nguyên khoá idempotency
- [ ] Khối ẩn với vai kinh doanh; vai ngoài ma trận bị chặn
- [ ] Kiểm thử đầu-cuối cho chuyển thành công, chặn lệch dải, chặn nhiệt ngoài ngưỡng, chặn tồn âm
### Chỗ hở
- GAP-29 ô số lượng wireframe gợi ý chuyển một phần nhưng thiết kế hiện chỉ chuyển cả lô

## E05-I04 · Quản lý cách ly và đề nghị giải phóng hoặc huỷ trên SCR008
- Loại: issue
- Cha: E05
- Nhãn: type:issue
- Spec: SCR008, API-INV-04, API-INV-05, FR-INV-05
- Phụ thuộc: E05-I01
### Mô tả
QC và nhân viên kho xem lô đang cách ly hoặc tạm giữ và thao tác theo đúng máy trạng thái: khả dụng, cách ly, tạm giữ, rồi giải phóng về khả dụng hoặc huỷ. Cách ly và tạm giữ là lệnh trực tiếp; giải phóng và huỷ chỉ là đề nghị chờ người khác duyệt ở issue kế tiếp. Cấm nhảy bước, nhất là khả dụng sang huỷ. Quyết định giải phóng hoặc huỷ chỉ nằm ở màn này; màn xử lý case sai lệch nhiệt chỉ có liên kết sang đây.
### Định nghĩa xong
- [ ] Một lô đi từ khả dụng sang cách ly, tạm giữ rồi đề nghị giải phóng chạy đầu-cuối từ màn
- [ ] Thử huỷ thẳng một lô khả dụng bị từ chối và màn hiện lý do

## E05-I04-S01 · API đổi trạng thái lô và danh sách đề nghị (BE)
- Loại: sub-issue
- Cha: E05-I04
- Vai: BE-1
- Nhãn: type:sub-issue
- Spec: API-INV-04, API-INV-05, FR-INV-05, INVENTORY_DB
- Phụ thuộc: E05-I01, E01-I16
### Mô tả
Hai điểm cuối: lệnh đổi trạng thái do người dùng khởi xướng và danh sách đề nghị (vừa là hàng đợi chờ duyệt vừa là lịch sử quyết định). Lô tự vào cách ly do nhận hàng hay cảnh báo nhiệt không đi qua các điểm cuối này.
### Định nghĩa xong
- [ ] Máy trạng thái ép ở server: cách ly và tạm giữ là lệnh trực tiếp; giải phóng và huỷ chỉ tạo đề nghị; cấm khả dụng sang huỷ và cách ly sang khả dụng
- [ ] Lý do bắt buộc từ 1 đến 500 ký tự; mỗi lô chỉ một đề nghị đang mở; lệnh kèm phiên bản lô, lô đổi giữa chừng trả xung đột
- [ ] Vai theo từng bước kiểm ở service: cách ly và đề nghị cho kho và QC, tạm giữ chỉ QC
- [ ] Mỗi bước ghi sự kiện trạng thái chỉ thêm trong cùng giao dịch kèm nhật ký, sự kiện mang lane, mã truy xuất và thời điểm nghiệp vụ
- [ ] Danh sách đề nghị lọc theo trạng thái, sắp theo thời điểm, trả các vai trò duyệt lấy từ cấu hình, phân trang
- [ ] Lô thuộc case thu hồi đang mở không bị đổi trạng thái qua điểm cuối này; kiểm thử cho các nhánh trên
- [ ] Ca đối chiếu với hành vi của prototype ghi ở bảng khác prototype của spec có kiểm thử riêng
### Chỗ hở
- GAP-37 chưa quyết có thêm trạng thái "đang thu hồi" cho lô hay chỉ dùng dấu chặn

## E05-I04-S02 · Màn quản lý cách ly SCR008 kèm ô lọc trạng thái (FE)
- Loại: sub-issue
- Cha: E05-I04
- Vai: FE-1
- Nhãn: type:sub-issue
- Spec: SCR008, API-INV-01, API-INV-05, FR-INV-05
- Phụ thuộc: E05-I04-S01, E05-I05
### Mô tả
Dựng danh sách lô, các nút bước chuyển, form đề nghị, bảng đề nghị chờ duyệt và lịch sử quyết định. Nút ở mỗi dòng sinh từ danh sách bước hợp lệ server trả về, màn không tự suy bước. Wireframe chưa có ô lọc trạng thái, thiết kế hiện chỉ lọc qua tham số trên đường dẫn; issue này thêm ô lọc nhưng phải phác và có BrSE duyệt vì là thay đổi so với wireframe đã duyệt.
### Định nghĩa xong
- [ ] Danh sách lô cách ly và tạm giữ kèm lý do và thời điểm gần nhất; nút bước chuyển chỉ hiện bước server cho phép
- [ ] Ô lọc trạng thái (khả dụng, cách ly, tạm giữ) trên màn thay cho tham số trên đường dẫn; mặc định cách ly và tạm giữ; giá trị lạ quay về mặc định; bản phác được BrSE duyệt trước khi dựng
- [ ] Form đề nghị giải phóng hoặc huỷ bắt buộc lý do; nút duyệt ẩn với người đã đề nghị; câu quy tắc người duyệt lấy vai trò từ API
- [ ] Bảng đề nghị chờ duyệt và lịch sử quyết định ở dạng chỉ đọc
- [ ] Xung đột phiên bản và lô không còn tạm giữ lúc duyệt: thông báo và tải lại
- [ ] Vai ngoài kho và QC bị chặn; kiểm thử đầu-cuối cho 20 tình huống chấp nhận của màn
### Chỗ hở
- GAP-27 wireframe chưa có ô lọc trạng thái, Lab4 chỉ lọc qua tham số đường dẫn

## E05-I05 · Duyệt hoặc từ chối đề nghị giải phóng và huỷ lô (API-INV-06)
- Loại: issue
- Cha: E05
- Vai: BE-2
- Nhãn: type:issue, constraint:haccp-temp
- Spec: API-INV-06, SCR008, FR-INV-05
- Phụ thuộc: E05-I04-S01, E10-I06-S01, E02-I03
### Mô tả
Điểm cuối thuộc module nhiệt độ nhưng được màn cách ly gọi: duyệt hoặc từ chối một đề nghị đã tạo ở issue trước. Giải phóng do vai trò thứ nhất duyệt, huỷ do vai trò thứ hai, đọc từ khoá cấu hình, người duyệt phải khác người đề nghị. Khi duyệt, module nhiệt độ gọi lệnh của tồn kho để đổi trạng thái lô trong cùng giao dịch. Luật giải phóng sau cảnh báo nhiệt (cảnh báo phải đo lại xong, người đo lại không được duyệt) nằm ở thiết kế kỹ thuật của epic nhiệt độ; issue này nối và kiểm thử tích hợp hai chiều với phần đó.
### Định nghĩa xong
- [ ] Đúng vai trò theo loại đề nghị mới duyệt được, không có ngoại lệ cho admin; người đề nghị tự duyệt bị từ chối
- [ ] Đề nghị chỉ quyết một lần; từ chối bắt buộc lý do; hai người duyệt cùng lúc thì một thắng
- [ ] Khi duyệt, trạng thái lô đổi trong cùng giao dịch và có sự kiện trạng thái; lô đã đổi từ lúc đọc trả xung đột
- [ ] Hai điều kiện giải phóng sau cảnh báo nhiệt được gọi từ phần luật của epic nhiệt độ và có kiểm thử tích hợp cho cả hai nhánh
- [ ] Phản hồi và sự kiện mang đủ trường (loại quyết định, người duyệt, người đề nghị) cho module nhiệt độ
- [ ] Có khoá idempotency; kiểm thử quyền qua
### Chỗ hở
- GAP-36 chưa chốt sự kiện chỉ phát khi duyệt hay cả khi từ chối, và duyệt huỷ có kiểm cảnh báo chờ đo lại hay không

## E05-I06 · Kiểm kho mù (SCR007, API-INV-08..11)
- Loại: issue
- Cha: E05
- Nhãn: type:issue
- Spec: SCR007, FR-INV-04, INVENTORY_DB
- Phụ thuộc: E05-I01
### Mô tả
Nhân viên tạo phiếu kiểm theo khu vực hoặc vị trí, nhập số đếm thực tế mà không thấy số tồn của hệ thống; hệ thống tính chênh lệch, chênh lệch vượt ngưỡng cần một người khác với người đếm duyệt, rồi ghi điều chỉnh tồn vào sổ kèm lý do. Màn mới có wireframe, bốn API chưa có hợp đồng nên việc viết hợp đồng nằm trong issue. Ngưỡng chênh lệch chưa có nguồn và không được tự đặt.
### Định nghĩa xong
- [ ] Luồng tạo phiếu, đếm không thấy số hệ thống, thấy chênh lệch, duyệt bởi người khác, ghi điều chỉnh chạy được đầu-cuối
- [ ] Người đếm không bao giờ nhận được số tồn hệ thống trong bất kỳ phản hồi nào của luồng
### Chỗ hở
- GAP-30 chưa có nguồn cho ngưỡng chênh lệch buộc duyệt hai bước

## E05-I06-S01 · API phiếu kiểm và ghi số đếm (BE)
- Loại: sub-issue
- Cha: E05-I06
- Vai: BE-1
- Nhãn: type:sub-issue
- Spec: API-INV-08, API-INV-09, API-INV-10, FR-INV-04, INVENTORY_DB
- Phụ thuộc: E05-I01
### Mô tả
Viết hợp đồng và hiện thực ba API: đọc phiếu, tạo phiếu theo khu vực hoặc vị trí, ghi số đếm theo từng dòng. Số sổ sách được chụp lúc đếm để đối chiếu nhưng không bao giờ trả cho người đếm.
### Định nghĩa xong
- [ ] Tài liệu hợp đồng ba API được BrSE đọc lại
- [ ] Tạo phiếu theo khu vực hoặc vị trí chụp số sổ sách lúc đếm vào dòng kiểm
- [ ] Phản hồi của người đếm không chứa số sổ sách; có kiểm thử khẳng định lược đồ phản hồi
- [ ] Ghi số đếm có khoá phiên bản; quyền cho vai kho; người đếm được ghi lại
- [ ] Kiểm thử các nhánh: phiếu không tồn tại, dòng đã chốt, đếm hai lần
### Chỗ hở
- GAP-30 hợp đồng dùng ngưỡng nhưng giá trị chưa có

## E05-I06-S02 · Chênh lệch, duyệt hai bước và điều chỉnh tồn (BE)
- Loại: sub-issue
- Cha: E05-I06
- Vai: BE-2
- Nhãn: type:sub-issue
- Spec: API-INV-11, FR-INV-04, INVENTORY_DB
- Phụ thuộc: E05-I06-S01, E02-I03, E01-I13
### Mô tả
API quyết định phiếu: tính chênh lệch tự động, so với ngưỡng đọc từ khoá cấu hình, ép người duyệt khác người đếm, rồi ghi điều chỉnh vào sổ di chuyển. Hành vi khi khoá ngưỡng chưa có giá trị theo quyết định của issue làm rõ, không tự đặt số.
### Định nghĩa xong
- [ ] Chênh lệch tính tự động từ số sổ sách đã chụp và số đếm
- [ ] Ngưỡng đọc từ khoá cấu hình tạo mới; hành vi khi chưa có giá trị được ghi trong tài liệu theo quyết định của issue làm rõ
- [ ] Người duyệt khác người đếm được ép ở server và ở tầng dữ liệu; tự duyệt bị từ chối
- [ ] Điều chỉnh sau khi duyệt vào sổ di chuyển, bắt buộc lý do, vào nhật ký
- [ ] Có khoá idempotency; kiểm thử hai người duyệt cùng lúc và người đếm tự duyệt
### Chỗ hở
- GAP-30 ngưỡng chênh lệch chưa có nguồn, khoá cấu hình chưa có giá trị

## E05-I06-S03 · Màn kiểm kho mù SCR007 (FE)
- Loại: sub-issue
- Cha: E05-I06
- Vai: FE-1
- Nhãn: type:sub-issue
- Spec: SCR007, FR-INV-04
- Phụ thuộc: E05-I06-S01, E05-I06-S02
### Mô tả
Dựng màn tạo phiếu, bảng nhập số đếm không hiện số hệ thống, gửi số đếm, hiện chênh lệch, thông báo cần người khác duyệt, và nút ghi điều chỉnh.
### Định nghĩa xong
- [ ] Tạo phiếu, nhập số đếm, gửi số đếm chạy theo wireframe với API thật; không có chỗ nào trên màn hay trong mạng hiện số hệ thống trước khi gửi
- [ ] Sau khi gửi hiện chênh lệch và thông báo cần người khác duyệt khi vượt ngưỡng; người đếm không thấy nút duyệt
- [ ] Nút ghi điều chỉnh bắt buộc lý do
- [ ] Trạng thái trống, đang tải, lỗi mạng, xung đột phiên bản hiện đúng
- [ ] Kiểm thử đầu-cuối luồng không vượt ngưỡng và luồng vượt ngưỡng cần duyệt
### Chỗ hở
- GAP-12 ma trận vai trò màn chưa dựng ở prototype, khách chưa duyệt

## E05-I07 · Phiếu chuyển dải nhiệt tạm thời (SCR034, API-INV-12..14)
- Loại: issue
- Cha: E05
- Nhãn: type:issue, constraint:haccp-temp
- Spec: SCR034, API-INV-12, API-INV-13, API-INV-14, BR-TEMP-02
- Phụ thuộc: E05-I01
### Mô tả
Một lô có thể được đề nghị chuyển tạm sang dải nhiệt khác trong thời hạn tối đa theo cấu hình; QC khác người đề nghị duyệt; phiếu chưa duyệt không có hiệu lực. Điểm then chốt: phiếu đã duyệt không mở khoá luật chặn lệch dải, di chuyển sang vị trí khác dải vẫn bị chặn và phiếu được gắn vào case sai lệch để QC xử lý. Hết hạn thì lô quay về dải chuẩn và lô chưa trả về bị cảnh báo. Màn mới có wireframe, ba API chưa có hợp đồng.
### Định nghĩa xong
- [ ] Luồng tạo phiếu, QC khác duyệt, phiếu hết hạn quay về dải chuẩn chạy được đầu-cuối trên môi trường thử
- [ ] Phiếu đã duyệt không làm hết chặn lệch dải: ca di chuyển lệch dải vẫn bị từ chối và ghi sự kiện
### Chỗ hở
- GAP-31 chưa rõ hạn hiệu lực của phiếu tính từ lúc đề nghị hay từ lúc duyệt

## E05-I07-S01 · API phiếu dải nhiệt tạm: tạo, đọc, duyệt (BE)
- Loại: sub-issue
- Cha: E05-I07
- Vai: BE-2
- Nhãn: type:sub-issue, constraint:haccp-temp
- Spec: API-INV-12, API-INV-13, API-INV-14, BR-TEMP-02, INVENTORY_DB
- Phụ thuộc: E05-I01, E02-I03, E01-I13
### Mô tả
Viết hợp đồng và hiện thực ba API: đọc danh sách phiếu, tạo phiếu, quyết định phiếu. Tạo phiếu thiếu trường thì không lưu; hạn tối đa theo khoá cấu hình; QC duyệt phải khác người đề nghị.
### Định nghĩa xong
- [ ] Tài liệu hợp đồng ba API được BrSE đọc lại
- [ ] Tạo phiếu thiếu trường không lưu; hạn vượt tối đa theo khoá cấu hình bị từ chối với mã lỗi chung
- [ ] Duyệt đúng vai theo khoá, khác người đề nghị; phiếu chưa duyệt không có hiệu lực
- [ ] Phiếu đã duyệt không mở khoá luật chặn lệch dải trong lệnh di chuyển, có kiểm thử
- [ ] Mã phiếu đã duyệt cung cấp được cho module nhiệt độ để gắn vào case sai lệch
- [ ] Có khoá idempotency; kiểm thử quyền qua
### Chỗ hở
- GAP-31 mốc bắt đầu của hạn hiệu lực chưa chốt

## E05-I07-S02 · Job hết hạn phiếu và cảnh báo lô chưa trả về dải chuẩn (BE)
- Loại: sub-issue
- Cha: E05-I07
- Vai: BE-1
- Nhãn: type:sub-issue, constraint:haccp-temp
- Spec: BR-TEMP-02, INVENTORY_DB
- Phụ thuộc: E05-I07-S01
### Mô tả
Tác vụ định kỳ quét phiếu đã duyệt hết hạn, chuyển phiếu sang hết hiệu lực, đưa lô về dải chuẩn và phát cảnh báo khi lô chưa trả về. Cách gửi cảnh báo cho người dùng chưa được thiết kế nên cần chốt kênh với epic trang chủ hoặc nhiệt độ.
### Định nghĩa xong
- [ ] Tác vụ quét dùng chỉ mục một phần của phiếu đã duyệt, chu kỳ đọc từ cấu hình
- [ ] Phiếu hết hạn chuyển trạng thái, hiệu lực dải của lô quay về chuẩn; chạy lại tác vụ không tạo kết quả trùng
- [ ] Lô chưa trả về dải chuẩn phát một cảnh báo qua kênh thống nhất với chủ kênh
- [ ] Kiểm thử bằng đồng hồ giả cho phiếu hết hạn, phiếu chưa hết hạn và lô đã trả về
### Chỗ hở
- GAP-33 chưa rõ cảnh báo phiếu hết hạn gửi cho ai và qua kênh nào

## E05-I07-S03 · Màn phiếu chuyển dải nhiệt tạm SCR034 (FE)
- Loại: sub-issue
- Cha: E05-I07
- Vai: FE-1
- Nhãn: type:sub-issue
- Spec: SCR034, API-INV-12, API-INV-13, API-INV-14
- Phụ thuộc: E05-I07-S01
### Mô tả
Dựng form đề nghị phiếu, danh sách phiếu chờ duyệt, nút duyệt dành cho QC, thông báo hạn tối đa, thông báo phiếu hết hạn và thông báo nhắc luật chặn lệch dải vẫn áp dụng khi phiếu đã duyệt.
### Định nghĩa xong
- [ ] Form đề nghị, danh sách chờ duyệt, nút duyệt chạy theo wireframe với API thật
- [ ] Thông báo hạn tối đa lấy số giờ từ cấu hình; thông báo phiếu đã duyệt không mở khoá chặn lệch dải hiện đúng
- [ ] Nút duyệt chỉ hiện với QC khác người đề nghị
- [ ] Trạng thái trống, đang tải, lỗi mạng, xung đột phiên bản hiện đúng
- [ ] Kiểm thử đầu-cuối luồng đề nghị và duyệt, và luồng đề nghị vượt hạn tối đa
### Chỗ hở
- GAP-12 ma trận vai trò màn chưa dựng ở prototype, khách chưa duyệt

## E05-I08 · Làm rõ với khách các điểm của tồn kho (type:clarification)
- Loại: issue
- Cha: E05
- Vai: BrSE
- Nhãn: type:issue, type:clarification
- Spec: SCR006, SCR007, SCR008, SCR034, FR-INV-04
### Mô tả
Gom các câu hỏi khách mà thiết kế đang chạy bằng cách đọc của nhà thầu hoặc còn thiếu số: có cho di chuyển một phần lô hay chỉ cả lô; ngưỡng chênh lệch kiểm kho buộc duyệt hai bước; hạn hiệu lực phiếu dải nhiệt tạm tính từ lúc đề nghị hay lúc duyệt; vai trò duyệt giải phóng, huỷ và việc huỷ có kiểm cảnh báo chờ đo lại; quyền xem lô cách ly của kinh doanh; nguồn định danh thiết bị kho cho mỗi dịch chuyển; hệ thống chỉ cần theo dõi tồn ở mức lô hay cần thêm đơn vị tồn kho riêng; lô thuộc case thu hồi có cần hiện thành một trạng thái riêng hay chỉ bị chặn bằng dấu chặn; ma trận vai trò ba màn chưa dựng. Các issue build vẫn chạy theo mặc định; câu trả lời khác mặc định thì mở issue sửa.
### Định nghĩa xong
- [ ] Gửi khách bộ câu hỏi đóng, mỗi câu có phương án mặc định hiện hành và hệ quả nếu khác
- [ ] Bộ câu hỏi có hai câu về dữ liệu lô: giữ tồn ở mức lô hay thêm đơn vị tồn kho; trạng thái riêng cho lô đang thu hồi hay chỉ dùng dấu chặn, kèm hệ quả với hợp đồng dữ liệu nếu chọn thêm trạng thái
- [ ] Ghi câu trả lời vào nhật ký quyết định; điền giá trị ngưỡng chênh lệch và số giờ phiếu vào cấu hình khi có
- [ ] Mỗi câu trả lời khác mặc định có một issue sửa hoặc cập nhật issue E05 bị ảnh hưởng
- [ ] Cập nhật danh sách giá trị tạm còn chờ khách xác nhận
### Chỗ hở
- GAP-29 di chuyển một phần lô và kiểm sức chứa là cách đọc nhà thầu chưa khách xác nhận
- GAP-30 ngưỡng chênh lệch kiểm kho chưa có nguồn
- GAP-31 mốc bắt đầu hạn hiệu lực phiếu dải nhiệt tạm chưa chốt
- GAP-12 ma trận vai trò các màn chưa dựng ở prototype cần khách duyệt

## E05-I10 · Giao diện đặt tên của kho cho module khác (lệnh và truy vấn)
- Loại: issue
- Cha: E05
- Vai: BE-2
- Nhãn: type:issue, constraint:haccp-temp, constraint:recall
- Spec: INVENTORY_DB, ADR-003, FR-INV-05, DR-LOT-01
- Phụ thuộc: E05-I01
### Mô tả
Các module nhiệt độ, truy xuất, phân bổ và xuất kho không được vào bảng của kho nên cần một giao diện đặt tên do kho phơi ra. Gồm lệnh đặt và gỡ dấu chặn lô theo loại thu hồi hoặc nhiệt độ, lệnh đưa lô vào cách ly hoặc tạm giữ do hệ thống khởi xướng, truy vấn số đã nhận và số còn trong kho của lô, truy vấn lịch sử vị trí của lô, và đọc lô khả dụng kèm phiên bản, tồn, trạng thái, dấu chặn và case sai lệch nhiệt đang mở. Đọc được sau khi giữ khoá dòng lô, và lệnh trừ tồn chạy trong cùng giao dịch của bên gọi.
### Định nghĩa xong
- [ ] Lệnh đặt và gỡ dấu chặn lô cho hai loại thu hồi và nhiệt độ chạy được, ghi sổ cái và sự kiện, có kiểm thử đặt hai dấu cùng lúc rồi gỡ từng dấu
- [ ] Lệnh cách ly và chuyển tạm giữ do hệ thống khởi xướng dùng đúng máy trạng thái của lô, ghi sự kiện trạng thái kèm lý do và nguồn
- [ ] Truy vấn số đã nhận và số còn trong kho theo lô, truy vấn lịch sử vị trí theo lô trả đúng sau chuỗi nhận, di chuyển, điều chỉnh
- [ ] Giao diện đọc lô khả dụng trả phiên bản lô, tồn, trạng thái, dấu chặn đang mở và mã case sai lệch nhiệt đang mở, đọc nhất quán sau khi giữ khoá dòng lô
- [ ] Lệnh trừ tồn khả dụng của lô (phân bổ) và lệnh ghi bút toán lấy hàng vào sổ di chuyển (xếp hàng) đều kèm phiên bản lô, chạy trong cùng giao dịch của bên gọi, không bao giờ để tồn âm, và có kiểm thử hai bên gọi đồng thời trên một lô
- [ ] Hợp đồng giao diện được ghi thành tài liệu và gửi cho chủ các module nhiệt độ, truy xuất, phân bổ, xuất kho
### Chỗ hở
- GAP-37 chưa quyết trạng thái "đang thu hồi" của lô hay chỉ dùng dấu chặn

## E05-I09 · Bản đồ vị trí kho, sức chứa và gợi ý vị trí SCR005 (giai đoạn 2)
- Loại: issue
- Cha: E05
- Nhãn: type:issue, phase-2
- Spec: SCR005, API-INV-07, FR-INV-01, FR-INV-02
- Phụ thuộc: E05-I01
- Giai đoạn: 2
### Mô tả
Màn bản đồ vị trí kho hiện sức chứa, dải nhiệt, mức sử dụng và trạng thái đóng hoặc mở của từng vị trí; chặn cất vào vị trí vượt sức chứa hoặc đang đóng; đề xuất vị trí cất phù hợp dựa trên dải nhiệt, trạng thái, sức chứa và khả năng lấy hàng theo hạn. Thiết kế hiện chưa có cột sức chứa hay trạng thái đóng mở ở bảng vị trí và chưa có bảng dùng cho màn này, nên cần bổ sung dữ liệu trước. Di chuyển một phần lô gắn với quyết định của khách ở issue làm rõ.
### Định nghĩa xong
- [ ] Bản đồ hiện sức chứa, mức sử dụng và trạng thái đóng mở của vị trí với dữ liệu thật
- [ ] Cất vào vị trí vượt sức chứa hoặc đang đóng bị chặn; màn hiện vị trí đề xuất kèm lý do
### Chỗ hở
- GAP-32 bảng vị trí chưa có sức chứa và trạng thái đóng mở

## E05-I09-S01 · API sức chứa và gợi ý vị trí, bổ sung cột sức chứa (BE)
- Loại: sub-issue
- Cha: E05-I09
- Vai: BE-2
- Nhãn: type:sub-issue, phase-2
- Spec: API-INV-07, FR-INV-01, FR-INV-02, INVENTORY_DB, MASTER_DB
- Phụ thuộc: E05-I01, E03-I06-S01
- Giai đoạn: 2
### Mô tả
Viết hợp đồng API đọc sức chứa, bổ sung cột sức chứa và trạng thái đóng mở cho vị trí (chỉ thêm cột, thống nhất với chủ bảng master), tính mức sử dụng từ tồn, chặn cất vượt sức chứa hoặc vào vị trí đóng, và đề xuất vị trí cất.
### Định nghĩa xong
- [ ] Tài liệu hợp đồng API được BrSE đọc lại; cột sức chứa và trạng thái đóng mở thêm bằng migration chỉ thêm
- [ ] Mức sử dụng của từng vị trí tính từ tồn hiện tại và khớp tổng lô trong vị trí
- [ ] Lệnh nhận hàng và di chuyển bị chặn khi vị trí đích vượt sức chứa hoặc đang đóng, mã lỗi chung
- [ ] Đề xuất vị trí cất dựa trên dải nhiệt, trạng thái, sức chứa và khả năng lấy theo hạn; ghi lý do khi người dùng chọn vị trí khác đề xuất
- [ ] Kiểm thử quyền, vượt sức chứa, vị trí đóng, gợi ý rỗng
### Chỗ hở
- GAP-32 cột sức chứa thuộc bảng của epic dữ liệu gốc nên cần thống nhất thay đổi lược đồ

## E05-I09-S02 · Màn bản đồ vị trí kho SCR005 (FE)
- Loại: sub-issue
- Cha: E05-I09
- Vai: FE-1
- Nhãn: type:sub-issue, phase-2
- Spec: SCR005, API-INV-07
- Phụ thuộc: E05-I09-S01
- Giai đoạn: 2
### Mô tả
Dựng bản đồ vị trí với ô vị trí hiện sức chứa, mức sử dụng, dải nhiệt và trạng thái đóng hoặc mở; khối gợi ý vị trí và lý do chọn vị trí khác.
### Định nghĩa xong
- [ ] Bản đồ và khối gợi ý chạy theo wireframe với API thật
- [ ] Vị trí đóng hoặc vượt sức chứa hiện rõ và không chọn được; thông báo lỗi chặn hiện đúng
- [ ] Trạng thái trống, đang tải, lỗi mạng hiện đúng
- [ ] Kiểm thử đầu-cuối cho chọn vị trí gợi ý và chọn vị trí khác kèm lý do
### Chỗ hở
- GAP-12 ma trận vai trò màn chưa dựng ở prototype, khách chưa duyệt
