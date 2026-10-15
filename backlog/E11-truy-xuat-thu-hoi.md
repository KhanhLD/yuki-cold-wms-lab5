# E11 · Truy xuất & thu hồi
<!-- lab5-epic: E11 -->

## E11 · Truy xuất & thu hồi
- Loại: epic
- Nhãn: type:epic, constraint:rice-beef-trace, constraint:recall
- Spec: SCR022, SCR023, TRC_TRACEABILITY_SEARCH, TRC_RECALL_CASE_LIFECYCLE, TRACE_RECALL_DB, FR-TRC-01, FR-TRC-02, BR-TRACE-03, DR-TRACE-01, NFR-PERF-02, JP-15
### Mô tả
Tìm ngược xuôi từ một lô, SKU, nhà cung cấp, xuất xứ gạo hoặc mã cá thể bò tới mọi giao dịch, đơn, chuyến, khách liên quan theo ba làn (gạo, bò, nội bộ), và quản lý case thu hồi: mở case chặn ngay các lô, ghi phần đã xử lý từng lô, đóng case khi số lượng cân hai vế. Truy xuất một lô trong ba năm phải xong trong giới hạn thời gian của yêu cầu phi chức năng. Thông báo thu hồi có phiên bản (FR-TRC-03) lùi sang giai đoạn 2 vì chưa có màn nên không thành issue. Công cụ kiểm dữ liệu di trú dùng chung bảng của module này nhưng thuộc E13.

## E11-I01 · Dữ liệu liên kết truy xuất và nền case thu hồi
- Loại: issue
- Cha: E11
- Nhãn: type:issue, constraint:rice-beef-trace, constraint:recall
- Spec: TRACE_RECALL_DB, TRC_TRACEABILITY_SEARCH, DR-TRACE-01, NFR-PERF-02
- Giai đoạn: 1
### Mô tả
Nền dữ liệu cho cả tìm kiếm và thu hồi. Bảng liên kết lô – giao dịch là bản chép có chủ ý, ghi từ sự kiện của các module khác để truy xuất không phải nối nhiều bảng lúc tìm (đó là điểm đạt giới hạn thời gian ba năm); sổ cái nguồn luôn thắng khi lệch. Phần case thu hồi gồm bảng case, đầu mối liên lạc (dữ liệu cá nhân, tách bảng và lưu ngắn hơn), dòng lô và việc. Bảng phiên bản thông báo thu hồi thuộc giai đoạn 2 nên chưa tạo. Hai bảng kiểm di trú thuộc E13.

## E11-I01-S01 · Lược đồ TRACE_RECALL_DB cho liên kết và case thu hồi (BE)
- Loại: sub-issue
- Cha: E11-I01
- Vai: BE-1
- Nhãn: type:sub-issue, constraint:rice-beef-trace, constraint:recall
- Spec: TRACE_RECALL_DB, DR-TRACE-01, NFR-PERF-02
- Giai đoạn: 1
### Mô tả
Tạo bảng liên kết lô – giao dịch (khoá duy nhất theo loại liên kết, khoá nguồn, lô; chỉ mục theo lô, theo mã truy xuất của từng làn và theo đối tác), bảng case, đầu mối liên lạc tách riêng, dòng lô của case (khoá lạc quan theo phiên bản, cột sinh cho các số xử lý, ràng buộc đẳng thức ở case), và bảng việc chỉ ghi thêm. Ràng buộc mã bò đúng 10 chữ số ASCII ở cả bảng liên kết. Ràng buộc đóng case chụp sáu tổng và case đã đóng bất biến.
### Định nghĩa xong
- [ ] Migration chạy sạch từ trống và chạy lại không lỗi; các khoá duy nhất và chỉ mục theo lô, mã, đối tác có mặt
- [ ] Ràng buộc mã bò 10 chữ số ASCII từ chối mã 9 chữ số, 11 chữ số, có chữ cái, chữ số toàn khổ (kiểm thử chèn trực tiếp)
- [ ] Trigger chặn cập nhật hai lần cho case đã đóng và chặn xoá dòng việc
- [ ] Ràng buộc đẳng thức ở bảng case từ chối tổng không cân khi đóng
- [ ] Đầu mối liên lạc nằm bảng riêng, không xuất hiện trong bảng case, nhật ký hay sự kiện (kiểm bằng truy vấn)
- [ ] Tài liệu dữ liệu cập nhật khớp bảng thật
### Chỗ hở
- GAP-87 thời hạn lưu đầu mối, người phụ trách ngoài hệ thống và văn bản tự do chứa tên người chưa có câu trả lời

