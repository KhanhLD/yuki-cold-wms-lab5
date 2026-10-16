# E01 · Nền tảng & hạ tầng
<!-- lab5-epic: E01 -->

## E01 · Nền tảng & hạ tầng
- Loại: epic
- Nhãn: type:epic
- Spec: ADR-001, ADR-002, ADR-003, ADR-005, ADR-009, ADR-012, PLATFORM_DB, OPS-ENV-01, NFR-PERF-01
### Mô tả
Toàn bộ phần dùng chung mà mọi epic nghiệp vụ đứng lên trên: khung ứng dụng một khối chia mười module có kiểm ranh giới tự động, hạ tầng bốn môi trường bằng mã tại AWS Tokyo, đường phát hành, quan sát, sao lưu và khôi phục, thư viện API chung, đồng hồ nghiệp vụ, kho cấu hình có phiên bản, sổ audit chỉ ghi thêm, lưu giữ và xoá có duyệt, đường tải tệp, và khung giao diện React dùng chung. Epic không chứa luật nghiệp vụ nào của kho hay vận tải; mọi giá trị nghiệp vụ lấy từ kho cấu hình do epic này dựng.

## E01-I01 · Khung ứng dụng và ranh giới mười module
- Loại: issue
- Cha: E01
- Nhãn: type:issue
- Spec: ADR-001, ADR-002, NFR-PERF-01
### Mô tả
Dựng một ứng dụng Spring Boot duy nhất chia mười module Spring Modulith theo đồ thị phụ thuộc một chiều của tài liệu kiến trúc, rồi khoá ranh giới bằng kiểm tra tự động. Hai việc làm song song được: dựng khung (BE-1) và viết các luật kiểm (BE-2). Prototype chỉ là mẫu luật nghiệp vụ, không mang mã sang.

## E01-I01-S01 · Dựng khung mười module và named interface
- Loại: sub-issue
- Cha: E01-I01
- Vai: BE-1
- Nhãn: type:sub-issue
- Spec: ADR-001, ADR-002, ADR-008
### Mô tả
Tạo kho mã và bản dựng, mỗi module một package gốc kèm named interface; module nền tảng nằm đáy đồ thị; ánh xạ module sang tiền tố đường dẫn API đúng bảng kiến trúc (kể cả hai cạnh phụ thuộc đã bổ sung sau). Cũng dựng phần nền của cơ chế sự kiện: bảng phát hành sự kiện (outbox) của module nền tảng, cấu hình đăng ký người nghe và danh mục sự kiện một nơi để mọi module phát sự kiện cùng giao dịch; phần người nghe, thử lại và việc nền đứng trên đó thuộc epic tích hợp.
### Định nghĩa xong
- [ ] Mười module có package gốc và named interface, đồ thị phụ thuộc khớp bảng của tài liệu kiến trúc
- [ ] Mỗi module có tiền tố đường dẫn riêng dưới phiên bản v1 và một điểm kiểm tra sức khoẻ trả 200
- [ ] Ứng dụng khởi động cục bộ bằng một lệnh, tài liệu hướng dẫn nhà phát triển cập nhật
- [ ] Sơ đồ module sinh từ mã được đặt cạnh tài liệu kiến trúc để đối chiếu khi review
- [ ] Bảng phát hành sự kiện và danh mục sự kiện được dựng; một sự kiện mẫu ghi cùng giao dịch với dữ liệu, rollback thì sự kiện cũng không còn, và module nghiệp vụ đăng ký thêm sự kiện của mình mà không sửa lõi

## E01-I01-S02 · Luật kiểm ranh giới module và cấm lấy giờ hệ thống trực tiếp
- Loại: sub-issue
- Cha: E01-I01
- Vai: BE-2
- Nhãn: type:sub-issue
- Spec: ADR-002, ADR-005, NFR-PERF-01
- Phụ thuộc: E01-I01-S01
### Mô tả
Viết các kiểm tra chạy mỗi lần build để ranh giới không mục dần: phụ thuộc vòng, gọi vào package nội bộ, phụ thuộc ngoài bảng cho phép, và các luật mà Modulith không tự kiểm.
### Định nghĩa xong
- [ ] Kiểm tra cấu trúc module chạy ở mọi build; một ca thử cố tình vi phạm làm build đỏ
- [ ] Luật bổ sung: lớp báo cáo không gọi lệnh ghi, entity của module này không xuất hiện ở chữ ký API module khác, không có SQL tới bảng module khác
- [ ] Luật cấm gọi trực tiếp đồng hồ hệ thống trong package nghiệp vụ (chỉ đồng hồ nghiệp vụ và tầng hạ tầng được gọi)
- [ ] Có kiểm thử cho từng luật bằng mã vi phạm mẫu, và các luật được nối vào pipeline ở E01-I05
### Chỗ hở
- GAP-18 Chưa epic nào sở hữu bộ ca kiểm tham chiếu rút từ prototype

