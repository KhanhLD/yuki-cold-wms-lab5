# E13 · Tích hợp, chuyển đổi dữ liệu & nghiệm thu
<!-- lab5-epic: E13 -->

## E13 · Tích hợp, chuyển đổi dữ liệu & nghiệm thu
- Loại: epic
- Nhãn: type:epic, constraint:rice-beef-trace
- Spec: ADR-008, IF-ERP-01, IF-MAIL-01, SCR031, SCR032, DR-MIG-01, ACC-FUN-01, ACC-TEMP-01, ACC-DATE-01, ACC-SCH-01, ACC-TRC-01, ACC-MIG-01, ACC-NFR-01, ACC-GO-01
### Mô tả
Lớp nối hệ thống với bên ngoài và đưa hệ thống vào vận hành: khung adapter với outbox và việc nền, adapter ERP hai chiều, mail relay, hai màn giám sát (tích hợp, di trú), công cụ kiểm và nạp dữ liệu di trú từ sáu nguồn cũ, rồi các cổng nghiệm thu, đào tạo và go-live. Adapter bản đồ thuộc E08, adapter datalogger thuộc E10, phần outbox phía nền tảng và việc dựng gói lưu trữ có manifest (E12-I05) thuộc E01 và E12; epic này làm phía người nghe, thử lại và giám sát. Ba mục quản trị dự án của yêu cầu vận hành (nhịp họp, nhật ký thay đổi, sổ rủi ro) là việc quản lý chứ không phải sản phẩm giao được nên không thành issue.

## E13-I01 · Khung tích hợp: adapter, người nghe outbox và việc nền
- Loại: issue
- Cha: E13
- Nhãn: type:issue
- Spec: ADR-008, ADR-012
- Giai đoạn: 1
### Mô tả
Phần dùng chung của mọi adapter theo ADR-008: mọi giao thức ngoài nằm trong module tích hợp, module nghiệp vụ không biết giao thức; chiều vào gọi lệnh của module nghiệp vụ, chiều ra nghe sự kiện từ outbox. Chia hai việc song song: khung người nghe và thử lại, và các việc theo lịch có khoá phân tán. Bảng phát hành sự kiện của nền tảng do E01 dựng, issue này dựa lên đó.

## E13-I01-S01 · Khung adapter, người nghe idempotent, thử lại có giới hạn, sự kiện lỗi (BE)
- Loại: sub-issue
- Cha: E13-I01
- Vai: BE-1
- Nhãn: type:sub-issue
- Spec: ADR-008, ADR-012, PLATFORM_DB
- Giai đoạn: 1
### Mô tả
Dựng khung chung: giao diện adapter chiều vào và chiều ra, người nghe sự kiện chạy sau commit và idempotent theo mã sự kiện (bảng giao nhận có khoá duy nhất theo mã sự kiện), ghi mỗi lần giao nhận số lần thử và lỗi cuối, thử lại theo khoảng chờ và số lần lấy từ khoá cấu hình, hết lượt thì phát sự kiện thất bại (file vào bị từ chối, thư gửi lỗi) và bắn cảnh báo vận hành. Mọi lần giao nhận mang correlation ID; log không ghi token, chữ ký, ảnh gốc. Bảng giao nhận, bảng file vào và trạng thái adapter của PLATFORM_DB do issue này tạo. Kiểm tra kiến trúc trong CI chặn module nghiệp vụ import thư viện giao thức ngoài.
### Định nghĩa xong
- [ ] Phát lại cùng sự kiện hai lần cho mỗi người nghe mẫu chỉ tạo một bản giao nhận (kiểm thử bắt buộc theo thiết kế)
- [ ] Lỗi đối tác không làm hỏng giao dịch nghiệp vụ đã commit; hết lượt thử sinh sự kiện thất bại và cảnh báo vận hành
- [ ] Số lần thử, khoảng chờ, cỡ file tối đa đọc từ khoá cấu hình; thiếu khoá thì không khởi động
- [ ] Bài kiểm tra ranh giới trong CI đỏ khi một module nghiệp vụ import thư viện SFTP, HTTP hoặc SMTP
- [ ] Mọi bản giao nhận có correlation ID tìm được trong log có cấu trúc; log không chứa bí mật
- [ ] Tài liệu hướng dẫn viết adapter mới (một trang) kèm một adapter mẫu chạy được
- [ ] Bảng giao nhận (khoá duy nhất theo mã sự kiện), bảng file vào và trạng thái adapter của PLATFORM_DB migrate được và dùng được cho adapter mẫu
## E13-I01-S02 · Việc theo lịch có khoá phân tán: phát lại outbox, quét landing, khung cho việc nền khác (BE)
- Loại: sub-issue
- Cha: E13-I01
- Vai: BE-2
- Nhãn: type:sub-issue
- Spec: ADR-008, ADR-012
- Giai đoạn: 1
### Mô tả
Ứng dụng chạy nhiều task nên mỗi việc nền lấy khoá trong cơ sở dữ liệu để chỉ một task chạy một lượt. Dựng khung đó (lấy khoá, giải phóng khi task chết, bỏ lượt không lỗi) rồi cài hai việc của tích hợp: phát lại các bản ghi outbox chưa hoàn tất theo chu kỳ (at-least-once) và quét vùng landing bắt file bị lỡ thông báo (an toàn khi trùng nhờ hash và khoá idempotency). Các việc nền của nhiệt độ (E10) và lưu giữ (E01) dùng lại khung này; chu kỳ mỗi việc là khoá cấu hình riêng.
### Định nghĩa xong
- [ ] Hai task chạy cùng lúc: một lấy được khoá, task kia bỏ lượt không lỗi; task đang giữ khoá chết thì khoá được nhả sau thời gian chờ
- [ ] Việc phát lại outbox xử lý bản ghi chưa hoàn tất và đánh dấu hoàn tất; chạy lại không tạo giao nhận đôi
- [ ] Việc quét landing bắt file đã lỡ thông báo và bỏ qua file đã xử lý
- [ ] Chu kỳ mỗi việc đọc từ khoá cấu hình riêng; E10-I04-S02, E01-I13 và E01-I17-S01 gắn việc của mình vào khung mà không sửa khung
- [ ] Có số đo thời gian từ lúc đến hạn tới lúc việc chạy, ghi trong báo cáo kiểm thử

