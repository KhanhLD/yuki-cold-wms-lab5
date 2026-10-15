# E12 · Trang chủ & báo cáo
<!-- lab5-epic: E12 -->

## E12 · Trang chủ & báo cáo
- Loại: epic
- Nhãn: type:epic
- Spec: SCR002, SCR024, SCR025, API-HOM-01, API-RPT-01, API-RPT-02, API-RPT-03, API-RPT-04, FR-RPT-01, FR-RPT-02
### Mô tả
Lớp chỉ đọc nằm trên cùng, gồm trang chủ phân theo từng vai trò (việc chờ, thông báo, menu), cổng báo cáo vận hành với bộ lọc và xuất CSV, và màn xuất bằng chứng kiểm toán có manifest. Cả ba đọc dữ liệu của các module khác qua reader và không có đường ghi nghiệp vụ. Hai màn báo cáo mới chỉ có wireframe, nên mỗi issue ở đây gồm cả việc viết hợp đồng API còn thiếu. Phần việc chờ của luồng duyệt giờ lái và báo cáo tuân thủ giờ lái do epic giờ lái xe đăng ký vào cơ chế chung của epic này, không tạo ở đây.

## E12-I01 · Trang chủ theo vai trò: việc chờ, thông báo, menu
- Loại: issue
- Cha: E12
- Nhãn: type:issue
- Spec: SCR002, API-HOM-01, NFR-SEC-02, PLATFORM_DB
- Phụ thuộc: E01-I19, E02-I03
### Mô tả
Điểm vào duy nhất sau đăng nhập của cả chín vai trò. Gồm API trang chủ ở lớp báo cáo và màn SCR002; hai phần làm song song. Số việc chờ chỉ là gợi ý ưu tiên, không phải quyền; lệnh duyệt vẫn kiểm vai trò và người đề nghị ở service. Bộ tám loại việc chờ và vai trò được đếm suy từ prototype, chưa khách xác nhận.
### Chỗ hở
- GAP-15 Bộ loại việc chờ, đánh dấu đã đọc và làm mới định kỳ chưa có nguồn
- GAP-21 Loại việc của luồng giờ lái và báo cáo tuân thủ giờ lái nằm ở epic khác nhưng cần khung của epic này

## E12-I01-S01 · API trang chủ: việc chờ, thông báo, dịch vụ ghi thông báo
- Loại: sub-issue
- Cha: E12-I01
- Vai: BE-1
- Nhãn: type:sub-issue
- Spec: API-HOM-01, PLATFORM_DB, NFR-SEC-02, NFR-PERF-01
### Mô tả
API đọc ở lớp báo cáo trả danh sách việc chờ theo loại kèm số đếm và danh sách thông báo mới nhất. Cơ chế đăng ký loại việc (mã loại, màn xử lý, vai trò được đếm, nguồn đếm) để module khác thêm loại mà không sửa lõi; loại của luồng duyệt giờ lái do epic giờ lái xe đăng ký (vai trò đếm đọc từ khoá vai trò duyệt, nguồn đếm là bảng yêu cầu của luồng đó), và loại cảnh báo phiếu dải nhiệt sắp hết hạn của kho đăng ký được theo cùng cách nếu khách chọn kênh này. Số đếm loại trừ đề nghị do chính người xem tạo. Người duyệt đọc từ kho cấu hình, thiếu khoá thì số đếm của loại đó để rỗng chứ không lỗi. Kèm dịch vụ ghi thông báo trong ứng dụng dùng chung cho module khác.
### Định nghĩa xong
- [ ] Hợp đồng API-HOM-01 và cơ chế đăng ký loại việc được viết rõ; các loại thuộc epic này chạy được, loại của luồng giờ lái do epic giờ lái xe đăng ký sau
- [ ] Số đếm loại trừ đề nghị do chính người xem tạo; vai trò không phụ trách loại nào nhận danh sách rỗng
- [ ] Thiếu hoặc sai khoá người duyệt thì số đếm loại đó rỗng, các loại còn lại vẫn trả, không có lỗi 500
- [ ] Thông báo trả tối đa theo khoá cấu hình, mới nhất trước, đúng người nhận hoặc vai trò, tham số không chứa dữ liệu cá nhân
- [ ] Dịch vụ ghi thông báo dùng chung có kiểm thử; mail đi qua kênh khác, không dùng SMS
- [ ] p95 đọc trên bộ dữ liệu test đạt mục tiêu hiệu năng đọc

