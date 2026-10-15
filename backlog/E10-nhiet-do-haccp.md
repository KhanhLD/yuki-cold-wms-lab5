# E10 · Nhiệt độ & HACCP
<!-- lab5-epic: E10 -->

## E10 · Nhiệt độ & HACCP
- Loại: epic
- Nhãn: type:epic, constraint:haccp-temp
- Spec: SCR019, SCR020, SCR021, SCR033, TMP_LOGGER_INGEST_DEVIATION, TMP_LOT_RELEASE_APPROVAL, TEMPERATURE_DB, ADR-011, FR-TEMP-01, FR-TEMP-02, FR-TEMP-03, FR-TEMP-04, BR-TEMP-01, BR-HACCP-01, DR-TEMP-01, IF-LOG-01, JP-01, JP-02, JP-03, JP-04, JP-05, JP-17
### Mô tả
Toàn bộ phần giám sát nhiệt độ theo HACCP của kho lạnh: nạp file datalogger theo ca, đánh giá từng số đo bằng ngưỡng có phiên bản, sinh cảnh báo và tự cách ly lô, theo dõi hạn xử lý 15 phút (xác nhận, đo lại, leo thang), duyệt giải phóng hoặc huỷ lô theo bốn mắt, quản lý case sai lệch, và báo cáo độ đủ hồ sơ HACCP. Ba dải nhiệt và hạn xử lý là chính sách có phiên bản, không viết cứng trong mã. Phần kiểm nhiệt lúc nhận, di chuyển và trước xuất phát thuộc các epic kho và giao hàng; epic này chỉ nhận sự kiện từ đó để mở case.

## E10-I01 · Ngưỡng nhiệt có phiên bản theo ngày hiệu lực
- Loại: issue
- Cha: E10
- Nhãn: type:issue, constraint:haccp-temp
- Spec: ADR-011, SCR033, API-SYS-07, BR-TEMP-01, JP-01
- Giai đoạn: 1
### Mô tả
Ba dải nhiệt (thường, mát, đông) là dữ liệu chủ có phiên bản và ngày hiệu lực; mọi số đo, kiểm nhận, di chuyển và cảnh báo phải lưu phiên bản đã dùng. Issue này gồm phần lõi ở module dữ liệu chủ (mô hình, đề nghị và duyệt, tra phiên bản theo thời điểm đo) và phần màn cấu hình ngưỡng. Màn SCR033 chỉ có wireframe nên hợp đồng API phải được viết trong chính issue này. Khối quy tắc giờ lái trên cùng màn thuộc E09, khối phiên bản cấu hình và ngày nghiệp vụ thuộc E01.

## E10-I01-S01 · Mô hình phiên bản ngưỡng, đề nghị – duyệt, tra phiên bản theo thời điểm (BE)
- Loại: sub-issue
- Cha: E10-I01
- Vai: BE-1
- Nhãn: type:sub-issue, constraint:haccp-temp
- Spec: ADR-011, API-SYS-07, API-MST-10, API-MST-11, BR-TEMP-01
- Phụ thuộc: E03-I01, E03-I05, E01-I16, E02-I03
- Giai đoạn: 1
### Mô tả
Dựng bảng phiên bản ngưỡng trong module dữ liệu chủ: số phiên bản tăng theo dải, cận dưới và cận trên một chữ số thập phân (được rỗng một đầu, đủ cho dải đông chỉ có trần), ngày hiệu lực, nguồn, người đề xuất, người duyệt. Phiên bản đã ghi không sửa, không xoá. Kích hoạt đi qua đề nghị rồi duyệt: người duyệt là trưởng QC và khác người đề xuất, ngày hiệu lực phải ở tương lai so với lúc duyệt. Cung cấp hàm tra "phiên bản có hiệu lực tại thời điểm đo" qua named interface cho các module dùng. Danh sách phiên bản (API-SYS-07) và nhánh ngưỡng nhiệt của API-MST-10 / 11 mới có dòng trong danh mục, chưa có hợp đồng nên phải viết hợp đồng trước khi cài. Bảng dải nhiệt và bảng phiên bản ngưỡng do E03-I01 tạo trong lược đồ dữ liệu chủ, khung đề nghị – duyệt theo loại master do E03-I05 dựng; issue này dùng lại khung đó cho loại ngưỡng nhiệt mà không sửa lõi, và làm phần luật ngưỡng, phiên bản hoá, tra phiên bản.
### Định nghĩa xong
- [ ] Hợp đồng API-SYS-07, API-MST-10, API-MST-11 (nhánh ngưỡng nhiệt) viết xong: đường dẫn, vai trò, mã lỗi theo bảng lỗi chung; ba API chạy được trên môi trường test
- [ ] Luật ngưỡng chạy ở tầng dịch vụ: có ít nhất một cận, cận dưới nhỏ hơn cận trên khi có cả hai, cấm sửa và xoá phiên bản đã ghi; không thêm migration trùng với bảng của E03-I01
- [ ] Dữ liệu khởi tạo đủ ba dải đúng JP-01..JP-03, nguồn ghi "mẫu RFP", biên tính là trong dải
- [ ] Đề nghị và duyệt ngưỡng nhiệt chạy trên khung đề nghị – duyệt chung của E03-I05 (không dựng khung riêng); duyệt bị từ chối khi người duyệt trùng người đề xuất hoặc ngày hiệu lực không ở tương lai; vai trò sai nhận 403
- [ ] Hàm tra phiên bản (qua named interface cho nhiệt độ, kho và giao hàng, kể cả tra tại giờ xuất phát) có kiểm thử tích hợp cho ba ca: đúng mốc đổi phiên bản, trước phiên bản đầu tiên (không có ngưỡng), hai phiên bản cùng ngày hiệu lực
- [ ] Duyệt xong phát sự kiện kích hoạt qua outbox và người nghe của module nhiệt độ nhận được trong kiểm thử
### Chỗ hở
- GAP-75 màn cấu hình ngưỡng ghi hai nơi khác nhau giữa các tài liệu LAB-4
- GAP-83 chưa rõ có sửa phiên bản trước ngày hiệu lực hay không

