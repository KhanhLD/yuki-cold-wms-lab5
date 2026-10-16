# E06 · Hạn dùng & phân bổ FEFO
<!-- lab5-epic: E06 -->

## E06 · Hạn dùng & phân bổ FEFO
- Loại: epic
- Nhãn: type:epic, constraint:fefo-expiry, constraint:date-reversal, constraint:expiry
- Spec: SCR009, SCR010, SCR011, SCR035, SCR037, ALC_DATE_REVERSAL_CHECK, ALC_FEFO_ALLOCATION, ALLOCATION_DB, ADR-004, FR-OUT-02

### Mô tả
Gom toàn bộ phần hạn dùng và phân bổ lô cho đơn: khai loại hạn của SKU, quy tắc cửa sổ giao theo thoả thuận, chuỗi loại trừ năm bước theo thứ tự cố định, xếp hạng FEFO / FIFO, kiểm 日付逆転 theo cặp khách gốc × SKU với khoá cặp, luồng ngoại lệ cửa sổ giao và màn tra lịch sử giao. Server giữ mọi luật; màn hình chỉ hiển thị kết quả từng bước từng lô. Epic này chịu ba ràng buộc đặc thù của sản phẩm: hai loại hạn có hai nhánh quyết định khác nhau, thứ tự loại trừ không đổi, và lô sớm hạn phải đi trước nhưng không bao giờ được đảo hạn so với lịch sử khách đã nhận.

### Định nghĩa xong
- [ ] Mọi issue con của epic ở trạng thái xong và qua kiểm thử của mình
- [ ] Một dòng đơn chạy trọn luồng đánh giá, xin ngoại lệ (nếu đủ điều kiện), duyệt, phân bổ lô hạng 1 trên môi trường thử, có dấu vết chặn ở mọi lần bị loại

## E06-I01 · Màn cấu hình loại hạn theo SKU (SCR009)
- Loại: issue
- Cha: E06
- Vai: FE-1
- Nhãn: type:issue, constraint:expiry
- Spec: SCR009, API-MST-02, API-MST-03, BR-EXP-01, JP-06
- Phụ thuộc: E03-I03-S01
### Mô tả
Dựng màn để quản trị chọn một SKU và khai đúng một trong hai loại hạn (hạn an toàn hoặc hạn ngon nhất). Đổi loại hạn tạo phiên bản SKU mới có ngày hiệu lực là ngày nghiệp vụ, không hồi tố. API đọc / ghi SKU do epic dữ liệu gốc làm; issue này chỉ làm màn, gọi đúng hợp đồng đã có và kiểm thử theo kịch bản của màn. Màn không tự suy nhánh chặn hay cảnh báo; việc đó do server làm ở màn nhận hàng và màn phân bổ.
### Định nghĩa xong
- [ ] Chọn SKU hiện đúng cấu hình đang hiệu lực, cặp radio luôn có đúng một giá trị và bảng giải nghĩa hai loại hạn hiện đủ hai nhánh
- [ ] Bấm đổi đọc lại SKU rồi gửi lại đủ sáu trường nội dung đã đọc (chỉ đổi loại hạn) theo đúng luồng đọc rồi lưu của API ở E03-I03-S01, ngày hiệu lực bằng ngày nghiệp vụ và phiên bản kỳ vọng; thành công hiện thông báo đã lưu và cập nhật khung cấu hình
- [ ] Xung đột phiên bản thì đọc lại SKU và báo; đổi lần hai trong cùng ngày nghiệp vụ bị từ chối và hiện đúng thông điệp lỗi của mã
- [ ] Vai trò khác quản trị chỉ xem (khối đổi bị ẩn), phiên hết hạn về màn đăng nhập, màn không có ô nhập ngày sản xuất
- [ ] 11 kịch bản AC-SCR009-01..11 chạy tự động qua
### Chỗ hở
- GAP-41 chưa rõ lô còn tồn nhận trước ngày hiệu lực dùng loại hạn cũ hay mới khi phân bổ

## E06-I02 · Màn đặt luật cửa sổ giao theo thoả thuận (SCR011)
- Loại: issue
- Cha: E06
- Nhãn: type:issue, constraint:fefo-expiry
- Spec: SCR011, API-MST-06, API-MST-08, BR-DELWIN-01, JP-09
- Phụ thuộc: E03-I04-S01
### Mô tả
Màn chỉ có wireframe. Gồm điểm cuối đặt luật cửa sổ giao theo thoả thuận (API-MST-08, thuộc E06 vì màn chính của nó là SCR011 và epic dữ liệu gốc không làm) và màn đặt luật. Điểm cuối đọc thoả thuận (API-MST-06) do epic dữ liệu gốc làm, trả cả số phiên bản thoả thuận mà dòng đơn của phân bổ ghi lại. Nút mở màn này nằm ở màn khách và thoả thuận của epic dữ liệu gốc. Thoả thuận chưa có luật phải hiện rõ là chờ nghiệp vụ, không suy giá trị mặc định.
### Định nghĩa xong
- [ ] Đặt được luật cho một thoả thuận trên môi trường thử, màn hiện luật mới và lần đánh giá phân bổ kế tiếp dùng luật đó
- [ ] Đặc tả màn và hợp đồng API-MST-08 đã duyệt