## E13-I02 · Adapter ERP hai chiều qua SFTP quản lý
- Loại: issue
- Cha: E13
- Nhãn: type:issue
- Spec: IF-ERP-01, ADR-008
- Giai đoạn: 1
### Mô tả
Trao đổi CSV UTF-8 với ERP qua SFTP quản lý: chiều vào (đơn hàng, hàng về) và chiều ra (kết quả tồn, kết quả xuất phát), có phiên bản schema và khoá idempotency; checksum sai hoặc trùng khoá thì từ chối cả file, không nạp một phần. Schema thật của ERP chưa có nên bộ phân tích cột tách riêng theo phiên bản schema và dùng file mẫu tạm theo mẫu tên file; khi khách trả lời chỉ thêm một bộ phân tích.

## E13-I02-S01 · Chiều vào: nhận file ERP, kiểm checksum – schema – khoá, nạp một giao dịch cho cả file (BE)
- Loại: sub-issue
- Cha: E13-I02
- Vai: BE-1
- Nhãn: type:sub-issue
- Spec: IF-ERP-01, ADR-008
- Phụ thuộc: E13-I01-S01, E13-I06, E07-I02-S01
- Giai đoạn: 1
### Mô tả
File đặt vào vùng landing của dịch vụ SFTP quản lý, sự kiện báo ứng dụng, adapter kiểm checksum, phiên bản schema, khoá idempotency rồi gọi lệnh tạo đơn hoặc ghi hàng về của module nghiệp vụ trong một giao dịch cho cả file. Sai bất kỳ điều kiện nào thì từ chối cả file, phát sự kiện file vào bị từ chối và không nạp dòng nào. Việc quét landing của E13-I01-S02 bắt file lỡ thông báo. Cột thật chờ câu trả lời của khách nên dùng bộ phân tích theo phiên bản với file mẫu tạm.
### Định nghĩa xong
- [ ] File hợp lệ mẫu nạp đủ trong một giao dịch; một dòng lỗi nghiệp vụ làm rollback cả file và sinh sự kiện bị từ chối
- [ ] Checksum sai, trùng khoá idempotency, phiên bản schema lạ đều từ chối cả file và không nạp dòng nào (ba kiểm thử)
- [ ] Dòng đơn đi qua cùng lệnh tạo dòng đơn và cùng quy tắc tham chiếu duy nhất với đường tạo thủ công (API-ALC-05, E07-I02-S01)
- [ ] Gửi lại file đã xử lý (cùng hash) xử lý an toàn và không tạo đơn đôi
- [ ] Bộ phân tích tách riêng theo phiên bản schema; thêm một bộ mới không sửa bộ cũ (chứng minh bằng một bộ giả)
- [ ] Kết quả mỗi lần chạy hiện được ở màn giám sát tích hợp (số dòng, số từ chối, lý do)
### Chỗ hở
- GAP-91 cột thật của file ERP chưa có (câu khách mở), mọi bộ phân tích hiện chỉ dựa file mẫu tạm

## E13-I02-S02 · Chiều ra: ghi file kết quả tồn và xuất phát lên SFTP có checksum (BE)
- Loại: sub-issue
- Cha: E13-I02
- Vai: BE-2
- Nhãn: type:sub-issue
- Spec: IF-ERP-01, ADR-008
- Phụ thuộc: E13-I01-S01
- Giai đoạn: 1
### Mô tả
Người nghe các sự kiện xuất phát và kết quả kiểm kho ghi file kết quả có checksum lên SFTP, đặt tên theo mẫu ngày, phát sự kiện đã xuất file. Thất bại thì thử lại theo khung chung, hết lượt hiện ở màn giám sát. Phải xác nhận sự kiện nào của nghiệp vụ sinh file nào vì hiện tài liệu chỉ nêu ví dụ, không có bảng đối ứng đầy đủ.
### Định nghĩa xong
- [ ] Bảng đối ứng sự kiện nghiệp vụ với file ra được chốt và ghi vào tài liệu tích hợp
- [ ] Sự kiện xuất phát sinh đúng một file kết quả có checksum; phát lại sự kiện không sinh file thứ hai
- [ ] ERP hoặc SFTP lỗi: thử lại theo khung, hết lượt thì có sự kiện thất bại và hiện ở màn giám sát
- [ ] Tên file đúng mẫu ngày, nội dung UTF-8, dòng đếm và tổng kiểm khớp dữ liệu nguồn trong ca kiểm thử
### Chỗ hở
- GAP-91 chưa có bảng đối ứng đầy đủ giữa sự kiện nghiệp vụ và file ra; cột file chưa chốt

