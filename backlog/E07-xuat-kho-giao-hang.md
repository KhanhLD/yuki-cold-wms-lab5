# E07 · Xuất kho & giao hàng
<!-- lab5-epic: E07 -->

## E07 · Xuất kho & giao hàng
- Loại: epic
- Nhãn: type:epic, constraint:departure-gate, constraint:date-reversal, constraint:rice-beef-trace, constraint:expiry
- Spec: SCR012, SCR013, SCR014, SCR015, OUT_DEPARTURE_GATE, SHIPPING_DB, ADR-007, FR-OUT-01, FR-OUT-04, FR-OUT-05, FR-OUT-06

### Mô tả
Phần từ dòng đơn đến lúc khách nhận hàng: tạo và xem đơn, quét xếp hàng lên xe có đối chiếu với phân bổ, cổng kiểm trước xuất phát và xác nhận xuất phát, ghi bằng chứng bàn giao (POD) theo điều khoản giao, và khung PWA cho thiết bị tài xế với hàng đợi POD mã hoá khi mất mạng. Giao dịch kho không chạy offline; chỉ POD được xếp hàng. Epic mang các ràng buộc đặc thù: cổng xuất phát chặn theo nhiệt, seal, giờ, 日付逆転 theo thứ tự chuyến và lô thu hồi; truy xuất gạo và bò chặn lúc xếp hàng; dữ liệu cá nhân của người nhận tách bảng lưu ngắn hạn. Hai hạng mục Phase 2 (gộp nhiệm vụ chọn hàng, liên kết trả hàng và khiếu nại) chưa có màn nên không lập issue.

### Định nghĩa xong
- [ ] Mọi issue con của epic ở trạng thái xong và qua kiểm thử của mình
- [ ] Một chuyến chạy trọn trên môi trường thử: quét xếp đủ, kiểm trước xuất phát đạt, xác nhận xuất phát, ghi POD online và một POD đi qua hàng đợi offline

## E07-I01 · Cơ sở dữ liệu giao hàng (SHIPPING_DB)
- Loại: issue
- Cha: E07
- Vai: BE-1
- Nhãn: type:issue, constraint:departure-gate
- Spec: SHIPPING_DB, DR-POD-01, BR-POD-01
### Mô tả
Dựng lược đồ của module giao hàng theo file thiết kế: xác nhận xếp hàng, kết quả kiểm trước xuất phát, bản ghi POD theo phiên bản, tệp bằng chứng, seal của tuyến, xác nhận xuất phát và bảng chi tiết người nhận. Tên, quan hệ và chữ ký của người nhận nằm ở bảng riêng có nhóm lưu giữ ngắn hơn bảng POD chính. Seal ghi thêm, số seal duy nhất toàn bảng theo thiết kế hiện tại.
### Định nghĩa xong
- [ ] Migration tạo đủ 7 bảng với khoá, ràng buộc, chỉ mục; chạy lên và lùi xuống trên cơ sở dữ liệu rỗng không lỗi
- [ ] Kiểm thử ràng buộc: một dòng xếp đã xác nhận cho mỗi dòng phân bổ trên một chuyến, số seal duy nhất, phiên bản POD liên kết bản trước, mỗi tệp bằng chứng thuộc một bản ghi POD
- [ ] Bảng kết quả kiểm, seal, xác nhận xuất phát chỉ ghi thêm (thử UPDATE và DELETE bị từ chối)
- [ ] Bảng chi tiết người nhận tách khỏi bảng POD chính và có cột phục vụ xoá nội dung khi hết hạn lưu giữ
- [ ] Tài liệu lược đồ trong repo khớp migration
### Chỗ hở
- GAP-46 việc tách dữ liệu cá nhân của người nhận chưa được bộ phận pháp chế của khách xác nhận
- GAP-09 nhóm lưu giữ của các bảng giao hàng chưa có trong bảng lưu giữ chung

## E07-I02 · Danh sách đơn và tạo dòng đơn (SCR012)
- Loại: issue
- Cha: E07
- Nhãn: type:issue
- Spec: SCR012, API-ALC-05, API-ALC-01, FR-OUT-01
- Phụ thuộc: E06-I03
### Mô tả
Màn danh sách đơn theo ngày yêu cầu, khách và trạng thái phân bổ kèm biểu mẫu tạo một dòng đơn gồm khách, SKU, số lượng, ngày khách muốn nhận, cách bàn giao và mã tham chiếu, cùng lối mở màn phân bổ. Màn chỉ có wireframe, hai điểm cuối chưa có hợp đồng ở chế độ cần dùng. Điều kiện giao giữ trên dòng để chọn bộ bằng chứng POD. Đơn của khách × SKU chưa có thoả thuận hiệu lực vẫn tạo được kèm cảnh báo nhưng không phân bổ được.
### Định nghĩa xong
- [ ] Tạo dòng đơn trên môi trường thử, thấy dòng mới trong danh sách và mở được màn phân bổ từ dòng đó
- [ ] Đặc tả màn và hợp đồng hai điểm cuối đã duyệt