## E12-I01-S02 · Màn SCR002: lời chào, việc chờ, thông báo, menu
- Loại: sub-issue
- Cha: E12-I01
- Vai: FE-1
- Nhãn: type:sub-issue
- Spec: SCR002, API-HOM-01, API-ACS-02
- Phụ thuộc: E12-I01-S01
### Mô tả
Lời chào và vai trò, khung việc chờ, khung thông báo, và menu lấy từ danh sách màn của phiên nhóm theo mười hai khu vực nghiệp vụ. Menu chỉ là lớp trải nghiệm: mở thẳng URL của màn ngoài menu vẫn do màn đó chặn. Một bộ đếm lỗi không làm hỏng trang.
### Định nghĩa xong
- [ ] Lời chào hiện tên hiển thị (rỗng thì hiện mã người dùng) và vai trò; người nhiều vai trò thấy hợp các màn và loại việc
- [ ] Menu nhóm theo khu vực, ẩn nhóm rỗng, không hiện mã màn
- [ ] Khung việc chờ hiện dòng và số, hiện dấu gạch khi số đếm rỗng, có trạng thái rỗng; bấm một dòng mở đúng màn xử lý
- [ ] Khung thông báo theo mẫu câu của từng loại, bấm điều hướng theo đối tượng; không có thông báo thì hiện câu rỗng
- [ ] Lỗi API trang chủ vẫn giữ lời chào và menu; hết phiên thì về SCR001

## E12-I02 · Cổng báo cáo hợp nhất (SCR024)
- Loại: issue
- Cha: E12
- Nhãn: type:issue
- Spec: SCR024, API-RPT-01, API-RPT-02, FR-RPT-01
- Phụ thuộc: E12-I06, E01-I19, E02-I03
### Mô tả
Cổng đưa người dùng vào mười hai báo cáo vận hành, lọc theo kỳ, khách, SKU, dải nhiệt và trạng thái, và xuất CSV khớp bộ lọc. Hai API chưa có hợp đồng nên được viết trong issue. Báo cáo nào dùng bộ lọc nào và vai trò nào xem báo cáo nào chưa có nguồn, nên cả hai là dữ liệu cấu hình, giá trị chờ câu trả lời ở E12-I06. Khung báo cáo nhận đăng ký, nên báo cáo mới chỉ cần một dòng đăng ký.
### Chỗ hở
- GAP-13 Ánh xạ báo cáo với vai trò và bộ lọc chưa có nguồn, hành vi nút mở chưa vẽ

## E12-I02-S01 · API cổng báo cáo: danh mục, lọc, CSV, phân quyền
- Loại: sub-issue
- Cha: E12-I02
- Vai: BE-1
- Nhãn: type:sub-issue
- Spec: API-RPT-01, API-RPT-02, FR-RPT-01, NFR-SEC-02
### Mô tả
API danh mục báo cáo với bộ lọc, trả bảng và tổng; API xuất CSV theo đúng bộ lọc đang áp dụng. CSV kế thừa quyền xem, che trường nhạy cảm và thời hạn lưu giữ của dữ liệu nguồn. Truy vấn đi qua view trên reader, không có đường ghi. Quá trần số dòng thì trả lỗi chuẩn.
### Định nghĩa xong
- [ ] Hợp đồng API-RPT-01 và API-RPT-02 viết đủ (tham số, phân trang, trần dòng, mã lỗi, vai trò) và đưa vào nhóm đã chi tiết hoá
- [ ] Khung đăng ký báo cáo: mã, tên, bộ lọc áp dụng, vai trò xem, nguồn view; thêm báo cáo không sửa lõi
- [ ] CSV mã hoá UTF-8, khớp bộ lọc, che trường nhạy cảm, quá trần dòng thì trả lỗi chuẩn
- [ ] Vai trò không được xem báo cáo nhận 403 chuẩn; ánh xạ báo cáo và vai trò đọc từ dữ liệu cấu hình
- [ ] Luật kiểm kiến trúc: lớp báo cáo không có đường ghi và truy vấn dùng reader

