# E03 · Dữ liệu gốc
<!-- lab5-epic: E03 -->

## E03 · Dữ liệu gốc
- Loại: epic
- Nhãn: type:epic
- Spec: MASTER_DB, DR-MST-01, SCR026, SCR027, SCR028, SCR029, SCR036
### Mô tả
Dựng bộ danh mục gốc mà mọi module khác chỉ đọc: SKU, khách hàng và thoả thuận khách × SKU, ánh xạ mã khách cũ sang mới, nhà cung cấp, vị trí kho kèm dải nhiệt, thiết bị ghi nhiệt, xe và tài xế. Quy tắc chung của cả epic là mỗi lần lưu tạo một phiên bản có ngày hiệu lực, không ghi đè lịch sử, và giao dịch đã ghi giữ nguyên phiên bản lúc ghi. Chỉ SCR026 có spec đầy đủ; SCR027, SCR028, SCR029 và SCR036 mới có wireframe nên mỗi issue màn kèm việc chi tiết hoá hợp đồng API. Epic không gồm phần ghi dữ liệu giờ làm của tài xế trên SCR036 (thuộc E09) và phần giao diện SCR009 dùng chung API SKU (thuộc E06).

## E03-I01 · Lược đồ MASTER_DB và cơ chế phiên bản hoá theo ngày hiệu lực
- Loại: issue
- Cha: E03
- Vai: BE-1
- Nhãn: type:issue
- Spec: MASTER_DB, DR-MST-01
### Mô tả
Tạo toàn bộ bảng danh mục của module masterdata và cơ chế dùng chung để lưu phiên bản theo ngày hiệu lực: mở bản mới thì đóng bản đang mở trong cùng một giao dịch, tuyệt đối không ghi đè. Các issue SKU, khách, nhà cung cấp, vị trí, xe và tài xế đều dựng trên khung này nên làm trước. Bảng dải nhiệt nằm trong lược đồ này; bảng phiên bản ngưỡng nhiệt do E10-I01-S01 migrate và sở hữu để chỉ có một chủ migration cho bảng đó.
### Định nghĩa xong
- [ ] Migration tạo đủ nhóm bảng danh mục (dải nhiệt, vị trí, nhà cung cấp, SKU và phiên bản, khách và hồ sơ khách, ánh xạ mã khách, thoả thuận, xe, tài xế, hồ sơ điểm dừng, phiên bản master, đề nghị thay đổi) và chạy sạch trên cơ sở dữ liệu trống
- [ ] Bảng phiên bản ngưỡng nhiệt không nằm trong migration này; phần của epic nhiệt độ cắm vào cùng khung phiên bản
- [ ] Hàm mở phiên bản mới đóng bản cũ trong cùng giao dịch, có kiểm thử cho trường hợp hai lần lưu đồng thời
- [ ] Truy vấn "phiên bản hiệu lực tại ngày X" có chỉ mục và kiểm thử với ngày trước, đúng ngày và sau ngày hiệu lực
- [ ] Phần nhận diện của khách nằm bảng riêng tách khỏi bản ghi lưu giữ dài; bảng tài xế không có cột thông tin cá nhân
- [ ] Bộ dữ liệu mẫu nhỏ để chạy kiểm thử các issue sau, không chứa dữ liệu thật
- [ ] Tài liệu bảng được cập nhật nếu lược đồ chạy khác bản thiết kế
### Chỗ hở
- GAP-35 chưa rõ có cần ngày hiệu lực ở tương lai cho nhóm master ngoài SKU, ngưỡng, ánh xạ mã khách và thoả thuận