## E11-I01-S02 · Người nghe sự kiện ghi liên kết lô – giao dịch và việc đối soát (BE)
- Loại: sub-issue
- Cha: E11-I01
- Vai: BE-2
- Nhãn: type:sub-issue, constraint:rice-beef-trace
- Spec: TRC_TRACEABILITY_SEARCH, TRACE_RECALL_DB, DR-TRACE-01
- Phụ thuộc: E11-I01-S01, E04-I03-S02, E05-I03-S01, E05-I04-S01
- Giai đoạn: 1
### Mô tả
Nghe tám sự kiện của kho, phân bổ và giao hàng (nhận, đổi trạng thái, di chuyển, phân bổ, giao được khách nhận, pick đạt, xuất phát, POD) và ghi đúng một dòng liên kết cho mỗi bộ loại – khoá nguồn – lô. Làn và mã truy xuất chép từ dữ liệu lúc nhận lô; thời điểm nghiệp vụ lấy từ nội dung sự kiện, không phải lúc nghe. Phát lại không tạo dòng thứ hai. Thêm một việc đối soát chỉ đọc so bảng liên kết với sổ cái nguồn và báo lệch, không tự sửa. Đây là phía tiêu thụ của outbox chung do E01 dựng.
### Định nghĩa xong
- [ ] Mỗi trong tám sự kiện có một kiểm thử: sinh đúng loại liên kết, đúng thời điểm nghiệp vụ, đúng làn và mã
- [ ] Phát lại cùng sự kiện hai lần chỉ có một dòng liên kết
- [ ] Mã bò sai dạng gặp ở dữ liệu liên kết sinh cảnh báo vận hành và không bị sửa
- [ ] Việc đối soát báo được một ca lệch giả lập và không ghi gì vào bảng nguồn
- [ ] Log không chứa dữ liệu cá nhân và có correlation ID

## E11-I02 · API tìm truy xuất theo làn gạo, bò, nội bộ
- Loại: issue
- Cha: E11
- Vai: BE-1
- Nhãn: type:issue, constraint:rice-beef-trace
- Spec: API-TRC-01, TRC_TRACEABILITY_SEARCH, FR-TRC-01, BR-TRACE-03, JP-15
- Phụ thuộc: E11-I01-S01
- Giai đoạn: 1
### Mô tả
Cài lệnh đọc tìm theo lô, SKU, nhà cung cấp, xuất xứ gạo hoặc mã bò, trả với mỗi lô làn, trường pháp định theo làn, mọi giao dịch theo thời gian, một bước lên (nhà cung cấp), một bước xuống (khách đã giao) theo khoá độ sâu, điểm dừng khi thiếu liên kết đối tác (lô chưa rời kho không phải điểm dừng), trạng thái nhận từng lần giao, và vết các lần quét xếp hàng bị chặn do thu hồi. Khớp chính xác không phân biệt hoa thường là lựa chọn nhà thầu. Mã bò phải đúng 10 chữ số, không bù số 0, không cắt. Vượt trần kết quả thì trả lỗi chứ không cắt im lặng. Mỗi lượt tìm ghi một dòng nhật ký ở nền tảng.
### Định nghĩa xong
- [ ] Mười bốn ca biên của thiết kế có kiểm thử xanh, gồm mã bò 9 chữ số, 11 chữ số, chữ số toàn khổ, lô lane nội bộ thiếu nhà cung cấp, chuyến huỷ sau xuất phát, POD chỉ có dòng từ chối
- [ ] Tìm theo xuất xứ gạo chỉ trả lô làn gạo; tìm theo mã bò chỉ trả lô làn bò
- [ ] Vượt trần kết quả trả mã lỗi chung, không trả phần đầu; không có lô khớp trả 200 với danh sách rỗng
- [ ] Vết quét bị chặn hiện trong kết quả của lô thuộc case thu hồi đang mở
- [ ] Mỗi lượt tìm có đúng một dòng nhật ký; vai trò ngoài ma trận nhận 403
- [ ] Hợp đồng API-TRC-01 trong tài liệu khớp hành vi thật, gồm trường vết quét bị chặn
### Chỗ hở
- GAP-89 khớp chính xác hay một phần và độ sâu làn nội bộ chưa được khách xác nhận
- GAP-89 chưa rõ các ca kiểm truy xuất thất bại ngoài lỗi định dạng và ảnh hưởng tới kết quả tìm