## E01-I02 · Terraform nền tảng: mạng, khoá, bí mật, quyền
- Loại: issue
- Cha: E01
- Vai: BE-1
- Nhãn: type:issue
- Spec: ADR-009, OPS-ENV-01, NFR-SEC-03
### Mô tả
Mô tả bằng Terraform lớp nền chung của bốn môi trường (dev, test, UAT, prod) tại AWS Tokyo: mạng riêng cho mỗi môi trường, mạng con kín cho tính toán và CSDL, khoá mã hoá do khách hàng quản lý, kho bí mật, quyền truy cập cơ sở, vận hành qua phiên SSM có ghi nhật ký. Một bộ module dùng chung, khác nhau theo biến môi trường; trạng thái tách và khoá theo môi trường; mọi thay đổi qua pull request. Mô hình tài khoản AWS (mỗi môi trường một tài khoản) mới là đề xuất, nên module nhận cấu trúc tài khoản làm tham số.
### Định nghĩa xong
- [ ] Bộ module dùng chung với biến theo môi trường; plan và apply chạy sạch cho dev, các môi trường còn lại chỉ khác biến
- [ ] Mạng riêng, mạng con kín, khoá KMS và kho bí mật được dựng; không có bí mật nào nằm trong mã hay biến thường
- [ ] Truy cập vận hành qua SSM có ghi nhật ký, không có cổng SSH nào mở ra ngoài
- [ ] Trạng thái Terraform tách và khoá theo từng môi trường; thay đổi hạ tầng chỉ vào qua pull request có người duyệt
- [ ] Tài liệu triển khai ghi lựa chọn tài khoản hiện dùng và chỗ nào đổi khi khách chốt mô hình
### Chỗ hở
- GAP-09 Mô hình tài khoản AWS và phạm vi lưu giữ còn chờ khách

## E01-I03 · Terraform tính toán và CSDL, vai trò CSDL
- Loại: issue
- Cha: E01
- Vai: BE-2
- Nhãn: type:issue
- Spec: ADR-009, ADR-003, NFR-AVL-01, NFR-BCP-01
- Phụ thuộc: E01-I02
### Mô tả
Dựng lớp chạy ứng dụng và dữ liệu: ECS Fargate với ít nhất hai task trên hai AZ sau ALB ở prod, Aurora PostgreSQL Multi-AZ qua RDS Proxy kèm reader cho lớp báo cáo, sao lưu liên tục theo thời điểm; dev dùng cấu hình hạ quy mô ngoài giờ. Đồng thời tạo ba vai trò CSDL theo luật sổ chỉ ghi thêm: vai trò ứng dụng (không DDL, chỉ INSERT và SELECT trên bảng chỉ ghi thêm), vai trò chạy migration, vai trò purge (vai trò duy nhất được xoá).
### Định nghĩa xong
- [ ] Dịch vụ ECS chạy từ hai task trở lên trên hai AZ ở prod, kiểm tra sức khoẻ gắn với ALB; số task và cấu hình là biến theo môi trường
- [ ] Aurora có writer, reader, RDS Proxy và khôi phục theo thời điểm đã bật; dev dùng cấu hình hạ quy mô
- [ ] Ba vai trò CSDL có quyền đúng; ca thử: tài khoản ứng dụng UPDATE hoặc DELETE bảng chỉ ghi thêm bị từ chối
- [ ] Chuỗi kết nối và mật khẩu lấy từ kho bí mật lúc chạy, không có trong image

## E01-I04 · Terraform giao diện, tệp và đường nạp file
- Loại: issue
- Cha: E01
- Vai: BE-1
- Nhãn: type:issue
- Spec: ADR-009, NFR-SEC-03, DR-RET-01
- Phụ thuộc: E01-I02
### Mô tả
Dựng phần biên: SPA tĩnh trên S3 sau CloudFront và WAF cùng tên miền với API, bucket tệp mã hoá bằng KMS cho ảnh và bằng chứng (truy cập bằng URL ký ngắn hạn), bucket archive bật khoá đối tượng kèm vòng đời theo nhóm hồ sơ, và đường nạp file gồm SFTP quản lý (chỉ bật ở prod), S3 landing, EventBridge. Phần xử lý nội dung file thuộc epic tích hợp; epic này chỉ giao hạ tầng và điểm nối.
### Định nghĩa xong
- [ ] SPA phục vụ qua CloudFront, API qua cùng tên miền, không bật CORS rộng
- [ ] Bucket tệp mã hoá KMS và chỉ truy cập bằng URL ký ngắn hạn; thử truy cập công khai bị từ chối
- [ ] Bucket archive bật khoá đối tượng, vòng đời cấu hình theo nhóm hồ sơ lưu giữ
- [ ] SFTP chỉ tồn tại ở prod; dev, test, UAT dùng điểm nạp giả lập; S3 landing phát sự kiện tới EventBridge
- [ ] Tài liệu hạ tầng ghi tên các điểm nối để epic tích hợp dùng
### Chỗ hở
- GAP-09 Thời hạn lưu giữ của nhiều nhóm bản ghi chưa có nguồn

## E01-I05 · Pipeline CI: build, kiểm module, quét lỗ hổng, đóng gói
- Loại: issue
- Cha: E01
- Vai: BE-2
- Nhãn: type:issue
- Spec: ADR-009, ADR-002, OPS-ENV-01
- Phụ thuộc: E01-I01-S02
### Mô tả
Mỗi pull request chạy build, kiểm ranh giới module, kiểm thử, quét lỗ hổng cộng sinh SBOM, và kiểm migration theo kiểu mở rộng rồi thu gọn (bản N chỉ thêm, xoá hoặc đổi tên ở bản N+1). Kết quả xanh mới được hợp nhất; từ nhánh chính đóng gói image có checksum đẩy lên ECR.
### Định nghĩa xong
- [ ] Pipeline chạy trên mỗi pull request theo đúng thứ tự các bước; bước đỏ chặn hợp nhất
- [ ] Lỗ hổng mức cao chưa xử lý làm pipeline đỏ; SBOM được lưu kèm artifact
- [ ] Kiểm migration báo đỏ khi xoá hoặc đổi tên cột trong cùng bản phát hành với mã còn dùng cột đó (có ca thử)
- [ ] Image có checksum, chỉ đẩy lên ECR từ nhánh chính