## E03-I02 · API danh sách chọn dùng chung (API-MST-01)
- Loại: issue
- Cha: E03
- Vai: BE-2
- Nhãn: type:issue
- Spec: API-MST-01, MASTER_DB
- Phụ thuộc: E03-I01
### Mô tả
Một điểm cuối chỉ đọc trả danh sách chọn cho form của nhiều màn: dải nhiệt, vị trí, nhà cung cấp, khách, xe, tài xế, mã lý do nhận hàng và tình trạng bao bì. Mỗi loại có phạm vi vai trò riêng vì có loại chứa dữ liệu vận hành của người khác. Hai loại cuối đọc từ khoá cấu hình, khoá tình trạng bao bì hiện đang để trống nên điểm cuối phải trả danh sách rỗng chứ không tự đặt giá trị.
### Định nghĩa xong
- [ ] Đủ tám loại danh sách, luôn trả bản hiện hành của master
- [ ] Mỗi loại kiểm vai trò ở server, có kiểm thử vai được và vai bị từ chối cho từng loại
- [ ] Loại mã lý do nhận hàng và tình trạng bao bì đọc từ cấu hình; danh mục rỗng trả mảng rỗng, không giá trị mặc định tự bịa
- [ ] Hợp đồng được đối chiếu với bốn màn gọi (nhận hàng, chi tiết lô, lập tuyến, vị trí kho) và ghi lại chỗ lệch nếu có
- [ ] Loại xe lọc được theo dải nhiệt và trả mã xe kèm dải; loại tài xế chỉ trả định danh vận hành, không cột cá nhân, có kiểm thử lược đồ phản hồi
### Chỗ hở
- GAP-24 khoá danh mục tình trạng bao bì còn trống, chưa có giá trị khách xác nhận

## E03-I03 · Quản lý SKU có ngày hiệu lực (API đọc / lưu và màn SCR026)
- Loại: issue
- Cha: E03
- Nhãn: type:issue, constraint:expiry, constraint:rice-beef-trace
- Spec: SCR026, API-MST-02, API-MST-03, DR-MST-01
- Phụ thuộc: E03-I01
### Mô tả
Admin tạo và sửa SKU, mọi vai trừ tài xế xem danh sách và lịch sử phiên bản. Mỗi lần lưu là một phiên bản mới có ngày hiệu lực. Loại hạn (消費期限 hay 賞味期限), dải nhiệt và lane truy xuất lưu ở đây là đầu vào của luật nhận hàng, di chuyển và phân bổ nên sai ở đây là sai dây chuyền. Cùng API này được màn đổi loại hạn của epic hạn dùng gọi lại, nên hợp đồng đóng băng ở sub-issue BE trước khi bên đó dựng giao diện.
### Định nghĩa xong
- [ ] Luồng đầu-cuối tạo SKU, sửa SKU rồi xem lịch sử phiên bản chạy được trên môi trường thử
- [ ] Giao dịch quá khứ vẫn hiển thị phiên bản cũ sau khi SKU được sửa
### Chỗ hở
- GAP-35 vòng đời master còn mở: đề nghị và duyệt cho SKU, ngừng SKU, phiên bản lên lịch, đổi dải nhiệt khi còn tồn

## E03-I03-S01 · API đọc và lưu SKU theo ngày hiệu lực (BE)
- Loại: sub-issue
- Cha: E03-I03
- Vai: BE-1
- Nhãn: type:sub-issue, constraint:expiry, constraint:rice-beef-trace
- Spec: API-MST-02, API-MST-03, DR-MST-01
- Phụ thuộc: E03-I01, E01-I14, E01-I16
### Mô tả
Điểm cuối đọc có hai chế độ (danh sách có lọc và phân trang; chi tiết kèm lịch sử phiên bản, đọc được tại một ngày bất kỳ) và điểm cuối lưu nhận đủ sáu trường nội dung cùng phiên bản kỳ vọng. Lưu thì đóng bản cũ, mở bản mới trong một giao dịch. Ngày sản xuất gốc của lô không bao giờ đổi qua API này.
### Định nghĩa xong
- [ ] Đọc danh sách và chi tiết chạy được, tham số ngày hiệu lực chọn đúng phiên bản, lịch sử có người đổi và khoảng hiệu lực
- [ ] Lưu tạo SKU mới thành phiên bản 1; mã trùng bị từ chối; lưu nội dung không đổi thì không sinh phiên bản mới
- [ ] Ngày hiệu lực trước ngày nghiệp vụ hoặc không sau phiên bản mới nhất bị từ chối với mã lỗi chung; luồng đổi loại hạn của màn SCR009 đi qua đúng điểm cuối này và lần đổi thứ hai trong cùng ngày nhận mã lỗi riêng
- [ ] Thân yêu cầu có ngày sản xuất bị từ chối, lô đã nhận không bị tính lại hạn
- [ ] Hai admin lưu cùng SKU: người sau nhận lỗi xung đột phiên bản và không ghi đè
- [ ] Chỉ admin ghi được; kiểm thử quyền cho mọi vai còn lại; mỗi lần lưu ghi nhật ký trước và sau
- [ ] Ca đối chiếu với hành vi của prototype ghi ở bảng khác prototype của spec có kiểm thử riêng
### Chỗ hở
- GAP-35 đổi dải nhiệt khi SKU còn tồn ở vị trí dải cũ chưa có luật chặn hay cảnh báo