## E13-I03 · Mail relay doanh nghiệp và hàng đợi thử lại
- Loại: issue
- Cha: E13
- Vai: BE-1
- Nhãn: type:issue
- Spec: IF-MAIL-01, ADR-008
- Phụ thuộc: E13-I01-S01
- Giai đoạn: 1
### Mô tả
Thông báo đã duyệt (cảnh báo nhiệt, leo thang, duyệt, báo cáo) gửi qua mail relay doanh nghiệp bằng SMTP, không có kênh tin nhắn điện thoại. Người nghe sự kiện đưa thư vào hàng đợi, thử lại theo khung chung, hết lượt thì phát sự kiện thư gửi lỗi và hiện ở màn giám sát; không bao giờ thất bại im lặng. Thông số relay (máy chủ, xác thực, giới hạn gửi) chưa có nên dùng điểm cuối giả lập ở môi trường kiểm thử.
### Định nghĩa xong
- [ ] Sự kiện cảnh báo và leo thang nhiệt độ sinh đúng một thư vào hàng đợi cho người nhận theo vai trò
- [ ] Relay giả lập từ chối: thư được thử lại theo khoảng chờ cấu hình; hết lượt thì có sự kiện thư lỗi và dòng ở màn giám sát
- [ ] Phát lại sự kiện hai lần không gửi hai thư
- [ ] Thông tin đăng nhập relay lấy từ kho bí mật, không nằm trong mã hay log
- [ ] Môi trường dev, test, UAT chỉ dùng điểm cuối giả lập, không gửi tới người thật
### Chỗ hở
- GAP-97 thông số relay chưa có (máy chủ, xác thực, giới hạn gửi)

## E13-I04 · Màn SCR031: theo dõi tích hợp bên ngoài
- Loại: issue
- Cha: E13
- Nhãn: type:issue
- Spec: SCR031, API-SYS-02, API-SYS-03, IF-ERP-01
- Giai đoạn: 1
### Mô tả
Màn chỉ có wireframe: nhật ký các lần chạy tích hợp (đối tác, thời điểm nghiệp vụ, tên file, số dòng, số bị từ chối, kết quả, lỗi, thao tác chạy lại cùng khoá chống trùng), lịch sử file datalogger đã nạp, và nhật ký thông báo thư (nội dung, người nhận, kênh, trạng thái gửi). Chỉ hai API (xem nhật ký, chạy lại) có dòng trong danh mục; phần nhật ký thông báo chưa có API nên phải bổ sung hoặc chốt nguồn dữ liệu. Chia API và giao diện.

## E13-I04-S01 · API nhật ký tích hợp, chạy lại và nguồn nhật ký thư (BE)
- Loại: sub-issue
- Cha: E13-I04
- Vai: BE-2
- Nhãn: type:sub-issue
- Spec: API-SYS-02, API-SYS-03, IF-MAIL-01, IF-ERP-01
- Phụ thuộc: E13-I01-S01
- Giai đoạn: 1
### Mô tả
Viết hợp đồng và cài API-SYS-02 (liệt kê các lần chạy, lọc theo đối tác, trạng thái, khoảng thời gian, phân trang) và API-SYS-03 (chạy lại một lần chạy lỗi với cùng khoá chống trùng, ghi người thao tác). Chốt tập trạng thái của một lần chạy (tài liệu chỉ dùng chỗ giữ chỗ) và nguồn dữ liệu cho khối nhật ký thư và lịch sử file logger; nếu cần endpoint mới thì ghi vào danh mục API. Chạy lại phải an toàn với trùng lặp.
### Định nghĩa xong
- [ ] Hai API có hợp đồng viết xong và chạy được; tập trạng thái chạy được chốt và ghi vào tài liệu
- [ ] Chạy lại một lần chạy lỗi dùng cùng khoá idempotency; chạy lại lần chạy thành công bị từ chối
- [ ] Nguồn dữ liệu nhật ký thư và lịch sử file logger được chốt (endpoint nào), kèm hợp đồng nếu thêm mới
- [ ] Chỉ vai trò được liệt kê gọi được; vai trò khác nhận 403; thao tác chạy lại ghi nhật ký kiểm toán
- [ ] Lần gọi bản đồ lỗi do adapter của E08-I03 ghi qua khung chung hiện cùng bảng nhật ký với các lần chạy khác

### Chỗ hở
- GAP-92 danh mục API không có điểm cuối cho nhật ký thông báo thư; tập trạng thái lần chạy chưa chốt

