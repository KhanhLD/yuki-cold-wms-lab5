# E04 · Nhập kho
<!-- lab5-epic: E04 -->

## E04 · Nhập kho
- Loại: epic
- Nhãn: type:epic
- Spec: SCR003, SCR004, INB_RECEIVING_EXPIRY_CHECK, API-INB-01, API-INB-02, API-INB-03, API-INV-02
### Mô tả
Toàn bộ luồng nhận hàng ở kho: nhân viên kho hoặc QC kiểm từng dòng giao, hệ thống chạy chuỗi luật nhận (hạn dùng, ngày sản xuất, truy xuất gạo và bò, nhiệt độ, dải vị trí) rồi tạo lô bất biến hoặc đưa lô vào cách ly. Epic gồm luật phía server, API ghi dòng nhận, màn kiểm nhận SCR004, đường sửa dòng nhận bằng bút toán đảo, và phần lịch hẹn nhập SCR003 của giai đoạn 2. Kèm hai issue làm rõ với khách: đối soát tổng dòng nhận với số báo, và các danh mục còn trống chặn go-live. Lược đồ bảng tồn kho dùng chung với epic tồn kho nên nằm ở issue đầu tiên của E05. Phần mở case sai lệch nhiệt thuộc epic nhiệt độ, epic này chỉ phát sự kiện đủ dữ liệu.

## E04-I01 · Luật hạn dùng và ngày sản xuất lúc nhận hàng
- Loại: issue
- Cha: E04
- Vai: BE-2
- Nhãn: type:issue, constraint:expiry
- Spec: INB_RECEIVING_EXPIRY_CHECK, JP-06, JP-07, JP-19, FR-REC-02
- Phụ thuộc: E03-I01, E05-I01, E01-I13, E01-I14
### Mô tả
Đóng gói luật phía server cho ba thứ: hạn an toàn (消費期限), hạn ngon nhất và ngày sản xuất, tất cả so với ngày nghiệp vụ nhận do đồng hồ nghiệp vụ cấp chứ không do client gửi. Hàng thuộc loại hạn an toàn đã đạt hạn không được nhận; hàng thuộc loại hạn ngon nhất quá hạn chỉ cảnh báo; ngày sản xuất sau ngày nhận là lỗi nhập liệu. Ba luật này là câu trả lời của khách giả lập, chưa khách thật xác nhận, nên giá trị tập kết quả được phép đọc từ khoá cấu hình thay vì viết cứng.
### Định nghĩa xong
- [ ] Hàm kiểm nhận ngày nghiệp vụ nhận và loại hạn của SKU hiệu lực tại ngày đó, không đọc ngày nhận từ yêu cầu
- [ ] Hàng 消費期限 có hạn bằng hoặc trước ngày nhận bị từ chối khi quyết định là nhận; chỉ còn từ chối hoặc tạm giữ theo chỉ thị chủ hàng khi khoá cấu hình cho phép; thiếu mã lý do chỉ thị thì trả mã lỗi riêng
- [ ] Hàng loại hạn ngon nhất có hạn trước ngày nhận trả cảnh báo mà vẫn cho mọi quyết định; hạn đúng ngày nhận không cảnh báo
- [ ] Ngày sản xuất sau ngày nhận bị chặn tại trường; hạn trước ngày sản xuất bị chặn; cùng ngày hợp lệ
- [ ] Kiểm thử đơn vị phủ các ca biên của bảng ca biên trong thiết kế (hạn đúng ngày nhận cho cả hai loại, hạn trước ngày sản xuất, tạm giữ không có chỉ thị)
- [ ] Số phiên bản khoá cấu hình đã đọc được trả kèm kết quả để lưu vào dòng nhận
### Chỗ hở
- GAP-25 ba luật hạn lúc nhận và tập kết quả của hàng 消費期限 đã đạt là giá trị khách giả lập, chưa khách thật xác nhận