## E10-I01-S02 · Màn SCR033: khung trang và khối ngưỡng nhiệt (FE)
- Loại: sub-issue
- Cha: E10-I01
- Vai: FE-1
- Nhãn: type:sub-issue, constraint:haccp-temp
- Spec: SCR033, ADR-011, JP-01, JP-02, JP-03
- Phụ thuộc: E10-I01-S01
- Giai đoạn: 1
### Mô tả
Dựng khung trang SCR033 (route, bố cục ba khối, kiểm vai trò ở giao diện) rồi làm khối ngưỡng nhiệt: bảng theo dải gồm phiên bản, khoảng nhiệt, ngày hiệu lực, trạng thái đang áp dụng hoặc sắp áp dụng, cùng form tạo phiên bản mới. Màn không vẽ số ngưỡng cứng, mọi giá trị lấy từ dữ liệu. Có dòng nhắc rằng kết quả đã ghi không bị tính lại. Hai nhóm khác gắn khối của họ vào khung này (E09-I01-S03 đã phụ thuộc vào đây), nên khung phải xong sớm. Khối ngưỡng nhiệt viết như một panel độc lập cùng mẫu với panel của E01-I15-S02.
### Định nghĩa xong
- [ ] Khung SCR033 có route, kiểm vai trò và ba chỗ gắn khối; E09 và E01 gắn được khối của họ mà không sửa khung
- [ ] Khối ngưỡng nhiệt là panel độc lập, cùng cách gắn với panel của E01-I15-S02; không phụ thuộc mã của khối khác
- [ ] Bảng ngưỡng hiện đủ ba dải với phiên bản đang áp dụng và phiên bản sắp áp dụng, giá trị đọc từ API
- [ ] Form tạo phiên bản kiểm trường bắt buộc, một chữ số thập phân, ngày hiệu lực ở tương lai; lỗi từ máy chủ hiện đúng trường
- [ ] Có trạng thái đang tải, rỗng, lỗi mạng và 403; nhãn tiếng Nhật dùng đúng nhãn đã duyệt của wireframe
- [ ] Kiểm tay với vai trò trưởng QC và vai trò không đủ quyền, ghi lại ảnh chụp trong issue

## E10-I02 · Nạp file logger, đánh giá theo ngưỡng và sinh cảnh báo
- Loại: issue
- Cha: E10
- Nhãn: type:issue, constraint:haccp-temp
- Spec: TMP_LOGGER_INGEST_DEVIATION, API-TMP-03, FR-TEMP-01, FR-TEMP-02, DR-TEMP-01
- Giai đoạn: 1
### Mô tả
Lõi nghiệp vụ nhiệt độ: nhận file CSV của một thiết bị cho một ca, giữ SHA-256 của file gốc, phân loại từng dòng (nhận, từ chối kèm số dòng, ngoại lệ không đánh giá), đánh giá mỗi số đo theo phiên bản ngưỡng tại thời điểm đo, gom chuỗi vượt ngưỡng thành cảnh báo mang đỉnh, thời lượng và phiên bản của số đo đỉnh, rồi xác định lô bị ảnh hưởng và tự cách ly. Toàn bộ chạy trong một giao dịch cho cả file. Giá trị mức độ cảnh báo và thời lượng cộng một nhịp là giả định nhà thầu đọc từ khoá cấu hình.

## E10-I02-S01 · Lược đồ TEMPERATURE_DB cho số đo, cảnh báo và hàng đợi hoàn chỉnh (BE)
- Loại: sub-issue
- Cha: E10-I02
- Vai: BE-1
- Nhãn: type:sub-issue, constraint:haccp-temp
- Spec: TEMPERATURE_DB, DR-TEMP-01, JP-05, JP-04
- Giai đoạn: 1
### Mô tả
Tạo các bảng thiết bị logger, định nghĩa ca, lô file đã nạp, số đo, cảnh báo, liên kết cảnh báo – lô, đo lại và hàng đợi hoàn chỉnh theo thiết kế dữ liệu LAB-4. Bảng số đo là bảng lớn nhất hệ thống nên phân vùng theo tháng, có phân vùng mặc định để nạp không bao giờ lỗi vì thiếu phân vùng. Số đo và cảnh báo chỉ ghi thêm (cảnh báo chỉ cho cập nhật các cột xử lý). Phiên bản ngưỡng được kiểm ở tầng dịch vụ, không dùng trigger đọc bảng của module khác. Bảng case sai lệch, dòng liên kết case và tệp đính kèm thuộc E10-I06.
### Định nghĩa xong
- [ ] Migration chạy sạch từ trống và chạy lại không lỗi; có phân vùng tháng và phân vùng mặc định
- [ ] Trigger chặn sửa, xoá số đo và chặn cập nhật cột đặc trưng của cảnh báo; tài khoản ứng dụng chỉ có quyền thêm và đọc trên bảng số đo
- [ ] Khoá duy nhất bảo vệ: SHA-256 file, bộ ba thiết bị – ngày – ca, thiết bị – thời điểm của số đo, mục hàng đợi theo thiết bị – ngày – ca – loại
- [ ] Số đo lưu một chữ số thập phân °C và thời điểm ISO 8601 theo JP-05; kiểm thử chèn giá trị nhiều chữ số bị từ chối
- [ ] Issue này sở hữu bảng thiết bị logger; cột thiếu so với màn (khu, người phụ trách, hiệu lực) và quy trình thêm hoặc đổi thiết bị được ghi thành đề xuất gửi E03-I06-S02, chỉ thêm cột sau khi E10-I08 có câu trả lời của khách
- [ ] Tài liệu dữ liệu cập nhật đúng với bảng thật, kể cả ghi chú phân vùng
### Chỗ hở
- GAP-84 quy trình thêm thiết bị, lịch phân vùng và thời hạn lưu giữ của danh mục thiết bị chưa có câu trả lời