## E13-I04-S02 · Màn SCR031 (FE)
- Loại: sub-issue
- Cha: E13-I04
- Vai: FE-1
- Nhãn: type:sub-issue
- Spec: SCR031, API-SYS-02, API-SYS-03
- Phụ thuộc: E13-I04-S01
- Giai đoạn: 1
### Mô tả
Làm ba khối của màn: bảng các lần chạy tích hợp với bộ lọc và nút chạy lại cho dòng lỗi, ghi chú rằng lỗi checksum hoặc trùng khoá thì từ chối cả file, lịch sử file datalogger đã nạp với phân trang, và nhật ký thông báo thư kèm trạng thái gửi. Số lần thử không vẽ (là mặc định kỹ thuật). Nhãn tiếng Nhật dùng bộ đã duyệt, các nhãn chưa có người bản ngữ xem được đánh dấu chờ.
### Định nghĩa xong
- [ ] Bảng lần chạy hiện đủ cột từ API; nút chạy lại chỉ có ở dòng lỗi và hỏi xác nhận trước khi gọi
- [ ] Khối lịch sử file logger và nhật ký thư hiện đúng dữ liệu và phân trang
- [ ] Chạy lại thành công làm dòng đổi trạng thái; chạy lại lỗi hiện thông báo từ máy chủ
- [ ] Trạng thái rỗng, lỗi mạng, 403 có hiển thị; chỉ vai trò đủ quyền vào được màn

## E13-I05 · Công cụ kiểm dữ liệu di trú và màn SCR032
- Loại: issue
- Cha: E13
- Nhãn: type:issue, constraint:rice-beef-trace
- Spec: DR-MIG-01, SCR032, API-SYS-04, API-SYS-05, API-SYS-06
- Giai đoạn: 1
### Mô tả
Đưa lịch sử giao hàng từ sáu nguồn điện tử cũ vào hệ thống một lần trước go-live: mỗi dòng nguồn có đúng một kết quả trong bốn loại (chấp nhận, từ chối, trùng, chờ nghiệp vụ xem) kèm lý do có tên và số dòng nguồn; không tự sửa ngày, khách, lô, số lượng; mã bò 9 chữ số vào loại chờ nghiệp vụ xem, không đệm thành 10; tổng nguồn phải bằng tổng bốn loại. Màn SCR032 chỉ có wireframe và hiện tiến độ theo nguồn và theo lần diễn tập. Chia công cụ, API và màn. Việc nạp các dòng đã chấp nhận vào lịch sử giao hàng và chỗ ghi chữ ký của nghiệp vụ chưa có thiết kế riêng, làm theo hướng của ADR-008 trong sub-issue nạp, xem chỗ hở.

## E13-I05-S01 · Công cụ kiểm và phân loại dòng di trú, lineage (BE)
- Loại: sub-issue
- Cha: E13-I05
- Vai: BE-1
- Nhãn: type:sub-issue, constraint:rice-beef-trace
- Spec: DR-MIG-01, TRACE_RECALL_DB, TRC_TRACEABILITY_SEARCH, JP-15
- Giai đoạn: 1
### Mô tả
Tạo hai bảng lượt kiểm và dòng kết quả (chỉ ghi thêm; ràng buộc tổng nguồn bằng bốn loại, loại chờ nghiệp vụ xem nằm ngoài số từ chối) và công cụ chạy một lần: đọc file nguồn, kiểm checksum, phân loại từng dòng, ghi lý do có tên và số dòng nguồn, đếm theo loại, từ chối lượt khi tổng lệch. Dòng làn bò mã chỉ gồm chữ số nhưng độ dài khác 10 vào chờ nghiệp vụ xem; mã có ký tự khác chữ số bị từ chối; không bao giờ làm tròn hay đệm. Lượt kiểm chỉ ghi kết quả và không nạp vào bảng nghiệp vụ. Giới hạn số dòng mỗi lượt đọc từ khoá cấu hình. Tên cột thật của sáu nguồn chưa có nên bộ phân tích tách theo loại file.
### Định nghĩa xong
- [ ] Hai bảng migrate được; ràng buộc cân tổng bốn loại từ chối lượt kiểm lệch (kiểm thử chèn trực tiếp)
- [ ] Bộ ca bẩn của thiết kế (mã bò 9 chữ số, 11 chữ số, chữ cái, thiếu hạn, trùng khoá khác số lượng) cho đúng kết quả và lý do; không có sửa dữ liệu nào
- [ ] Không có dòng nào được ghi vào bảng nghiệp vụ hay bảng liên kết truy xuất
- [ ] File vượt giới hạn số dòng bị từ chối cả lượt kèm hướng dẫn chia file, không cắt im lặng
- [ ] Mỗi dòng truy ngược được tới file, số dòng nguồn và phép biến đổi áp dụng (lineage)
### Chỗ hở
- GAP-94 chưa có thiết kế nạp các dòng đã chấp nhận vào bảng nghiệp vụ, và chưa có nơi ghi chữ ký của nghiệp vụ
- GAP-98 chưa có thời hạn lưu giữ và cột dữ liệu cá nhân của nguồn di trú