## E07-I02-S01 · API tạo dòng đơn và danh sách dòng đơn (API-ALC-05, API-ALC-01)
- Loại: sub-issue
- Cha: E07-I02
- Vai: BE-2
- Nhãn: type:sub-issue
- Spec: API-ALC-05, API-ALC-01, FR-OUT-01
- Phụ thuộc: E06-I03
### Mô tả
Viết hợp đồng chi tiết rồi cài điểm cuối tạo dòng đơn và chế độ danh sách của điểm cuối đọc dòng đơn (hợp đồng hiện chỉ có chế độ đọc một dòng cho màn phân bổ). Danh sách lọc theo ngày yêu cầu, khách, trạng thái, phân trang và có giới hạn số dòng kết quả theo cấu hình, vượt giới hạn thì yêu cầu thu hẹp chứ không cắt lặng lẽ. Tham chiếu trùng và dữ liệu gốc chưa định nghĩa là lỗi xử lý được.
### Định nghĩa xong
- [ ] Hợp đồng chi tiết cả hai chế độ viết theo khuôn API và được duyệt
- [ ] Tạo dòng đơn từ chối tham chiếu trùng, khách hoặc SKU chưa có trong dữ liệu gốc, số lượng không dương, bằng mã lỗi đúng
- [ ] Đơn của cặp chưa có thoả thuận hiệu lực tạo được và trả cờ cảnh báo
- [ ] Danh sách phân trang đúng, vượt giới hạn dòng trả yêu cầu thu hẹp kèm số dòng khớp
- [ ] Phân quyền: vai trò kinh doanh và quản trị tạo được, vai trò kho và điều phối chỉ xem
### Chỗ hở
- GAP-44 chế độ danh sách chưa có hợp đồng, chỉ có chế độ đọc một dòng

## E07-I02-S02 · Màn danh sách đơn và tạo dòng đơn (SCR012)
- Loại: sub-issue
- Cha: E07-I02
- Vai: FE-1
- Nhãn: type:sub-issue
- Spec: SCR012, API-MST-01, API-ALC-05
- Phụ thuộc: E07-I02-S01, E03-I02, E03-I03-S01, E03-I04-S01
### Mô tả
Viết đặc tả chi tiết của màn rồi dựng biểu mẫu tạo dòng đơn, danh sách đơn có cột trạng thái phân bổ, cảnh báo thiếu thoả thuận và lối mở màn phân bổ theo từng dòng. Ô chọn khách, SKU, điều kiện giao lấy từ điểm cuối danh mục dùng chung.
### Định nghĩa xong
- [ ] Đặc tả màn viết xong theo khuôn các màn đã chi tiết, có item, trạng thái rỗng / lỗi / quá giới hạn dòng và tiêu chí chấp nhận
- [ ] Tạo dòng đơn báo lỗi tại ô cho thiếu dữ liệu, tham chiếu trùng, dữ liệu gốc chưa định nghĩa; thành công đưa dòng mới lên danh sách
- [ ] Dòng thiếu thoả thuận hiện cảnh báo và không có lối mở phân bổ
- [ ] Vai trò chỉ xem không thấy biểu mẫu tạo; có kiểm thử giao diện cho các nhánh trên

## E07-I03 · Quét xếp hàng lên xe (SCR013)
- Loại: issue
- Cha: E07
- Nhãn: type:issue, constraint:rice-beef-trace, constraint:expiry
- Spec: SCR013, API-OUT-01, API-OUT-02, FR-OUT-04
- Phụ thuộc: E07-I01, E06-I05, E08-I01, E08-I06
### Mô tả
Nhân viên kho mở một chuyến đã công bố và xếp từng dòng phân bổ bằng cách quét SKU, lô, nhập số lượng, quét xe và seal. Server đối chiếu với phân bổ, bản công bố của chuyến và trạng thái lô sau khi giữ khoá lô, trả mọi lý do chặn cùng lúc. Khớp thì xác nhận, không khớp thì ghi dòng bị chặn làm dấu vết. Lý do chặn gồm sai lô, sai SKU, lệch dải nhiệt lô và xe, lệch số lượng, sai xe hoặc seal, hạn an toàn đã đạt, thiếu trường truy xuất luật định của gạo hoặc bò, lô hết khả dụng, lô có ca sai lệch nhiệt hoặc thuộc ca thu hồi mở. Không có hàng đợi offline cho giao dịch này.
### Định nghĩa xong
- [ ] Một chuyến thử được xếp đủ bằng máy quét thật hoặc giả lập bàn phím và cả hai sub-issue qua kiểm thử