## E06-I02-S01 · API đặt luật cửa sổ giao của thoả thuận (API-MST-08)
- Loại: sub-issue
- Cha: E06-I02
- Vai: BE-2
- Nhãn: type:sub-issue, constraint:fefo-expiry
- Spec: API-MST-08, BR-DELWIN-01, JP-09
- Phụ thuộc: E03-I04-S01
### Mô tả
Viết hợp đồng chi tiết rồi cài điểm cuối đặt luật cửa sổ giao cho một thoả thuận: một trong ba giá trị (1/3, 1/2, theo ngày trên nhãn), ngày hiệu lực, phiên bản kỳ vọng. Mỗi lần đặt tạo phiên bản thoả thuận mới (số này được API đọc thoả thuận ở E03-I04-S01 trả ra và dòng đơn ghi lại); chỉ vai trò kinh doanh được ghi; mọi thay đổi ghi nhật ký trước và sau. Điểm cuối nằm ở module dữ liệu gốc nhưng do E06 sở hữu vì chỉ màn SCR011 gọi.
### Định nghĩa xong
- [ ] Hợp đồng chi tiết viết theo khuôn API và được duyệt, dùng bảng mã lỗi chung
- [ ] Giá trị ngoài ba giá trị cho phép bị từ chối bằng mã lỗi đúng; ngày hiệu lực không hợp lệ bị từ chối
- [ ] Đặt luật tạo phiên bản thoả thuận mới, bản cũ giữ nguyên; hai người đặt cùng lúc thì người sau nhận xung đột phiên bản
- [ ] Vai trò ngoài kinh doanh nhận 403; kiểm thử quyền cho từng vai
- [ ] Kiểm thử tích hợp: sau khi đặt luật, API đánh giá phân bổ (E06-I05-S01) thấy luật và phiên bản mới
### Chỗ hở
- GAP-44 điểm cuối chưa có hợp đồng trong LAB-4

## E06-I02-S02 · Màn đặt luật cửa sổ giao (SCR011)
- Loại: sub-issue
- Cha: E06-I02
- Vai: FE-1
- Nhãn: type:sub-issue, constraint:fefo-expiry
- Spec: SCR011, API-MST-06, API-MST-08
- Phụ thuộc: E06-I02-S01, E03-I04-S01
### Mô tả
Viết đặc tả chi tiết của màn rồi dựng: danh sách thoả thuận khách × SKU kèm luật đang hiệu lực, đặt một trong ba giá trị với ngày hiệu lực, bảng minh hoạ cách tính (tổng hạn, tỷ lệ áp, ngày biên). Màn mở được từ nút ở màn khách và thoả thuận, nhận mã thoả thuận qua đường dẫn. Thoả thuận chưa có luật hiện nhãn chờ nghiệp vụ, không có giá trị suy ra.
### Định nghĩa xong
- [ ] Đặc tả màn viết xong theo khuôn của các màn đã chi tiết, được duyệt, có bảng item, trạng thái và tiêu chí chấp nhận
- [ ] Danh sách hiện đúng luật đang hiệu lực; thoả thuận chưa có luật hiện nhãn chờ nghiệp vụ và không có giá trị suy ra
- [ ] Đặt luật mới gửi đúng một trong ba giá trị cùng ngày hiệu lực; giá trị ngoài tập bị chặn tại ô
- [ ] Bảng minh hoạ tính ngày biên giống hàm của server với hạn lẻ (làm tròn xuống), ngày giao đúng biên được tính là đạt
- [ ] Mở màn từ nút của màn khách và thoả thuận (E03-I04-S02) vào đúng thoả thuận; chỉ vai trò kinh doanh được ghi, vai trò khác chỉ xem
- [ ] Có kiểm thử giao diện cho các nhánh trên
### Chỗ hở
- GAP-44 hai API của thoả thuận chưa có hợp đồng chi tiết

## E06-I03 · Cơ sở dữ liệu phân bổ (ALLOCATION_DB)
- Loại: issue
- Cha: E06
- Vai: BE-1
- Nhãn: type:issue, constraint:date-reversal, constraint:fefo-expiry
- Spec: ALLOCATION_DB, DR-HIST-01
### Mô tả
Dựng lược đồ của module phân bổ theo file thiết kế: dòng đơn, phân bổ, yêu cầu ngoại lệ, lịch sử giao đã được khách nhận (mốc của 日付逆転), hàng đợi business-review, nhật ký bỏ qua dòng thiếu hạn, sự kiện ngoại lệ hạn dùng và sự kiện chặn phân bổ. Các bảng lịch sử và nhật ký chỉ ghi thêm. Nhóm lưu giữ của từng bảng để cấu hình được vì giá trị chưa chốt.
### Định nghĩa xong
- [ ] Migration tạo đủ 8 bảng với khoá, ràng buộc và chỉ mục theo thiết kế; chạy lên và lùi xuống trên cơ sở dữ liệu rỗng không lỗi
- [ ] Kiểm thử ràng buộc: số lượng dương, trạng thái hợp lệ, mỗi đơn × lô chỉ một yêu cầu ngoại lệ đang chờ, mã tham chiếu đơn không trùng
- [ ] Bảng lịch sử và nhật ký không cho sửa / xoá qua tài khoản ứng dụng (thử UPDATE và DELETE đều bị từ chối)
- [ ] Dòng đơn có cột ghi lại số phiên bản thoả thuận lấy từ API thoả thuận của E03-I04-S01; thử ghi dòng đơn có thoả thuận mà thiếu số phiên bản thì bị từ chối
- [ ] Truy vấn tìm mốc theo cặp khách × SKU dùng chỉ mục, có kiểm tra kế hoạch truy vấn trên dữ liệu thử cỡ lớn
- [ ] Tài liệu lược đồ trong repo khớp migration
### Chỗ hở
- GAP-09 nhóm lưu giữ của các bảng phân bổ chưa có trong bảng lưu giữ chung

