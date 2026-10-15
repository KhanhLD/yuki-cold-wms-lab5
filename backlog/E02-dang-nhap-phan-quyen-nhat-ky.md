# E02 · Đăng nhập, phân quyền, nhật ký
<!-- lab5-epic: E02 -->

## E02 · Đăng nhập, phân quyền, nhật ký
- Loại: epic
- Nhãn: type:epic
- Spec: ADR-006, SCR001, SCR030, API-ACS-01, API-ACS-02, API-ACS-03, API-ACS-04, API-ACS-05, API-ACS-06, IF-IDP-01, NFR-SEC-01, NFR-SEC-02, NFR-AUD-01, DR-PRIV-01
### Mô tả
Danh tính và quyền của người dùng: đăng nhập một lần qua nhà cung cấp danh tính của khách kèm xác thực nhiều lớp cho vai trò đặc quyền, phiên làm việc và khoá màn hình ở máy dùng chung, kho người dùng và liên kết định danh, khung kiểm quyền hai lớp dùng cho mọi module (kể cả luật người duyệt khác người đề nghị), màn quản lý vai trò, và đường đọc nhật ký audit. Phần ghi nhật ký (bảng, trigger, mẫu đảo) nằm ở E01-I16; epic này dùng nó. Nhà cung cấp danh tính của khách chưa biết, nên toàn bộ làm trên đặc tả OIDC chung và IdP thử, câu hỏi khách gom ở E02-I06.

## E02-I01 · Đăng nhập OIDC với MFA, phiên làm việc và đăng xuất
- Loại: issue
- Cha: E02
- Nhãn: type:issue
- Spec: ADR-006, SCR001, IF-IDP-01, API-ACS-01, API-ACS-02
- Phụ thuộc: E01-I11, E01-I16, E02-I02
### Mô tả
Toàn bộ luồng vào hệ thống: nút đăng nhập chuyển sang nhà cung cấp danh tính, ứng dụng đổi mã lấy token ở phía server rồi chỉ phát cookie phiên cho trình duyệt, vai trò đặc quyền phải có bằng chứng MFA, mọi lần vào hay bị từ chối đều có audit và không ghi được audit thì không cấp phiên. Bốn phần làm song song: đăng nhập phía server, phiên và đăng xuất, màn SCR001, bộ kiểm thử đầu cuối (xác thực là mảng an toàn nên chạy kiểm thử đủ bộ ở mọi lần đổi).
### Chỗ hở
- GAP-03 Chưa quyết nơi lưu phiên của lớp backend-for-frontend
- GAP-20 Cách nhận diện máy dùng chung do client tự khai

## E02-I01-S01 · Đăng nhập OIDC phía server: hai chặng, kiểm token, MFA, audit
- Loại: sub-issue
- Cha: E02-I01
- Vai: BE-1
- Nhãn: type:sub-issue
- Spec: API-ACS-01, ADR-006, IF-IDP-01, NFR-SEC-01, NFR-AUD-01
### Mô tả
Chặng một chuyển trình duyệt sang nhà cung cấp danh tính với luồng mã uỷ quyền kèm PKCE, state và nonce. Chặng hai nhận callback, đổi mã phía server, kiểm chữ ký, issuer, audience, hạn token và nonce, tra người dùng theo cặp issuer và subject, kiểm tài khoản còn hoạt động và có vai trò, kiểm bằng chứng MFA cho vai trò đặc quyền theo giá trị cấu hình, ghi audit cùng giao dịch cấp phiên, rồi phát cookie phiên. Lỗi chặng hai quay về SCR001 qua cookie ngắn hạn chỉ chứa mã lỗi.
### Định nghĩa xong
- [ ] Hai chặng chạy được với IdP thử; đường dẫn quay lại sai dạng bị từ chối bằng mã chuẩn, đúng dạng thì quay về đúng màn
- [ ] Token không xuất hiện ở phản hồi, URL hay log (kiểm thử tự động quét)
- [ ] Vai trò đặc quyền thiếu bằng chứng MFA thì không có phiên, mã chuẩn; vai trò thường không bị đòi MFA
- [ ] Tài khoản chưa liên kết, bị khoá hoặc không có vai trò trả 403 chuẩn và có audit từ chối
- [ ] Ép lỗi ghi audit thì trả 503 và không có phiên nào được cấp
- [ ] Tài liệu hợp đồng API-ACS-01 cập nhật các điểm đã chốt khi hiện thực