## E13-I05-S02 · API xem lượt kiểm, tạo lượt kiểm và danh sách ngoại lệ (BE)
- Loại: sub-issue
- Cha: E13-I05
- Vai: BE-2
- Nhãn: type:sub-issue, constraint:rice-beef-trace
- Spec: API-SYS-04, API-SYS-05, API-SYS-06, DR-MIG-01
- Phụ thuộc: E13-I05-S01
- Giai đoạn: 1
### Mô tả
Viết hợp đồng và cài ba API: liệt kê lượt kiểm theo nguồn và theo lần diễn tập (kèm tỷ lệ từ chối, tổng khớp hay không), tạo lượt kiểm mới và danh sách ngoại lệ có thể tải xuống. Danh mục có điểm cuối tạo lượt kiểm nhưng màn không vẽ chỗ tải file nguồn, nên phải chốt lượt kiểm được tạo bằng đường nào (màn hay công cụ dòng lệnh của quản trị) trước khi cài. Chỉ quản trị viên được gọi.
### Định nghĩa xong
- [ ] Ba API có hợp đồng viết xong và chạy được; cách tạo lượt kiểm được chốt và ghi vào tài liệu
- [ ] Danh sách trả đúng tổng và tỷ lệ từ chối; ca tổng lệch được cờ rõ
- [ ] Danh sách ngoại lệ tải xuống được, có dòng nguồn và lý do, không lẫn dữ liệu cá nhân trong log
- [ ] Vai trò khác quản trị viên nhận 403
### Chỗ hở
- GAP-93 điểm cuối tạo lượt kiểm tồn tại nhưng màn không có chỗ tải file nguồn

## E13-I05-S03 · Màn SCR032: tiến độ di trú (FE)
- Loại: sub-issue
- Cha: E13-I05
- Vai: FE-1
- Nhãn: type:sub-issue, constraint:rice-beef-trace
- Spec: SCR032, API-SYS-04, API-SYS-06
- Phụ thuộc: E13-I05-S02
- Giai đoạn: 1
### Mô tả
Làm bảng kết quả di trú theo từng lần diễn tập và theo nguồn (số nhận, từ chối trong đó có chờ nghiệp vụ xem, trùng, tỷ lệ từ chối, cờ tổng khớp), dòng ghi chú đẳng thức tổng nguồn bằng tổng bốn loại, giải thích bốn loại kết quả, và nút tải danh sách ngoại lệ. Màn có hiện từng dòng hay chỉ cho tải xuống chưa chốt, theo kết luận ở GAP-93.
### Định nghĩa xong
- [ ] Bảng hiện đúng số liệu từ API; dòng tổng lệch được tô cờ rõ
- [ ] Nút tải danh sách ngoại lệ cho ra tệp đúng nội dung
- [ ] Trạng thái rỗng (chưa có lượt kiểm), lỗi mạng, 403 có hiển thị; chỉ quản trị viên vào được màn
### Chỗ hở
- GAP-93 chưa chốt màn hiện từng dòng hay chỉ tải xuống

## E13-I05-S04 · Nạp dòng di trú đã chấp nhận và đã ký vào lịch sử giao đã nhận (BE)
- Loại: sub-issue
- Cha: E13-I05
- Vai: BE-2
- Nhãn: type:sub-issue, constraint:rice-beef-trace
- Spec: DR-MIG-01, ADR-008, DR-HIST-01
- Phụ thuộc: E13-I05-S01, E13-I06
- Giai đoạn: 1
### Mô tả
Lượt kiểm chỉ ghi kết quả; sub-issue này làm bước nạp mà module phân bổ cần trước khi bật phân bổ thật, vì mốc 日付逆転 dựa vào lịch sử giao đã được khách nhận. Công cụ chạy một lần, không qua outbox, chỉ nạp dòng đã chấp nhận và dòng chờ nghiệp vụ xem đã có chữ ký, gọi lệnh của module phân bổ (module chủ dữ liệu tự nạp, không ghi thẳng bảng), đánh dấu nguồn là di trú và giữ liên kết về dòng kiểm. Không tự sửa ngày, khách, lô, số lượng. Chỗ ghi chữ ký của nghiệp vụ và thứ tự nạp chưa có thiết kế nên cách ghi ký được chốt theo câu trả lời ở E13-I06 trước khi cài.
### Định nghĩa xong
- [ ] Cách ghi chữ ký của nghiệp vụ cho dòng chờ xem được chốt và ghi vào tài liệu theo câu trả lời ở E13-I06
- [ ] Chỉ dòng chấp nhận và dòng chờ xem đã ký được nạp; dòng từ chối, trùng, chờ xem chưa ký không bao giờ vào bảng nghiệp vụ (kiểm thử âm)
- [ ] Nạp đi qua lệnh của module phân bổ, mỗi dòng nạp mang dấu nguồn di trú và liên kết về dòng kiểm; chạy lại không nạp đôi
- [ ] Sau nạp, tổng số dòng nạp khớp tổng dòng chấp nhận và đã ký của lượt kiểm
- [ ] Có bước chặn bật phân bổ thật khi lịch sử chưa nạp xong, ghi trong danh mục kiểm tra phát hành
### Chỗ hở
- GAP-94 chưa có thiết kế nạp và nơi ghi chữ ký của nghiệp vụ