## E04-I02 · Kiểm truy xuất gạo và bò lúc nhận ở hai thời điểm
- Loại: issue
- Cha: E04
- Vai: BE-1
- Nhãn: type:issue, constraint:rice-beef-trace
- Spec: INB_RECEIVING_EXPIRY_CHECK, JP-15, BR-TRACE-01, BR-TRACE-02, FR-REC-05
- Phụ thuộc: E03-I01, E05-I01
### Mô tả
Hàng thuộc lane gạo hoặc bò phải mang mã truy xuất đúng luật ngay lúc nhận. Có hai thời điểm khác nhau. Thời điểm 1: mã thiếu, sai định dạng hoặc thuộc nhầm lane thì chặn tại trường và không ghi gì. Thời điểm 2: mã đủ định dạng nhưng kiểm truy xuất thất bại thì dòng nhận được giữ lại và lô vào cách ly. Mã bò phải đúng 10 chữ số ASCII, không bao giờ tự đệm, cắt hay đổi chữ số toàn góc. Việc kiểm mã bò của dữ liệu di trú đi đường khác và không thuộc issue này.
### Định nghĩa xong
- [ ] Lane của dòng nhận (gạo, bò, nội bộ) suy từ SKU; chỉ lane gạo và lane bò có trường truy xuất, trường của lane khác bị từ chối
- [ ] Thời điểm 1: lane gạo thiếu xuất xứ, lane bò thiếu mã hoặc mã không đúng 10 chữ số ASCII bị chặn tại trường; không ghi gì và giá trị sai không bao giờ được lưu
- [ ] Ca đối chứng mã bò 9 chữ số bị chặn khi nhập hàng, không bị đệm thành 10 chữ số; ca chữ số toàn góc cũng bị từ chối
- [ ] Thời điểm 2: đường ghi dòng giữ lại với mã lý do do server đặt, lô vào cách ly và mã kết quả riêng chạy được, kiểm thử bằng cách ép kết quả kiểm thất bại
- [ ] Cột lưu mã bò 10 ký tự có ràng buộc ở tầng dữ liệu làm hàng rào thứ hai, kiểm thử ghi thẳng giá trị sai bị từ chối
### Chỗ hở
- GAP-25 chưa có nguồn cho tập ca kiểm truy xuất thất bại ngoài lỗi định dạng nên thời điểm 2 chưa phát sinh từ dữ liệu thật

## E04-I03 · API ghi dòng nhận hàng (API-INB-01)
- Loại: issue
- Cha: E04
- Nhãn: type:issue, constraint:expiry, constraint:rice-beef-trace, constraint:haccp-temp
- Spec: API-INB-01, FR-REC-04, DR-LOT-01
- Phụ thuộc: E04-I01, E04-I02
### Mô tả
Một điểm cuối ghi một dòng kiểm nhận: nhận, từ chối hoặc tạm giữ kèm mã lý do, nhiệt độ đo, ảnh, hạn và mã truy xuất. Thành công thì tạo lô bất biến (trừ từ chối), ghi sổ di chuyển loại nhận và có thể đưa lô vào cách ly tự động. Điểm cuối ghép các luật của hai issue trước theo đúng thứ tự trong thiết kế kỹ thuật, và là nơi bảo đảm gửi lại không tạo trùng. Kho không chạy ngoại tuyến nên không có hàng đợi gửi lại.
### Định nghĩa xong
- [ ] Một dòng nhận gửi từ máy khách thử cho ra đúng ba loại kết quả: nhận (lô khả dụng), từ chối (không lô), tạm giữ hoặc cách ly (lô cách ly)
- [ ] Gửi lại cùng yêu cầu sau lỗi mạng không tạo lô thứ hai trên môi trường thử