## E06-I04 · Kiểm 日付逆転 theo khách gốc × SKU và khoá cặp
- Loại: issue
- Cha: E06
- Nhãn: type:issue, constraint:date-reversal
- Spec: ALC_DATE_REVERSAL_CHECK, ADR-004, BR-DATE-01, JP-10
- Phụ thuộc: E06-I03
### Mô tả
Hàm luật duy nhất của cấm đảo hạn, đặt trong module phân bổ và dùng chung cho lúc phân bổ lẫn lúc kiểm trước xuất phát. Mốc là hạn lớn nhất đã được khách nhận của cặp khách gốc × SKU; hạn đề xuất sớm hơn mốc thì chặn, bằng mốc thì qua, không so với hôm nay, không mượn lịch sử khách khác, không có ngoại lệ. Kèm khoá cặp để hai lệnh song song cùng cặp không cùng đọc một mốc cũ.
### Định nghĩa xong
- [ ] Hàm được gọi qua một giao diện chung từ phân bổ và từ cổng xuất phát, không nơi nào tự tính mốc
- [ ] Cả hai sub-issue qua kiểm thử và bộ ca biên 18 ca của thiết kế chạy xanh

## E06-I04-S01 · Hàm tính mốc và so hạn
- Loại: sub-issue
- Cha: E06-I04
- Vai: BE-1
- Nhãn: type:sub-issue, constraint:date-reversal
- Spec: ALC_DATE_REVERSAL_CHECK, BR-DATE-01, JP-10
- Phụ thuộc: E01-I13, E01-I14, E03-I05
### Mô tả
Cài hàm tính mốc và so sánh: xác định cặp và ngày áp dụng (ngày nghiệp vụ lúc phân bổ, ngày chuyến lúc xuất phát), gộp lịch sử của mã khách cũ chỉ qua ánh xạ đã duyệt và có hiệu lực tại ngày áp dụng, xử lý dòng lịch sử thiếu hạn (cũ hơn mốc thì bỏ qua và ghi nhật ký, mới hơn mốc hoặc cặp chỉ có dòng thiếu hạn thì chuyển business-review và chặn riêng cặp). Chế độ xuất phát nhận thêm tập chuyến khác cùng cặp do cổng xuất phát truyền vào. Kết quả gồm đạt, chặn kèm mốc và dòng làm mốc, hoặc business-review kèm lý do.
### Định nghĩa xong
- [ ] Bảng ca biên 18 ca của thiết kế chạy thành kiểm thử tự động, tất cả xanh, gồm lịch sử không đơn điệu, hai lần giao cùng hạn, ánh xạ chưa duyệt và ánh xạ có hiệu lực sau ngày áp dụng
- [ ] Cặp chưa có lịch sử thì qua và không mượn lịch sử khách khác; khách đổi mã vẫn bị so với lịch sử mã cũ khi ánh xạ đủ điều kiện
- [ ] Dòng lịch sử thiếu hạn mới hơn mốc đưa cặp vào hàng đợi business-review và chặn đúng cặp đó; dòng nguồn không bị sửa
- [ ] Hạn đề xuất sớm hơn mốc trả chặn kèm mốc, dòng làm mốc và (ở chế độ xuất phát) chuyến gây đảo
- [ ] Cờ cấu hình bật / tắt gộp mã khách và chính sách dòng thiếu hạn đọc từ kho cấu hình, không viết cứng
### Chỗ hở
- GAP-40 mốc "hạn lớn nhất" và nhánh chỉ có dòng thiếu hạn là cách đọc chưa khách thật xác nhận
- GAP-42 chưa có người ký và màn xử lý hàng đợi business-review
- GAP-51 mốc dựa vào lịch sử giao nạp từ lượt kiểm di trú, thứ tự nạp trước khi bật phân bổ thật chưa nêu