## E13-I06 · Làm rõ với khách: schema ERP, thông số relay và nguồn di trú
- Loại: issue
- Cha: E13
- Vai: BrSE
- Nhãn: type:issue, type:clarification
- Spec: IF-ERP-01, IF-MAIL-01, DR-MIG-01, ADR-008
- Giai đoạn: 1
### Mô tả
Các câu của bộ phận CNTT và chủ dữ liệu của khách đang chặn việc tích hợp và di trú, hỏi dạng câu đóng có phương án mặc định: (1) schema cột và tên file thật của ba loại file ERP, và sự kiện nào sinh file ra; (2) thông số mail relay (máy chủ, xác thực, giới hạn gửi) và nhà cung cấp bản đồ (phần bản đồ do E08 theo dõi); (3) schema cột của sáu nguồn di trú, có cột cá nhân hay không, thời hạn lưu kết quả kiểm và dữ liệu thô; (4) ai nạp dòng đã chấp nhận vào hệ thống và nơi nghiệp vụ ký các dòng chờ xem; (5) lượt kiểm được tạo bằng đường nào. Không tự đặt giá trị thay khách.
### Định nghĩa xong
- [ ] Danh sách câu hỏi đóng gửi đúng người trả lời (CNTT khách, chủ dữ liệu), mỗi câu có phương án mặc định và nêu issue bị ảnh hưởng
- [ ] Câu trả lời ghi vào sổ quyết định; schema trả lời được thêm thành bộ phân tích mới chứ không sửa bộ cũ
- [ ] Các issue E13-I02-S01, E13-I02-S02, E13-I03, E13-I05-S01, E13-I05-S02 được cập nhật theo câu trả lời hoặc ghi rõ giữ giả định hiện tại
### Chỗ hở
- GAP-91 schema ERP
- GAP-94 đường nạp và ký dữ liệu di trú
- GAP-97 thông số relay

## E13-I07 · Diễn tập di trú hai lần và nghiệm thu di trú
- Loại: issue
- Cha: E13
- Vai: QA-1
- Nhãn: type:issue, constraint:rice-beef-trace
- Spec: ACC-MIG-01, DR-MIG-01
- Phụ thuộc: E13-I05-S03, E13-I05-S04
- Giai đoạn: 1
### Mô tả
Cổng nghiệm thu di trú yêu cầu hai lần diễn tập, có lineage, có từ chối, và nghiệp vụ ký. Lập kế hoạch và chạy hai lần diễn tập trên dữ liệu đã che, mỗi lần đối chiếu số liệu bằng đúng đẳng thức tổng nguồn, ghi chênh lệch giữa hai lần, và thu bằng chứng cho nghiệp vụ ký. Dùng ca bẩn của thiết kế (mã bò 9 chữ số, trùng khoá, thiếu hạn) làm bộ kiểm cố định.
### Định nghĩa xong
- [ ] Hai biên bản diễn tập, mỗi biên bản có số dòng theo bốn loại, tổng khớp, danh sách ngoại lệ đính kèm
- [ ] Chênh lệch giữa lần một và lần hai được giải thích từng dòng (dữ liệu nguồn đổi hay lỗi công cụ)
- [ ] Mọi dòng chờ nghiệp vụ xem có người ký hoặc có quyết định để lại, ghi vào sổ
- [ ] Dữ liệu dùng để diễn tập đã che, không chứa dữ liệu cá nhân thật
### Chỗ hở
- GAP-94 chưa có nơi ghi chữ ký nghiệp vụ trong hệ thống

## E13-I08 · Bộ kịch bản nghiệm thu nghiệp vụ
- Loại: issue
- Cha: E13
- Vai: QA-1
- Nhãn: type:issue, constraint:haccp-temp, constraint:rice-beef-trace
- Spec: ACC-FUN-01, ACC-TEMP-01, ACC-DATE-01, ACC-SCH-01, ACC-TRC-01
- Phụ thuộc: E09-I04-S03, E09-I08-S01, E09-I08-S02
- Giai đoạn: 1
### Mô tả
Năm cổng nghiệm thu nghiệp vụ không có tài liệu thiết kế riêng nên bộ kịch bản được dựng từ các bảng ca biên đã có ở thiết kế chi tiết: chuỗi nhận – lô – phân bổ – chọn – xuất xe – POD không đi tắt, nhiệt độ (biên, vượt ngưỡng, thiếu số đo, review trễ), hạn và đảo ngày, lịch tuyến và giờ làm của tài xế (kịch bản do E09 cung cấp, issue này chỉ gom và chạy), truy xuất và đối chiếu số lượng thu hồi. Mỗi kịch bản trỏ về kịch bản chấp nhận của màn và bản ghi kết quả mong đợi, để chủ nghiệp vụ của khách chạy lại và ký.
### Định nghĩa xong
- [ ] Mỗi trong năm cổng có danh sách kịch bản đánh số, dữ liệu vào, kết quả mong đợi dạng bản ghi hay log, trỏ tới ca biên hoặc kịch bản chấp nhận nguồn
- [ ] Mỗi kịch bản có chủ chạy và người ký ở phía khách theo chủ sở hữu của cổng
- [ ] Bộ dữ liệu cho kịch bản được tạo bằng tập lệnh, che hết thông tin thật
- [ ] Một lần chạy thử toàn bộ trên môi trường test có bảng kết quả, ca không chạy được kèm lý do
- [ ] Kịch bản của cổng lịch tài xế lấy nguyên từ bảng ca biên và kịch bản sự kiện bất ngờ của E09 (E09-I04-S03, E09-I08-S01, E09-I08-S02), không viết lại ở đây
- [ ] Ca nào chưa có thiết kế để dựng kết quả mong đợi được ghi thành chỗ hở, không tự đặt kết quả