## E03-I03-S02 · Màn quản lý SKU SCR026 (FE)
- Loại: sub-issue
- Cha: E03-I03
- Vai: FE-1
- Nhãn: type:sub-issue, constraint:expiry, constraint:rice-beef-trace
- Spec: SCR026, DR-MST-01
- Phụ thuộc: E03-I03-S01
### Mô tả
Màn danh sách SKU, khối lịch sử phiên bản và form tạo hoặc sửa. Loại hạn chọn đúng một trong hai; lane gạo hoặc bò phải có nhãn loại mã pháp định, lane nội bộ phải để trống. Vai không phải admin chỉ đọc, tài xế không vào được màn.
### Định nghĩa xong
- [ ] Danh sách, lịch sử và form hiển thị đúng theo wireframe, nhãn tiếng Nhật theo bộ nhãn đã duyệt
- [ ] Kiểm trước ở client: trường bắt buộc, loại hạn loại trừ nhau, quy tắc nhãn theo lane, ngày hiệu lực không trước ngày nghiệp vụ
- [ ] Vai ngoài admin thấy màn ở chế độ chỉ đọc; tài xế bị chặn; phiên hết hạn về màn đăng nhập
- [ ] Xung đột phiên bản và SKU đã biến mất hiện thông báo và tải lại dữ liệu mới
- [ ] Bộ kiểm thử đầu-cuối cho 15 tình huống chấp nhận của màn đều qua
### Chỗ hở
- GAP-38 màn chưa có nhãn thông báo "đã lưu" sau khi tạo thành công

## E03-I04 · Khách hàng và thoả thuận khách × SKU (SCR027, API-MST-04..07)
- Loại: issue
- Cha: E03
- Nhãn: type:issue
- Spec: SCR027, API-MST-04, API-MST-06, DR-MST-01
- Phụ thuộc: E03-I01
### Mô tả
Quản lý khách và thoả thuận khách × SKU có ngày hiệu lực, không ghi đè lịch sử; danh sách thoả thuận cũng được màn luật cửa sổ giao đọc. SCR027 mới có wireframe nên việc viết hợp đồng cho bốn API nằm trong issue này. Bản ghi khách còn mang cửa sổ giờ nhận hàng và thời gian dừng tại điểm giao mà lập tuyến cần (bảng hồ sơ điểm dừng); API và form khách của issue này đọc ghi cả hai cùng bản ghi khách, module vận tải chỉ đọc qua giao diện đặt tên. Khối ánh xạ mã khách trên cùng màn gọi API của issue ánh xạ mã. Lối vào cấu hình luật cửa sổ giao chỉ là điều hướng sang màn của epic hạn dùng.
### Định nghĩa xong
- [ ] Luồng tạo khách, tạo thoả thuận, sửa thoả thuận có ngày hiệu lực chạy được đầu-cuối
- [ ] Thoả thuận đã dùng cho dòng đơn cũ vẫn đọc được đúng phiên bản lúc đó
### Chỗ hở
- GAP-35 khách có cần thêm cột liên hệ ngoài tên và mốc tính thời hạn lưu giữ một năm chưa rõ
- GAP-39 danh mục API và màn LAB-4 không có chỗ ghi bảng hồ sơ điểm dừng; mở rộng API khách và form SCR027 là thay đổi thiết kế