## E06-I04-S02 · Khoá cặp khách gốc × SKU và thứ tự khoá thống nhất
- Loại: sub-issue
- Cha: E06-I04
- Vai: BE-2
- Nhãn: type:sub-issue, constraint:date-reversal
- Spec: ADR-004, ALC_DATE_REVERSAL_CHECK
### Mô tả
Cài tiện ích khoá dùng chung cho mọi lệnh đọc mốc rồi ghi: khoá mức giao dịch theo băm của khoá "khách gốc | SKU", trong đó khách gốc là cả thành phần ánh xạ đã duyệt bất kể ngày hiệu lực. Thứ tự lấy khoá cố định: chuyến, dòng đơn, các cặp theo thứ tự sắp, các lô theo mã lô; trạng thái lô và ca thu hồi chỉ đọc sau khi giữ khoá lô. Hết giờ chờ thì quay lui và trả lỗi theo bảng lỗi chung.
### Định nghĩa xong
- [ ] Tiện ích khoá có một chỗ gọi duy nhất cho phân bổ và cổng xuất phát, kèm tài liệu ngắn về thứ tự khoá
- [ ] Kiểm thử đồng thời: hai phân bổ song song cùng khách gốc × SKU (một dưới mã cũ, một dưới mã mới) tuần tự hoá, người sau thấy mốc mới hoặc nhận lỗi xung đột
- [ ] Kiểm thử nhiều luồng cố ý lấy khoá theo mọi thứ tự lệnh có thể xảy ra không sinh deadlock
- [ ] Hết giờ chờ khoá quay lui sạch, trả mã lỗi đúng và cho gửi lại cùng khoá idempotency
- [ ] Số đo thời gian chờ khoá ở tải cao của kịch bản kiểm tải được ghi lại để chốt thời gian chờ tối đa
### Chỗ hở
- GAP-49 ghi dấu vết mỗi lần chặn chỉ giữ lần đầu của ngày, cần xác nhận có đủ cho kiểm toán

## E06-I05 · Chuỗi loại trừ, xếp hạng FEFO và phân bổ nguyên tử (ALC_FEFO_ALLOCATION)
- Loại: issue
- Cha: E06
- Nhãn: type:issue, constraint:fefo-expiry, constraint:expiry, constraint:date-reversal
- Spec: ALC_FEFO_ALLOCATION, FR-OUT-02, JP-16, BR-FEFO-01
- Phụ thuộc: E06-I03, E06-I04
### Mô tả
Hai lệnh của module phân bổ: đánh giá (tư vấn, không khoá, trả kết quả từng bước từng lô) và phân bổ (khoá, chạy lại toàn bộ chuỗi trên dữ liệu mới rồi ghi). Chuỗi cố định năm bước: hạn an toàn đạt hoặc quá, lệch dải nhiệt, đang cách ly kể cả lô thuộc ca thu hồi mở, cửa sổ giao theo thoả thuận, 日付逆転; lô còn lại xếp FEFO, cùng hạn thì FIFO theo giờ nhận. Hạn ngon nhất chỉ cảnh báo và phải xác nhận. Chỉ lô hạng 1 còn tồn được phân bổ.
### Định nghĩa xong
- [ ] Hai API chạy trên môi trường thử với hợp đồng đã chi tiết; bộ ca biên 22 ca của thiết kế chạy tự động xanh
- [ ] Phân bổ song song cùng lô hoặc cùng dòng đơn không bao giờ vượt tồn hay vượt số còn thiếu

## E06-I05-S01 · API đánh giá chuỗi loại trừ (API-ALC-02)
- Loại: sub-issue
- Cha: E06-I05
- Vai: BE-1
- Nhãn: type:sub-issue, constraint:fefo-expiry, constraint:expiry, constraint:recall
- Spec: API-ALC-02, BR-EXP-02, JP-06, JP-07, BR-DELWIN-01
- Phụ thuộc: E01-I13, E01-I14, E03-I04-S01, E05-I02-S01
### Mô tả
Điểm cuối đánh giá một dòng đơn: tính ngày so hạn, chạy năm bước cho từng lô và trả kết quả từng bước, bước loại, cảnh báo hạn ngon nhất, điều kiện được xin ngoại lệ, lô gợi ý hạng 1 và các dấu (phiên bản lô, phiên bản thoả thuận, dấu lịch sử) để lệnh phân bổ so lại. Mỗi lần chặn ghi một dòng dấu vết bất biến và phát sự kiện chặn. Luật cửa sổ chưa cấu hình hoặc đơn không có thoả thuận thì chặn theo business-review, không suy mặc định.
### Định nghĩa xong
- [ ] Hạn an toàn bằng hoặc trước ngày so thì chặn ở bước 1 dù hôm nay còn hạn; hạn ngon nhất gần hạn hoặc đã qua chỉ sinh cảnh báo, không loại
- [ ] Lô lệch dải nhiệt chặn ở bước 2, lô cách ly hoặc thuộc ca thu hồi mở chặn ở bước 3, cả hai không có ngoại lệ
- [ ] Ba luật cửa sổ giao tính đúng với hạn lẻ: giao đúng ngày biên đạt, vượt một ngày chặn; thoả thuận chưa có luật trả business-review
- [ ] Lô chỉ bị chặn riêng ở bước 4 bởi luật đã cấu hình mới có cờ đủ điều kiện xin ngoại lệ
- [ ] Mỗi lần chặn có đúng một dòng dấu vết cho mỗi đơn × lô × bước × ngày nghiệp vụ và một sự kiện phát ra
- [ ] Ngày so hạn lấy từ đồng hồ nghiệp vụ và luật đọc qua hàm đọc khoá cấu hình có ngày, không gọi giờ hệ thống trực tiếp; số phiên bản thoả thuận trả kèm kết quả lấy từ API thoả thuận, không tự đặt
- [ ] Kiểm thử hợp đồng khớp file API, gồm các mã 400, 403, 404, 422 của hợp đồng
### Chỗ hở
- GAP-40 cách đọc ngày so hạn, làm tròn xuống và ngưỡng 3 ngày chưa khách thật xác nhận
- GAP-50 nhãn cho trạng thái business-review còn thiếu ở wireframe