## E07-I03-S01 · API danh sách chuyến cần xếp và xác nhận xếp hàng (API-OUT-01, API-OUT-02)
- Loại: sub-issue
- Cha: E07-I03
- Vai: BE-1
- Nhãn: type:sub-issue, constraint:rice-beef-trace, constraint:expiry, constraint:recall
- Spec: API-OUT-01, API-OUT-02, FR-OUT-04, JP-15, JP-06
- Phụ thuộc: E07-I01, E06-I05, E08-I01, E08-I06, E05-I02-S01, E10-I06-S03, E05-I10
### Mô tả
Điểm cuối đọc chuyến có hai chế độ: danh sách chuyến cần xếp và chi tiết một chuyến kèm các lý do chặn đứng tính sẵn trên lô đã phân bổ. Điểm cuối xác nhận xếp nhận một lần quét, giữ khoá lô rồi mới đọc trạng thái lô và ca thu hồi, tính mọi lý do cùng lúc. Quét đạt trừ lô thuộc ca thu hồi thì từ chối mà không ghi gì; có lý do chặn khác thì ghi dòng bị chặn. Chỉ lý do lệch dải nhiệt mới mở ca sai lệch. Đã qua ngày chuyến thì không ghi nhưng trả đủ lý do đã tính.
### Định nghĩa xong
- [ ] Quét khớp mọi trường thì ghi dòng đã xác nhận và tăng tiến độ xếp của chuyến; mỗi dòng phân bổ chỉ có một dòng xác nhận trên một chuyến
- [ ] Mọi lý do chặn của danh sách trên trả cùng một lượt, mỗi lý do có mã đúng bảng mã lỗi; quét sai SKU dùng mã riêng
- [ ] Làn gạo thiếu trường truy xuất bắt buộc, làn bò có mã cá thể không đủ 10 chữ số đều bị chặn xuất, không làm tròn hay đệm mã
- [ ] Lô thuộc ca thu hồi mở: quét đạt thì 422 không ghi; quét kèm lý do chặn khác thì ghi dòng bị chặn có mã thu hồi
- [ ] Lệch dải nhiệt lô và xe mở một ca sai lệch nguồn xếp hàng, lý do khác không mở ca
- [ ] Lệnh mở ca sai lệch của E10-I06-S03 chạy trong cùng giao dịch xếp hàng; sự kiện xếp hàng đạt mang mã lô, SKU, số lượng, chuyến, mã giao dịch và thời điểm nghiệp vụ đủ cho người nghe truy xuất của E11-I01-S02
- [ ] Lỗi hình thức không ghi dòng chặn; gửi lại cùng khoá idempotency trả kết quả cũ; hợp đồng khớp file API
### Chỗ hở
- GAP-48 danh sách chuyến và seal phụ thuộc phần công bố tuyến và cấp seal của epic lập tuyến, chưa có hợp đồng

## E07-I03-S02 · Màn quét xếp hàng (SCR013)
- Loại: sub-issue
- Cha: E07-I03
- Vai: FE-1
- Nhãn: type:sub-issue, constraint:rice-beef-trace
- Spec: SCR013, API-OUT-01, API-OUT-02
- Phụ thuộc: E07-I03-S01
### Mô tả
Dựng khung danh sách chuyến và khung chi tiết với thẻ từng dòng phân bổ, năm ô quét theo thứ tự (SKU, lô, số lượng, xe, seal), nút xác nhận và các khung lý do chặn; thẻ dòng hiện lý do chặn đứng trước khi quét. Máy quét gửi mã kèm Enter như bàn phím. Mất mạng thì khoá nút ghi và giữ nội dung đã quét, không xếp hàng.
### Định nghĩa xong
- [ ] Máy quét như bàn phím chuyển con trỏ qua các ô đúng thứ tự và gửi khi Enter ở ô seal, không cần bấm thêm
- [ ] Bỏ trống ô bắt buộc thì không gửi; số seal không bao giờ hiện lại, chỉ hiện đã cấp hay chưa
- [ ] Mọi lý do chặn hiện đồng thời; ca thu hồi hiện khung riêng; lý do đứng hiện trên thẻ trước khi quét
- [ ] Mất mạng khoá nút xác nhận, giữ nội dung đã nhập, có mạng lại thì gửi lại cùng khoá idempotency
- [ ] 21 kịch bản AC-SCR013-01..21 có kiểm thử tự động (ca nào chưa tự động hoá được ghi lý do)
### Chỗ hở
- GAP-50 cách đọc "không cần bấm thêm" bằng Enter ở ô seal là giả định nhà thầu chưa xác nhận với người dùng kho

## E07-I04 · Cổng xuất phát: kiểm trước xuất phát và xác nhận xuất phát (OUT_DEPARTURE_GATE)
- Loại: issue
- Cha: E07
- Nhãn: type:issue, constraint:departure-gate, constraint:date-reversal
- Spec: OUT_DEPARTURE_GATE, API-OUT-03, API-OUT-04, FR-OUT-05
- Phụ thuộc: E07-I01, E07-I03, E06-I04, E08-I01, E08-I06
### Mô tả
Cổng cuối trước khi hàng rời kho, gồm hai lệnh của module giao hàng. Kiểm trước xuất phát ghi nhiệt xe, nhiệt hàng, seal, giờ xuất phát; server tính kết quả một lần sau khi giữ khoá và luôn ghi một dòng bất biến đạt hoặc dừng kèm mọi lý do, giá trị ngoài chính sách tự mở ca sai lệch. Xác nhận xuất phát chỉ thành công khi lần kiểm gần nhất đạt, chuyến và seal không đổi, đã xếp đủ và dưới khoá mọi lô còn khả dụng, không thu hồi, không đảo hạn. 日付逆転 được kiểm lại theo thứ tự chuyến bằng chính hàm của module phân bổ.
### Định nghĩa xong
- [ ] Hai điểm cuối chạy trên môi trường thử, bộ ca biên của thiết kế xanh và một chuyến thử xuất phát thành công