## E12-I02-S02 · Màn SCR024: bộ lọc, danh sách báo cáo, xem và xuất CSV
- Loại: sub-issue
- Cha: E12-I02
- Vai: FE-1
- Nhãn: type:sub-issue
- Spec: SCR024, API-RPT-01, API-RPT-02
- Phụ thuộc: E12-I02-S01
### Mô tả
Màn có năm tiêu chí lọc, danh sách mười hai báo cáo với nút mở, nút xuất CSV, và thông báo khi vai trò không được xem. Nút mở dẫn tới màn xem báo cáo (bộ lọc, tổng, bảng, CSV) mà wireframe chưa vẽ, nên màn xem được dựng chung cho mọi báo cáo.
### Định nghĩa xong
- [ ] Năm tiêu chí lọc hoạt động; danh mục khách, SKU, trạng thái lấy từ dữ liệu chính
- [ ] Danh sách mười hai báo cáo, mở được màn xem chung hiện bảng, tổng và bộ lọc áp dụng
- [ ] Nút xuất CSV theo bộ lọc đang áp dụng; hiện thông báo kế thừa quyền và thời hạn lưu
- [ ] Vai trò không được xem báo cáo nhận thông báo từ chối; phân trang theo cỡ trang cấu hình
- [ ] Trạng thái đang tải, rỗng và lỗi; hết phiên về SCR001

## E12-I03 · Nhóm báo cáo 1: tồn kho, hạn dùng, nhập hàng, giao hàng
- Loại: issue
- Cha: E12
- Vai: BE-2
- Nhãn: type:issue
- Spec: FR-RPT-01, SCR024, API-RPT-01
- Phụ thuộc: E12-I02-S01, E12-I06
### Mô tả
Sáu báo cáo của danh mục: tồn theo hạn dùng, hàng gần hạn, hàng cách ly, chênh lệch nhập hàng, kết quả giao hàng, mức đầy đủ của bằng chứng giao nhận. Mỗi báo cáo là một view đọc trên reader kèm định nghĩa cột, công thức và bộ lọc áp dụng. Danh mục không nêu cột hay công thức, nên bản định nghĩa do issue viết và ghi là giả định chờ khách; phân quyền xem đọc từ cấu hình.
### Định nghĩa xong
- [ ] Mỗi báo cáo có tài liệu định nghĩa (cột, công thức, bộ lọc áp dụng) ghi rõ là giả định chờ khách
- [ ] Sáu view chạy qua khung đăng ký; số liệu khớp truy vấn trực tiếp trên bộ dữ liệu mẫu
- [ ] Bộ lọc kỳ, khách, SKU, dải nhiệt, trạng thái áp đúng cho báo cáo có dùng tiêu chí đó
- [ ] CSV của từng báo cáo khớp bộ lọc và che trường nhạy cảm
- [ ] p95 đọc mỗi báo cáo trên bộ dữ liệu có khối lượng của E01-I10 đạt mục tiêu hiệu năng đọc
### Chỗ hở
- GAP-13 Danh mục không nêu cột, công thức và bộ lọc cho từng báo cáo