## E06-I05-S02 · API phân bổ nguyên tử và xếp hạng FEFO / FIFO (API-ALC-03)
- Loại: sub-issue
- Cha: E06-I05
- Vai: BE-2
- Nhãn: type:sub-issue, constraint:fefo-expiry, constraint:date-reversal
- Spec: API-ALC-03, BR-FEFO-01, JP-08, JP-16
- Phụ thuộc: E06-I04-S02, E05-I01, E05-I02-S01, E01-I13, E05-I10
### Mô tả
Điểm cuối phân bổ một lô: khoá dòng đơn, cặp, rồi lô theo thứ tự thống nhất; chạy lại chuỗi trên dữ liệu mới; so các dấu của lần đánh giá; chỉ nhận lô hạng 1 còn tồn trong tập xếp hạng (gồm lô đã được duyệt ngoại lệ cửa sổ giao nhưng vẫn chịu FEFO); ghi phân bổ, trừ tồn lô, cập nhật dòng đơn trong một giao dịch. Gửi lại cùng khoá idempotency trả kết quả đã lưu.
### Định nghĩa xong
- [ ] Lô không phải hạng 1 bị từ chối bằng mã riêng kèm lô đứng đầu; lô hạng 1 có ngoại lệ đã duyệt được phân bổ và gắn mã duyệt vào lần phân bổ
- [ ] Hai yêu cầu cùng lô: đúng một thành công, người sau nhận mã xung đột kèm lô kế tiếp đề xuất; dấu lô, thoả thuận hoặc lịch sử lệch thì trả 409 và không ghi
- [ ] Lô vẫn bị chuỗi chặn dưới khoá (ví dụ ca thu hồi vừa mở) trả 422 và để lại dòng chặn, không ghi phân bổ
- [ ] Lô có cảnh báo hạn ngon nhất mà chưa xác nhận bị từ chối bằng mã riêng; có xác nhận thì lưu cờ xác nhận cùng phân bổ
- [ ] Gửi lại cùng khoá idempotency không phân bổ lần hai và không kiểm lại tồn
- [ ] Đọc lô, tồn, dấu chặn và trừ tồn qua giao diện đặt tên của module tồn kho (hợp đồng chốt cùng E05-I01), trong cùng giao dịch với ghi phân bổ; không truy cập trực tiếp bảng của kho
### Chỗ hở
- GAP-40 lô hạng 1 bắt buộc là cách đọc của nhà thầu, khách thật chưa xác nhận đường vượt
- GAP-43 chưa có thao tác huỷ hay đảo một phân bổ đã ghi

## E06-I05-S03 · Bộ kiểm thử ca biên chuỗi loại trừ và FEFO
- Loại: sub-issue
- Cha: E06-I05
- Vai: QA-1
- Nhãn: type:sub-issue, constraint:fefo-expiry
- Spec: ALC_FEFO_ALLOCATION, API-ALC-02, API-ALC-03
- Phụ thuộc: E06-I05-S01, E06-I05-S02
### Mô tả
Viết và chạy bộ kiểm thử tích hợp cho cả hai điểm cuối trên cơ sở dữ liệu thật của môi trường thử, bám bảng ca biên của thiết kế (FEFO, FIFO cùng hạn, ba luật cửa sổ, hạn an toàn, ca thu hồi mở giữa lúc đánh giá và phân bổ, ngoại lệ đã duyệt nhưng còn lô hạn sớm hơn) và kiểm thử đồng thời.
### Định nghĩa xong
- [ ] 22 ca biên của thiết kế có kiểm thử tương ứng, mỗi ca ghi rõ mã ca trong tên kiểm thử
- [ ] Ca luật không lùi: đặt ngày nghiệp vụ giả lập lùi thì kết quả ngày so hạn không đổi và không có chỗ nào gọi giờ hệ thống trực tiếp; các ca rút từ prototype được đánh dấu để đối chiếu
- [ ] Kiểm thử đồng thời hai phân bổ cùng lô và hai phân bổ cùng dòng đơn chạy lặp 50 lần không lần nào vượt tồn
- [ ] Báo cáo kết quả ghi số ca pass / fail, ca nào còn dựa trên cách đọc chưa khách xác nhận
- [ ] Kiểm thử chạy được trong CI của dự án trong thời gian dưới giới hạn đã thoả thuận với nền tảng