## E07-I04-S01 · API kiểm trước xuất phát (API-OUT-03)
- Loại: sub-issue
- Cha: E07-I04
- Vai: BE-1
- Nhãn: type:sub-issue, constraint:departure-gate, constraint:recall
- Spec: API-OUT-03, OUT_DEPARTURE_GATE, JP-01, JP-02, JP-03
- Phụ thuộc: E07-I01, E07-I03-S01, E08-I01, E08-I06, E10-I06-S03, E01-I13, E01-I14
### Mô tả
Nhận nhiệt độ xe, nhiệt độ hàng, seal, giờ xuất phát; lấy khoá theo thứ tự thống nhất (chuyến, dòng đơn, cặp, lô); tính mọi lý do chặn: nhiệt ngoài ngưỡng của dải theo phiên bản ngưỡng hiệu lực tại giờ xuất phát, seal chưa cấp hoặc sai, giờ lệch quá độ lệch cho phép hoặc ngoài khung giờ dịch vụ, còn dòng chưa xếp, lô thuộc ca thu hồi mở, lô đã xếp có ca sai lệch nhiệt mở. Ghi một dòng bất biến mỗi lần kiểm; nhiệt, seal, giờ sai tự mở (hoặc nhập vào) ca sai lệch của chuyến, tối đa một ca mở mỗi chuyến.
### Định nghĩa xong
- [ ] Dải đông: nhiệt xe đúng ngưỡng thì đạt, lệch một chút thì dừng và mở ca gắn các lô đã xếp; dải mát và thường dùng biên là trong dải
- [ ] Giờ lệch quá độ lệch cho phép hoặc ngoài khung giờ dịch vụ thì dừng và mở ca của chuyến, không gắn lô
- [ ] Thiếu nhiệt độ hoặc seal trả lỗi hình thức, không đánh giá và không ghi dòng
- [ ] Lô thu hồi, dòng chưa xếp, 日付逆転 theo thứ tự chuyến (nêu chuyến gây đảo) đều ghi dòng dừng với mã đúng và không mở ca sai lệch
- [ ] Kiểm lặp khi ca của chuyến đang mở thì nhập lý do vào ca đó, không mở ca thứ hai
- [ ] Lệnh mở ca sai lệch của E10-I06-S03 chạy trong cùng giao dịch kiểm; ngưỡng, độ lệch giờ và khung giờ dịch vụ đọc qua hàm đọc khoá cấu hình có ngày, giờ lấy từ đồng hồ nghiệp vụ
- [ ] Ngoài ngày chuyến không ghi; mỗi lần kiểm một dòng mới, dòng cũ không sửa
### Chỗ hở
- GAP-47 chính sách nhiệt xe dùng ngưỡng của dải hàng và độ lệch giờ cho phép là giả định chưa khách xác nhận
- GAP-48 phụ thuộc cấp seal ở epic lập tuyến

## E07-I04-S02 · API xác nhận xuất phát (API-OUT-04)
- Loại: sub-issue
- Cha: E07-I04
- Vai: BE-2
- Nhãn: type:sub-issue, constraint:departure-gate, constraint:date-reversal
- Spec: API-OUT-04, JP-10, JP-18, FR-OUT-05
- Phụ thuộc: E07-I01, E06-I04-S02, E08-I01, E01-I14
### Mô tả
Lệnh xác nhận xuất phát: kiểm lần kiểm gần nhất đạt, chuyến và seal không đổi từ lúc kiểm, tiến độ xếp đủ; dưới khoá chuyến, cặp, lô kiểm lại lô còn khả dụng, không cảnh báo nhiệt chưa xong, không thuộc ca thu hồi, không đảo hạn theo thứ tự chuyến. Thành công ghi xác nhận xuất phát, từ đó kế hoạch và seal cố định và lô coi như đã rời kho. Lệnh không ghi lịch sử giao đã nhận và không ghi dòng chặn. Sub-issue này cũng hiện thực cổng nội bộ RouteLoadingStateLookup do giao vận khai báo (hợp đồng ở E08-I01): trả chuyến đã bắt đầu xếp hàng chưa và đã xuất phát chưa, đọc từ bảng xếp hàng và bảng xuất phát của module giao hàng; giao vận (E09-I07-S02) gọi cổng này để từ chối sửa tuyến khi chuyến đã xếp hàng.
### Định nghĩa xong
- [ ] Kiểm đạt rồi xác nhận thì ghi đúng một dòng xuất phát và cố định kế hoạch, seal của chuyến
- [ ] Ca thu hồi mở sau lần kiểm đạt vẫn chặn xác nhận bằng 422, không ghi gì
- [ ] Seal bị thay giữa lần kiểm và lúc xuất phát bị chặn bằng mã riêng; lô đã xếp bị cách ly vì cảnh báo nhiệt sau lần kiểm cũng bị chặn
- [ ] Hai người cùng xác nhận một chuyến: chỉ một thành công, người sau nhận xung đột phiên bản
- [ ] Chưa tới hoặc đã qua ngày chuyến không ghi; xác nhận không phát sự kiện ghi lịch sử giao đã nhận
- [ ] Cổng RouteLoadingStateLookup trả đúng ba trạng thái (chưa xếp, đã bắt đầu xếp, đã xuất phát) cho chuyến thử và đọc dữ liệu qua giao diện đặt tên, giao vận không đọc bảng của giao hàng; kiểm thử hợp đồng với phía gọi
- [ ] Sự kiện xuất phát mang mã chuyến, các lô đã xếp, thời điểm nghiệp vụ đủ cho người nghe truy xuất của E11-I01-S02
### Chỗ hở
- GAP-47 kiểm 日付逆転 theo thứ tự chuyến kể cả chuyến chưa được khách nhận là câu trả lời của khách giả lập