## E01-I06 · Pipeline CD: cổng duyệt, blue/green, rollback, bản ghi phát hành
- Loại: issue
- Cha: E01
- Vai: BE-1
- Nhãn: type:issue
- Spec: ADR-009, OPS-ENV-01, NFR-AVL-01
- Phụ thuộc: E01-I03, E01-I05
### Mô tả
Chuỗi triển khai dev, test, UAT, prod; UAT và prod cần người duyệt. Prod dùng blue/green qua CodeDeploy với ALB draining để phát hành trong giờ dịch vụ không gián đoạn; kiểm sức khoẻ không đạt thì quay về bản cũ. Mỗi bản phát hành sinh một bản ghi gồm checksum image, chênh lệch cấu hình (kể cả thay đổi khoá cấu hình), người duyệt, mốc sao lưu và lệnh rollback.
### Định nghĩa xong
- [ ] Chuỗi triển khai chạy đủ bốn môi trường; UAT và prod dừng chờ người duyệt, người duyệt được ghi lại
- [ ] Prod chuyển tải theo blue/green; diễn tập một lần kiểm sức khoẻ thất bại và quay về bản cũ thành công
- [ ] Bản ghi phát hành lưu đủ checksum, chênh lệch cấu hình, người duyệt, mốc sao lưu, lệnh rollback
- [ ] Phát hành ở test trong lúc có tải nền không làm rớt request (đo bằng kịch bản của E01-I10)

## E01-I07 · Quan sát và khung log có correlation ID
- Loại: issue
- Cha: E01
- Vai: BE-1
- Nhãn: type:issue
- Spec: NFR-OPS-01, ADR-009
- Phụ thuộc: E01-I01-S01
### Mô tả
Dựng quan sát bằng CloudWatch và OpenTelemetry: metrics, trace theo module, log có cấu trúc mà mọi dòng mang correlation ID đi xuyên request, job và sự kiện. Log không chứa token, chữ ký hay ảnh POD gốc. Cảnh báo cho kịch bản sự cố nghiêm trọng đi kèm runbook. Quy ước log hiện chỉ là bản nháp còn nhiều câu hỏi, nên issue này chốt bản dùng được về mặt kỹ thuật (định dạng, múi giờ, tên category, danh sách điều cấm ghi) và ghi thành giả định.
### Định nghĩa xong
- [ ] Log có cấu trúc, mọi dòng mang correlation ID; phản hồi lỗi 500 trả cùng ID để tra ngược
- [ ] Bộ lọc che dữ liệu nhạy cảm; kiểm thử tự động quét log không thấy token, chữ ký, ảnh gốc
- [ ] Metrics và trace theo từng module, có bảng theo dõi cơ bản
- [ ] Có cảnh báo hành động được cho từng kịch bản sự cố nghiêm trọng và runbook; diễn tập một ca
- [ ] Tài liệu quy ước log cập nhật thành bản dùng được, ghi rõ phần là giả định kỹ thuật
### Chỗ hở
- GAP-07 Quy ước log còn là bản nháp, chờ xác nhận

## E01-I08 · Sao lưu, khôi phục và diễn tập hằng quý
- Loại: issue
- Cha: E01
- Vai: BE-2
- Nhãn: type:issue
- Spec: NFR-BCP-01, ADR-009
- Phụ thuộc: E01-I03
### Mô tả
Biến mục tiêu khôi phục thành việc kiểm được: khôi phục theo thời điểm, chuyển dự phòng trong vùng, và quy trình diễn tập mỗi quý có bằng chứng và hành động khắc phục khi trượt. Phương án khi mất cả vùng Tokyo chưa được khách xác nhận nên chỉ ghi rõ là chưa chốt, không dựng.
### Định nghĩa xong
- [ ] Khôi phục về một thời điểm chạy được trên môi trường test và có số đo thời gian
- [ ] Diễn tập chuyển dự phòng một AZ hoặc một instance, so thời gian với mục tiêu RTO và RPO
- [ ] Quy trình diễn tập hằng quý, mẫu biên bản bằng chứng và mục hành động khắc phục khi trượt mục tiêu
- [ ] Tài liệu ghi rõ phương án mất cả vùng chưa chốt và phụ thuộc câu trả lời của E01-I20

## E01-I09 · Môi trường dev, test, UAT: che dữ liệu và đặt lại dữ liệu
- Loại: issue
- Cha: E01
- Vai: BE-1
- Nhãn: type:issue
- Spec: OPS-ENV-01, ADR-005, ADR-009
- Phụ thuộc: E01-I02, E01-I03
### Mô tả
Tách bốn môi trường đúng nghĩa: dữ liệu thật chỉ ở prod; test và UAT dùng bộ dữ liệu đã che với khối lượng đủ đo tải; có công cụ đặt lại dữ liệu chỉ chạy được ngoài prod (cần vì ngày nghiệp vụ giả lập chỉ tiến); khoá cho phép ngày giả lập cố định tắt ở prod và Terraform không cho ghi đè; dev có IdP thử và điểm cuối giả lập cho đối tác.
### Định nghĩa xong
- [ ] Quy trình tạo bộ dữ liệu che cho test và UAT chạy được, kiểm không còn dữ liệu cá nhân thật
- [ ] Công cụ đặt lại dữ liệu từ chối chạy ở prod (có ca thử) và chạy được ở ba môi trường còn lại
- [ ] Khoá cho phép ngày giả lập là tắt ở prod do Terraform giữ, các môi trường khác bật được
- [ ] Dev có IdP thử và điểm cuối giả lập đối tác; hướng dẫn nối cho epic đăng nhập và tích hợp