## E04-I03-S01 · Khung ghi dòng nhận: thứ tự kiểm, chống trùng, ảnh, giao dịch ghi (BE)
- Loại: sub-issue
- Cha: E04-I03
- Vai: BE-1
- Nhãn: type:sub-issue, constraint:expiry, constraint:rice-beef-trace
- Spec: API-INB-01, FR-REC-04, DR-LOT-01, INVENTORY_DB
- Phụ thuộc: E04-I01, E04-I02, E05-I01, E01-I18
### Mô tả
Phần khung của lệnh ghi: kiểm hình thức và nạp dữ liệu chủ, chạy các luật theo thứ tự quy định, chống trùng, kiểm ảnh, rồi ghi dòng nhận, lô và sổ di chuyển trong một giao dịch. Bộ kiểm nhiệt độ và dải vị trí do sub-issue song song cung cấp và được cắm vào khung này.
### Định nghĩa xong
- [ ] Kiểm hình thức báo mọi lỗi trường trong một lần phản hồi; mã lý do phải thuộc tập của quyết định đã chọn
- [ ] Thứ tự kiểm đúng thiết kế: hình thức, ảnh, ngày sản xuất, truy xuất thời điểm 1, nhiệt độ và dải, luật hạn, ghi
- [ ] Cùng khoá idempotency và cùng nội dung trả lại đúng phản hồi đã lưu; cùng khoá khác nội dung bị từ chối; một ảnh gốc chỉ dùng cho một dòng nhận
- [ ] Ảnh được kiểm khoá đối tượng, mã băm SHA-256 và chủ sở hữu theo URL ký sẵn của dịch vụ tải tệp dùng chung
- [ ] Một giao dịch ghi dòng nhận bất biến, lô bất biến (trừ từ chối), sổ di chuyển loại nhận và số phiên bản cấu hình đã đọc
- [ ] Chỉ vai kho và QC được gọi; có kiểm thử quyền
- [ ] Phản hồi 201 đủ trường (kết quả, dòng, lô, sự kiện trạng thái, cảnh báo) và tài liệu hợp đồng khớp phản hồi thật
### Chỗ hở
- GAP-25 danh mục mã lý do theo từng quyết định là danh sách mô phỏng

## E04-I03-S02 · Nhiệt độ lúc nhận, dải vị trí, cách ly tự động và sự kiện (BE)
- Loại: sub-issue
- Cha: E04-I03
- Vai: BE-2
- Nhãn: type:sub-issue, constraint:haccp-temp
- Spec: API-INB-01, BR-TEMP-02, JP-01, JP-02, JP-03
- Phụ thuộc: E03-I01, E05-I01, E01-I14, E10-I01-S01
### Mô tả
Nhiệt độ đo bắt buộc ở mọi dòng nhận và được so với phiên bản ngưỡng hiệu lực của dải SKU. Ngoài dải thì dòng vẫn được ghi nhưng server ép quyết định thành tạm giữ, lô vào cách ly tự động, và một sự kiện đủ dữ liệu được phát để module nhiệt độ mở case sai lệch. Vị trí cất khác dải SKU luôn bị chặn không ngoại lệ. Cách ép tạm giữ khi nhiệt ngoài dải là cách đọc thận trọng của nhà thầu, chưa khách xác nhận.
### Định nghĩa xong
- [ ] Nhiệt độ bắt buộc, tối đa một chữ số thập phân; so với ngưỡng hiệu lực tại ngày nghiệp vụ, biên tính là đạt
- [ ] Nhiệt độ ngoài dải: dòng được ghi, quyết định bị ép thành tạm giữ với mã lý do nhiệt, lô vào cách ly tự động dù người kiểm chọn gì; dòng từ chối vẫn ghi số đo và vẫn phát sự kiện
- [ ] Vị trí cất khác dải SKU bị từ chối, không ghi dòng hay lô, chỉ ghi sự kiện chặn; lô khả dụng không cất được vào khu cách ly
- [ ] Sự kiện lô mới, từ chối, tạm giữ hoặc chặn phát qua outbox kèm lô, nhiệt độ đo, dải, phiên bản ngưỡng, vị trí, người kiểm, thời điểm nghiệp vụ; sự kiện nhận hàng mang thêm lane và mã truy xuất để bên nghe truy xuất đọc mà không gọi ngược module kho
- [ ] Dải không có phiên bản ngưỡng hiệu lực trả mã lỗi dữ liệu chủ riêng
- [ ] Kiểm thử ca biên của dải đông (đúng ngưỡng là đạt, vượt một phần mười là ngoài dải) và hai dải còn lại
### Chỗ hở
- GAP-25 luật ép tạm giữ khi nhiệt ngoài dải và payload sự kiện chặn khi không có dòng hay lô cần xác nhận lại