## E13-I09 · Ma trận truy vết kiểm thử và nghiệm thu phi chức năng
- Loại: issue
- Cha: E13
- Nhãn: type:issue
- Spec: OPS-TEST-01, ACC-NFR-01
- Phụ thuộc: E01-I10
- Giai đoạn: 1
### Mô tả
Quyết định go-live dựa trên bằng chứng kiểm thử, nên cần một ma trận cho thấy mỗi bài kiểm thử gắn với yêu cầu nào, chạy trên bộ dữ liệu và môi trường nào, ra kết quả gì và dính lỗi nào, cùng cổng phi chức năng: hiệu năng với 80 người dùng đồng thời, truy xuất ba năm, khôi phục sau sự cố, tiếp cận, quan sát. Công cụ tải, môi trường và bản sao lưu do E01 dựng; issue này dùng chúng và ghi bằng chứng. Bốn luồng việc làm độc lập nên chia thành sub-issue: ma trận cùng báo cáo tiếp cận và theo dõi mục trượt ngưỡng, đo hiệu năng, diễn tập khôi phục, diễn tập quan sát sự cố.

## E13-I09-S01 · Ma trận truy vết, báo cáo tiếp cận và theo dõi mục trượt ngưỡng (QA)
- Loại: sub-issue
- Cha: E13-I09
- Vai: QA-1
- Nhãn: type:sub-issue
- Spec: OPS-TEST-01, ACC-NFR-01, NFR-ACC-01
- Phụ thuộc: E13-I09-S02, E13-I09-S03, E13-I09-S04
- Giai đoạn: 1
### Mô tả
Dựng ma trận bằng tập lệnh đọc nhãn gắn trên các bài kiểm thử: mỗi bài trỏ về yêu cầu nó kiểm, bộ dữ liệu và môi trường nó chạy, kết quả thu được và lỗi liên quan; yêu cầu nào chưa có bài kiểm thử bị liệt kê. Chạy kiểm tra tiếp cận trên các màn đã dựng và lập báo cáo. Gom kết quả của ba luồng còn lại, mục nào trượt ngưỡng thì mở hành động khắc phục có người chịu trách nhiệm và hạn. Ngưỡng lấy thẳng từ yêu cầu vì không có đơn vị thiết kế riêng.
### Định nghĩa xong
- [ ] Ma trận sinh bằng tập lệnh từ nhãn của bài kiểm thử, không nhập tay; chạy lại cho cùng kết quả
- [ ] Tập lệnh liệt kê yêu cầu chưa có bài kiểm thử và có một ca thử cố ý để chứng minh nó bắt được
- [ ] Báo cáo tiếp cận cho các màn đã dựng, mỗi lỗi kèm màn và mức nghiêm trọng
- [ ] Bảng theo dõi gom kết quả hiệu năng, khôi phục, quan sát; mục nào trượt ngưỡng có hành động khắc phục, người chịu trách nhiệm và hạn
### Chỗ hở
- GAP-95 hai cổng này không có đơn vị thiết kế riêng nên ngưỡng lấy trực tiếp từ yêu cầu

## E13-I09-S02 · Cổng hiệu năng: 80 người dùng đồng thời và truy xuất ba năm (BE)
- Loại: sub-issue
- Cha: E13-I09
- Vai: BE-1
- Nhãn: type:sub-issue, constraint:rice-beef-trace
- Spec: ACC-NFR-01, NFR-PERF-01, NFR-PERF-02
- Phụ thuộc: E01-I10
- Giai đoạn: 1
### Mô tả
Chạy kịch bản tải với 80 người dùng đồng thời trên bộ dữ liệu giống thật (sinh bằng bộ sinh của E01-I10, đã che dữ liệu) và đo thời gian đọc, ghi ở p95; đo riêng thời gian truy xuất một lô trong ba năm. Ghi cấu hình môi trường và bộ dữ liệu cùng kết quả; phát hiện truy vấn chậm nhất khi trượt ngưỡng.
### Định nghĩa xong
- [ ] Báo cáo p95 đọc và ghi với 80 người dùng đồng thời, kèm cấu hình môi trường và bộ dữ liệu
- [ ] Báo cáo thời gian truy xuất ba năm trên bộ dữ liệu cỡ yêu cầu, lặp ít nhất ba lần
- [ ] Mục trượt ngưỡng nêu truy vấn hoặc bước chậm nhất kèm đề xuất xử lý
- [ ] Kịch bản và tập lệnh chạy lại được từ repo kiểm thử, không chứa dữ liệu thật
### Chỗ hở
- GAP-96 bộ dữ liệu cỡ ba năm cho phép đo hiệu năng chưa có đặc tả nơi sinh ra