## E01-I10 · Bộ kiểm tải và đo độ sẵn sàng
- Loại: issue
- Cha: E01
- Vai: QA-1
- Nhãn: type:issue
- Spec: NFR-PERF-01, NFR-AVL-01, JP-18
- Phụ thuộc: E01-I03
### Mô tả
Dựng kịch bản kiểm tải với số người dùng đồng thời theo yêu cầu và dữ liệu có khối lượng giống production, đo p95 đọc và ghi; và phép đo độ sẵn sàng tính đúng cửa sổ giờ dịch vụ, loại trừ bảo trì đã duyệt. Bộ sinh dữ liệu khối lượng lớn (giao hàng, số đo nhiệt) được thiết kế để epic truy xuất dùng lại cho yêu cầu truy xuất ba năm.
### Định nghĩa xong
- [ ] Bộ sinh dữ liệu tạo được khối lượng giao hàng và số đo nhiệt đủ lớn, chạy lại cho kết quả giống nhau
- [ ] Kịch bản tải chạy bằng một lệnh, in p95 đọc và p95 ghi so với mục tiêu
- [ ] Báo cáo độ sẵn sàng đếm đúng theo cửa sổ giờ dịch vụ và loại trừ bảo trì đã duyệt
- [ ] Hướng dẫn đưa kịch bản tải vào bước trước phát hành; ghi rõ không chạy ở mọi pull request

## E01-I11 · Khung API chung: phong bì lỗi, mã YCL, CSRF, header
- Loại: issue
- Cha: E01
- Vai: BE-1
- Nhãn: type:issue
- Spec: NFR-LOC-01, NFR-OPS-01, ADR-002
- Phụ thuộc: E01-I01-S01
### Mô tả
Thư viện dùng chung cho mọi module để các API trả lỗi và đọc yêu cầu thống nhất: phong bì lỗi với mã, câu tiếng Nhật và phần bổ sung theo ngữ cảnh; bảng mã lỗi chung nạp vào ứng dụng làm nguồn duy nhất (gồm mã của mọi module như phân bổ, giao hàng, giao vận, nhiệt độ); ánh xạ ngoại lệ sang mã HTTP; chặn CSRF cho lệnh ghi; kiểm kiểu nội dung; header không cache và correlation ID; lỗi 5xx không lộ chi tiết nội bộ. Mỗi request chỉ trả một ca response.
### Định nghĩa xong
- [ ] Mọi lỗi trả đúng phong bì; mã lạ không có trong bảng mã chung bị phát hiện ở kiểm thử
- [ ] Câu thông báo lấy từ cột tiếng Nhật của bảng mã, không có chuỗi chép tay trong mã
- [ ] Lệnh ghi thiếu hoặc sai CSRF trả 403, sai kiểu nội dung trả 415
- [ ] Mọi phản hồi mang header không cache và correlation ID; lỗi 5xx chỉ trả mã chung kèm ID
- [ ] Có kiểm thử tích hợp mẫu cho các nhóm trạng thái 400, 401, 403, 404, 409, 422, 500
### Chỗ hở
- GAP-11 Quy ước API chưa có giới hạn tần suất và chính sách ngừng phiên bản v1

## E01-I12 · Idempotency, phân trang và khoá lạc quan dùng chung
- Loại: issue
- Cha: E01
- Vai: BE-2
- Nhãn: type:issue
- Spec: PLATFORM_DB, ADR-003, NFR-PERF-01
- Phụ thuộc: E01-I11, E01-I13
### Mô tả
Ba cơ chế mọi module dùng. Khoá idempotency: dấu vân tay SHA-256 của method, đường dẫn và thân chuẩn hoá; gửi lại cùng khoá và cùng nội dung trả đúng phản hồi đã lưu mà không kiểm lại trạng thái; khác nội dung hoặc chưa có kết quả trả 409; chỉ lưu phản hồi thành công; thời gian giữ lấy từ khoá cấu hình và dài hơn hàng đợi offline của tài xế. Phân trang với trần số dòng, vượt trần thì trả lỗi chứ không cắt im lặng. Khoá lạc quan bằng version, lệch thì trả 409.
### Định nghĩa xong
- [ ] Gửi lại cùng khoá và cùng nội dung trả phản hồi đã lưu kèm cờ phát lại, không xử lý lại; khác nội dung trả 409
- [ ] Khoá hết hạn được dọn theo khoá cấu hình thời gian giữ
- [ ] Phân trang theo trang và cỡ trang; vượt trần dòng trả lỗi chuẩn, cỡ trang quá lớn bị từ chối
- [ ] Khoá lạc quan dùng chung: lệch version trả 409, có helper cho module khác và ca thử hai người sửa cùng bản ghi