## E04-I03-S03 · Bộ kiểm thử đầu-cuối 18 ca biên của lệnh nhận hàng (QA)
- Loại: sub-issue
- Cha: E04-I03
- Vai: QA-1
- Nhãn: type:sub-issue
- Spec: API-INB-01, INB_RECEIVING_EXPIRY_CHECK
- Phụ thuộc: E04-I03-S01, E04-I03-S02
### Mô tả
Viết và chạy bộ kiểm thử tự động qua API thật cho bảng ca biên của thiết kế kỹ thuật, đặt trước hết các ca đối chứng của hợp đồng thử nghiệm: hạn đúng ngày nhận, mã bò 9 chữ số, nhiệt đúng biên, ảnh dùng lại, gửi lại cùng khoá.
### Định nghĩa xong
- [ ] 18 ca biên chạy tự động trong quy trình kiểm thử, mỗi ca khẳng định mã kết quả và trạng thái lô
- [ ] Có ca đối chứng mã bò 9 chữ số, ca hai yêu cầu khác khoá cùng một ảnh, ca gửi lại cùng khoá sau lỗi mạng
- [ ] Ca ngoài bảng (client gửi tạm giữ nhưng nhiệt ngoài dải, client gửi từ chối nhưng nhiệt ngoài dải) có kiểm thử riêng
- [ ] Mỗi ca lệch hợp đồng được mở một lỗi cho BE kèm bước tái hiện
- [ ] Có ca kiểm riêng cho luật thời điểm nghiệp vụ không lùi của lệnh nhận hàng
- [ ] Báo cáo kết quả chạy lưu cùng kho mã kiểm thử

## E04-I04 · Màn kiểm nhận hàng SCR004
- Loại: issue
- Cha: E04
- Nhãn: type:issue, constraint:expiry, constraint:rice-beef-trace
- Spec: SCR004, FR-REC-02, FR-REC-03
- Phụ thuộc: E04-I03
### Mô tả
Màn một dòng giao mỗi lần cho nhân viên kho hoặc QC trên máy quét cầm tay: quét SKU và lô nhà cung cấp, nhập số lượng, ngày, bao bì, nhiệt độ, mã truy xuất theo lane, chụp ảnh, chọn quyết định kèm mã lý do và vị trí cất. Server sở hữu mọi luật, màn chỉ kiểm trước điều biết chắc và hiển thị kết quả theo mã. Kho không có chế độ ngoại tuyến.
### Định nghĩa xong
- [ ] Nhân viên kho hoàn tất một dòng nhận đầu-cuối cho từng quyết định (nhận, từ chối, tạm giữ) trên môi trường thử
- [ ] Từ khung kết quả mở được chi tiết lô vừa tạo và chuyển sang dòng kế tiếp