## E10-I02-S02 · Nhận file, phân loại dòng, đánh giá và gom cảnh báo (BE)
- Loại: sub-issue
- Cha: E10-I02
- Vai: BE-2
- Nhãn: type:sub-issue, constraint:haccp-temp
- Spec: API-TMP-03, TMP_LOGGER_INGEST_DEVIATION, FR-TEMP-01, FR-TEMP-02, JP-05
- Phụ thuộc: E10-I02-S01, E10-I01-S01, E01-I13, E01-I14
- Giai đoạn: 1
### Mô tả
Cài các bước nhận request (khoá idempotency, vai trò, cỡ file), đọc file, phân loại dòng và đánh giá. Mỗi dòng rơi vào đúng một nhóm: nhận, từ chối kèm số dòng và lý do, hoặc ngoại lệ không đánh giá và không bao giờ tính là đạt (lệch múi giờ, ngoài ca, thời điểm tương lai, thiếu phiên bản ngưỡng). File trùng hash, ca đã có file, số đo trùng thời điểm đều bị từ chối cả file. Chuỗi vượt ngưỡng kết thúc ở số đo đạt hoặc ở khoảng trống dài hơn một nhịp 15 phút; cảnh báo mang phiên bản của số đo đỉnh. Biên tính là đạt.
### Định nghĩa xong
- [ ] Bảng ca biên của thiết kế (biên đúng, vượt một chữ số, chuỗi qua mốc đổi phiên bản, trước phiên bản đầu, ngày giờ không có thật, tiêu đề sai) đều có kiểm thử và xanh
- [ ] Gửi lại cùng khoá idempotency trả đúng phản hồi đã lưu; khoá khác cho cùng file trả lỗi trùng file; ca đã có file trả lỗi trùng ca
- [ ] Lỗi mức cả file không ghi gì và không khoá ca, người dùng nạp lại được
- [ ] Mức độ cảnh báo và hai cận phân mức đọc từ khoá cấu hình; thiếu khoá thì dừng với mã lỗi hệ thống, không dùng số mặc định trong mã
- [ ] Kiểm "phiên bản ghi trên bản ghi có hiệu lực tại thời điểm bản ghi" chạy ở tầng dịch vụ trong cùng giao dịch, có kiểm thử tích hợp cho mốc đổi phiên bản
- [ ] Hợp đồng API-TMP-03 trong tài liệu khớp hành vi thực tế sau khi chạy
### Chỗ hở
- GAP-85 mức critical, thời lượng cộng một nhịp và cột CSV là giả định nhà thầu chưa khách xác nhận

## E10-I02-S03 · Lô bị ảnh hưởng, tự cách ly, ghi giao dịch và sự kiện (BE)
- Loại: sub-issue
- Cha: E10-I02
- Vai: BE-1
- Nhãn: type:sub-issue, constraint:haccp-temp
- Spec: TMP_LOGGER_INGEST_DEVIATION, TEMPERATURE_DB, FR-TEMP-02, JP-04
- Phụ thuộc: E10-I02-S02, E05-I01, E05-I10
- Giai đoạn: 1
### Mô tả
Xác định lô nằm trong vùng của logger tại một thời điểm bất kỳ của khoảng cảnh báo (bỏ lô đã huỷ), phân ba loại: còn tồn và đang khả dụng thì cách ly, còn tồn nhưng đã cách ly hoặc tạm giữ thì giữ nguyên, đã rời kho kể từ lúc bắt đầu cảnh báo thì vào danh sách theo dõi. "Rời kho" có một định nghĩa duy nhất, lấy qua cổng SPI do module nhiệt độ khai báo. Khoá lô của cả file theo thứ tự cố định trước khi ghi cảnh báo đầu tiên để không kẹt với lệnh xuất phát. Ghi file, số đo, cảnh báo, lô; mở mục hàng đợi cho khoảng trống so với nhịp ghi (mục không tự đóng); phát sự kiện qua outbox cùng giao dịch.
### Định nghĩa xong
- [ ] Ba loại xử lý lô đúng với các ca biên của thiết kế (lô vùng khác, khu cách ly, chuyển ra trước cảnh báo, nhận sau cảnh báo, pick lên chuyến chưa xuất phát, một phân bổ trên hai chuyến)
- [ ] Kiểm thử đồng thời: nạp hai cảnh báo trỏ lô theo thứ tự ngược trong lúc lệnh xuất phát giữ lô, không deadlock
- [ ] Lỗi lệnh cách ly làm rollback cả file, không có cảnh báo nào mà lô chưa cách ly
- [ ] Khoảng trống so với nhịp ghi mở đúng một mục hàng đợi cho mỗi thiết bị – ngày – ca – loại, chạy lại không tạo trùng
- [ ] Sự kiện nạp file và cảnh báo được ghi vào outbox cùng giao dịch; rollback thì không có sự kiện và tệp gốc không mồ côi
- [ ] Phản hồi 201 trả đủ số dòng, danh sách cảnh báo, số lô cách ly, số mục hàng đợi mở

## E10-I03 · Adapter datalogger: S3 landing sang lệnh nạp
- Loại: issue
- Cha: E10
- Vai: BE-2
- Nhãn: type:issue, constraint:haccp-temp
- Spec: IF-LOG-01, ADR-008, TMP_LOGGER_INGEST_DEVIATION, JP-04
- Phụ thuộc: E10-I02-S02, E13-I01-S01
- Giai đoạn: 1
### Mô tả
Không có kết nối trực tiếp tới thiết bị: file CSV được đặt vào vùng landing, adapter ở module tích hợp kiểm file, gọi lệnh nạp nội bộ của module nhiệt độ (cùng lệnh với màn tải tay, người nạp để rỗng) rồi báo kết quả. File trùng hash xử lý idempotent, dòng sai bị từ chối kèm số dòng, file lỗi hình thức hiện ở màn giám sát tích hợp. Tên file theo mẫu của điểm tích hợp không chứa mã ca trong khi lệnh nạp cần ca, nên cách suy ra ca phải được chốt trước khi cài.
### Định nghĩa xong
- [ ] Cách xác định mã ca cho file đến từ adapter được chốt bằng văn bản (xem câu hỏi ở E10-I08) và cài đúng như vậy
- [ ] File mẫu hợp lệ đặt vào landing dẫn tới đúng kết quả như tải tay: số dòng, cảnh báo, hàng đợi
- [ ] File cùng hash đặt lần hai không tạo bản ghi thứ hai và không báo lỗi
- [ ] File vượt cỡ tối đa theo khoá cấu hình hoặc sai tiêu đề bị từ chối và sinh sự kiện từ chối, hiện ở màn giám sát
- [ ] Log adapter có correlation ID và không ghi nội dung file
### Chỗ hở
- GAP-81 tên file của điểm tích hợp thiếu mã ca trong khi lệnh nạp bắt buộc có ca