## E02-I01-S02 · Phiên làm việc: API đọc phiên, đăng xuất, hết hạn, máy dùng chung
- Loại: sub-issue
- Cha: E02-I01
- Vai: BE-2
- Nhãn: type:sub-issue
- Spec: API-ACS-02, API-ACS-03, ADR-006, NFR-SEC-02
### Mô tả
API đọc phiên trả người dùng, tập vai trò (đọc lại mỗi request), danh sách màn được mở, mốc hết hạn tuyệt đối, ngưỡng không thao tác, cờ máy dùng chung và ngày nghiệp vụ. Đăng xuất huỷ phiên phía server và ghi audit. Thiết kế chưa quyết nơi lưu trạng thái phiên và chưa có bảng phiên, nên issue này chọn phương án (có tính đến nhiều task chạy song song) và cập nhật thiết kế trước khi viết mã.
### Định nghĩa xong
- [ ] Phương án lưu phiên được chọn, ghi vào tài liệu thiết kế (kể cả bảng nếu cần) cùng hệ quả khi chạy nhiều task
- [ ] API-ACS-02 trả đúng theo hợp đồng; đổi vai trò có hiệu lực ở request kế tiếp
- [ ] Hết hạn tuyệt đối hoặc quá ngưỡng không thao tác trả 401 đúng mã; máy dùng chung áp ngưỡng rút gọn từ cấu hình
- [ ] Đăng xuất huỷ phiên của cookie gửi lên, ghi audit và xoá cookie lỗi tạm
- [ ] Cơ chế huỷ phiên đang mở của một người được dựng sẵn để E02-I04-S01 gọi khi thêm vai trò đặc quyền
- [ ] Phiên hết hạn hoặc bị huỷ không làm mất bản ghi POD đang chờ trên thiết bị tài xế: chỉ đòi đăng nhập lại rồi gửi tiếp (ca thử cùng epic xuất kho)

## E02-I01-S03 · Màn SCR001: đăng nhập, thông báo lỗi, hết phiên, khoá màn hình
- Loại: sub-issue
- Cha: E02-I01
- Vai: FE-1
- Nhãn: type:sub-issue
- Spec: SCR001, API-ACS-01, API-ACS-02
- Phụ thuộc: E01-I19
### Mô tả
Màn công khai duy nhất của hệ thống: chỉ có nút đăng nhập một lần, không ô mật khẩu. Hiện lý do phải đăng nhập lại (lỗi callback, quá thời lượng tối đa, bị đăng xuất vì không thao tác) và dựng lớp phủ khoá màn hình cho máy quét dùng chung ở cửa kho. Màn giữ đường dẫn quay lại để sau đăng nhập về đúng màn đang làm dở.
### Định nghĩa xong
- [ ] Chỉ có nút đăng nhập một lần; bấm thì chuyển sang chặng một, có trạng thái đang xử lý callback
- [ ] Thông báo lỗi hiện đúng câu theo mã (callback sai, IdP không phản hồi, thiếu MFA, chưa được cấp quyền); nút thử lại chỉ có ở lỗi thử lại được
- [ ] Phân biệt thông báo quá thời lượng tối đa với bị đăng xuất vì không thao tác, bộ đếm không thao tác theo ngưỡng từ API đọc phiên
- [ ] Lớp phủ khoá màn hình máy dùng chung, đăng nhập lại bằng tài khoản riêng về đúng màn cũ, không giữ dữ liệu nhập dở
- [ ] Đã có phiên mà mở SCR001 thì vào thẳng trang chủ; có trạng thái đang tải và lỗi mạng
### Chỗ hở
- GAP-20 Chưa quyết có giữ dữ liệu nhập dở khi khoá màn hình hay không