## E01-I13 · Kho cấu hình có phiên bản
- Loại: issue
- Cha: E01
- Vai: BE-2
- Nhãn: type:issue
- Spec: ADR-012, PLATFORM_DB, ADR-003
- Phụ thuộc: E01-I01-S01
### Mô tả
Nơi duy nhất giữ mọi giả định và mặc định kỹ thuật. Bảng danh mục khoá (schema giá trị, cờ khách giả lập, giả định nhà thầu hoặc giả định kỹ thuật, nguồn, người duyệt, phạm vi môi trường) và bảng phiên bản giá trị (chỉ thêm, có ngày hiệu lực theo ngày nghiệp vụ, không hồi tố). Module đọc qua một named interface theo khoá và ngày nghiệp vụ, đọc một lần trong giao dịch và lưu số phiên bản đã dùng vào kết quả. Thiếu khoá bắt buộc thì ứng dụng không lên. Nạp sẵn mọi khoá của danh mục giả định, gồm khoá của nhập kho, tồn kho, phân bổ, giao hàng, giao vận, nhiệt độ, truy xuất và tích hợp để các epic đó chỉ việc đọc.
### Định nghĩa xong
- [ ] Hai bảng có trigger chặn sửa và xoá phiên bản cũ; ca thử không cho đặt hiệu lực hồi tố
- [ ] Hàm đọc theo khoá và ngày nghiệp vụ trả đúng phiên bản, có ca thử ngày biên
- [ ] Khởi động thiếu khoá bắt buộc thì dừng với mã cấu hình sai; khoá để trống có chủ ý (ví dụ depot lập tuyến ở môi trường thật, danh mục bao bì, hạn nhắc ca thiếu file nhiệt) cũng chặn khởi động ở prod và nằm trong danh sách kiểm phát hành, còn dev, test, UAT có giá trị giả lập
- [ ] Nạp đủ khoá của mọi nhóm trong danh mục kèm cờ và nhãn tiếng Nhật, gồm khoá phân bổ, giao hàng, giao vận, thời gian chờ khoá CSDL và thời gian giữ khoá idempotency; script đối chiếu số khoá nạp với danh mục, không lệch
- [ ] Kích hoạt phiên bản làm mới bộ đệm và phát sự kiện đổi cấu hình qua cơ chế sự kiện chung
### Chỗ hở
- GAP-06 Quy trình đổi khoá cấu hình chưa có API trong danh mục

## E01-I14 · Đồng hồ nghiệp vụ và ngày nghiệp vụ giả lập
- Loại: issue
- Cha: E01
- Vai: BE-1
- Nhãn: type:issue
- Spec: ADR-005, API-SYS-11, JP-05, NFR-LOC-01, PLATFORM_DB
- Phụ thuộc: E01-I11, E01-I13
### Mô tả
Named interface đồng hồ nghiệp vụ trả ngày nghiệp vụ theo lịch Tokyo và thời điểm nghiệp vụ; mọi luật so ngày lấy mốc từ đây (ngày so hạn, ngày hiệu lực của master và của phiên bản luật, kỳ năm, mốc thời gian của bằng chứng giao nhận). Helper kẹp luật không lùi dùng chung: thời điểm mới của một đối tượng không sớm hơn bước trước của chính nó và của bước tiền đề. Mọi bản ghi giao dịch mang hai mốc nghiệp vụ và ghi thật; lưu theo chuẩn ISO 8601, nhiệt độ một chữ số thập phân, hiển thị JST. Prod không có chức năng đổi ngày; ở dev, test, UAT, API đổi ngày chỉ cho đẩy tới và ghi sổ riêng. Hợp đồng API-SYS-11 chưa chi tiết hoá nên được viết trong issue này.
### Định nghĩa xong
- [ ] Đồng hồ nghiệp vụ cung cấp ngày và thời điểm nghiệp vụ; mã nghiệp vụ chỉ lấy mốc từ đây, luật kiểm của E01-I01-S02 bắt buộc điều đó
- [ ] Helper không lùi có kiểm thử: thời điểm mới không sớm hơn mốc trước; khi phải kẹp thì ghi log kèm correlation ID
- [ ] Hợp đồng API-SYS-11 được viết đủ và cập nhật vào danh mục API đã chi tiết hoá
- [ ] API-SYS-11 chạy: đẩy ngày tới được; lùi bị từ chối bằng mã chuẩn; ở prod trả lỗi vì giả lập tắt; mỗi lần đổi có dòng sổ, sự kiện và audit
- [ ] Serializer lưu và trả thời gian dạng ISO 8601 có offset, nhiệt độ quá một chữ số thập phân bị từ chối

## E01-I15 · Quy trình đổi cấu hình và màn cấu hình trên SCR033
- Loại: issue
- Cha: E01
- Nhãn: type:issue
- Spec: SCR033, ADR-012, API-SYS-10, API-SYS-11
- Phụ thuộc: E01-I13, E01-I14, E01-I16
### Mô tả
Phần kho cấu hình mà người dùng chạm tới. SCR033 là màn dùng chung của ba nhóm việc; epic này chỉ chịu trách nhiệm khối cấu hình giả định và điều khiển ngày nghiệp vụ, còn khối ngưỡng nhiệt và khối luật giờ lái thuộc epic của chúng. Wireframe hiện không vẽ hai khối của epic này, nên giao diện được thiết kế bổ sung.
### Chỗ hở
- GAP-06 Không có API đề xuất hay duyệt đổi khoá và wireframe thiếu khối cấu hình

## E01-I15-S01 · API đọc danh sách cấu hình và quy trình đề xuất, duyệt
- Loại: sub-issue
- Cha: E01-I15
- Vai: BE-1
- Nhãn: type:sub-issue
- Spec: API-SYS-10, ADR-012, PLATFORM_DB, NFR-AUD-01
### Mô tả
Chi tiết hoá và chạy API-SYS-10 (danh sách khoá, phiên bản, cờ, nguồn). Thêm cặp API đề xuất và duyệt đổi khoá theo luật của kho cấu hình: người duyệt là vai trò chủ miền của khoá và khác người đề xuất, giá trị phải đúng schema, không hồi tố. Cặp API này mới so với danh mục hiện tại nên cần một dòng khai báo thay đổi thiết kế.
### Định nghĩa xong
- [ ] Hợp đồng API-SYS-10 và cặp API đề xuất, duyệt được viết đủ (tham số, mã lỗi, vai trò) và đưa vào danh mục
- [ ] Danh sách khoá trả đúng phiên bản đang áp dụng, phiên bản tương lai, cờ và nguồn; lọc theo vai trò được xem
- [ ] Duyệt: người đề xuất tự duyệt bị chặn bằng mã chuẩn, sai vai trò chủ miền bị chặn, giá trị sai schema bị từ chối tại trường
- [ ] Mỗi lần kích hoạt phiên bản ghi audit và chênh lệch cấu hình cho bản ghi phát hành của E01-I06