## E10-I04 · Hạn xử lý cảnh báo: xác nhận, đo lại, leo thang
- Loại: issue
- Cha: E10
- Nhãn: type:issue, constraint:haccp-temp
- Spec: TMP_LOGGER_INGEST_DEVIATION, API-TMP-02, API-TMP-04, FR-TEMP-03, JP-17
- Giai đoạn: 1
### Mô tả
Hạn review đầu và hạn đo lại là 15 phút tính từ lúc cảnh báo được tạo (sau khi nạp file), theo đồng hồ thật, và được lưu trên cảnh báo để đổi cấu hình không dời hạn cũ. Gồm hai API cho QC (xác nhận, đo lại) và hai việc chạy nền (leo thang quá hạn, nhắc ca thiếu file). Không có việc nền thì không giữ được hạn khi chẳng ai mở màn, đây là khác biệt lớn với prototype.

## E10-I04-S01 · API xác nhận cảnh báo và đo lại (BE)
- Loại: sub-issue
- Cha: E10-I04
- Vai: BE-1
- Nhãn: type:sub-issue, constraint:haccp-temp
- Spec: API-TMP-02, API-TMP-04, FR-TEMP-03, JP-17, TMP_LOGGER_INGEST_DEVIATION
- Phụ thuộc: E10-I02-S03, E05-I01, E01-I13, E01-I14, E05-I10
- Giai đoạn: 1
### Mô tả
Xác nhận là đúng một cập nhật có điều kiện chưa xác nhận, ghi người, giờ thật và thời gian phản hồi; xác nhận muộn vẫn ghi và đánh dấu quá hạn để vào báo cáo quản lý. Đo lại chỉ cho QC, một lần cho mỗi cảnh báo, so với phiên bản ngưỡng hiệu lực lúc đo lại: trong ngưỡng thì lô đang cách ly chuyển sang tạm giữ (không bao giờ tự giải phóng), vẫn lệch thì lô giữ cách ly, mở case sai lệch và đặt dấu chặn loại nhiệt độ. Hai QC thao tác cùng lúc thì một người thắng.
### Định nghĩa xong
- [ ] Hai request xác nhận cùng lúc: một thành công, một nhận lỗi đã xác nhận; xác nhận muộn ghi được và có cờ quá hạn
- [ ] Đo lại trong ngưỡng chuyển đúng các lô còn cách ly sang tạm giữ, lô đã tạm giữ hoặc chỉ ở danh sách theo dõi giữ nguyên
- [ ] Đo lại vẫn lệch mở đúng một case nguồn nhiệt độ gồm mọi lô của cảnh báo và đặt dấu chặn, cảnh báo sang trạng thái mở case
- [ ] Đo lại lần hai, giá trị sai định dạng, vai trò không phải QC đều nhận đúng mã lỗi
- [ ] Hạn lưu trên cảnh báo không đổi khi đổi khoá cấu hình sau đó (có kiểm thử)
- [ ] Hợp đồng API-TMP-02 và API-TMP-04 trong tài liệu khớp hành vi chạy thật

## E10-I04-S02 · Việc nền leo thang quá hạn và nhắc ca thiếu file (BE)
- Loại: sub-issue
- Cha: E10-I04
- Vai: BE-2
- Nhãn: type:sub-issue, constraint:haccp-temp
- Spec: TMP_LOGGER_INGEST_DEVIATION, ADR-008, JP-17, BR-HACCP-01
- Phụ thuộc: E10-I02-S03, E13-I01-S02, E01-I13, E01-I14
- Giai đoạn: 1
### Mô tả
Hai việc nền của module nhiệt độ chạy bằng khoá phân tán trong cơ sở dữ liệu để nhiều task chỉ chạy một lượt. Việc leo thang quét cảnh báo quá hạn xác nhận và quá hạn đo lại, leo thang mỗi loại đúng một lần tới trưởng QC và phát sự kiện. Chu kỳ chạy phải đủ ngắn để độ trễ sau hạn không vượt một chu kỳ. Việc nhắc ca thiếu file mở mục thiếu file cho ca quá hạn; khi khoá hạn nhắc còn trống (chưa có giá trị từ khách) thì không mở mục nào và ghi cảnh báo vận hành mỗi lượt, không tự đặt hạn.
### Định nghĩa xong
- [ ] Hai task chạy cùng lúc, chỉ một lấy được khoá và xử lý; task kia bỏ lượt mà không lỗi
- [ ] Cảnh báo quá hạn chưa xác nhận leo thang đúng một lần; thiếu đo lại leo thang thêm một lần riêng; độ trễ đo được không vượt một chu kỳ job
- [ ] Khoá hạn nhắc ca trống: không có mục thiếu file nào được mở, mỗi lượt có một cảnh báo vận hành; khoá có giá trị: mục mở đúng một lần cho mỗi thiết bị – ngày – ca
- [ ] Kiểm thử với đồng hồ giả lập đổi ngày nghiệp vụ không làm lệch hạn (hạn đo theo giờ thật)
- [ ] Danh mục kiểm tra phát hành có dòng chặn go-live khi khoá hạn nhắc còn trống
### Chỗ hở
- GAP-73 khoá hạn nhắc ca thiếu file còn trống, đang chặn go-live

## E10-I05 · Màn SCR019: bảng điều khiển cảnh báo nhiệt độ
- Loại: issue
- Cha: E10
- Nhãn: type:issue, constraint:haccp-temp
- Spec: SCR019, API-TMP-01, FR-TEMP-01, FR-TEMP-02, FR-TEMP-03
- Giai đoạn: 1
### Mô tả
Màn có spec đầy đủ (khoảng 35 nhóm mục, 25 kịch bản chấp nhận) gồm khối tải file theo ca, kết quả nạp, dòng bị từ chối hoặc ngoại lệ, ca thiếu file, danh sách cảnh báo, chi tiết, nút xác nhận, ô đo lại, lô bị ảnh hưởng và lịch sử file. Giao diện chỉ gửi file và hiển thị, mọi đánh giá do máy chủ làm. Chia theo vai để làm song song: API đọc, hai nửa giao diện, và kiểm thử đầu cuối bám các kịch bản chấp nhận.