## E03-I04-S01 · Hợp đồng và API khách, thoả thuận (BE)
- Loại: sub-issue
- Cha: E03-I04
- Vai: BE-2
- Nhãn: type:sub-issue
- Spec: API-MST-04, API-MST-05, API-MST-06, API-MST-07, MASTER_DB
- Phụ thuộc: E03-I01
### Mô tả
Viết hợp đồng cho bốn API (đọc và lưu khách; đọc và lưu thoả thuận) theo khuôn của các API đã có rồi hiện thực. Đọc chạy cho vai kinh doanh và quản trị, lưu chỉ admin. API khách đọc và lưu thêm cửa sổ giờ nhận và thời gian dừng cùng bản ghi khách. API thoả thuận trả điều khoản giao, luật cửa sổ giao (rỗng khi chưa thoả thuận) và số phiên bản, để màn tạo dòng đơn và màn luật cửa sổ đọc. Dòng đơn của phân bổ cần biết số phiên bản thoả thuận đã dùng, nên API phải trả số phiên bản này.
### Định nghĩa xong
- [ ] Tài liệu hợp đồng bốn API đủ tham số, phản hồi, mã lỗi và quyền, được BrSE đọc lại
- [ ] Đọc khách và thoả thuận có lọc, phân trang và đọc theo ngày hiệu lực; thoả thuận trả điều khoản giao, luật cửa sổ giao và số phiên bản
- [ ] Lưu khách kèm cửa sổ giờ nhận và thời gian dừng (số phút dương, không đặt cận trên); giao diện đặt tên chỉ đọc cho lập tuyến
- [ ] Có đường nạp ban đầu cửa sổ giờ nhận và thời gian dừng của khách (nhập hàng loạt từ tệp do khách hoặc BrSE cung cấp) trước khi dựng tuyến: kiểm từng dòng, báo dòng lỗi, không nạp nửa chừng, nạp lại không tạo trùng, và có kiểm thử với bộ dữ liệu mẫu
- [ ] Lưu tạo phiên bản mới có ngày hiệu lực, bản cũ giữ nguyên; lỗi xung đột phiên bản trả đúng mã
- [ ] Phần nhận diện của khách lưu bảng riêng, phản hồi danh sách không lộ cột ngoài quyền
- [ ] Kiểm thử quyền và kiểm thử lịch sử phiên bản đều qua
### Chỗ hở
- GAP-35 khách có cột cá nhân nào ngoài tên hay không (câu khách còn mở)

## E03-I04-S02 · Màn SCR027: khối khách, thoả thuận và ánh xạ mã khách (FE)
- Loại: sub-issue
- Cha: E03-I04
- Vai: FE-1
- Nhãn: type:sub-issue
- Spec: SCR027, API-MST-09
- Phụ thuộc: E03-I04-S01, E03-I05
### Mô tả
Dựng màn theo wireframe: bảng khách, bảng thoả thuận kèm form thêm hoặc sửa, khối ánh xạ mã khách cũ sang mới kèm đề nghị và duyệt, và nút mở cấu hình luật cửa sổ giao của thoả thuận. Người đề nghị ánh xạ không thấy nút duyệt.
### Định nghĩa xong
- [ ] Ba khối chạy đúng wireframe với dữ liệu từ API thật, có trạng thái trống, đang tải và lỗi mạng
- [ ] Form khách có ô cửa sổ giờ nhận và thời gian dừng, kiểm trước giờ mở trước giờ đóng và số phút dương
- [ ] Form thoả thuận kiểm trước các trường bắt buộc và ngày hiệu lực; lỗi từ server hiện tại đúng ô
- [ ] Khối ánh xạ ẩn nút duyệt với người đã đề nghị; trạng thái chờ duyệt, đã duyệt, bị từ chối hiển thị rõ
- [ ] Nút cấu hình luật cửa sổ giao mở đúng màn tương ứng kèm mã thoả thuận
- [ ] Kiểm thử đầu-cuối luồng tạo thoả thuận và luồng đề nghị ánh xạ qua
### Chỗ hở
- GAP-12 ma trận vai trò của màn chưa dựng ở prototype mới chép từ màn gần nhất, khách chưa duyệt