## E01-I15-S02 · Khối cấu hình và điều khiển ngày nghiệp vụ trên SCR033
- Loại: sub-issue
- Cha: E01-I15
- Vai: FE-1
- Nhãn: type:sub-issue
- Spec: SCR033, ADR-012, API-SYS-10, API-SYS-11
- Phụ thuộc: E01-I15-S01, E01-I19, E10-I01-S02
### Mô tả
Khối giao diện hiện bảng khoá cấu hình kèm nguồn, cờ và nhãn giả định chưa được khách xác nhận, biểu mẫu đề xuất và duyệt, và ô đổi ngày nghiệp vụ giả lập chỉ hiện ở môi trường cho phép. Khung trang SCR033 (route, bố cục, các chỗ gắn khối) do E10-I01-S02 dựng; khối này là panel độc lập gắn vào khung đó, có API và biểu mẫu duyệt riêng, để ngưỡng nhiệt và luật giờ lái cũng gắn panel của họ theo cùng cách.
### Định nghĩa xong
- [ ] Bảng khoá hiện nguồn, cờ và đúng ba loại nhãn giả định bằng tiếng Nhật
- [ ] Luồng đề xuất rồi duyệt hiện đúng trạng thái; nút duyệt ẩn với người đề xuất nhưng việc chặn vẫn do server làm
- [ ] Ô đổi ngày nghiệp vụ chỉ hiện ở dev, test, UAT và không tồn tại ở prod
- [ ] Có trạng thái đang tải, lỗi và rỗng; panel gắn vào đúng chỗ của khung SCR033 do E10-I01-S02 dựng mà không sửa khung, và không giả định panel khác dùng chung API hay biểu mẫu duyệt

## E01-I16 · Sổ audit chỉ ghi thêm, bút toán đảo và mẫu quyết một lần
- Loại: issue
- Cha: E01
- Nhãn: type:issue
- Spec: ADR-003, NFR-AUD-01, PLATFORM_DB, ADR-005
- Phụ thuộc: E01-I03, E01-I14
### Mô tả
Hạ tầng dấu vết dùng chung: bảng audit chỉ ghi thêm được bảo vệ bằng trigger và quyền CSDL, cộng hai mẫu mà mọi module nghiệp vụ dùng lại là bút toán đảo (sửa sai bằng một dòng đảo và một dòng mới trong cùng giao dịch) và bảng đề nghị duyệt một lần (đúng một lần cập nhật cột quyết định). Hai nửa làm song song.

## E01-I16-S01 · Bảng audit, bộ ghi audit cùng giao dịch và chặn ở tầng CSDL
- Loại: sub-issue
- Cha: E01-I16
- Vai: BE-1
- Nhãn: type:sub-issue
- Spec: ADR-003, NFR-AUD-01, PLATFORM_DB
### Mô tả
Bảng sổ audit có correlation ID, trigger chặn UPDATE, DELETE, TRUNCATE; vai trò ứng dụng chỉ INSERT và SELECT. Named interface ghi audit chạy cùng giao dịch với lệnh nghiệp vụ để huỷ giao dịch thì dòng audit cũng không còn, và lệnh bắt buộc có audit thì không commit được nếu thiếu dòng.
### Định nghĩa xong
- [ ] Bảng audit và trigger tạo xong; ca thử UPDATE, DELETE, TRUNCATE bằng tài khoản ứng dụng đều bị từ chối
- [ ] Bộ ghi audit chạy cùng giao dịch; rollback nghiệp vụ thì dòng audit cũng không còn
- [ ] Dòng audit mang correlation ID, người thực hiện, hai mốc thời gian nghiệp vụ và thật
- [ ] Khai báo được lệnh bắt buộc có audit, thiếu audit thì giao dịch không commit (có ca thử)

## E01-I16-S02 · Mẫu bút toán đảo và mẫu đề nghị duyệt một lần
- Loại: sub-issue
- Cha: E01-I16
- Vai: BE-2
- Nhãn: type:sub-issue
- Spec: ADR-003, NFR-AUD-01, ADR-005
- Phụ thuộc: E01-I16-S01
### Mô tả
Hai helper và mẫu migration dùng chung. Bút toán đảo: dòng đảo trỏ về dòng gốc, bắt buộc lý do, người thực hiện, hai mốc thời gian, rồi dòng mới mang giá trị đúng; bản gốc không đổi. Đề nghị duyệt một lần: dòng đề nghị chỉ được một lần cập nhật cột quyết định từ trạng thái mở, ràng buộc người quyết khác người đề nghị bằng CHECK, kèm dòng audit cùng giao dịch.
### Định nghĩa xong
- [ ] Helper bút toán đảo có ca thử: đảo một dòng, tạo dòng thay thế cùng giao dịch, bản gốc giữ nguyên
- [ ] Mẫu đề nghị duyệt một lần có trigger mẫu; cập nhật lần hai, cập nhật cột ngoài danh sách, đổi từ trạng thái đã quyết đều bị chặn
- [ ] CHECK người quyết khác người đề nghị có ca thử
- [ ] Tài liệu hướng dẫn module nghiệp vụ dùng hai mẫu, kèm ví dụ migration