## E10-I05-S01 · API đọc cảnh báo, file đã nạp, ca thiếu file và tuỳ chọn (BE)
- Loại: sub-issue
- Cha: E10-I05
- Vai: BE-2
- Nhãn: type:sub-issue, constraint:haccp-temp
- Spec: API-TMP-01, SCR019, TEMPERATURE_DB
- Phụ thuộc: E10-I02-S01
- Giai đoạn: 1
### Mô tả
Một endpoint đọc duy nhất phục vụ SCR019 với các chế độ: danh sách cảnh báo (chưa xác nhận lên trước, cờ quá hạn, cờ đã leo thang, sắp theo mức độ), chi tiết một cảnh báo kèm lô và đo lại, ca quá hạn chưa có file, lịch sử file đã nạp phân trang, và tuỳ chọn thiết bị – ca cho form. Tài liệu ghi rõ việc gộp bốn chế độ vào một endpoint là lựa chọn tạm của nhóm viết, chưa chốt; cần chốt trước khi cài để không đổi hợp đồng sau khi FE đã bám vào.
### Định nghĩa xong
- [ ] Quyết định giữ gộp hay tách endpoint được ghi lại; hợp đồng cuối cùng khớp hành vi thật
- [ ] Danh sách trả đúng thứ tự sắp theo mức độ rồi thời gian còn lại, kèm cờ quá hạn và leo thang do máy chủ tính
- [ ] Chi tiết cảnh báo trả đủ đầu, cuối, đỉnh, mức vượt, thời lượng, phiên bản ngưỡng, các hạn, lô theo ba loại xử lý
- [ ] Phân trang lịch sử file theo khoá cỡ trang; lọc theo thiết bị đúng dạng mã
- [ ] Vai trò ngoài ma trận nhận 403; cảnh báo không tồn tại nhận 404
### Chỗ hở
- GAP-82 chưa chốt gộp hay tách bốn chế độ; thứ tự phụ trong cùng mức độ lệch với kịch bản người dùng

## E10-I05-S02 · SCR019 phần tải file, kết quả nạp và ca thiếu file (FE)
- Loại: sub-issue
- Cha: E10-I05
- Vai: FE-1
- Nhãn: type:sub-issue, constraint:haccp-temp
- Spec: SCR019, API-TMP-03, API-TMP-01
- Phụ thuộc: E10-I05-S01, E10-I02-S02
- Giai đoạn: 1
### Mô tả
Làm nửa trên của màn: form chọn thiết bị, ca, ngày nghiệp vụ của file và file CSV; kiểm tiêu đề trước khi gửi; gửi kèm khoá idempotency; hiện kết quả nạp (số số đo, số dòng bị từ chối, số ngoại lệ, số cảnh báo), thông báo file đã xử lý, danh sách dòng bị từ chối kèm số dòng và lý do, dòng ngoại lệ, và danh sách ca thiếu file. Khi khoá hạn nhắc còn trống, khối "không có ca thiếu" dễ gây hiểu lầm nên cần dòng cảnh báo rõ rằng hệ thống chưa kiểm ca thiếu.
### Định nghĩa xong
- [ ] Form báo lỗi trường bắt buộc, ngày không hợp lệ và sai tiêu đề CSV trước khi gửi
- [ ] Kết quả nạp, file trùng, dòng từ chối và dòng ngoại lệ hiện đúng với phản hồi máy chủ trong các ca: hợp lệ, trùng, một phần dòng sai, lỗi cả file
- [ ] Gửi lại sau lỗi mạng dùng cùng khoá idempotency và không nạp đôi
- [ ] Khối ca thiếu file hiện đúng dữ liệu; khi khoá nhắc trống hiện cảnh báo chưa kiểm thay vì "không thiếu"
- [ ] Vai trò nhân viên kho tải được file nhưng không thấy nút xác nhận hay đo lại

## E10-I05-S03 · SCR019 phần cảnh báo, chi tiết, xác nhận, đo lại, lô ảnh hưởng (FE)
- Loại: sub-issue
- Cha: E10-I05
- Vai: FE-1
- Nhãn: type:sub-issue, constraint:haccp-temp
- Spec: SCR019, API-TMP-02, API-TMP-04, API-TMP-01
- Phụ thuộc: E10-I05-S01, E10-I04-S01
- Giai đoạn: 1
### Mô tả
Làm nửa dưới: bảng cảnh báo với mức độ, hạn xác nhận còn lại, cờ quá hạn, cờ đã leo thang, chi tiết cảnh báo (khối thông tin, thời điểm tạo, hạn, trạng thái, case liên kết), nút xác nhận, ô nhập đo lại một chữ số thập phân với hai nhánh kết quả, bảng lô bị ảnh hưởng theo ba loại, và lịch sử file đã tải có phân trang. Danh sách tự làm mới theo khoá chu kỳ cấu hình. Ẩn nút theo vai trò chỉ là tiện ích, quyền thật do máy chủ kiểm.
### Định nghĩa xong
- [ ] Danh sách hiện đủ cột, đếm ngược hạn từ dữ liệu máy chủ, đánh dấu quá hạn và đã leo thang đúng cờ trả về
- [ ] Xác nhận và đo lại cập nhật chi tiết tại chỗ; hai người thao tác cùng lúc thấy thông báo lỗi đã có người làm
- [ ] Ô đo lại chặn nhập sai định dạng và chặn gửi lần hai
- [ ] Bảng lô hiện đúng ba loại xử lý; chuyển sang SCR020 khi cảnh báo đã mở case
- [ ] Trạng thái rỗng, đang tải, lỗi mạng, 403 và 404 đều có hiển thị; chu kỳ tự làm mới đọc từ cấu hình

## E10-I05-S04 · Kiểm thử đầu cuối bám kịch bản chấp nhận SCR019 (QA)
- Loại: sub-issue
- Cha: E10-I05
- Vai: QA-1
- Nhãn: type:sub-issue, constraint:haccp-temp
- Spec: SCR019, FR-TEMP-03, JP-17
- Phụ thuộc: E10-I05-S02, E10-I05-S03
- Giai đoạn: 1
### Mô tả
Viết và chạy kịch bản tự động trên trình duyệt cho 25 kịch bản chấp nhận của SCR019, ưu tiên các ca khó đo bằng tay: biên đúng dải, chuỗi qua mốc đổi phiên bản, hai người xác nhận cùng lúc, đo lại muộn, leo thang đúng một lần, khoá nhắc ca trống. Dữ liệu tạo bằng file mẫu tự sinh, không dùng dữ liệu thật.
### Định nghĩa xong
- [ ] Mỗi kịch bản chấp nhận có một ca kiểm thử trỏ đúng mã kịch bản, kết quả xanh trên môi trường test
- [ ] Ca hai người xác nhận cùng lúc và ca leo thang một lần có chạy lặp, không chập chờn
- [ ] Bộ file CSV mẫu và hướng dẫn chạy lại nằm trong repo kiểm thử, không chứa dữ liệu thật
- [ ] Báo cáo tóm tắt nêu kịch bản không tự động hoá được và lý do