## E02-I01-S04 · Bộ kiểm thử đầu cuối cho đăng nhập và phiên
- Loại: sub-issue
- Cha: E02-I01
- Vai: QA-1
- Nhãn: type:sub-issue
- Spec: SCR001, API-ACS-01, ADR-006, NFR-SEC-01
- Phụ thuộc: E02-I01-S01, E02-I01-S02, E02-I01-S03
### Mô tả
Bộ kiểm thử chạy trên IdP thử bao phủ toàn bộ tình huống chấp nhận của màn (mười lăm tình huống), cộng ca bảo mật. Đây là khu vực xác thực nên bộ này chạy đủ ở mọi lần có thay đổi liên quan.
### Định nghĩa xong
- [ ] Mười lăm tình huống chấp nhận của SCR001 đều có ca kiểm tự động và chạy xanh trên IdP thử
- [ ] Ca bảo mật: token không lộ ở URL, log hay phản hồi; cookie phiên đúng thuộc tính; callback dùng lại state bị từ chối
- [ ] Bộ chạy trong pipeline CI và đỏ thì chặn phát hành
- [ ] Báo cáo kết quả lưu cùng bản ghi phát hành của E01-I06

## E02-I02 · Người dùng, liên kết định danh và hồ sơ cá nhân tối thiểu
- Loại: issue
- Cha: E02
- Vai: BE-1
- Nhãn: type:issue
- Spec: PLATFORM_DB, ADR-006, DR-PRIV-01, NFR-SEC-01
- Phụ thuộc: E01-I16
### Mô tả
Dựng bốn bảng nền của danh tính: người dùng nội bộ, liên kết với định danh của nhà cung cấp (issuer và subject), tập vai trò của mỗi người (chín vai trò, một người có thể có nhiều), và hồ sơ cá nhân tối thiểu tách riêng để có thời hạn lưu giữ riêng. Mọi cột người thực hiện trong hệ thống trỏ vào mã người dùng nội bộ; định danh của nhà cung cấp không đi ra ngoài bảng liên kết. Hiện chưa có quy trình cấp tài khoản lần đầu (tạo người dùng, liên kết, gán quản trị đầu tiên), nên issue này dựng công cụ do vận hành chạy, không phải API công khai, cho tới khi khách chọn cách cấp tài khoản.
### Định nghĩa xong
- [ ] Bốn bảng cùng ràng buộc và migration; không có khoá ngoại ra ngoài hệ thống, định danh nhà cung cấp chỉ nằm ở bảng liên kết
- [ ] Tên hiển thị nằm bảng riêng và xoá được theo thời hạn mà bản ghi giữ lâu hơn vẫn tham chiếu được bằng mã người dùng
- [ ] Công cụ vận hành tạo người dùng, liên kết issuer và subject, gán quản trị đầu tiên; chạy được ở dev và ghi audit
- [ ] Tra theo issuer và subject trả đúng người dùng và vai trò; tài khoản bị khoá không tra ra phiên
- [ ] Một người một tài khoản: trùng cặp issuer và subject bị từ chối (có ca thử)
### Chỗ hở
- GAP-04 Chưa có quy trình cấp tài khoản và quản trị đầu tiên

## E02-I03 · Khung phân quyền hai lớp và chặn tự duyệt
- Loại: issue
- Cha: E02
- Vai: BE-2
- Nhãn: type:issue
- Spec: ADR-006, NFR-SEC-02, API-ACS-02, PLATFORM_DB
- Phụ thuộc: E02-I02, E01-I11, E01-I13
### Mô tả
Bộ công cụ phân quyền mà mọi module dùng. Lớp một: ma trận màn theo vai trò đưa vào dữ liệu, là nguồn duy nhất cho danh sách màn của phiên. Lớp hai: kiểm quyền từng lệnh ở service trước khi ghi, kể cả khi API bị gọi thẳng. Cộng helper người duyệt khác người đề nghị so mã người dùng và vai trò, và vai trò duyệt đọc từ kho cấu hình. Ma trận có bảy màn đang suy diễn, đánh dấu chờ khách duyệt nhưng không đổi hành vi.
### Định nghĩa xong
- [ ] Ma trận nạp từ một nguồn dữ liệu; danh sách màn của API đọc phiên sinh từ đó và là hợp của các vai trò khi có nhiều vai trò
- [ ] Khai báo quyền cho lệnh ghi dùng chung; ca thử gọi thẳng API bằng vai trò sai trả 403 chuẩn dù giao diện đã ẩn
- [ ] Helper người duyệt khác người đề nghị trả 403 chuẩn khi trùng người; có ca thử với một luồng mẫu
- [ ] Luật kiểm kiến trúc: mọi điểm vào ghi phải có khai báo quyền, thiếu thì build đỏ
- [ ] Bảy màn suy diễn được đánh dấu chờ khách duyệt ngay trong dữ liệu ma trận
### Chỗ hở
- GAP-12 Khách chưa duyệt ma trận vai trò và bảy màn mới chỉ suy diễn