## E01-I17 · Lưu giữ, purge có duyệt và legal hold
- Loại: issue
- Cha: E01
- Nhãn: type:issue
- Spec: DR-RET-01, ADR-003, ADR-009, PLATFORM_DB
- Phụ thuộc: E01-I04, E01-I13, E01-I16
### Mô tả
Thực hiện việc lưu giữ theo nhóm hồ sơ: thời hạn là khoá cấu hình, job theo lịch chỉ tạo đề nghị purge liệt phạm vi và số dòng, người duyệt khác người đề nghị, legal hold chặn xoá, xuất archive kèm manifest SHA-256 trước khi xoá. Job theo lịch dùng khung job với khoá phân tán qua CSDL do epic tích hợp dựng. Platform nằm đáy đồ thị nên không được gọi ngược lên module trên; việc đếm và xoá dữ liệu module khác đi qua cổng do module khai báo. Hai nửa làm song song.
### Chỗ hở
- GAP-08 Job purge ở module nền tảng nhưng phải xoá bảng module khác
- GAP-09 Chưa có thời hạn cho sổ audit, thông báo và nhiều bảng khác

## E01-I17-S01 · Thời hạn lưu giữ, đề nghị purge và thực thi xoá có archive
- Loại: sub-issue
- Cha: E01-I17
- Vai: BE-1
- Nhãn: type:sub-issue
- Spec: DR-RET-01, ADR-003, ADR-009
- Phụ thuộc: E13-I01-S02
### Mô tả
Đọc thời hạn theo nhóm hồ sơ từ kho cấu hình; job tạo đề nghị purge chạy trên khung việc nền có khoá phân tán của E13-I01-S02; cổng đóng góp dữ liệu lưu giữ do từng module khai báo để nền tảng không phụ thuộc ngược; duyệt; kiểm lại legal hold ngay trước khi xoá; xuất archive kèm manifest lên bucket khoá đối tượng; xoá bằng vai trò CSDL purge; audit ghi phạm vi, số dòng, checksum và người duyệt.
### Định nghĩa xong
- [ ] Thời hạn từng nhóm hồ sơ đọc từ kho cấu hình, không viết cứng
- [ ] Job tạo đề nghị liệt đúng phạm vi và số dòng; cổng đóng góp có một module mẫu chạy thử
- [ ] Duyệt chặn người đề nghị tự duyệt; xoá chỉ chạy sau duyệt và sau lần kiểm legal hold cuối
- [ ] Archive có manifest SHA-256 được ghi trước khi xoá; manifest lệch thì không xoá
- [ ] Audit của lượt purge ghi đủ phạm vi, số dòng, checksum, người duyệt và chính nó không bị purge

## E01-I17-S02 · Legal hold: đặt, gỡ và chặn purge
- Loại: sub-issue
- Cha: E01-I17
- Vai: BE-2
- Nhãn: type:sub-issue
- Spec: DR-RET-01, PLATFORM_DB, ADR-003
- Phụ thuộc: E01-I16
### Mô tả
Bảng legal hold theo phạm vi (lô, case thu hồi, khách, khoảng thời gian) và API đặt, gỡ có audit. Hợp đồng API chưa có trong danh mục nên được viết trong issue này; vai trò được đặt hay gỡ hold chưa có trong ma trận, nên dùng vai trò quản trị làm giả định cho tới khi có câu trả lời ở E01-I20.
### Định nghĩa xong
- [ ] Bảng legal hold và hợp đồng API đặt, gỡ được viết đủ và chạy; mỗi lần đặt hoặc gỡ có audit
- [ ] Phạm vi chồng nhiều hold vẫn chặn đúng; gỡ một hold không mở khoá bản ghi còn dính hold khác
- [ ] Ca thử: bản ghi dính hold không bị xoá trong bất kỳ lượt purge nào
- [ ] Vai trò đặt, gỡ hold đọc từ cấu hình, mặc định quản trị và ghi rõ là giả định
### Chỗ hở
- GAP-09 Vai trò đặt và gỡ legal hold chưa có trong ma trận vai trò

## E01-I18 · API xin URL ký sẵn để tải tệp đính kèm
- Loại: issue
- Cha: E01
- Vai: BE-1
- Nhãn: type:issue
- Spec: API-SYS-01, ADR-009, NFR-SEC-03, ADR-012
- Phụ thuộc: E01-I04, E01-I11, E01-I13
### Mô tả
Một cửa duy nhất để màn nhận hàng, POD, bằng chứng tuyến và case sai lệch xin URL tải ảnh hoặc tài liệu: client khai mục đích, kiểu, kích thước và SHA-256, server trả URL PUT ngắn hạn lên S3 mã hoá. Giới hạn kiểu, cỡ và thời hạn URL theo mục đích lấy từ khoá cấu hình. Không có API nào nhận nhị phân qua ứng dụng. Bốn mục đích hiện có (ảnh nhận hàng, bằng chứng giao nhận POD, bằng chứng tuyến, tệp của case sai lệch) giữ nguyên; bằng chứng của phiên bản luật giờ lái dùng chung mục đích bằng chứng tuyến, vì cùng nhóm vai trò vận tải nên không phải sửa hợp đồng. Issue cũng cung cấp hàm kiểm tệp đúng đối tượng và đúng hash cho lệnh nghiệp vụ của module khác, loại đối tượng sở hữu do module gọi khai báo; các màn chỉ gọi API này, không dựng lại.
### Định nghĩa xong
- [ ] Endpoint theo hợp đồng: kiểm vai trò theo mục đích, kích thước, kiểu, hash; trả URL ngắn hạn theo khoá cấu hình
- [ ] Sai kích thước trả 413, sai kiểu trả 415 bằng mã chuẩn; không tồn tại đường nhận nhị phân qua ứng dụng
- [ ] Hàm dùng chung kiểm tệp thuộc đúng đối tượng và hash khớp, nhận loại đối tượng sở hữu do module gọi khai báo (đơn, dòng nhận, chuyến, case, phiên bản luật), có tài liệu cho module khác gọi
- [ ] Ca thử với từng mục đích có vai trò khác nhau, kể cả gửi lại từ hàng đợi POD offline cùng khoá idempotency và mỗi tệp chỉ dùng cho một bản ghi
- [ ] Ba chính sách còn hở (kiểm nội dung thật, dọn tệp mồ côi, giới hạn tần suất) được chốt ở mức kỹ thuật và ghi thành giả định
- [ ] Danh sách mã lỗi trả về của endpoint được đối chiếu với bảng mã chung và sai lệch được ghi lại
### Chỗ hở
- GAP-10 Bảng mã lỗi ghi hai mã dùng ở endpoint này mà endpoint không phát sinh
- GAP-11 Kiểm nội dung thật, dọn tệp mồ côi và giới hạn tần suất chưa có chính sách