## E12-I04 · Nhóm báo cáo 2: nhiệt độ, HACCP, thu hồi, lịch sử thay đổi
- Loại: issue
- Cha: E12
- Vai: BE-2
- Nhãn: type:issue
- Spec: FR-RPT-01, SCR024, API-RPT-01
- Phụ thuộc: E12-I02-S01, E12-I06, E02-I05
### Mô tả
Năm báo cáo còn lại thuộc epic này: thiếu bản ghi HACCP, sai lệch nhiệt độ, mức tuân thủ thời hạn xử lý cảnh báo nhiệt, đối chiếu thu hồi, và lịch sử thay đổi dành cho kiểm toán đọc từ sổ audit. Cùng cách làm với nhóm một. Báo cáo tuân thủ giờ lái (báo cáo thứ mười trong danh mục) do epic giờ lái xe cung cấp view và đăng ký vào khung này.
### Định nghĩa xong
- [ ] Mỗi báo cáo có tài liệu định nghĩa (cột, công thức, bộ lọc áp dụng) ghi rõ là giả định chờ khách
- [ ] Năm view chạy qua khung đăng ký; số liệu khớp truy vấn trực tiếp trên bộ dữ liệu mẫu
- [ ] Báo cáo lịch sử thay đổi đọc qua API đọc audit của E02-I05, không đọc thẳng bảng audit
- [ ] CSV từng báo cáo khớp bộ lọc, che trường nhạy cảm và giữ thời hạn lưu của nguồn
- [ ] p95 đọc mỗi báo cáo trên bộ dữ liệu có khối lượng của E01-I10 đạt mục tiêu hiệu năng đọc
### Chỗ hở
- GAP-13 Danh mục không nêu cột, công thức và bộ lọc cho từng báo cáo
- GAP-21 Báo cáo tuân thủ giờ lái thuộc epic giờ lái xe nên danh mục chỉ đủ mười hai khi epic đó đăng ký

## E12-I05 · Xuất bằng chứng kiểm toán có manifest (SCR025)
- Loại: issue
- Cha: E12
- Nhãn: type:issue
- Spec: SCR025, API-RPT-03, API-RPT-04, FR-RPT-02, IF-ARCH-01
- Phụ thuộc: E12-I06, E01-I04, E01-I16
### Mô tả
Đóng gói bản ghi case, sự kiện, tệp đính kèm, chỉ mục và mã băm nguồn của một kỳ kèm manifest SHA-256 lên bucket khoá đối tượng; manifest lệch thì từ chối, không phát hành; xuất lại cùng phạm vi cho cùng nội dung và cùng checksum; mỗi lần xuất ghi audit. Hai API chưa có hợp đồng. Danh mục loại bằng chứng, định dạng đầu ra, vai trò kiểm toán viên và thời hạn lưu gói xuất chưa có nguồn. Việc dựng gói nằm ở đây; epic tích hợp chỉ dùng chung khung job và sự kiện.
### Chỗ hở
- GAP-14 Danh mục loại bằng chứng, định dạng, vai trò và thời hạn lưu gói chưa có nguồn

## E12-I05-S01 · API tạo và tra gói bằng chứng, manifest, xuất lại
- Loại: sub-issue
- Cha: E12-I05
- Vai: BE-2
- Nhãn: type:sub-issue
- Spec: API-RPT-03, API-RPT-04, FR-RPT-02, IF-ARCH-01
- Phụ thuộc: E13-I01-S02
### Mô tả
API tạo gói chạy không đồng bộ bằng việc nền trên khung có khoá phân tán của epic tích hợp (trả mã tra cứu) và API tra trạng thái, danh sách tệp, manifest. Gói gồm đủ thành phần theo loại và kỳ, sắp xếp và định dạng ổn định để cùng phạm vi cho cùng checksum. Bảng lịch sử xuất lưu trạng thái và lỗi. Tải về qua URL ngắn hạn có kiểm quyền.
### Định nghĩa xong
- [ ] Hợp đồng API-RPT-03 và API-RPT-04 viết đủ (loại bằng chứng, kỳ, trạng thái xử lý, mã lỗi, vai trò) và đưa vào nhóm đã chi tiết hoá
- [ ] Gói có đủ thành phần, manifest SHA-256 lưu ở bucket khoá đối tượng; manifest lệch thì từ chối, không phát hành, mã chuẩn
- [ ] Xuất lại cùng phạm vi cho cùng nội dung và cùng checksum (hai lần liên tiếp và khác ngày)
- [ ] Mỗi lần xuất ghi audit; lịch sử xuất lưu trạng thái và lỗi
- [ ] Danh mục loại bằng chứng đọc từ cấu hình, giá trị chờ câu trả lời ở E12-I06