## E13-I09-S03 · Diễn tập khôi phục sau sự cố (BE)
- Loại: sub-issue
- Cha: E13-I09
- Vai: BE-2
- Nhãn: type:sub-issue
- Spec: ACC-NFR-01, NFR-BCP-01
- Phụ thuộc: E01-I10
- Giai đoạn: 1
### Mô tả
Diễn tập một lần khôi phục từ bản sao lưu trên môi trường gần thật theo mục tiêu thời gian và điểm khôi phục của yêu cầu, ghi mốc bắt đầu, mốc dịch vụ hoạt động lại và dữ liệu mất nếu có. Bản sao lưu và quy trình khôi phục do E01 dựng; issue này chạy và ghi bằng chứng.
### Định nghĩa xong
- [ ] Biên bản diễn tập có mốc thời gian từng bước, thời gian khôi phục và điểm khôi phục đo được
- [ ] Sau khôi phục có kiểm đối chiếu số liệu mẫu với trước sự cố
- [ ] Nếu trượt mục tiêu thì có hành động khắc phục để S01 theo dõi
- [ ] Biên bản không chứa dữ liệu thật hay bí mật

## E13-I09-S04 · Diễn tập quan sát: sự cố có cảnh báo hành động được (BE)
- Loại: sub-issue
- Cha: E13-I09
- Vai: BE-2
- Nhãn: type:sub-issue
- Spec: ACC-NFR-01, NFR-OPS-01
- Phụ thuộc: E01-I10
- Giai đoạn: 1
### Mô tả
Gây một sự cố mẫu trên môi trường kiểm thử (ví dụ adapter hết lượt thử) rồi kiểm cảnh báo có tới người xử lý, kèm hướng dẫn xử lý, và tìm được toàn bộ dòng log, vết liên quan nhờ mã tương quan. Cảnh báo và bảng quan sát do E01 dựng; issue này chạy kịch bản và ghi bằng chứng.
### Định nghĩa xong
- [ ] Sự cố mẫu sinh cảnh báo gửi đúng người xử lý trong thời gian ghi nhận được
- [ ] Từ cảnh báo tới log và vết bằng mã tương quan, không cần đoán
- [ ] Hướng dẫn xử lý gắn với cảnh báo được kiểm bằng cách làm theo từng bước
- [ ] Mục trượt có hành động khắc phục để S01 theo dõi

## E13-I10 · Go-live, rollback và hypercare
- Loại: issue
- Cha: E13
- Vai: PM
- Nhãn: type:issue
- Spec: ACC-GO-01, OPS-SUP-01
- Phụ thuộc: E13-I07, E13-I08, E13-I09
- Giai đoạn: 1
### Mô tả
Chuẩn bị cổng go-live: danh mục kiểm tra phát hành (có dòng chặn cho các khoá cấu hình còn trống), kịch bản rollback đã tập, biên bản go-live cho sáu chủ ký, rủi ro còn lại được liệt kê; và kế hoạch hypercare bốn tuần với ma trận mức độ nghiêm trọng, đường leo thang, kênh liên lạc, mẫu báo cáo SLA. Gom kết quả từ các cổng nghiệm thu phía trước.
### Định nghĩa xong
- [ ] Danh mục kiểm tra phát hành có từng mục kèm người chịu trách nhiệm, bằng chứng, và dòng chặn cho khoá cấu hình còn trống
- [ ] Kịch bản rollback được tập một lần trên môi trường gần thật, có thời gian ghi lại
- [ ] Mẫu biên bản go-live có chỗ cho sáu chữ ký và danh sách rủi ro còn lại
- [ ] Kế hoạch hypercare bốn tuần: ma trận mức độ, đường leo thang, kênh, lịch báo cáo SLA hằng tuần
### Chỗ hở
- GAP-95 hai cổng này không có đơn vị thiết kế riêng nên nội dung lấy từ yêu cầu vận hành

## E13-I11 · Đào tạo người dùng bằng tiếng Nhật cho từng nhóm vai trò
- Loại: issue
- Cha: E13
- Vai: BrSE
- Nhãn: type:issue
- Spec: OPS-TRN-01
- Giai đoạn: 1
### Mô tả
Đào tạo riêng cho từng nhóm vai trò trước go-live bằng tiếng Nhật, không dùng buổi chung: chương trình theo từng nhóm (bảy nhóm vai trò theo yêu cầu), thực hành trên môi trường thử, bài kiểm tra cuối buổi, danh sách tham dự, lịch đào tạo lại. Nội dung bám các màn đã dựng và dữ liệu đã che. Nhãn tiếng Nhật đã duyệt bởi chủ dự án nhưng chưa người bản ngữ xem nên người bản ngữ phía công ty cần rà tài liệu trước khi giao khách.
### Định nghĩa xong
- [ ] Mỗi nhóm trong bảy nhóm vai trò có chương trình riêng, danh sách màn học và bài thực hành
- [ ] Môi trường thử và dữ liệu đã che dùng cho thực hành được chuẩn bị và kiểm
- [ ] Mỗi nhóm có bài kiểm tra cuối buổi với đáp án và ngưỡng đạt
- [ ] Danh sách tham dự và lịch đào tạo lại cho người vắng
- [ ] Tài liệu tiếng Nhật được người bản ngữ rà trước khi giao khách
### Chỗ hở
- GAP-95 không có đơn vị thiết kế riêng, nội dung lấy từ yêu cầu vận hành