## E10-I06 · Giải phóng hoặc huỷ lô bốn mắt và case sai lệch
- Loại: issue
- Cha: E10
- Nhãn: type:issue, constraint:haccp-temp
- Spec: TMP_LOT_RELEASE_APPROVAL, API-TMP-05, API-TMP-08, SCR020, FR-TEMP-04
- Giai đoạn: 1
### Mô tả
Sau cảnh báo, lô đi từ cách ly sang tạm giữ rồi giải phóng hoặc huỷ, hai bước cuối chỉ qua đề nghị cộng duyệt bốn mắt: người duyệt khác người đề nghị, và với giải phóng còn phải khác người đã đo lại, lô không còn cảnh báo chờ đo lại, xét trên mọi loại liên kết cảnh báo – lô. Case sai lệch dùng chung cho mọi nguồn (nhận hàng, di chuyển, quét xếp hàng, kiểm trước xuất phát, nhiệt độ) và chỉ đóng khi đủ lý do, điều tra, hành động khắc phục, ít nhất một tệp và mọi lô đã được quyết. Màn SCR020 chỉ có wireframe nên bốn API case (API-TMP-05..08) phải viết hợp đồng. Điểm cuối quyết định (API-INV-06), màn SCR008 và API đề nghị thuộc E05 (E05-I05); epic này cung cấp phần luật điều kiện giải phóng và sự kiện mà điểm cuối đó gọi.

## E10-I06-S01 · Luật điều kiện giải phóng sau cảnh báo nhiệt và sự kiện quyết định (BE)
- Loại: sub-issue
- Cha: E10-I06
- Vai: BE-2
- Nhãn: type:sub-issue, constraint:haccp-temp
- Spec: TMP_LOT_RELEASE_APPROVAL, FR-TEMP-04, JP-17, TEMPERATURE_DB
- Phụ thuộc: E10-I02-S01, E10-I04-S01, E01-I16, E02-I03
- Giai đoạn: 1
### Mô tả
Module nhiệt độ cung cấp phần luật mà điểm cuối quyết định (API-INV-06, do E05-I05 dựng phần HTTP, vai trò, bốn mắt, quyết một lần) gọi vào. Luật gồm hai điều kiện chỉ áp cho giải phóng: lô không còn liên kết, ở bất kỳ loại xử lý nào, với một cảnh báo đang chờ đo lại (báo cảnh báo mới nhất), và người duyệt không phải người đã đo lại một cảnh báo của lô trong "đợt hiện tại" tính theo giờ thật kể từ lần giải phóng được duyệt gần nhất, không theo ngày nghiệp vụ. Duyệt huỷ không kiểm hai điều này (chờ khách xác nhận). Khi duyệt xong, module phát sự kiện quyết định giải phóng hoặc huỷ kèm lô, loại quyết định, người duyệt; từ chối thì không phát. Phần việc đọc trường từ named interface của kho phải khớp E05-I05. Nạp file tạo cảnh báo mới đúng lúc duyệt phải tuần tự hoá trên khoá lô.
### Định nghĩa xong
- [ ] Các ca biên của thiết kế về giải phóng (người đo lại tự duyệt, lô chỉ ở danh sách theo dõi, hai đợt liên tiếp, ngày giả lập lùi, cảnh báo mới chen vào, cảnh báo chờ đo lại) có kiểm thử xanh ở tầng luật
- [ ] Kiểm thử tích hợp hai chiều với điểm cuối của E05-I05 cho cả nhánh giải phóng bị chặn và nhánh được duyệt
- [ ] Sự kiện quyết định chỉ phát khi duyệt, đủ trường lô, loại quyết định, người duyệt, ghi cùng giao dịch với đổi trạng thái lô
- [ ] Kiểm thử đồng thời: nạp file sinh cảnh báo mới trong lúc duyệt giải phóng, kết quả là một trong hai thứ tự hợp lệ, không có lô khả dụng còn cảnh báo chờ đo lại
- [ ] Giải phóng khi case còn mở không gỡ dấu chặn của case; phân bổ, pick, xuất phát vẫn bị chặn tới khi đóng case
- [ ] Trường mà named interface của kho trả cho sự kiện quyết định (loại, người duyệt, người đề nghị) được chốt cùng E05-I05 và ghi vào tài liệu; sự kiện chỉ phát khi duyệt, không phát khi từ chối

### Chỗ hở
- GAP-76 quyết định giải phóng được gắn cho màn SCR008 ở danh mục API nhưng cho SCR020 ở danh mục chức năng
- GAP-36 API-INV-06 mang tiền tố kho nhưng chạy ở module nhiệt độ; chia việc giữa E05-I05 và issue này
- GAP-77 chưa rõ duyệt huỷ có phải kiểm đo lại như giải phóng không

## E10-I06-S02 · Case sai lệch: lược đồ, API-TMP-05..08, điều tra, bằng chứng, đóng case (BE)
- Loại: sub-issue
- Cha: E10-I06
- Vai: BE-1
- Nhãn: type:sub-issue, constraint:haccp-temp
- Spec: API-TMP-05, API-TMP-06, API-TMP-07, API-TMP-08, TEMPERATURE_DB
- Phụ thuộc: E10-I02-S01, E05-I01, E05-I07-S01, E01-I16, E01-I18, E05-I10
- Giai đoạn: 1
### Mô tả
Tạo bảng case, liên kết case – lô và tệp đính kèm; viết hợp đồng và cài danh sách case (có mức độ, thời gian còn lại), ghi điều tra, đính kèm bằng chứng (tệp tải trước qua API tải lên chung, kiểm SHA-256), và đóng case. Đóng case chỉ khi có lý do, điều tra, hành động khắc phục, ít nhất một tệp và mọi lô của case đã được quyết; khoá chia sẻ lô trước khi đọc trạng thái; gỡ đúng các dấu chặn loại nhiệt độ của chính case (dấu thu hồi giữ nguyên); case đã đóng thì bất biến. Phát sự kiện đóng case.
### Định nghĩa xong
- [ ] Bốn API có hợp đồng viết xong, chạy được, vai trò và mã lỗi theo quy ước chung
- [ ] Đóng case thiếu một trong ba văn bản hoặc thiếu tệp bị từ chối; còn lô cách ly hoặc tạm giữ bị từ chối kèm danh sách lô
- [ ] Đóng case gỡ dấu chặn nhiệt độ của case đó và giữ nguyên dấu thu hồi khi lô thuộc cả hai
- [ ] Sửa hoặc đính tệp vào case đã đóng bị từ chối
- [ ] Văn bản dài quá giới hạn bị từ chối; tệp sai hash hoặc đã dùng cho bản ghi khác bị từ chối
- [ ] Sự kiện đóng case ghi vào outbox cùng giao dịch
- [ ] Case có cột tham chiếu phiếu dải tạm đã duyệt (mã phiếu từ E05-I07-S01) và truy vấn "lô có case sai lệch nhiệt đang mở" cho giao hàng đọc được