## E07-I04-S03 · Bộ kiểm thử ca biên cổng xuất phát
- Loại: sub-issue
- Cha: E07-I04
- Vai: QA-1
- Nhãn: type:sub-issue, constraint:departure-gate
- Spec: OUT_DEPARTURE_GATE, API-OUT-03, API-OUT-04
- Phụ thuộc: E07-I04-S01, E07-I04-S02, E09-I04-S03
### Mô tả
Viết và chạy kiểm thử tích hợp cho hai điểm cuối trên cơ sở dữ liệu thật của môi trường thử, bám bảng ca biên của thiết kế (đảo hạn theo thứ tự chuyến, biên nhiệt từng dải, seal, giờ, thu hồi mở sau lần kiểm, kiểm lặp) và kiểm thử đồng thời hai người cùng xác nhận.
### Định nghĩa xong
- [ ] 15 ca biên của thiết kế có kiểm thử, mỗi ca ghi mã ca trong tên kiểm thử
- [ ] Nhận biên bản kiểm thử ranh giới của E09-I04-S03 và xác nhận: giờ xuất phát ngoài khung giờ dịch vụ bị chặn ở kho bằng mã lỗi riêng của kho, kiểm trước xuất phát không bao giờ trả dòng giờ lái; ca luật không lùi của đồng hồ nghiệp vụ và các ca rút từ prototype được đánh dấu
- [ ] Kiểm thử đồng thời hai xác nhận xuất phát cùng chuyến và hai chuyến cùng cặp khách × SKU chạy lặp 50 lần không deadlock và không lọt đảo hạn
- [ ] Báo cáo kết quả liệt ca nào dựa trên giả định chưa khách xác nhận
- [ ] Chạy được trong CI của dự án

## E07-I05 · Màn kiểm tra trước vận chuyển (SCR014)
- Loại: issue
- Cha: E07
- Vai: FE-1
- Nhãn: type:issue, constraint:departure-gate
- Spec: SCR014, API-OUT-01, API-OUT-03, API-OUT-04, JP-05
- Phụ thuộc: E07-I04
### Mô tả
Dựng màn cho nhân viên kho nhập nhiệt xe, nhiệt hàng, seal, giờ xuất phát và xem kết quả đạt hoặc dừng kèm đủ lý do, nút xác nhận xuất phát bật khi đủ điều kiện, bảng lịch sử các lần kiểm bất biến, và khung danh sách chuyến chờ xuất phát. QC và điều phối chỉ xem. Giờ hiển thị theo giờ Nhật, nhiệt độ một chữ số thập phân. Màn không tự so ngưỡng; chỉ hiển thị kết quả server.
### Định nghĩa xong
- [ ] Hiện dữ kiện chuyến, ngưỡng của dải với phiên bản hiệu lực và chính sách giờ; thiếu nhiệt độ hoặc seal thì không gửi
- [ ] Kết quả hiện đủ lý do cùng lúc, gồm chuyến đi trước gây đảo hạn khi có; số seal không bao giờ hiện lại
- [ ] Nút xác nhận chỉ bật khi lần kiểm gần nhất đạt, đã xếp đủ và không còn ca sai lệch mở; bật tắt chỉ là giao diện, server vẫn kiểm lại
- [ ] Mất mạng khoá cả nút kiểm và nút xuất phát, giữ nội dung đã nhập; QC và điều phối không thấy nút ghi
- [ ] 20 kịch bản AC-SCR014-01..20 có kiểm thử giao diện tự động (ca nào chưa tự động hoá được ghi lý do)
### Chỗ hở
- GAP-47 các giá trị nhiệt xe, độ lệch giờ, dùng lại seal còn chờ khách xác nhận

## E07-I06 · Bàn giao và POD trên thiết bị tài xế (SCR015)
- Loại: issue
- Cha: E07
- Nhãn: type:issue
- Spec: SCR015, API-OUT-05, API-OUT-06, BR-POD-01, FR-OUT-06
- Phụ thuộc: E07-I01, E07-I04, E06-I03
### Mô tả
Tài xế (hoặc văn phòng ghi thay từ POD giấy) ghi bằng chứng bàn giao của một đơn sau khi chuyến đã rời kho. Bộ trường chung bắt buộc cho mọi đơn; bộ riêng theo điều khoản giao (giao tại xe cần nhiệt hàng, giao tận cửa cần ảnh và bằng chứng dỡ hàng); điểm chuyển trách nhiệm lấy theo hợp đồng của đơn. Thiếu bằng chứng bắt buộc thì không hoàn tất. Sửa POD tạo phiên bản mới liên kết bản cũ. Phần mất mạng thuộc issue khung PWA.
### Định nghĩa xong
- [ ] Một POD ghi online trọn luồng cho cả hai điều khoản giao trên môi trường thử, và cả hai sub-issue qua kiểm thử