## E03-I05 · Ánh xạ mã khách cũ sang mới có đề nghị và duyệt (API-MST-09..11)
- Loại: issue
- Cha: E03
- Vai: BE-1
- Nhãn: type:issue, constraint:date-reversal
- Spec: API-MST-09, API-MST-10, API-MST-11, BR-DATE-01, ADR-004
- Phụ thuộc: E03-I01, E01-I16, E02-I03
### Mô tả
Khi khách đổi mã, điểm neo 日付逆転 vẫn phải tính theo khách gốc nên cần bảng ánh xạ mã cũ sang mới có ngày hiệu lực. Mọi thay đổi ánh xạ đi qua đề nghị, người duyệt phải khác người đề nghị. Khung đề nghị và duyệt được thiết kế theo loại master để epic nhiệt độ dùng lại cho phiên bản ngưỡng mà không phải sửa lõi.
### Định nghĩa xong
- [ ] API đọc ánh xạ, tạo đề nghị và quyết định đề nghị chạy được, ghi chống trùng bằng khoá idempotency
- [ ] Người duyệt khác người đề nghị được ép ở server, có kiểm thử trường hợp tự duyệt bị từ chối
- [ ] Ánh xạ đã duyệt có hiệu lực theo ngày, không ghi đè bản cũ; đề nghị bị từ chối không đổi dữ liệu
- [ ] Loại master của đề nghị là tham số; thêm loại mới không cần sửa mã khung, có tài liệu cách thêm
- [ ] Mỗi quyết định ghi nhật ký người, giờ nghiệp vụ và lý do
- [ ] Có giao diện đặt tên đọc "ánh xạ đã duyệt và hiệu lực tại một ngày" cho module khác và phát sự kiện khi một ánh xạ được duyệt để bên đọc xoá bộ đệm
### Chỗ hở
- GAP-35 định dạng mã khách cũ chưa có nguồn nên chưa ràng buộc hình dạng

## E03-I06 · Nhà cung cấp, vị trí kho và thiết bị ghi nhiệt (SCR028, SCR029)
- Loại: issue
- Cha: E03
- Nhãn: type:issue, constraint:haccp-temp
- Spec: SCR028, SCR029, MASTER_DB
- Phụ thuộc: E03-I01
### Mô tả
Hai màn quản trị còn lại của khu vực kho. Nhà cung cấp là danh mục có ngày hiệu lực. Vị trí kho gán dải nhiệt cho từng vị trí, và dải này là căn cứ chặn lệch dải khi nhận hàng và di chuyển lô. Thiết bị ghi nhiệt gán thiết bị vào dải hoặc khu, có hiệu lực và người phụ trách. Cả hai màn chỉ có wireframe nên hợp đồng sáu API được viết trong issue này.
### Định nghĩa xong
- [ ] Luồng tạo và sửa nhà cung cấp, vị trí và thiết bị ghi nhiệt chạy được đầu-cuối
- [ ] Đổi dải của vị trí hiện thông tin đủ để người dùng thấy ảnh hưởng tới lô đang để ở đó
### Chỗ hở
- GAP-12 ma trận vai trò của hai màn chưa dựng ở prototype mới chép từ màn gần nhất

## E03-I06-S01 · API nhà cung cấp và vị trí kho (BE)
- Loại: sub-issue
- Cha: E03-I06
- Vai: BE-2
- Nhãn: type:sub-issue, constraint:haccp-temp
- Spec: API-MST-12, API-MST-13, API-MST-14, API-MST-15, MASTER_DB
- Phụ thuộc: E03-I01
### Mô tả
Viết hợp đồng và hiện thực bốn API đọc, lưu cho nhà cung cấp và vị trí. Vị trí có mã theo khuôn dải, hàng, ô; hàng Q là khu cách ly của dải. Lưu chỉ admin, đọc cho vai nghiệp vụ.
### Định nghĩa xong
- [ ] Tài liệu hợp đồng bốn API được BrSE đọc lại
- [ ] Mã vị trí sai khuôn bị từ chối; vị trí bắt buộc có dải; cờ khu cách ly khớp hàng Q
- [ ] Lưu nhà cung cấp và vị trí tạo phiên bản có ngày hiệu lực, không ghi đè
- [ ] Kiểm thử quyền và kiểm thử xung đột phiên bản qua
### Chỗ hở
- GAP-32 bảng vị trí chưa có cột sức chứa và trạng thái đóng hoặc mở dù màn bản đồ kho cần