## E11-I03 · Màn SCR022: tìm kiếm truy xuất
- Loại: issue
- Cha: E11
- Nhãn: type:issue, constraint:rice-beef-trace
- Spec: SCR022, NFR-PERF-02, JP-15
- Giai đoạn: 1
### Mô tả
Màn có spec đầy đủ (16 kịch bản chấp nhận): khối tìm với kiểu và giá trị, thẻ kết quả mỗi lô (làn, trường pháp định, mọi giao dịch), chuỗi một bước lên và xuống, điểm dừng, danh sách theo dõi cảnh báo nhiệt (gọi riêng API cảnh báo), vết quét bị chặn chỉ hiển thị, và liên kết sang màn thu hồi. Chia song song giao diện và kiểm thử, kèm phép đo giới hạn thời gian truy xuất ba năm trên bộ dữ liệu lớn.

## E11-I03-S01 · SCR022 giao diện tìm và thẻ kết quả (FE)
- Loại: sub-issue
- Cha: E11-I03
- Vai: FE-1
- Nhãn: type:sub-issue, constraint:rice-beef-trace
- Spec: SCR022, API-TRC-01, JP-15
- Phụ thuộc: E11-I02
- Giai đoạn: 1
### Mô tả
Làm ô tìm với kiểu tìm và kiểm giá trị ngay ở trường (mã bò đúng 10 chữ số ASCII, báo lỗi tại ô, không đệm số 0), thông báo vượt trần kết quả, thông báo không có lô khớp, thẻ mỗi lô với làn, trường pháp định, danh sách mọi giao dịch theo thời gian, khối một bước lên và xuống với điểm dừng, danh sách theo dõi cảnh báo nhiệt lấy từ API cảnh báo, vết quét bị chặn không có thao tác, và liên kết mở màn thu hồi. Mục "khách được phân bổ" của wireframe hiện lấy khách đã giao, cần xác nhận.
### Định nghĩa xong
- [ ] Mã bò 9 chữ số, 11 chữ số, có chữ cái, chữ số toàn khổ đều báo lỗi tại ô tìm và không gọi API
- [ ] Thẻ kết quả hiện đúng làn, trường pháp định theo làn, mọi giao dịch và điểm dừng với dữ liệu mẫu ba làn
- [ ] Danh sách theo dõi nhiệt hiện lô đã xuất nằm trong cảnh báo; vết quét bị chặn chỉ hiển thị
- [ ] Trạng thái rỗng, đang tải, lỗi mạng, 403 có hiển thị; liên kết sang SCR023 hoạt động
### Chỗ hở
- GAP-88 mục khách được phân bổ trên wireframe khác dữ liệu khách đã giao mà API trả