## E07-I06-S01 · API lấy biểu mẫu POD và ghi POD (API-OUT-05, API-OUT-06)
- Loại: sub-issue
- Cha: E07-I06
- Vai: BE-2
- Nhãn: type:sub-issue
- Spec: API-OUT-05, API-OUT-06, DR-POD-01, BR-POD-01
- Phụ thuộc: E07-I01, E06-I03, E01-I14, E01-I16, E01-I18
### Mô tả
Điểm cuối trả biểu mẫu theo điều khoản của đơn kèm điểm chuyển trách nhiệm của hợp đồng; điểm cuối ghi POD (ghi mới và sửa bằng phiên bản mới liên kết bản trước) kiểm bộ trường bắt buộc, hash và quyền sở hữu của tệp bằng chứng, giờ bàn giao, khoá idempotency và quyền ghi theo vai trò. Đơn đã có POD thì từ chối bản đến sau mà không ghi đè. Ghi POD thành công phát sự kiện để module phân bổ ghi lịch sử giao đã được khách nhận làm mốc; bản bị từ chối ở dạng khách không nhận thì không vào mốc. Tên, quan hệ, chữ ký người nhận lưu ở bảng chi tiết riêng.
### Định nghĩa xong
- [ ] Thiếu bằng chứng bắt buộc của điều khoản thì không hoàn tất, kể cả khi gọi thẳng API; điểm chuyển trách nhiệm khác hợp đồng bị từ chối
- [ ] Chuyến chưa xuất phát thì chưa ghi được; giờ bàn giao ở tương lai hoặc trước giờ xuất phát bị từ chối
- [ ] Gửi lại cùng khoá idempotency trả kết quả cũ; đơn đã có POD thì bản đến sau nhận 409 và không đổi dữ liệu
- [ ] Tệp kiểm bằng hàm dùng chung của E01-I18 (không dựng đường tải riêng); tệp ảnh đã dùng cho POD khác hoặc hash không khớp bị từ chối; sửa POD tạo phiên bản mới, bản cũ không đổi
- [ ] Ghi thành công gây một dòng lịch sử giao đã nhận ở module phân bổ (trừ mã ngoại lệ khách từ chối); tên, quan hệ, chữ ký nằm ở bảng chi tiết riêng và không xuất hiện trong log
- [ ] Tài xế chỉ ghi đơn của chuyến mình; điều phối, trưởng vận tải và quản trị ghi thay có đánh dấu và nhật ký
- [ ] Cổng nội bộ RoutePodProgressLookup do giao vận khai báo (hợp đồng ở E08-I01) trả tiến độ POD theo điểm giao của chuyến, giao vận (E08-I04-S01) gọi qua giao diện đặt tên; sự kiện POD mang đủ trường (đơn, lô, điều khoản, khách có nhận hay không, thời điểm nghiệp vụ) cho người nghe truy xuất E11-I01-S02 và việc ghi lịch sử giao
### Chỗ hở
- GAP-46 việc tách dữ liệu người nhận chờ pháp chế khách xác nhận
- GAP-47 danh sách mã ngoại lệ và bộ trường theo điều khoản là giả định nhà thầu

## E07-I06-S02 · Màn bàn giao và POD, phần ghi online (SCR015)
- Loại: sub-issue
- Cha: E07-I06
- Vai: FE-1
- Nhãn: type:sub-issue
- Spec: SCR015, API-OUT-05, API-OUT-06, API-SYS-01
- Phụ thuộc: E07-I06-S01, E01-I18
### Mô tả
Dựng màn ghi POD: bộ trường theo điều khoản của đơn, mã ngoại lệ, tải ảnh và chữ ký (tính hash, xin đường dẫn tải, tải lên rồi mới gửi lệnh ghi), trạng thái đã ghi, form sửa nạp đủ từ các phiên bản, và đánh dấu ghi thay cho văn phòng. Màn kiểm đủ bộ trường trước khi gửi nhưng server vẫn là nơi quyết định.
### Định nghĩa xong
- [ ] Đơn giao tận cửa chỉ hiện bộ bằng chứng của điều khoản đó; đơn giao tại xe hiện nhiệt hàng bắt buộc một chữ số thập phân
- [ ] Thiếu bằng chứng bắt buộc thì không gửi và báo tại ô; điểm chuyển trách nhiệm hiện đúng theo hợp đồng
- [ ] Tải tệp chỉ qua API xin đường dẫn ký sẵn của E01-I18 (không đường tải riêng) kèm hash; hash lệch hoặc ảnh đã dùng hiện đúng thông điệp lỗi
- [ ] Sửa POD gửi lý do và phiên bản kỳ vọng, hiện phiên bản mới; vai trò kho không vào được màn
- [ ] 18 kịch bản AC-SCR015-01..18 có kiểm thử giao diện (các ca offline kiểm ở issue khung PWA)
### Chỗ hở
- GAP-50 nhãn tiếng Nhật của từng mã ngoại lệ chưa có nguồn