## E12-I05-S02 · Màn SCR025: tạo, xem manifest, tải, lịch sử xuất
- Loại: sub-issue
- Cha: E12-I05
- Vai: FE-1
- Nhãn: type:sub-issue
- Spec: SCR025, API-RPT-03, API-RPT-04
- Phụ thuộc: E12-I05-S01, E01-I19
### Mô tả
Chọn loại bằng chứng và kỳ, tạo bản xuất, xem manifest cùng danh sách tệp kèm SHA-256, tải về, xem lịch sử xuất và tạo lại bản y hệt. Trường hợp manifest lệch hiện thông báo từ chối rõ ràng.
### Định nghĩa xong
- [ ] Chọn loại bằng chứng và kỳ rồi tạo; trạng thái đang xử lý hiện đúng khi API trả mã tra cứu
- [ ] Hiện manifest, danh sách tệp kèm SHA-256, nút tải về
- [ ] Manifest lệch thì hiện thông báo từ chối, không có nút tải
- [ ] Lịch sử xuất có nút tạo lại bản cho cùng checksum
- [ ] Vai trò không được phép nhận thông báo từ chối; có trạng thái đang tải và lỗi

## E12-I06 · Làm rõ với khách: báo cáo, bằng chứng kiểm toán, việc chờ trang chủ
- Loại: issue
- Cha: E12
- Vai: BrSE
- Nhãn: type:issue, type:clarification
- Spec: FR-RPT-01, FR-RPT-02, SCR024, SCR025, SCR002
### Mô tả
Gom các câu hỏi khách chặn hai màn báo cáo và làm đổi trang chủ: mỗi báo cáo trong danh mục dùng bộ lọc nào và vai trò nào được xem; cột và công thức của từng báo cáo; nút mở báo cáo dẫn tới đâu; danh mục loại bằng chứng của bản xuất, định dạng đầu ra và thời hạn lưu gói; vai trò kiểm toán viên (nối với câu hỏi ở E02-I06); bộ tám loại việc chờ và người duyệt của các luồng; kênh nhận cảnh báo phiếu dải nhiệt sắp hết hạn (hộp việc chờ của trang chủ hay kênh khác); đánh dấu thông báo đã đọc và làm mới định kỳ. Mỗi câu kèm phương án đang dùng và hệ quả nếu khác.
### Định nghĩa xong
- [ ] Gói câu hỏi viết bằng tiếng Nhật trang trọng, mỗi câu nêu phương án đang dùng và hệ quả nếu đổi
- [ ] Câu trả lời ghi vào tài liệu thiết kế và dòng khai báo thay đổi kèm nguồn; câu chưa có trả lời được liệt thành giả định đang dùng
- [ ] Các issue bị chặn (E12-I02, E12-I03, E12-I04, E12-I05, E12-I01-S01) được cập nhật theo câu trả lời hoặc ghi rõ giả định
### Chỗ hở
- GAP-13 Báo cáo nào dùng bộ lọc nào và vai trò nào xem báo cáo nào chưa có nguồn
- GAP-14 Loại bằng chứng, định dạng, thời hạn lưu gói và vai trò kiểm toán viên
- GAP-15 Bộ tám loại việc chờ và đánh dấu thông báo đã đọc