## E04-I04-S01 · Màn SCR004: form, quét mã, luật hiển thị theo lane và hạn (FE)
- Loại: sub-issue
- Cha: E04-I04
- Vai: FE-1
- Nhãn: type:sub-issue, constraint:expiry, constraint:rice-beef-trace
- Spec: SCR004, API-INB-01, API-MST-01, API-MST-02, API-SYS-01
- Phụ thuộc: E04-I03-S01, E03-I02, E03-I03-S01, E01-I18
### Mô tả
Dựng toàn bộ 21 mục của wireframe. Ô quét nhận dữ liệu máy quét như bàn phím kèm phím Enter, vùng bấm đủ lớn khi đeo găng. Quét SKU gọi API đọc SKU để hiện dải, loại hạn và lane rồi bật đúng trường truy xuất. Màn đọc ngày nghiệp vụ từ phiên chỉ để hiển thị thông báo hạn, kết quả cuối vẫn do server.
### Định nghĩa xong
- [ ] 21 mục chạy theo wireframe, nhãn tiếng Nhật theo bộ nhãn đã duyệt, trường 梱包状態 là ô chữ khi danh mục rỗng và ô chọn khi có danh mục
- [ ] Quét SKU hiện dải, loại hạn, lane và bật đúng trường xuất xứ gạo hoặc mã bò; mã bò kiểm trước 10 chữ số ASCII
- [ ] Thông báo hạn: 消費期限 đã đạt khoá lựa chọn nhận và chọn sẵn từ chối; 賞味期限 đã qua chỉ cảnh báo; thông báo không chỉ phân biệt bằng màu
- [ ] Ảnh tải qua URL ký sẵn kèm SHA-256 trước khi gửi; mỗi lần bấm gửi dùng khoá idempotency mới và giữ nguyên khoá khi gửi lại sau lỗi mạng
- [ ] Lỗi từ server hiện tại đúng trường, báo đủ mọi trường lỗi cùng lúc; lỗi mạng giữ nội dung đã nhập
- [ ] Khung kết quả có bốn biến thể (nhận, từ chối, giữ, cách ly), link chi tiết lô và nút dòng kế tiếp
- [ ] Chỉ vai kho và QC vào được; phiên hết hạn về màn đăng nhập
### Chỗ hở
- GAP-25 thiết bị quét cầm tay và chuẩn hoá chữ số toàn góc của mã bò chưa khách xác nhận

## E04-I04-S02 · Kiểm thử chấp nhận màn SCR004 (QA)
- Loại: sub-issue
- Cha: E04-I04
- Vai: QA-1
- Nhãn: type:sub-issue
- Spec: SCR004
- Phụ thuộc: E04-I04-S01
### Mô tả
Chạy 22 tình huống chấp nhận của màn trên trình duyệt thật, gồm cả điều kiện làm việc của kho: máy quét giả lập bằng bàn phím kèm Enter, thao tác bằng vùng bấm lớn, mạng chập chờn.
### Định nghĩa xong
- [ ] 22 tình huống chấp nhận chạy tự động hoặc có biên bản chạy tay kèm ảnh chụp màn hình
- [ ] Kiểm thử ba lane (gạo, bò, nội bộ) và đủ ba quyết định
- [ ] Đo kích thước vùng bấm của ô quét và nút gửi, đối chiếu yêu cầu dùng găng tay
- [ ] Kiểm thử mất mạng giữa chừng: nội dung giữ nguyên, gửi lại không tạo lô thứ hai
- [ ] Danh sách lỗi gom theo mức độ; lỗi nặng chặn bàn giao

## E04-I05 · Sửa dòng nhận bằng bút toán đảo (API-INV-02 và luồng sửa trên SCR004)
- Loại: issue
- Cha: E04
- Nhãn: type:issue, constraint:expiry, constraint:rice-beef-trace
- Spec: API-INV-02, SCR004, FR-REC-04
- Phụ thuộc: E04-I03
### Mô tả
Khi người kiểm nhập sai số lượng, mã lô nhà cung cấp, hạn hoặc mã truy xuất, dòng cũ không bị ghi đè: hệ thống thêm dòng mới trỏ về dòng cũ và đưa lô về giá trị đúng. API đã có hợp đồng nhưng wireframe SCR004 chưa vẽ nút gọi nó, nên phần giao diện cần bổ sung và phải chốt bố cục với BrSE trước khi làm.
### Định nghĩa xong
- [ ] Người kiểm sửa một dòng nhận sai số lượng từ màn kiểm nhận và thấy dòng mới, lô đã điều chỉnh, dòng cũ vẫn nguyên
- [ ] Hai người sửa cùng một dòng: một người thành công, người kia nhận thông báo xung đột
### Chỗ hở
- GAP-23 wireframe SCR004 chưa có nút hay luồng sửa dòng nhận