## E07-I07 · Khung PWA và hàng đợi POD offline mã hoá (ADR-007)
- Loại: issue
- Cha: E07
- Nhãn: type:issue
- Spec: ADR-007, NFR-AVL-02, SCR015, FR-OUT-06
- Phụ thuộc: E07-I06
### Mô tả
Đóng gói màn tài xế thành PWA cài qua quản lý thiết bị và thêm hàng đợi POD khi mất mạng: bản ghi mã hoá bằng khoá không xuất được của thiết bị, lưu cục bộ, gắn với người ghi, đồng bộ khi có mạng lại, không tự xoá, cảnh báo sớm trước giới hạn thời gian và chặn đăng xuất khi còn bản ghi. Giao dịch kho tuyệt đối không xếp hàng; mất mạng thì khoá nút ghi. Khung này dùng chung cho màn tuyến của tài xế do epic lập tuyến làm.
### Định nghĩa xong
- [ ] Cài được lên thiết bị thử, một POD ghi lúc mất mạng được mã hoá và tự đồng bộ đúng một lần khi có mạng lại

## E07-I07-S01 · Vỏ PWA và khoá giao dịch kho khi mất mạng
- Loại: sub-issue
- Cha: E07-I07
- Vai: FE-1
- Nhãn: type:sub-issue
- Spec: ADR-007, NFR-AVL-02
- Phụ thuộc: E01-I19
### Mô tả
Thêm manifest và service worker cho ứng dụng, bảo đảm service worker không đệm hay xếp hàng bất kỳ lệnh ghi nào của kho (nhận, di chuyển, xếp, xuất phát), hiện trạng thái offline và khoá nút ghi ở mọi màn kho; chuẩn bị điểm gắn cho màn tuyến của tài xế dùng lại vỏ này.
### Định nghĩa xong
- [ ] Ứng dụng cài được như PWA trên thiết bị thử và mở được màn POD khi mất mạng
- [ ] Mất mạng ở màn kho khoá mọi nút ghi và hiện trạng thái offline; kiểm thử chứng minh không có lệnh ghi kho nào bị đệm hay phát lại khi có mạng
- [ ] Tài liệu ngắn mô tả điểm gắn của vỏ trên khung SPA chung (E01-I19) để epic lập tuyến dùng cho màn tuyến của tài xế; nêu rõ màn tuyến chạy online, không đệm lệnh ghi, và LAB-4 chưa quyết việc xem tuyến khi mất mạng
### Chỗ hở
- GAP-45 bản ghi chờ có thể mắc kẹt khi tài khoản bị khoá hoặc thu hồi phiên

## E07-I07-S02 · Hàng đợi POD mã hoá và đồng bộ
- Loại: sub-issue
- Cha: E07-I07
- Vai: FE-1
- Nhãn: type:sub-issue
- Spec: ADR-007, SCR015, FR-OUT-06, API-SYS-01
- Phụ thuộc: E07-I06-S02, E07-I07-S01, E01-I12, E01-I18
### Mô tả
Cài hàng đợi cục bộ: khoá mã hoá tạo trên thiết bị không xuất được, mỗi bản ghi một vectơ khởi tạo riêng, chỉ phần dữ liệu cần để đồng bộ nằm rõ; khoá idempotency tạo lúc bấm xác nhận và giữ nguyên qua mọi lần gửi lại; đồng bộ theo thứ tự xếp hàng khi mở ứng dụng và dùng đồng bộ nền nếu trình duyệt hỗ trợ; bản bị server từ chối giữ lại để xem, không ghi đè; hiện số bản ghi chờ, tuổi bản cũ nhất, cảnh báo sớm; chặn đăng xuất khi còn bản ghi.
### Định nghĩa xong
- [ ] Nội dung bản ghi trong kho cục bộ không đọc được nếu mở bằng công cụ trình duyệt; khoá không xuất được; chỉ đúng người ghi mới đồng bộ được
- [ ] Gửi lại bản ghi nhiều lần (ngắt mạng giữa chừng) chỉ tạo một POD trên server
- [ ] Thời gian máy chủ giữ khoá idempotency (khoá cấu hình ở E01-I12) không ngắn hơn giới hạn hàng đợi; kiểm thử cấu hình báo lỗi khi đặt ngược
- [ ] Bản ghi đến sau khi đơn đã có POD được giữ ở trạng thái bị từ chối kèm mã lý do và hiện cho người dùng xem
- [ ] Cảnh báo hiện khi bản cũ nhất tới ngưỡng cảnh báo sớm, không bản ghi nào bị tự xoá kể cả quá giới hạn
- [ ] Phiên hết hạn thì yêu cầu đăng nhập lại rồi mới đồng bộ, bản ghi vẫn nằm mã hoá trong lúc chờ; đăng xuất bị chặn khi còn bản ghi
### Chỗ hở
- GAP-45 chưa có quy trình nghiệp vụ khi bản ghi quá giới hạn mà thiết bị vẫn chưa có mạng