## E06-I06 · Màn phân bổ có cảnh báo (SCR010)
- Loại: issue
- Cha: E06
- Nhãn: type:issue, constraint:fefo-expiry, constraint:expiry, constraint:date-reversal
- Spec: SCR010, API-ALC-01, FR-OUT-02
- Phụ thuộc: E06-I05
### Mô tả
Màn trung tâm của epic, chi tiết nhất của bộ đặc tả: mở một dòng đơn, hiện ngày so hạn, số còn phải phân bổ, bảng kết quả năm bước cho từng lô, đề xuất FEFO, khung cảnh báo hạn ngon nhất và lối xin ngoại lệ cho lô chỉ bị chặn bởi cửa sổ giao. Màn không tự tính bước nào; chỉ hiển thị kết quả server trả và gửi lại các dấu nguyên văn. Giao diện chia hai phần (đánh giá và phân bổ; xin ngoại lệ) để làm song song, kèm một bộ kiểm thử chấp nhận riêng.
### Định nghĩa xong
- [ ] Hai phần giao diện và bộ kiểm thử đều xong; một dòng đơn chạy được từ nạp màn đến phân bổ thành công trên môi trường thử

## E06-I06-S01 · Giao diện đánh giá và phân bổ lô
- Loại: sub-issue
- Cha: E06-I06
- Vai: FE-1
- Nhãn: type:sub-issue, constraint:fefo-expiry, constraint:expiry, constraint:date-reversal
- Spec: SCR010, API-ALC-01, API-ALC-02, API-ALC-03
### Mô tả
Dựng phần chính của màn: nạp dòng đơn (hợp đồng đọc chi tiết), nút đánh giá lại, bảng lô × bước, dòng lô hợp lệ có ô nhập số lượng và nút phân bổ chỉ cho lô hạng 1, các dòng lô bị loại ở bước thu hồi, bước đảo hạn và bước cửa sổ, khung cảnh báo hạn ngon nhất với ô tích xác nhận, kết quả đã phân bổ, xung đột và đánh giá lại, bảng phân bổ đã ghi. Đánh giá tự chạy khi mở màn, bấm đánh giá lại và sau mọi phản hồi của lệnh phân bổ.
### Định nghĩa xong
- [ ] Bảng hiện đủ năm ô bước cho mỗi lô với badge có chữ (không chỉ màu), bước loại đầu tiên và câu lý do đúng mã lỗi
- [ ] Nút phân bổ chỉ xuất hiện ở lô hạng 1 còn tồn; số lượng ngoài khoảng 1 đến số còn thiếu bị chặn tại ô
- [ ] Lô có cảnh báo hạn ngon nhất bắt tích xác nhận trước khi gửi; thiếu xác nhận thì không gọi API
- [ ] Phản hồi 409 làm đánh giá lại tự động và hiện lô kế tiếp; phản hồi 422 hiện đúng khung (thu hồi, không có lô, không có thoả thuận)
- [ ] Gửi lại sau lỗi mạng giữ nguyên khoá idempotency, mỗi lần bấm mới dùng khoá mới
### Chỗ hở
- GAP-50 nhãn của trạng thái business-review và các câu còn thiếu ở wireframe

## E06-I06-S02 · Khối xin ngoại lệ cửa sổ giao và ngoại lệ đã duyệt trên SCR010
- Loại: sub-issue
- Cha: E06-I06
- Vai: FE-1
- Nhãn: type:sub-issue, constraint:fefo-expiry
- Spec: SCR010, API-ALC-04
- Phụ thuộc: E06-I07-S01
### Mô tả
Dựng khối xin ngoại lệ chỉ hiện cho lô đủ điều kiện và vai trò được xin (nhập lý do, gửi), thông báo đã gửi, khối ngoại lệ đã duyệt (hiện mã duyệt, nút phân bổ bằng mã chỉ khi lô là hạng 1), thông báo khi lô bị chặn cùng bước khác nên không xin được, và liên kết sang màn tra lịch sử giao theo đúng cặp khách × SKU.
### Định nghĩa xong
- [ ] Khối xin chỉ hiện khi lô đủ điều kiện và vai trò được phép; lý do bắt buộc có giới hạn độ dài và báo lỗi tại ô
- [ ] Gửi thành công hiện thông báo chờ duyệt và lô vẫn bị chặn; yêu cầu chưa duyệt không mở khoá phân bổ
- [ ] Ngoại lệ đã duyệt hiện mã duyệt; nút phân bổ chỉ có khi lô là hạng 1, nếu còn lô hạn sớm hơn thì chỉ hiện thông tin
- [ ] Lô bị chặn ở bước 4 cùng bước khác, hoặc chặn ở bước 5, không hiện khối xin và hiện câu giải thích
- [ ] Liên kết mở màn tra lịch sử đúng cặp khách × SKU của dòng đơn

## E06-I06-S03 · Kiểm thử chấp nhận màn phân bổ
- Loại: sub-issue
- Cha: E06-I06
- Vai: QA-1
- Nhãn: type:sub-issue, constraint:fefo-expiry, constraint:date-reversal
- Spec: SCR010, FR-OUT-02
- Phụ thuộc: E06-I06-S01, E06-I06-S02
### Mô tả
Viết và chạy kiểm thử giao diện từ đầu đến cuối cho 35 kịch bản chấp nhận của màn trên môi trường thử với dữ liệu cố định, gồm các ca JP-06, JP-07, ba luật cửa sổ, 日付逆転, thu hồi, ngoại lệ, đồng thời và quyền theo vai trò.
### Định nghĩa xong
- [ ] 35 kịch bản AC-SCR010-01..35 có kiểm thử tự động; ca nào chưa tự động hoá được ghi lý do
- [ ] Hai người phân bổ cùng lô qua hai phiên trình duyệt: một thành công, một thấy lô kế tiếp
- [ ] Ca ngoại lệ chạy trọn xin, duyệt (bằng người duyệt khác), phân bổ bằng mã
- [ ] Báo cáo kết quả có ảnh chụp màn cho các ca chặn