## E04-I05-S01 · API sửa dòng nhận bằng bút toán đảo (BE)
- Loại: sub-issue
- Cha: E04-I05
- Vai: BE-1
- Nhãn: type:sub-issue, constraint:rice-beef-trace
- Spec: API-INV-02, ADR-003, JP-15, JP-19
- Phụ thuộc: E04-I03-S01, E05-I01, E01-I16
### Mô tả
Điểm cuối thêm dòng nhận mới mang phiên bản cộng một và trỏ về dòng cũ kèm lý do sửa, đồng thời cập nhật lô và ghi một dòng điều chỉnh có dấu vào sổ di chuyển khi số lượng đổi, tất cả trong một giao dịch.
### Định nghĩa xong
- [ ] Dùng mẫu bút toán đảo và sổ audit chung của nền tảng; dòng mới có phiên bản cộng một, trỏ dòng cũ, kèm lý do sửa; dòng cũ bất biến; mỗi dòng cũ chỉ có tối đa một dòng kế
- [ ] Lô đổi số lượng, mã lô nhà cung cấp, hạn, mã truy xuất theo dòng mới; dòng điều chỉnh vào sổ trong cùng giao dịch; tồn khả dụng không bao giờ âm
- [ ] Ngày sản xuất và thời điểm nhận không đổi được; yêu cầu đổi ngày sản xuất bị từ chối
- [ ] Kiểm lại lane gạo và bò như lúc nhận; sửa không tạo ra lỗi truy xuất mới
- [ ] Không đổi trạng thái lô; có khoá idempotency; chỉ vai kho và QC gọi được
- [ ] Kiểm thử cho các tình huống trên, kể cả hai người sửa cùng lúc
### Chỗ hở
- GAP-23 quy trình "từ chối giao rồi nhận lại" thay cho đổi ngày sản xuất chưa được khách xác nhận

## E04-I05-S02 · Nút và luồng sửa dòng nhận trên SCR004 (FE)
- Loại: sub-issue
- Cha: E04-I05
- Vai: FE-1
- Nhãn: type:sub-issue
- Spec: SCR004, API-INV-02
- Phụ thuộc: E04-I05-S01, E04-I04-S01
### Mô tả
Bổ sung điểm vào và form sửa cho dòng vừa nhận. Vì sửa wireframe đã duyệt, bước đầu là phác bố cục tối thiểu (vị trí nút sửa, form, thông báo) cho BrSE duyệt rồi mới dựng.
### Định nghĩa xong
- [ ] Bản phác bố cục được BrSE duyệt và ghi vào danh sách thay đổi thiết kế trước khi viết mã
- [ ] Form sửa mở với giá trị dòng hiện tại, chỉ cho sửa các trường được phép, bắt buộc lý do sửa
- [ ] Lỗi từ server (tồn âm, ngày sản xuất cố định, mã truy xuất sai) hiện tại đúng trường
- [ ] Sau khi sửa hiện dòng mới và lịch sử dòng cũ ở dạng chỉ đọc
- [ ] Kiểm thử đầu-cuối cho một luồng sửa số lượng và một luồng xung đột
### Chỗ hở
- GAP-23 bố cục nút sửa là thay đổi so với wireframe đã duyệt