## E02-I04 · Quản lý người dùng và vai trò (SCR030)
- Loại: issue
- Cha: E02
- Nhãn: type:issue
- Spec: SCR030, API-ACS-04, API-ACS-05, NFR-SEC-02, ADR-006
- Phụ thuộc: E02-I03, E02-I02, E01-I19
### Mô tả
Màn chỉ quản trị viên mở: xem danh sách người dùng cùng tập vai trò và gán vai trò cho người khác, mỗi lần đổi có audit và có hiệu lực ngay. Hai phần làm song song là API và màn. Luồng đề nghị đổi vai trò cần người thứ hai duyệt chưa được khách xác nhận nên bị ẩn theo hợp đồng hiện tại, và bảng tổ hợp vai trò bị cấm đang rỗng chờ khách.
### Chỗ hở
- GAP-05 Hai quản trị đổi vai trò của nhau cùng lúc có thể không còn quản trị nào
- GAP-16 Chưa rõ cách tìm người dùng khi không lưu email

## E02-I04-S01 · API danh sách người dùng và đặt vai trò
- Loại: sub-issue
- Cha: E02-I04
- Vai: BE-1
- Nhãn: type:sub-issue
- Spec: API-ACS-04, API-ACS-05, PLATFORM_DB, NFR-AUD-01
### Mô tả
API danh sách người dùng (phân trang, tên hiển thị, tập vai trò, ngày tạo; chế độ danh sách cho quản trị, chế độ chọn người cho vai trò chất lượng ở màn thu hồi) và API đặt toàn bộ tập vai trò kèm version mong đợi. Chặn tự đổi vai trò của chính mình trước mọi kiểm tra khác. Tập trùng thì không ghi. Bảng tổ hợp cấm đọc từ dữ liệu, rỗng thì không từ chối tổ hợp nào. Thêm vai trò đặc quyền thì huỷ phiên đang mở của người đó. Luật luôn còn ít nhất một quản trị phải đúng cả khi hai quản trị thao tác đồng thời.
### Định nghĩa xong
- [ ] Danh sách phân trang đúng; chỉ quản trị dùng chế độ danh sách, vai trò chất lượng chỉ dùng chế độ chọn người
- [ ] Đặt vai trò: lệch version trả 409, tập trùng không ghi, tự đổi vai trò mình bị chặn
- [ ] Bảng tổ hợp cấm đọc từ dữ liệu; có ca thử cả khi rỗng lẫn khi có dòng
- [ ] Mỗi lần đổi có audit; thêm vai trò đặc quyền huỷ phiên đang mở của người được gán
- [ ] Ca thử hai quản trị thu hồi vai trò của nhau đồng thời vẫn còn ít nhất một quản trị

## E02-I04-S02 · Màn SCR030: danh sách và gán vai trò
- Loại: sub-issue
- Cha: E02-I04
- Vai: FE-1
- Nhãn: type:sub-issue
- Spec: SCR030, API-ACS-04, API-ACS-05
- Phụ thuộc: E02-I04-S01
### Mô tả
Bảng người dùng tải đủ các trang, ô chọn vai trò, nút lưu, và các thông báo từ server: xung đột phiên bản, chặn tự đổi, vi phạm tối thiểu quyền. Khu chờ duyệt vẫn ẩn theo hợp đồng hiện tại nhưng được dựng sẵn để bật khi khách xác nhận. Cột mã tài xế ghép ở phía SPA từ dữ liệu master, lỗi đọc không làm hỏng bảng.
### Định nghĩa xong
- [ ] Bảng hiện đủ người dùng qua mọi trang, kèm tập vai trò, ngày tạo và mã tài xế (hiện dấu gạch khi không có)
- [ ] Gán vai trò một giá trị, lưu thành công cập nhật bảng; xung đột, tự đổi và vi phạm tối thiểu quyền hiện đúng thông báo
- [ ] Dòng của chính mình không có liên kết đổi vai trò; chỉ quản trị mở được màn
- [ ] Khu chờ duyệt ẩn theo hợp đồng và có thể bật bằng cờ khi khách xác nhận
- [ ] Lỗi đọc danh sách tài xế không chặn bảng; có trạng thái đang tải và lỗi hệ thống