## E06-I07 · Luồng ngoại lệ cửa sổ giao và màn duyệt ngoại lệ (SCR037)
- Loại: issue
- Cha: E06
- Nhãn: type:issue, constraint:fefo-expiry
- Spec: SCR037, API-ALC-04, API-ALC-06, API-ALC-07, BR-DELWIN-01
- Phụ thuộc: E06-I03
### Mô tả
Ngoại lệ chỉ áp cho cửa sổ giao theo thoả thuận, không bao giờ cho hạn an toàn, nhiệt, cách ly hay 日付逆転. Gồm ba điểm cuối (xin, danh sách chờ, quyết định) và màn duyệt chỉ có wireframe. Người duyệt là trưởng phòng kinh doanh và khác người xin. Luồng do nhà thầu đề xuất, khách chưa xác nhận.
### Định nghĩa xong
- [ ] Một yêu cầu xin đi trọn đến duyệt hoặc từ chối trên môi trường thử, và lô chỉ mở khoá bước 4 khi đã duyệt
- [ ] Đặc tả màn duyệt viết xong và được duyệt

## E06-I07-S01 · API xin, liệt kê và quyết định ngoại lệ (API-ALC-04, 06, 07)
- Loại: sub-issue
- Cha: E06-I07
- Vai: BE-2
- Nhãn: type:sub-issue, constraint:fefo-expiry
- Spec: API-ALC-04, API-ALC-06, API-ALC-07, BR-DELWIN-01
- Phụ thuộc: E01-I16, E02-I03
### Mô tả
Cài điểm cuối tạo yêu cầu (đã có hợp đồng), điểm cuối liệt kê hàng chờ và đã quyết, điểm cuối duyệt hoặc từ chối (hai điểm cuối sau cần viết hợp đồng chi tiết). Chỉ nhận lô bị chặn riêng bởi luật cửa sổ đã cấu hình, một đơn × lô một yêu cầu đang chờ, người duyệt đúng vai trò cấu hình và khác người xin; mọi thay đổi ghi nhật ký kiểm toán.
### Định nghĩa xong
- [ ] Hợp đồng chi tiết của hai điểm cuối còn lại viết xong theo khuôn API của dự án và được duyệt
- [ ] Yêu cầu cho lô bị chặn bởi 日付逆転, hạn an toàn, nhiệt hoặc cách ly bị từ chối bằng mã lỗi đúng, kể cả khi gọi thẳng API
- [ ] Người xin tự duyệt yêu cầu của mình bị từ chối bằng helper chặn tự duyệt chung của E02-I03; vai trò ngoài cấu hình không duyệt được; quyết định ghi vào sổ audit chung của E01-I16
- [ ] Yêu cầu thứ hai cho cùng đơn × lô khi đã có yêu cầu đang chờ bị từ chối
- [ ] Duyệt làm lô đủ điều kiện ở bước 4 của lần đánh giá kế tiếp và gắn mã duyệt vào phân bổ, kiểm thử tích hợp chứng minh điều đó
### Chỗ hở
- GAP-40 ngoại lệ vẫn chịu FEFO và luồng xin ngoại lệ đều là đề xuất nhà thầu chưa khách xác nhận

## E06-I07-S02 · Màn duyệt ngoại lệ phân bổ (SCR037)
- Loại: sub-issue
- Cha: E06-I07
- Vai: FE-1
- Nhãn: type:sub-issue, constraint:fefo-expiry
- Spec: SCR037, API-ALC-06, API-ALC-07
- Phụ thuộc: E06-I07-S01
### Mô tả
Màn chỉ có wireframe: viết đặc tả chi tiết rồi dựng hàng chờ duyệt và danh sách đã quyết, nút duyệt / từ chối kèm lý do, người xin chỉ xem yêu cầu của mình, người duyệt thấy toàn bộ.
### Định nghĩa xong
- [ ] Đặc tả màn viết xong theo khuôn các màn đã chi tiết, có item, trạng thái rỗng / lỗi và tiêu chí chấp nhận
- [ ] Hàng chờ hiện đúng yêu cầu theo vai trò; duyệt hoặc từ chối cập nhật danh sách và hiện mã yêu cầu
- [ ] Người xin không thấy nút quyết định cho yêu cầu của chính mình; vai trò ngoài ma trận không vào được màn
- [ ] Có kiểm thử giao diện cho duyệt, từ chối, xung đột khi hai người quyết cùng lúc