## E04-I06 · Làm rõ với khách về đối soát tổng dòng nhận với số báo (type:clarification)
- Loại: issue
- Cha: E04
- Vai: BrSE
- Nhãn: type:issue, type:clarification
- Spec: FR-REC-03, SCR003, API-INB-02, API-INB-03
### Mô tả
Yêu cầu nhận hàng đòi tổng các dòng khớp số báo và có người đủ quyền duyệt khi lệch, nhưng màn kiểm nhận chỉ ghi từng dòng; số báo nằm ở màn lịch hẹn nhập của giai đoạn 2 và yêu cầu không nêu vai trò duyệt hay ngưỡng lệch. Hiện yêu cầu chỉ được đáp ứng một phần. Issue này lấy quyết định của khách để E04-I08 làm đúng và để biết có phải kéo bước đối soát về giai đoạn 1.
### Định nghĩa xong
- [ ] Gửi khách câu hỏi đóng, mỗi câu có phương án mặc định: vai nào duyệt chênh lệch, ngưỡng lệch cho phép, đối soát khi đóng phiếu hay theo từng dòng, có kéo bước đối soát về giai đoạn 1 không
- [ ] Ghi câu trả lời vào nhật ký quyết định của dự án
- [ ] Cập nhật E04-I08 (và E04-I04 nếu bước đối soát về giai đoạn 1) theo câu trả lời, mở issue mới nếu phạm vi đổi
- [ ] Nếu khách chưa trả lời, giữ ghi chú giới hạn "đáp ứng một phần" trong tài liệu bàn giao
### Chỗ hở
- GAP-22 yêu cầu xếp giai đoạn 1 nhưng màn chứa số báo xếp giai đoạn 2 và hai API của màn chưa có hợp đồng

## E04-I07 · Làm rõ với khách các danh mục và luật còn trống của nhập kho (type:clarification)
- Loại: issue
- Cha: E04
- Vai: BrSE
- Nhãn: type:issue, type:clarification
- Spec: SCR004, INB_RECEIVING_EXPIRY_CHECK, FR-REC-02, SCR003
### Mô tả
Gom các câu hỏi khách chặn go-live hoặc đang chạy bằng giá trị nhà thầu tự đặt: danh mục tình trạng bao bì (khoá cấu hình đang trống, đến lúc có giá trị thì màn dùng ô chữ tự do), danh mục mã lý do theo từng quyết định và nhãn tiếng Nhật, những ca kiểm truy xuất thất bại ngoài lỗi định dạng, xác nhận lại ba luật hạn lúc nhận của khách giả lập, luật ép tạm giữ khi nhiệt ngoài dải, chuẩn hoá chữ số toàn góc và thiết bị quét, ma trận vai trò của màn lịch hẹn nhập.
### Định nghĩa xong
- [ ] Gửi khách bộ câu hỏi đóng với phương án mặc định hiện hành và hệ quả khi khác mặc định
- [ ] Bộ câu hỏi có câu xác nhận luật ép tạm giữ khi nhiệt ngoài dải lúc nhận: kể cả dòng người kiểm chọn từ chối vẫn ghi số đo và mở case sai lệch nguồn nhận hàng, hay chỉ ép với dòng nhận
- [ ] Nhập danh mục tình trạng bao bì thật vào khoá cấu hình và gỡ trạng thái chặn go-live khi có đủ giá trị
- [ ] Ghi câu trả lời vào nhật ký quyết định; câu khác mặc định có issue sửa mã hoặc kiểm thử đi kèm
- [ ] Cập nhật danh sách giá trị tạm còn chờ khách xác nhận
### Chỗ hở
- GAP-24 khoá danh mục tình trạng bao bì còn trống, là điều kiện chặn go-live
- GAP-25 các luật hạn, mã lý do và luật ép tạm giữ đang dùng giá trị khách giả lập hoặc nhà thầu tự đặt
- GAP-12 ma trận vai trò màn lịch hẹn nhập chưa khách duyệt