### Chỗ hở
- GAP-76 vị trí quyết định lô giữa SCR008 và SCR020 ảnh hưởng nội dung API-TMP-08

## E10-I06-S03 · Người nghe mở case từ nhận hàng, di chuyển và lệnh của giao hàng (BE)
- Loại: sub-issue
- Cha: E10-I06
- Vai: BE-2
- Nhãn: type:sub-issue, constraint:haccp-temp
- Spec: TMP_LOGGER_INGEST_DEVIATION, TEMPERATURE_DB, FR-TEMP-04
- Phụ thuộc: E10-I06-S02, E04-I03-S02, E05-I03-S01, E05-I04-S01
- Giai đoạn: 1
### Mô tả
Module nhiệt độ nghe sự kiện nhận hàng bị từ chối hoặc tạm giữ và sự kiện di chuyển lô để mở case nguồn nhận hàng hoặc di chuyển khi lý do là lệch dải nhiệt; đồng thời cung cấp lệnh để module giao hàng mở case nguồn quét xếp hàng và kiểm trước xuất phát. Phiếu chuyển dải tạm không mở khoá luật. Không mở case cho lý do thu hồi, đó là dấu chặn riêng của truy xuất. Người nghe idempotent theo mã sự kiện nên phát lại không tạo case thứ hai. Mã tuyến chỉ lưu như ID mờ do giao hàng truyền.
### Định nghĩa xong
- [ ] Sự kiện lệch dải lúc nhận mở case kể cả khi dòng bị từ chối và chưa có lô; di chuyển đúng dải thì bỏ qua
- [ ] Phát lại cùng sự kiện hai lần chỉ có một case
- [ ] Lệnh mở case của giao hàng chạy trong giao dịch của giao hàng và rollback cùng nó khi lỗi
- [ ] Ca phiếu dải tạm: vẫn chặn và vẫn mở case
- [ ] Không có đường nào mở case cho lý do thu hồi (có kiểm thử âm)
- [ ] Lệnh mở case cho nguồn quét xếp hàng và kiểm trước xuất phát gắn đúng lô; mỗi chuyến tối đa một case trước xuất phát đang mở, lần kiểm sau nhập vào case đó (E07-I03-S01 và E07-I04-S01 dùng lệnh này)

### Chỗ hở
- GAP-78 việc ép tạm giữ khi nhiệt độ lệch dải lúc nhận là cách hiểu của nhà thầu, chưa khách xác nhận

## E10-I06-S04 · Màn SCR020: xử lý case sai lệch (FE)
- Loại: sub-issue
- Cha: E10-I06
- Vai: FE-1
- Nhãn: type:sub-issue, constraint:haccp-temp
- Spec: SCR020, FR-TEMP-04, JP-17
- Phụ thuộc: E10-I06-S02
- Giai đoạn: 1
### Mô tả
Màn chỉ có wireframe bố cục. Làm hàng đợi case theo mức độ và thời gian còn lại, form chi tiết case (lý do, điều tra, hành động khắc phục, đính tệp với giới hạn kích thước và loại tệp lấy từ cấu hình chung), nút đóng case, liên kết sang màn quyết định lô, và liên kết mã tuyến chỉ hiện khi vai trò được mở màn đích. Nơi quyết định giải phóng hoặc huỷ phải theo kết luận ở GAP-76 trước khi vẽ nút.
### Định nghĩa xong
- [ ] Hàng đợi hiện cột mức độ và thời gian còn lại từ dữ liệu máy chủ, sắp theo mức độ
- [ ] Đóng case thiếu trường hoặc thiếu tệp hiện lỗi đúng trường từ máy chủ; case đã đóng chuyển sang chế độ chỉ xem
- [ ] Tệp đính kèm chặn sai loại và quá cỡ ngay ở giao diện, giá trị đọc từ cấu hình
- [ ] Liên kết mã tuyến là văn bản thường với vai trò không được mở màn đích
- [ ] Trạng thái rỗng, lỗi mạng, 403 và 404 có hiển thị; quản trị viên không thấy nút quyết định lô
### Chỗ hở
- GAP-76 chưa rõ quyết định lô nằm ở màn nào

## E10-I07 · Báo cáo HACCP: độ đủ hồ sơ và bộ hồ sơ theo kỳ
- Loại: issue
- Cha: E10
- Nhãn: type:issue, constraint:haccp-temp
- Spec: SCR021, API-TMP-09, API-TMP-10, BR-HACCP-01, JP-04
- Giai đoạn: 1
### Mô tả
Màn báo cáo đo độ đủ của hồ sơ HACCP theo kỳ so với nhịp ghi của chính công ty (logger 15 phút, mỗi lần nhập và xuất, cảnh báo và thời gian phản hồi, đo lại, case sai lệch), liệt kê các chỗ thiếu thành mục hàng đợi không tự đóng, và cho tải bộ hồ sơ theo kỳ kèm các phiên bản ngưỡng có hiệu lực trong kỳ. Màn chỉ có wireframe, hai API chưa có hợp đồng nên phải viết hợp đồng. Hai loại hồ sơ vệ sinh theo ca và khách – tài xế theo lượt xuất hiện trên wireframe nhưng chưa có nơi lưu dữ liệu.