## E03-I06-S02 · API thiết bị ghi nhiệt (BE)
- Loại: sub-issue
- Cha: E03-I06
- Vai: BE-1
- Nhãn: type:sub-issue, constraint:haccp-temp
- Spec: API-MST-16, API-MST-17, TEMPERATURE_DB
- Phụ thuộc: E03-I01, E10-I02-S01
### Mô tả
Hai API đọc và lưu thiết bị ghi nhiệt thuộc module nhiệt độ nhưng được màn vị trí kho gọi. Yêu cầu chức năng nói thiết bị gắn dải hoặc khu, có hiệu lực và người phụ trách, trong khi bảng thiết bị hiện chỉ có mã, dải, vị trí đặt và phiên bản. Bảng do epic nhiệt độ sở hữu, issue này dựng điểm cuối trên bảng đó và chốt với chủ bảng các cột còn thiếu.
### Định nghĩa xong
- [ ] Hợp đồng hai API được viết, thống nhất tên bảng và cột với chủ bảng ở epic nhiệt độ
- [ ] Đọc danh sách thiết bị theo dải; lưu tạo hoặc sửa chạy được với khoá phiên bản
- [ ] Mã thiết bị có ký tự dải khớp dải đã chọn; vị trí đặt phải tồn tại; vi phạm bị từ chối với mã lỗi chung
- [ ] Chỉ admin lưu; vai nghiệp vụ đọc; kiểm thử quyền qua
- [ ] Danh sách cột còn thiếu so với màn (khu, người phụ trách, hiệu lực) được ghi lại và chuyển cho chủ bảng
### Chỗ hở
- GAP-84 bảng thiết bị chưa có cột khu, người phụ trách và hiệu lực mà màn và chức năng yêu cầu, quy trình đổi thiết bị chưa chốt

## E03-I06-S03 · Màn nhà cung cấp SCR028 và vị trí kho SCR029 (FE)
- Loại: sub-issue
- Cha: E03-I06
- Vai: FE-1
- Nhãn: type:sub-issue
- Spec: SCR028, SCR029
- Phụ thuộc: E03-I06-S01, E03-I06-S02
### Mô tả
Dựng hai màn theo wireframe: danh sách và form thêm hoặc sửa nhà cung cấp; danh sách vị trí theo kho và dải kèm form gán dải nhiệt; khối thiết bị ghi nhiệt với form riêng.
### Định nghĩa xong
- [ ] SCR028 chạy đúng wireframe: danh sách, thêm, sửa, báo trùng mã
- [ ] SCR029 chạy khối vị trí và khối thiết bị ghi nhiệt với dữ liệu từ API thật
- [ ] Trạng thái trống, đang tải, lỗi mạng và xung đột phiên bản hiện đúng chỗ
- [ ] Vai ngoài admin chỉ xem; kiểm thử đầu-cuối cho ba luồng thêm nhà cung cấp, đổi dải vị trí, thêm thiết bị
### Chỗ hở
- GAP-32 wireframe SCR029 chưa có ô sức chứa và trạng thái đóng hoặc mở

## E03-I07 · Danh mục xe và tài xế (SCR036 phần xe, tài xế)
- Loại: issue
- Cha: E03
- Nhãn: type:issue
- Spec: SCR036, MASTER_DB, DR-ROUTE-01
- Phụ thuộc: E03-I01
### Mô tả
Quản lý xe (kèm dải nhiệt xe chở được) và tài xế (chỉ giữ định danh vận hành, không tên, không điện thoại, không số bằng lái). Chỉ phần xem và tạo, sửa xe, tài xế thuộc issue này. Khối ghi dữ liệu giờ làm của tài xế trên cùng màn thuộc epic giờ lái xe và được dựng sau, không làm ở đây.
### Định nghĩa xong
- [ ] Luồng tạo và sửa xe, tạo và sửa tài xế chạy được đầu-cuối
- [ ] Dữ liệu tài xế lưu và hiển thị không chứa thông tin cá nhân ngoài định danh vận hành
### Chỗ hở
- GAP-34 màn chung với phần dữ liệu giờ làm của tài xế, biên giữa hai epic cần giữ rõ