## E11-I03-S02 · Kiểm thử kịch bản chấp nhận SCR022 và phép đo truy xuất ba năm (QA)
- Loại: sub-issue
- Cha: E11-I03
- Vai: QA-1
- Nhãn: type:sub-issue, constraint:rice-beef-trace
- Spec: SCR022, NFR-PERF-02, JP-15
- Phụ thuộc: E11-I03-S01, E01-I10
- Giai đoạn: 1
### Mô tả
Viết và chạy kịch bản tự động cho 16 kịch bản chấp nhận của SCR022 và một phép đo hiệu năng: truy xuất một lô trên bộ dữ liệu cỡ ba năm (khoảng 300.000 dòng giao và 2,5 triệu số đo nhiệt, theo công thức của yêu cầu) phải xong trong 60 giây. Bộ dữ liệu sinh bằng tập lệnh, che hết thông tin thật. Dùng lại bộ sinh dữ liệu khối lượng lớn của E01-I10 (giao hàng, số đo nhiệt), không dựng bộ sinh khác.
### Định nghĩa xong
- [ ] Mỗi kịch bản chấp nhận có một ca kiểm thử tự động trỏ đúng mã, kết quả xanh
- [ ] Phép đo ba năm có báo cáo thời gian p95 trên bộ dữ liệu cỡ yêu cầu, kèm cấu hình môi trường đo
- [ ] Dữ liệu cỡ ba năm lấy từ bộ sinh của E01-I10, chạy lại được và không chứa dữ liệu thật
- [ ] Nếu quá giới hạn, báo cáo nêu truy vấn chậm nhất và đề xuất chỉ mục, không tự bỏ qua

## E11-I04 · Mở case thu hồi, chặn lô và tính số lượng
- Loại: issue
- Cha: E11
- Vai: BE-2
- Nhãn: type:issue, constraint:recall
- Spec: API-TRC-03, TRC_RECALL_CASE_LIFECYCLE, FR-TRC-02, TRACE_RECALL_DB
- Phụ thuộc: E11-I01-S01, E05-I01, E01-I13, E01-I14, E05-I10
- Giai đoạn: 1
### Mô tả
Mở case là một giao dịch duy nhất: kiểm hình thức, khoá các lô theo thứ tự cố định, gọi lệnh của module kho đặt dấu chặn loại thu hồi cho từng lô (phân bổ, quét xếp hàng đạt, kiểm trước xuất phát và xuất phát đều bị chặn sau đó), tính số nhận, số còn trong kho, số đã xuất phát ở phía máy chủ (client không gửi số lượng, mỗi phân bổ chỉ tính một lần dù chia hai chuyến), ghi case, đầu mối (không vào nhật ký hay sự kiện), việc, sự kiện mở case. Lỗi giữa chừng rollback gồm cả dấu chặn. Cần lệnh đặt dấu chặn của kho (E05) sẵn sàng. Trần số lô và số việc mỗi case đọc từ khoá cấu hình.
### Định nghĩa xong
- [ ] Mở case chặn mọi lô: phân bổ, quét đạt và xuất phát lô đó bị từ chối sau khi mở; lệnh đang giữ khoá lô thì mở case chờ và số đọc sau đã gồm tác động của lệnh kia
- [ ] Số nhận, còn trong kho, đã xuất phát do máy chủ tính; request gửi kèm số lượng bị từ chối
- [ ] Lỗi sau khi đặt dấu làm rollback toàn bộ, không lô nào bị chặn mà không có case
- [ ] Lô thuộc hai case mở có hai dấu riêng; mở case không đổi trạng thái lô và không mở case sai lệch nhiệt
- [ ] Vượt trần lô hoặc việc, hạn trước ngày nghiệp vụ, lô không tồn tại đều nhận đúng mã lỗi
- [ ] Đầu mối liên lạc không xuất hiện trong nhật ký và sự kiện (kiểm bằng truy vấn)
- [ ] Hợp đồng API-TRC-03 trong tài liệu khớp hành vi thật
- [ ] Lô thuộc case đang mở đọc được qua dấu chặn thu hồi ở kho sau khi giữ khoá lô, dùng cho chuỗi loại trừ, quét xếp hàng và cổng xuất phát; mở case chỉ đặt dấu, không đổi trạng thái lô

### Chỗ hở
- GAP-86 chưa có lệnh thêm lô vào case đang mở, lô phát hiện thêm phải mở case mới
- GAP-87 người phụ trách việc có thể ngoài hệ thống; văn bản tự do dễ chứa tên người