## E07-I07-S03 · Kiểm ngắt mạng trên thiết bị thật
- Loại: sub-issue
- Cha: E07-I07
- Vai: QA-1
- Nhãn: type:sub-issue
- Spec: ADR-007, NFR-AVL-02, SCR015
- Phụ thuộc: E07-I07-S02
### Mô tả
Chạy kịch bản ngắt mạng tại điểm bàn giao trên thiết bị quản lý thật đúng như tiêu chí chấp nhận của yêu cầu: ngắt, ghi POD, đợi, mở mạng, đối chiếu; lặp trên trình duyệt đã chuẩn hoá của đội thiết bị.
### Định nghĩa xong
- [ ] Biên bản kiểm ngắt mạng trên ít nhất một thiết bị thật gồm: ghi lúc mất mạng, đóng mở ứng dụng, mất điện thiết bị, có mạng lại, một POD trên server
- [ ] Kiểm thử chạy tới giới hạn thời gian của hàng đợi và xác nhận không mất bản ghi nào
- [ ] Kiểm thử đồng bộ lại bản ghi trùng và bản ghi bị từ chối cho kết quả đúng như thiết kế
- [ ] Ghi lại hành vi lưu trữ cục bộ khác nhau giữa các trình duyệt đã thử và khuyến nghị chuẩn hoá một trình duyệt
### Chỗ hở
- GAP-45 thiết bị mất khi còn bản ghi chưa gửi là rủi ro đã biết của thiết kế

## E07-I08 · Làm rõ với pháp chế khách: dữ liệu người nhận POD và thời hạn lưu hồ sơ giao hàng
- Loại: issue
- Cha: E07
- Vai: BrSE
- Nhãn: type:issue, type:clarification
- Spec: SCR015, SHIPPING_DB, DR-POD-01
### Mô tả
Thiết kế hiện tách tên, quan hệ người nhận và chữ ký sang bảng lưu một năm, bảng POD chính chỉ giữ mã vận hành. Chủ dự án đã nhận cách tách này nhưng bộ phận pháp chế của khách chưa xác nhận hai điểm: cách tách có đủ không, và chữ ký có tính là dữ liệu cá nhân không. Bảng lưu giữ chung cũng chưa nói POD, seal và kiểm trước xuất phát thuộc nhóm hồ sơ nào. Issue này lấy câu trả lời và chốt cấu hình cho issue cơ sở dữ liệu và issue ghi POD.
### Định nghĩa xong
- [ ] Hai câu hỏi (cách tách, chữ ký) và câu về nhóm lưu giữ của hồ sơ giao hàng gửi đến người trả lời đã ghi, kèm sơ đồ hai bảng dễ hiểu
- [ ] Câu trả lời ghi lại (người, ngày) hoặc ghi chưa trả lời kèm hạn nhắc
- [ ] Nếu câu trả lời khác thiết kế hiện tại, ghi rõ issue nào phải đổi (E07-I01, E07-I06-S01) và quyết định giữ hay đổi
### Chỗ hở
- GAP-46 cách tách dữ liệu người nhận chờ pháp chế
- GAP-09 nhóm lưu giữ hồ sơ giao hàng chưa nêu

## E07-I09 · Làm rõ với khách vận hành các giá trị của cổng xuất phát và POD
- Loại: issue
- Cha: E07
- Vai: BrSE
- Nhãn: type:issue, type:clarification, constraint:departure-gate
- Spec: SCR014, SCR015, OUT_DEPARTURE_GATE, ADR-007, BR-POD-01
### Mô tả
Gom các câu cần khách thật trả lời mà thiết kế đang chạy theo giả định nhà thầu hoặc khách giả lập: nhiệt xe dùng ngưỡng của dải hàng hay bảng riêng; số seal có được dùng lại giữa các chuyến không; độ lệch giờ xuất phát cho phép; kiểm lại 日付逆転 theo thứ tự chuyến có tính cả chuyến chưa được khách nhận không; bộ trường chung và riêng của POD cùng điểm chuyển trách nhiệm theo hợp đồng và danh sách mã ngoại lệ; việc phải làm khi bản ghi POD offline quá giới hạn thời gian mà thiết bị vẫn chưa có mạng; bàn giao ở điểm khác điểm của hợp đồng.
### Định nghĩa xong
- [ ] Bộ câu hỏi kèm ví dụ số liệu gửi đúng người trả lời đã ghi trong sổ câu hỏi mở
- [ ] Mỗi câu có câu trả lời ghi lại (người, ngày) hoặc ghi chưa trả lời kèm hạn nhắc
- [ ] Khoá cấu hình, đặc tả và các issue E07-I04, E07-I05, E07-I06, E07-I07 được rà lại và ghi quyết định giữ hay đổi
### Chỗ hở
- GAP-45 quy trình khi bản ghi offline quá giới hạn
- GAP-47 các giá trị chặn của cổng xuất phát và bộ bằng chứng POD là giả định chưa khách xác nhận