## E02-I05 · API đọc nhật ký audit
- Loại: issue
- Cha: E02
- Vai: BE-1
- Nhãn: type:issue
- Spec: API-ACS-06, NFR-AUD-01, PLATFORM_DB, ADR-003
- Phụ thuộc: E01-I16, E02-I03, E02-I06
### Mô tả
API đọc sổ audit chỉ có dòng trong danh mục, chưa có hợp đồng và wireframe không vẽ màn tiêu thụ. Issue này viết hợp đồng và chạy API cho người dùng nội bộ: lọc theo khoảng thời gian, loại sự kiện, người thực hiện, đối tượng; phân trang; chỉ đọc. Vai trò được đọc theo câu trả lời của khách về kiểm toán viên (hiện đề xuất quản trị). Gói xuất kèm checksum thuộc màn xuất bằng chứng ở E12, không làm ở đây.
### Định nghĩa xong
- [ ] Hợp đồng API-ACS-06 được viết đủ (lọc, phân trang, trần dòng, mã lỗi, vai trò) và chuyển sang nhóm đã chi tiết hoá
- [ ] Endpoint chạy: lọc đúng, thứ tự ổn định, vượt trần dòng trả lỗi chuẩn thay vì cắt
- [ ] Không trả định danh nhà cung cấp hay tên đã bị purge; tên rỗng thì hiện mã người dùng
- [ ] Vai trò ngoài danh sách được đọc nhận 403 chuẩn
- [ ] Truy vấn chạy trên reader và đo p95 trên bộ dữ liệu test của E01-I10
### Chỗ hở
- GAP-01 API đọc audit chưa có hợp đồng, chưa có màn và chưa có vai trò kiểm toán viên

## E02-I06 · Làm rõ với khách: IdP, vai trò, kiểm toán viên, cách cấp tài khoản, phiên
- Loại: issue
- Cha: E02
- Vai: BrSE
- Nhãn: type:issue, type:clarification
- Spec: ADR-006, SCR001, SCR030, NFR-SEC-01, NFR-AUD-01
### Mô tả
Gom các câu hỏi khách đang chặn hoặc làm đổi thiết kế đăng nhập và phân quyền: nhà cung cấp danh tính, claim MFA thật, cách chứng minh thiết bị quản lý của tài xế và có thay cách nhận diện máy dùng chung bằng chứng chỉ thiết bị không; ai cấp tài khoản và bằng cách nào; duyệt ma trận chín vai trò theo màn; có cần vai trò kiểm toán viên riêng; đổi vai trò có cần người thứ hai duyệt không; tổ hợp vai trò nào bị cấm; một người có nhiều vai trò không; có cần khoá tài khoản, đăng xuất mọi thiết bị và thu hồi phiên khi khoá; có giữ dữ liệu nhập dở khi khoá màn hình không; cách admin tìm người dùng; các giá trị phiên đang dùng tạm. Mỗi câu kèm phương án đang dùng và hệ quả nếu khác; riêng câu thu hồi phiên khi khoá phải nêu rõ việc không được làm mất bản ghi POD đang chờ gửi trên thiết bị.
### Định nghĩa xong
- [ ] Gói câu hỏi viết bằng tiếng Nhật trang trọng, mỗi câu nêu phương án đang dùng và hệ quả nếu đổi
- [ ] Câu trả lời ghi vào tài liệu thiết kế và dòng khai báo thay đổi kèm nguồn; câu chưa có trả lời được liệt thành giả định đang dùng
- [ ] Các issue bị chặn (E02-I01-S01, E02-I01-S03, E02-I04, E02-I05) được cập nhật theo câu trả lời hoặc ghi rõ giả định
- [ ] Phần đăng xuất mọi thiết bị và thu hồi phiên khi khoá có kết luận: làm hay hoãn, kèm lý do
### Chỗ hở
- GAP-02 Đăng xuất mọi thiết bị và thu hồi phiên khi khoá chưa được khách xác nhận
- GAP-12 Ma trận vai trò và vai trò kiểm toán viên chưa được khách duyệt
- GAP-16 Cách tìm người dùng khi không lưu email
- GAP-20 Cách nhận diện máy dùng chung do client tự khai
- GAP-16 Giao diện một vai trò nhưng mô hình dữ liệu cho nhiều vai trò