## E11-I05 · Ghi xử lý từng lô và đóng case cân bằng
- Loại: issue
- Cha: E11
- Vai: BE-1
- Nhãn: type:issue, constraint:recall
- Spec: API-TRC-02, API-TRC-04, API-TRC-05, TRC_RECALL_CASE_LIFECYCLE, FR-TRC-02
- Phụ thuộc: E11-I04, E01-I16, E01-I14
- Giai đoạn: 1
### Mô tả
Danh sách case kèm đẳng thức và tên hiển thị người phụ trách; ghi phần xử lý từng lô (huỷ tại kho, trả về, huỷ sau trả, chưa xử lý) với khoá lạc quan theo phiên bản dòng và đọc lại số thật lúc lưu; dòng chưa cân vẫn lưu được. Đóng case chỉ khi cả hai vế của đẳng thức bằng không, không dòng nào tự lệch, và số thật của lô chưa đổi từ lần lưu dòng; đóng chụp sáu tổng, gỡ các dấu chặn thu hồi của chính case (dấu của case khác và dấu nhiệt độ giữ nguyên) và khoá case thành bất biến. Cách tính "tồn chưa xử lý tính là chưa xử lý" là cách hiểu nhà thầu.
### Định nghĩa xong
- [ ] Mười sáu ca biên của thiết kế có kiểm thử, gồm hai dòng lệch bù nhau vẫn không đóng được, case rỗng đóng được, hai người đóng cùng lúc, hai người sửa một dòng
- [ ] Đóng case lệch trả khoảng lệch hai vế và lô lệch; tồn đổi sau lần lưu thì từ chối và yêu cầu lưu lại
- [ ] Đóng gỡ đúng dấu thu hồi của case và giữ dấu của case khác; không đổi trạng thái lô
- [ ] Sáu tổng chụp vào case khớp phép tính tay trên bộ dữ liệu mẫu gạo và bò
- [ ] Case đã đóng từ chối mọi sửa dòng
- [ ] Hợp đồng ba API trong tài liệu khớp hành vi thật
- [ ] Thời điểm đóng case không sớm hơn lúc mở và lần ghi cuối của dòng (kẹp, không lỗi) khi ngày nghiệp vụ giả lập đổi giữa chừng

### Chỗ hở
- GAP-86 chưa rõ có cho lưu trạng thái đang xử lý khi chưa cân, và ai được mở lại case đã đóng
- GAP-86 cách tính tồn chưa xử lý vào mục chưa xử lý chưa khách xác nhận

## E11-I06 · Màn SCR023: quản lý case thu hồi
- Loại: issue
- Cha: E11
- Nhãn: type:issue, constraint:recall
- Spec: SCR023, FR-TRC-02, JP-15
- Giai đoạn: 1
### Mô tả
Màn có spec đầy đủ (22 kịch bản chấp nhận): danh sách case với đẳng thức, form mở case (phạm vi, đầu mối, hạn, lô, việc với người phụ trách chọn từ người dùng nội bộ), chi tiết case, bảng đối chiếu lô và số lượng với các ô nhập số xử lý, khối đẳng thức hiện phần lệch, điểm dừng chuỗi, khách đã nhận lô và liên kết sang truy xuất. Quản trị viên chỉ xem. Chia giao diện và kiểm thử.

## E11-I06-S01 · SCR023 giao diện danh sách, mở case, đối chiếu và đóng case (FE)
- Loại: sub-issue
- Cha: E11-I06
- Vai: FE-1
- Nhãn: type:sub-issue, constraint:recall
- Spec: SCR023, API-TRC-02, API-TRC-03, API-TRC-04, API-TRC-05
- Phụ thuộc: E11-I05
- Giai đoạn: 1
### Mô tả
Làm danh sách case và chi tiết, form mở case có kiểm trường và hạn, chọn người phụ trách từ danh sách người dùng nội bộ đang hoạt động, bảng đối chiếu theo lô với ô nhập bốn số xử lý và hiện ba số do máy chủ tính, khối đẳng thức hiện khoảng lệch hai vế và chặn nút đóng khi chưa cân, xử lý xung đột phiên bản dòng, trạng thái chỉ xem cho case đã đóng và cho quản trị viên, đầu mối chỉ hiện ở chi tiết. Không có nút thêm lô vào case đang mở.
### Định nghĩa xong
- [ ] Form mở case kiểm trường bắt buộc, hạn, lô sai dạng; lỗi máy chủ hiện đúng trường
- [ ] Bảng đối chiếu và khối đẳng thức cập nhật sau mỗi lần lưu dòng; nút đóng chỉ bật khi hai vế bằng không
- [ ] Hai người sửa cùng một dòng: người sau thấy lỗi xung đột và tải lại được
- [ ] Case đã đóng và vai trò quản trị viên ở chế độ chỉ xem; đầu mối chỉ hiện ở chi tiết
- [ ] Trạng thái rỗng, lỗi mạng, gửi lại sau lỗi mạng dùng cùng khoá idempotency, 403, 404 có hiển thị
### Chỗ hở
- GAP-86 giao diện không có thao tác thêm lô vào case đang mở