## E01-I19 · Khung SPA dùng chung: bố cục, định tuyến, client API, tiếng Nhật, trợ năng
- Loại: issue
- Cha: E01
- Vai: FE-1
- Nhãn: type:issue
- Spec: NFR-LOC-01, NFR-ACC-01, ADR-001, API-ACS-02
- Phụ thuộc: E01-I11
### Mô tả
Khung React 19 và TypeScript mà mọi màn đứng trong: bố cục khung (thanh trên có vai trò và đăng xuất), định tuyến theo danh sách màn của phiên, client API dùng chung tự gắn CSRF và khoá idempotency do client tạo và giữ nguyên khi gửi lại, ánh xạ phong bì lỗi thành thông báo, từ điển nhãn tiếng Nhật và định dạng giờ JST, nền trợ năng mức AA cho khung. Khung xử lý phản hồi hết phiên bằng cách về màn đăng nhập kèm đường dẫn quay lại và hiện lớp phủ khoá màn hình; hai phần đó do epic đăng nhập dựng. Khung PWA cho tài xế thuộc epic xuất kho, không nằm ở đây.
### Định nghĩa xong
- [ ] Bố cục khung và định tuyến theo danh sách màn của phiên; URL ngoài danh sách vẫn do màn đích chặn, không dựa vào menu
- [ ] Client API tự gắn CSRF và khoá idempotency cho lệnh ghi, gửi lại giữ nguyên khoá, lỗi hiện đúng câu tiếng Nhật theo mã
- [ ] Từ điển nhãn tiếng Nhật và bộ định dạng giờ JST dùng chung, không còn chuỗi chữ cứng trong mã
- [ ] Kiểm tra trợ năng tự động đạt ở khung, bàn phím đi được toàn bộ khung
- [ ] Điểm nối cho lớp phủ khoá màn hình và chuyển hướng hết phiên được để sẵn cho E02-I01-S03

## E01-I20 · Làm rõ với khách: lưu giữ, BCP, tài khoản AWS, ngôn ngữ tài liệu
- Loại: issue
- Cha: E01
- Vai: BrSE
- Nhãn: type:issue, type:clarification
- Spec: DR-RET-01, NFR-BCP-01, ADR-009, PLATFORM_DB
### Mô tả
Gom các câu hỏi khách chặn thiết kế hạ tầng và lưu giữ: thời hạn lưu giữ cho sổ audit, thông báo, bảng giao nhận, archive, đề nghị purge và người dùng; nhóm lưu giữ cho các bảng tồn kho, phân bổ, giao hàng (POD, seal, kiểm trước xuất phát) và cho nhóm bảng vận tải (phiên bản luật giờ lái, bảng ước tính di chuyển) mà thiết kế chưa xếp nhóm; phiên bản luật phải giữ ít nhất bằng thời gian các tuyến còn trỏ tới nó, mức ba năm theo nhóm tuyến là đề xuất đang dùng, chờ khách xác nhận; mốc tính một năm cho dữ liệu cá nhân (cả phần nhận diện khách ở dữ liệu gốc); vai trò được đặt hoặc gỡ legal hold; mô hình tài khoản AWS; có tính mất cả vùng Tokyo trong mục tiêu khôi phục không; ngôn ngữ của tài liệu kỹ thuật bàn giao. Mỗi câu kèm phương án đang dùng và hệ quả nếu khách chọn khác.
### Định nghĩa xong
- [ ] Gói câu hỏi viết bằng tiếng Nhật trang trọng, mỗi câu nêu phương án đang dùng và hệ quả nếu đổi
- [ ] Danh sách bảng chưa có nhóm lưu giữ (tồn kho, phân bổ, giao hàng, phần nhận diện khách, phiên bản luật giờ lái và bảng ước tính di chuyển của vận tải) gom từ các epic nghiệp vụ và đưa vào gói câu hỏi
- [ ] Câu trả lời được ghi vào tài liệu thiết kế kèm nguồn; câu chưa có trả lời được liệt thành giả định đang dùng
- [ ] Các issue phụ thuộc (E01-I02, E01-I04, E01-I08, E01-I17) được cập nhật theo câu trả lời hoặc ghi rõ giả định
### Chỗ hở
- GAP-09 Thời hạn lưu giữ, legal hold và mô hình tài khoản AWS còn mở
- GAP-17 Tài liệu kỹ thuật theo yêu cầu bằng tiếng Nhật, tài liệu thiết kế đang bằng tiếng Việt