## E04-I08 · Lịch hẹn nhập hàng SCR003 và đối soát với số báo (giai đoạn 2)
- Loại: issue
- Cha: E04
- Nhãn: type:issue, phase-2
- Spec: SCR003, API-INB-02, API-INB-03, FR-REC-01
- Phụ thuộc: E04-I06
- Giai đoạn: 2
### Mô tả
Màn lịch hẹn nhập cho nhân viên kho: danh sách theo ngày, giờ đến dự kiến, cửa nhập, tạo và sửa lịch hẹn theo nhà cung cấp, dải nhiệt xe và số dòng dự kiến, cảnh báo trùng nhà cung cấp trong cùng khung giờ. Màn mới có wireframe, hai API chỉ có dòng trong danh mục và chưa có bảng, nên việc viết hợp đồng và thêm bảng nằm trong issue. Bước đối soát tổng các dòng nhận với số báo khi đóng phiếu cũng được thiết kế ở đây, theo câu trả lời của issue làm rõ.
### Định nghĩa xong
- [ ] Luồng tạo lịch hẹn, thấy cảnh báo trùng khung giờ, mở danh sách theo ngày chạy được đầu-cuối
- [ ] Bước đối soát khi đóng phiếu chạy theo câu trả lời của khách; trường hợp chưa có câu trả lời thì giá trị vai duyệt và ngưỡng để trống ở cấu hình
### Chỗ hở
- GAP-22 bước đối soát phụ thuộc quyết định của khách
- GAP-26 chưa có hợp đồng hai API, chưa có bảng lịch hẹn, chưa có API phiếu báo nhập

## E04-I08-S01 · Hợp đồng, bảng và API lịch hẹn nhập, đối soát với số báo (BE)
- Loại: sub-issue
- Cha: E04-I08
- Vai: BE-2
- Nhãn: type:sub-issue, phase-2
- Spec: API-INB-02, API-INB-03, FR-REC-01, FR-REC-03, INVENTORY_DB
- Giai đoạn: 2
### Mô tả
Viết hợp đồng cho API đọc theo ngày và API tạo hoặc sửa lịch hẹn, thêm bảng lịch hẹn cùng dòng dự kiến vào lược đồ tồn kho (chỉ thêm bảng, không đổi bảng cũ), rồi hiện thực. Thêm bước đối soát tổng dòng nhận với số báo khi đóng phiếu.
### Định nghĩa xong
- [ ] Tài liệu hợp đồng hai API được BrSE đọc lại, gồm cả phiếu báo nhập mà danh mục còn thiếu
- [ ] Bảng lịch hẹn và dòng dự kiến thêm vào lược đồ bằng migration không đụng bảng cũ
- [ ] Trùng mã nhà cung cấp trong cùng khung giờ trả cảnh báo, không chặn
- [ ] Trạng thái lịch hẹn theo dõi được từ tạo tới nhận xong
- [ ] Đối soát tổng các dòng nhận với số báo khi đóng phiếu; lệch cần người đúng vai duyệt theo khoá cấu hình, không đặt số ngưỡng cứng
- [ ] Kiểm thử quyền, trùng khung giờ và đối soát lệch đều qua
### Chỗ hở
- GAP-26 danh mục API chưa có hợp đồng cho hai điểm cuối của màn

## E04-I08-S02 · Màn lịch hẹn nhập hàng SCR003 (FE)
- Loại: sub-issue
- Cha: E04-I08
- Vai: FE-1
- Nhãn: type:sub-issue, phase-2
- Spec: SCR003, FR-REC-01
- Phụ thuộc: E04-I08-S01
- Giai đoạn: 2
### Mô tả
Dựng danh sách lịch hẹn theo ngày, form thêm hoặc sửa và cảnh báo trùng khung giờ theo wireframe, cùng chỗ hiển thị kết quả đối soát khi đóng phiếu.
### Định nghĩa xong
- [ ] Danh sách, form thêm hoặc sửa và cảnh báo trùng chạy theo wireframe với dữ liệu từ API thật
- [ ] Khối kết quả đối soát hiện số báo, tổng đã nhận và trạng thái duyệt chênh lệch
- [ ] Trạng thái trống, đang tải, lỗi mạng và xung đột phiên bản hiện đúng chỗ
- [ ] Kiểm thử đầu-cuối luồng tạo lịch hẹn và luồng đóng phiếu có lệch
### Chỗ hở
- GAP-12 ma trận vai trò của màn chưa khách duyệt