## E11-I06-S02 · Kiểm thử kịch bản chấp nhận SCR023 và đối chiếu số lượng gạo, bò (QA)
- Loại: sub-issue
- Cha: E11-I06
- Vai: QA-1
- Nhãn: type:sub-issue, constraint:recall, constraint:rice-beef-trace
- Spec: SCR023, FR-TRC-02, JP-15
- Phụ thuộc: E11-I06-S01
- Giai đoạn: 1
### Mô tả
Viết và chạy kịch bản tự động cho 22 kịch bản chấp nhận của SCR023, nhấn vào các ca khó: mở case chặn lô thật, đẳng thức hai vế cho case gạo và case bò, tồn đổi sau khi lưu dòng, đóng case giữ dấu của case khác, hai người cùng thao tác. Dữ liệu mẫu hai lô đại diện cho làn gạo và làn bò tự sinh, không dùng dữ liệu thật. Kết quả là bằng chứng cho cổng nghiệm thu truy xuất ở E13.
### Định nghĩa xong
- [ ] Mỗi kịch bản chấp nhận có một ca kiểm thử trỏ đúng mã, kết quả xanh
- [ ] Hai ca case gạo và case bò kiểm đẳng thức đủ sáu tổng với số tính tay
- [ ] Ca hai người cùng đóng và cùng sửa dòng chạy lặp không chập chờn
- [ ] Bộ dữ liệu mẫu và hướng dẫn chạy lại nằm trong repo kiểm thử, không chứa dữ liệu thật

## E11-I07 · Làm rõ với khách: các điểm chặn của truy xuất và thu hồi
- Loại: issue
- Cha: E11
- Vai: BrSE
- Nhãn: type:issue, type:clarification, constraint:recall
- Spec: SCR023, TRC_RECALL_CASE_LIFECYCLE, TRC_TRACEABILITY_SEARCH, FR-TRC-02
- Giai đoạn: 1
### Mô tả
Gom các câu khách đang để mở của epic thành một lượt hỏi câu đóng có phương án mặc định: (1) có cần thêm lô vào case đang mở hay luôn mở case mới; (2) có cho lưu trạng thái đang xử lý khi chưa cân (đang cho phép) và ai được mở lại case đã đóng (đang không cho); (3) tồn chưa xử lý có tính vào mục chưa xử lý không; (4) người phụ trách việc có thể là người ngoài hệ thống không, cùng thời hạn lưu đầu mối liên lạc; (5) khớp chính xác hay một phần, độ sâu làn nội bộ, và có cần hiện khách được phân bổ nhưng chưa giao; (6) các ca kiểm truy xuất thất bại ngoài lỗi định dạng. Không tự đặt giá trị thay khách.
### Định nghĩa xong
- [ ] Danh sách câu hỏi đóng gửi đúng người trả lời, mỗi câu có phương án mặc định và nêu issue bị ảnh hưởng
- [ ] Câu trả lời ghi vào sổ quyết định; câu nào đổi hành vi thì tạo hoặc sửa issue tương ứng trong epic
- [ ] Các issue E11-I02, E11-I04, E11-I05, E11-I06 được cập nhật theo câu trả lời hoặc ghi rõ giữ giả định hiện tại
### Chỗ hở
- GAP-86 thêm lô vào case đang mở
- GAP-86 lưu khi chưa cân và mở lại case
- GAP-89 khớp chính xác hay một phần