## E06-I08 · Màn tra lịch sử giao theo khách × SKU (SCR035)
- Loại: issue
- Cha: E06
- Nhãn: type:issue, constraint:date-reversal
- Spec: SCR035, API-ALC-08, DR-HIST-01, JP-10
- Phụ thuộc: E06-I04
### Mô tả
Màn tra cứu lịch sử giao đã được khách nhận của một cặp khách × SKU và mốc dùng cho 日付逆転: lô và hạn làm mốc, các dòng thiếu hạn (bỏ qua hay đưa cặp vào business-review), các mã khách cũ đã gộp qua ánh xạ hiệu lực. Chỉ hiện lịch sử của chính cặp, không bao giờ hiện của khách khác. Màn chỉ có wireframe nên có việc viết đặc tả và hợp đồng điểm cuối.
### Định nghĩa xong
- [ ] Màn hiện mốc, lô làm mốc, và giải thích vì sao một lô bị chặn theo cùng hàm luật của server
- [ ] Đặc tả màn và hợp đồng điểm cuối đã duyệt

## E06-I08-S01 · API lịch sử giao theo cặp khách × SKU (API-ALC-08)
- Loại: sub-issue
- Cha: E06-I08
- Vai: BE-1
- Nhãn: type:sub-issue, constraint:date-reversal
- Spec: API-ALC-08, DR-HIST-01, JP-10
### Mô tả
Viết hợp đồng chi tiết rồi cài điểm cuối đọc lịch sử giao đã được khách nhận theo cặp, trả kèm mốc tính bằng đúng hàm của luật, danh sách mã khách đã gộp, các dòng thiếu hạn và trạng thái business-review của cặp.
### Định nghĩa xong
- [ ] Hợp đồng chi tiết viết theo khuôn API, có phân trang và giới hạn dòng trả về theo cấu hình
- [ ] Mốc trả về trùng kết quả của hàm luật trên cùng dữ liệu (kiểm thử đối chiếu)
- [ ] Chỉ trả lịch sử của đúng cặp được hỏi; kiểm thử với khách thứ hai cùng SKU chứng minh không rò dữ liệu
- [ ] Phân quyền theo vai trò của ma trận; vai trò ngoài ma trận nhận 403
### Chỗ hở
- GAP-42 hàng đợi business-review chưa có người ký nên màn chỉ hiển thị trạng thái

## E06-I08-S02 · Màn tra lịch sử giao (SCR035)
- Loại: sub-issue
- Cha: E06-I08
- Vai: FE-1
- Nhãn: type:sub-issue, constraint:date-reversal
- Spec: SCR035, API-ALC-08
- Phụ thuộc: E06-I08-S01
### Mô tả
Viết đặc tả chi tiết của màn (chọn cặp, khung mốc, bảng dòng giao, khung dòng thiếu hạn, gợi ý ánh xạ mã cũ) rồi dựng màn theo đó. Màn nhận cặp khách × SKU từ liên kết trên màn phân bổ.
### Định nghĩa xong
- [ ] Đặc tả màn viết xong, có item, trạng thái rỗng / lỗi và tiêu chí chấp nhận
- [ ] Chọn cặp hiện đúng mốc, lô làm mốc, bảng dòng giao đã nhận và danh sách mã khách đã gộp
- [ ] Cặp đang ở business-review hiện rõ lý do và dòng thiếu hạn; không có nút sửa dòng nguồn
- [ ] Mở từ liên kết của màn phân bổ vào đúng cặp; có kiểm thử giao diện cho các nhánh trên

## E06-I09 · Làm rõ với khách các điểm mở của FEFO và 日付逆転
- Loại: issue
- Cha: E06
- Vai: BrSE
- Nhãn: type:issue, type:clarification, constraint:fefo-expiry
- Spec: SCR010, ALC_FEFO_ALLOCATION, BR-FEFO-01, ALC_DATE_REVERSAL_CHECK, SCR009
### Mô tả
Gom các câu cần khách thật trả lời mà thiết kế hiện đang chạy theo cách đọc của nhà thầu: có đường vượt FEFO hay không và ai duyệt; ngoại lệ cửa sổ giao đã duyệt có vẫn chịu FEFO không; lô còn tồn nhận trước ngày đổi loại hạn dùng loại hạn cũ hay mới; độ sâu lịch sử làm mốc và việc xoá lịch sử cũ; ai ký xử lý cặp đang business-review; số lượng mặc định điền sẵn ở ô phân bổ (hiện lấy số nhỏ hơn giữa số chưa phân bổ và tồn lô, theo prototype) có đúng với người dùng kho và kinh doanh không; ô kết quả có cần hiện thêm tổng hạn và tỷ lệ áp dụng hay chỉ ngày giao cuối, luật và thoả thuận như hiện nay (nguồn của phần thêm là story bản sau, không phải RFP, nên không chặn). Soạn bằng ví dụ số liệu để người trả lời không phải đọc đặc tả.
### Định nghĩa xong
- [ ] Bộ câu hỏi với ví dụ cụ thể gửi đúng người trả lời đã ghi trong sổ câu hỏi mở
- [ ] Mỗi câu có câu trả lời ghi lại (người, ngày) hoặc ghi chưa trả lời kèm hạn nhắc kế tiếp
- [ ] Đặc tả, khoá cấu hình và các issue E06-I05, E06-I06, E06-I07 được rà lại và ghi quyết định giữ hay đổi
### Chỗ hở
- GAP-40 FEFO hạng 1 là cách đọc nhà thầu, khách thật chưa xác nhận
- GAP-41 loại hạn áp cho lô tồn cũ khi SKU đổi loại hạn
- GAP-42 người ký xử lý cặp business-review