## E03-I07-S01 · API xe và tài xế (BE)
- Loại: sub-issue
- Cha: E03-I07
- Vai: BE-2
- Nhãn: type:sub-issue
- Spec: API-MST-18, API-MST-19, API-MST-20, API-MST-21, DR-PRIV-01
- Phụ thuộc: E03-I01
### Mô tả
Bốn API đọc và lưu xe, tài xế. Danh sách tài xế phải trả liên kết tới tài khoản đăng nhập để màn quản trị người dùng ghép cột; việc màn nào gán liên kết còn mở. Quyền: vai điều phối đọc, admin ghi.
### Định nghĩa xong
- [ ] Hợp đồng bốn API được viết rồi hiện thực, mã xe và mã tài xế kiểm khuôn
- [ ] Mỗi xe bắt buộc có dải nhiệt; mỗi tài khoản chỉ gắn một tài xế
- [ ] Phản hồi tài xế không có cột cá nhân; có kiểm thử khẳng định lược đồ phản hồi
- [ ] Lưu có khoá phiên bản; kiểm thử quyền và xung đột phiên bản qua
### Chỗ hở
- GAP-35 chưa rõ màn nào gán liên kết giữa tài khoản và tài xế

## E03-I07-S02 · Màn SCR036: khối xe và tài xế (FE)
- Loại: sub-issue
- Cha: E03-I07
- Vai: FE-1
- Nhãn: type:sub-issue
- Spec: SCR036
- Phụ thuộc: E03-I07-S01
### Mô tả
Dựng khối danh sách và form tạo, sửa xe và tài xế theo wireframe. Chừa chỗ cho khối ghi dữ liệu giờ làm để epic giờ lái xe gắn vào mà không phải dựng lại bố cục.
### Định nghĩa xong
- [ ] Danh sách xe và tài xế, form tạo và sửa chạy với API thật, lỗi hiện tại đúng ô
- [ ] Trạng thái trống, đang tải, lỗi mạng và xung đột phiên bản hiện đúng
- [ ] Vai ngoài admin chỉ xem; vị trí khối dữ liệu giờ làm có điểm gắn rõ và không hiện nút tạm
- [ ] Kiểm thử đầu-cuối luồng tạo xe và luồng tạo tài xế qua
### Chỗ hở
- GAP-34 ranh giới với khối dữ liệu giờ làm cần được E09 xác nhận

## E03-I08 · Làm rõ với khách về vòng đời master và danh mục (type:clarification)
- Loại: issue
- Cha: E03
- Vai: BrSE
- Nhãn: type:issue, type:clarification
- Spec: SCR026, SCR027, SCR028, SCR029, DR-MST-01
### Mô tả
Gom các câu hỏi khách về dữ liệu gốc mà thiết kế hiện chỉ giữ mặc định của nhà thầu: SKU có phải qua đề nghị và duyệt không; có ngừng SKU không và ngừng ra sao; có cần phiên bản lên lịch sau ngày nghiệp vụ không; đổi dải nhiệt SKU khi còn tồn thì cảnh báo hay chặn; định dạng mã khách cũ; cận trên của thời gian dừng giao tại điểm giao (bản mẫu giới hạn 1 đến 240 phút nhưng không có nguồn, nếu cần giới hạn thì phải thêm khoá cấu hình mới); có cần ngày hiệu lực tương lai cho master còn lại; màn nào gán tài khoản cho tài xế; ma trận vai trò hai màn nhà cung cấp và vị trí kho. Các issue build vẫn chạy theo mặc định hiện tại; câu trả lời khác mặc định thì mở issue sửa.
### Định nghĩa xong
- [ ] Gửi khách bộ câu hỏi đóng, mỗi câu kèm phương án mặc định và hệ quả nếu khác mặc định
- [ ] Ghi câu trả lời thật vào nhật ký quyết định của dự án, đánh dấu câu nào khác mặc định
- [ ] Mỗi câu trả lời khác mặc định có một issue sửa hoặc cập nhật vào issue E03 bị ảnh hưởng
- [ ] Cập nhật danh sách giả định còn chờ khách xác nhận
### Chỗ hở
- GAP-35 gom các câu hỏi vòng đời master của SKU và master còn lại
- GAP-35 chưa rõ màn nào gán tài khoản cho tài xế
- GAP-12 ma trận vai trò các màn chưa dựng ở prototype cần khách duyệt