## E10-I07-S01 · API độ đủ và bộ hồ sơ theo kỳ (BE)
- Loại: sub-issue
- Cha: E10-I07
- Vai: BE-1
- Nhãn: type:sub-issue, constraint:haccp-temp
- Spec: API-TMP-09, API-TMP-10, BR-HACCP-01, JP-04
- Phụ thuộc: E10-I02-S03, E10-I08
- Giai đoạn: 1
### Mô tả
Viết hợp đồng và cài API-TMP-09 (mục hàng đợi độ đủ theo kỳ, theo từng loại điểm đo) và API-TMP-10 (bộ hồ sơ theo kỳ gồm phiên bản ngưỡng có hiệu lực trong kỳ). Khi kỳ chứa nhiều dòng hơn trần kết quả chung thì từ chối chứ không trả một phần. Mục thiếu không bao giờ tự đóng. Chưa có điểm cuối đóng mục trong danh mục API dù thiết kế dữ liệu cho phép người dùng đóng kèm lý do; điểm cuối này chỉ được thêm sau khi khách trả lời ai được đóng, theo câu hỏi ở E10-I08.
### Định nghĩa xong
- [ ] Hai API có hợp đồng viết xong và chạy được; kỳ vượt trần kết quả bị từ chối với mã lỗi chung
- [ ] Độ đủ tính đúng cho các loại điểm đo: logger theo ca, mỗi lần nhận, mỗi lần xuất, cảnh báo, đo lại, case; có kiểm thử với dữ liệu có khoảng trống
- [ ] Bộ hồ sơ liệt kê đúng các phiên bản ngưỡng có hiệu lực trong kỳ, kể cả kỳ cắt ngang mốc đổi phiên bản
- [ ] Quyết định về điểm cuối đóng mục được ghi vào tài liệu theo câu trả lời của E10-I08, hoặc ghi rõ giữ chế độ chỉ xem
### Chỗ hở
- GAP-74 danh mục API không có điểm cuối đóng mục hàng đợi
- GAP-79 định dạng đầu ra, độ dài kỳ tối đa và nguồn bản ghi duyệt hằng ngày hằng tháng chưa có

## E10-I07-S02 · Màn SCR021: báo cáo HACCP (FE)
- Loại: sub-issue
- Cha: E10-I07
- Vai: FE-1
- Nhãn: type:sub-issue, constraint:haccp-temp
- Spec: SCR021, BR-HACCP-01, JP-04
- Phụ thuộc: E10-I07-S01
- Giai đoạn: 1
### Mô tả
Làm bộ lọc kỳ, bảng phiên bản ngưỡng có hiệu lực trong kỳ (không vẽ số cứng, lấy từ dữ liệu), khối độ đủ theo từng loại điểm đo, danh sách chỗ thiếu có ghi chú rằng hệ thống không tự đóng, và nút tải bộ hồ sơ theo kỳ. Hai dòng hồ sơ vệ sinh ca và khách – tài xế chỉ hiển thị khi có nguồn dữ liệu, theo kết luận ở GAP-80.
### Định nghĩa xong
- [ ] Bộ lọc kỳ kiểm ngày bắt đầu không sau ngày kết thúc; kỳ quá lớn hiện thông báo từ chối thay vì báo cáo cụt
- [ ] Khối độ đủ và danh sách chỗ thiếu hiện đúng dữ liệu API trong ca có và không có khoảng trống
- [ ] Tải bộ hồ sơ theo kỳ cho ra tệp đúng định dạng đã chốt
- [ ] Trạng thái rỗng, lỗi mạng, 403 có hiển thị; vai trò ngoài ma trận không vào được màn
### Chỗ hở
- GAP-80 hai loại hồ sơ vệ sinh và khách – tài xế chưa có bảng lưu

## E10-I08 · Làm rõ với khách: các điểm chặn của nhiệt độ và HACCP
- Loại: issue
- Cha: E10
- Vai: BrSE
- Nhãn: type:issue, type:clarification, constraint:haccp-temp
- Spec: SCR019, TMP_LOGGER_INGEST_DEVIATION, TMP_LOT_RELEASE_APPROVAL, BR-HACCP-01
- Giai đoạn: 1
### Mô tả
Gom các câu khách đang để mở và đang chặn việc của epic thành một lượt hỏi dạng câu đóng có phương án mặc định: (1) hạn nhắc ca thiếu file và giờ ca thật, khoá này trống và đang chặn go-live; (2) ai được đóng mục hàng đợi thiếu bản ghi, có cần lý do, có đóng khi file bù về không; (3) cách suy ra mã ca cho file adapter vì tên file không có ca, cùng cột CSV thật và quy tắc múi giờ; (4) ngưỡng phân mức critical và major cùng người nhận leo thang; (5) vai trò duyệt giải phóng, duyệt huỷ, và duyệt huỷ có phải kiểm đo lại không; (6) định dạng bộ hồ sơ theo kỳ, độ dài kỳ tối đa, và nguồn hồ sơ vệ sinh, khách – tài xế, duyệt giám sát; (7) khi thêm hoặc đổi một thiết bị ghi nhiệt thì ai làm, theo bước nào, và danh mục thiết bị có cần thêm khu, người phụ trách, ngày hiệu lực như màn quản lý vị trí đang vẽ, cùng thời hạn lưu giữ danh mục thiết bị. Không tự đặt giá trị nghiệp vụ thay khách.
### Định nghĩa xong
- [ ] Danh sách câu hỏi đóng gửi đúng người trả lời (chất lượng, CNTT), mỗi câu có phương án mặc định và nêu issue bị ảnh hưởng
- [ ] Câu trả lời được ghi vào sổ quyết định và, nếu đổi giá trị, trỏ tới khoá cấu hình liên quan
- [ ] Khoá hạn nhắc ca thiếu file có giá trị, hoặc có quyết định bằng văn bản về cách go-live khi khoá còn trống
- [ ] Câu về quy trình thêm hoặc đổi thiết bị và các cột còn thiếu của danh mục thiết bị có trong gói câu hỏi; câu trả lời chuyển cho E10-I02-S01 và E03-I06-S02, hai issue này không thêm cột khi chưa có trả lời
- [ ] Các issue E10-I03, E10-I04-S02, E10-I06-S01, E10-I07-S01 được cập nhật theo câu trả lời
### Chỗ hở
- GAP-73 khoá hạn nhắc ca đang chặn go-live
- GAP-74 người đóng mục hàng đợi
- GAP-77 duyệt huỷ có kiểm đo lại không
- GAP-84 quy trình thêm hoặc đổi thiết bị ghi nhiệt và các cột còn thiếu của danh mục thiết bị
