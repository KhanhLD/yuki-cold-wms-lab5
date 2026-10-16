# AI-report — LAB-5 đã dùng AI thế nào

Gần như toàn bộ phần viết, kiểm và đăng của LAB-5 do AI làm, chủ dự án (`KhanhLD`) giữ phần quyết định. Cách làm chung: AI viết backlog thành file ở máy (backlog-as-code), script kiểm, một reviewer độc lập khác model duyệt ở các cổng, rồi script mới đăng lên GitHub và đọc ngược để đối chiếu. Chủ dự án ngồi làm việc trực tiếp ba lần (lần 1: 1,5 giờ; lần 2: 1 giờ; lần 3 ở bước đóng bài) và trả lời thêm vài câu hỏi giữa chừng khi kết quả lệch kế hoạch. Giờ máy chạy không tính vào giờ báo cáo.

Từ dùng trong file: **phiên chính** là phiên Claude điều phối cả bài (Opus 5.5); **agent** là phiên con được giao một việc hẹp với file riêng; **cổng** là điểm dừng mà reviewer độc lập phải nhận thì mới làm bước sau.

## 1. Skill đã dùng và ý đồ prompt cho từng việc

**Trước khi chạy — chốt cách làm và lập plan.** `tkm:brainstorm` (mức high, chế độ hỏi từng câu) để chủ dự án chốt cách làm: backlog-as-code, ba cổng tự qua, chủ dự án chỉ ngồi ba lần. `tkm:create-plan` (mức high) lập plan 8 phase; plan qua một lượt rà độc lập và một lượt rà trước khi chạy rồi mới được duyệt. Toàn bộ phần chạy sau đó do `tkm:takumi --auto` điều phối theo plan.

**Chia việc (break-down).** Không nhờ một prompt sinh cả backlog. Ý đồ prompt là chia nhỏ để kiểm được:

- 5 agent viết song song (Sonnet 5.5, mức high), mỗi agent giữ riêng vài file epic trong `backlog/`, nhận cùng một bản quy ước định dạng (độ hạt ≤ khoảng 3 ngày công mỗi lá, mỗi lá đúng một vai, 2–8 ô định nghĩa xong viết thành điều quan sát được, luật giờ lái 2024 chỉ nằm ở #10). Gặp việc thuộc nhóm khác thì không tự viết mà ghi phiếu chuyển; hai vòng chuyển phiếu sau đó gỡ được 2 chỗ hai nhóm cùng nhận một đơn vị.
- 1 agent tổng hợp (Opus 5.5, mức medium) gộp mục "Chỗ hở" của mọi item thành bảng 98 dòng và dựng nháp PREMISE (15 dòng sửa spec, 16 giả định, 7 hướng chốt).
- Script kiểm backlog (định dạng, vai, phụ thuộc không vòng, luật giờ lái không lọt ra ngoài #10) và script kiểm public (chép đề, tên người, email, đường dẫn máy) chạy sau mỗi lượt viết.
- Chấm thử bằng `tkm:estimate-swp` (mức low, nguồn file ở máy, không đẩy điểm đi đâu) trước khi public, chỉ để bắt lá quá to: vòng 1 chấm 202 lá, 1071 SWP, 1 lá bị khuyên tách (#129) → tách thành 4 sub-issue → vòng 2 chấm 205 lá, 1021 SWP, 0 lá bị khuyên tách.
- Script đăng tạo 279 issue có quan hệ cha – con và gắn vào Project (821 lệnh, không lần nào chạm giới hạn tốc độ), rồi script đọc ngược đối chiếu toàn bộ.

**Chấm SWP.** `tkm:estimate-swp` chạy thật trên GitHub Project, mức high, không đẩy kết quả sang hệ thống KPI (dự án lab không có ở đó, nên dòng kiểm estimate cũ trong HTML ghi "unavailable" và skill chấm hết). Skill đọc Project, tự chọn 205 lá để chấm và 74 cha lấy tổng con; trước khi gọi người chấm, phiên chính so tập lá / tập cha của skill với tập đã đọc lúc đăng (khớp 205 / 74) để không chấm trùng cha – con. Ý đồ prompt của người chấm: cùng một bảng tra Scope × Complexity → SWP, mỗi lô 41 issue, trả đúng hợp đồng JSON của skill; skill tự kiểm hợp đồng. Có hai người chấm:

- Người chấm A (5 agent Sonnet) chấm cả 205 lá → 1048 SWP. Đây là điểm chính thức.
- Người chấm B (Opus, cùng bảng tra, không thấy kết quả A) chỉ chấm 15 dòng độ tin thấp (14 issue hỏi khách và 1 issue đào tạo), để lộ chỗ điểm không chắc chứ không để thay điểm.

Một chỗ không dùng nguyên skill: worker chấm riêng của skill không có công cụ đọc file, phải dán cả lô vào prompt (5 lô, khoảng 330 nghìn ký tự). Phiên chính thay bằng agent thường cùng model, chỉ được đọc 2 file (bảng tra, lô) và ghi 1 file kết quả; bảng tra, hợp đồng đầu ra, phần chọn tập, kiểm hợp đồng, chọn issue mẫu và dựng HTML vẫn là của skill. Chủ dự án đã xem và nhận cách này ở lần ngồi 2. Skill được dùng nguyên bản, không sửa dòng nào.

Skill tự chọn 5 issue mẫu (nhỏ nhất, dưới trung vị, lớn nhất, ngoại lệ, chỗ hai người chấm lệch nhau nhất) để chủ dự án kiểm theo thứ tự mức → điểm → lý do, thay vì đọc 205 dòng. `tkm:confidence` được dùng để gắn độ tin cho từng dòng.

**Quy ra giờ.** Ý đồ prompt: không để AI tự đoán giờ. Phiên chính hỏi chủ dự án giờ ước cho 5 issue mẫu và thêm 1 neo ở dải vừa (#155, 8 SWP, việc API thuần kỹ thuật), vì 3 trong 5 mẫu là việc hỏi khách hoặc đào tạo và sẽ kéo hệ số lệch. Hệ số k = trung vị giờ / SWP của 6 neo = 3,00; giờ mỗi lá = SWP × k làm tròn 0,5, cha = tổng con. Phép nhân và cộng do script làm, kết quả ghi thẳng vào trường "Giờ ước lượng" trên Project.

**Báo cáo tiến độ.** Một agent xếp sprint (Opus 5.5, mức medium) được giao: xếp các lá vào Sprint 1–3 theo phụ thuộc ghi ở chính lá đó và theo năng lực từng vai (mỗi người ≤ 60 giờ / sprint), rồi giả lập Sprint 1 với sự kiện lệch có ngày, mỗi sự kiện gắn một câu hỏi mở có thật trong LAB-4 — không bịa câu trả lời của khách. Script kiểm lại bản giả lập (năng lực, phụ thuộc, tổng), rồi sinh bảng trạng thái, burndown và tổng theo epic từ ảnh chụp Project; agent viết báo cáo chỉ dán bảng, không tự cộng số.

**AI Agent ghi SWP lên Project.** Một agent (Sonnet 5.5, mức medium) đóng vai "AI Agent ghi SWP": chỉ được chạy script ghi đã duyệt, đọc từ kế hoạch ghi 279 mục (Point (SWP), Giờ ước lượng, Người phụ trách giả định, Iteration, Status, đóng / mở issue). Prompt cấm viết lệnh GraphQL tay, cấm sửa tiêu đề / thân / nhãn, cấm đọc hay in token. Trình tự: chạy thử (292 lệnh dự kiến) → ghi thử 1 mục (#132) và đọc lại → chạy đủ → đọc ngược. Lượt chạy đủ đầu tiên dừng giữa chừng (xem §3); sau khi script được sửa, lượt chạy lại ghi xong không lỗi, và đọc ngược cho kết quả 279 / 279 mục, 0 khác biệt.

## 2. Ai review kết quả của AI, ở bước nào

Nguyên tắc: không lấy lời "đã xong" của agent làm bằng chứng. Sau mỗi agent, phiên chính tự chạy lại script; ở mỗi cổng, một reviewer khác model (Fable 5.1) không viết dòng nào của phần đang duyệt sẽ đọc lại và tự chạy lại kiểm tra.

| Bước | Ai review | Phát hiện gì | Xử lý thế nào |
|---|---|---|---|
| Trước khi chạy (lần ngồi 1, 1,5 giờ) | Chủ dự án, sau hai lượt rà plan độc lập | Lượt rà plan cho 7,5 / 10 với 16 điểm cần sửa; lượt rà trước khi chạy thấy 1 lỗi mức High: skill có thể chấm trùng cha – con | Sửa đủ 16 điểm rồi duyệt plan; thêm bước so tập lá / tập cha của skill với tập đã đăng trước mỗi lần chấm |
| Cổng 01 — script (trước khi viết backlog) | Reviewer độc lập: đọc trọn 5 script kiểm, chạy lại 11 bộ self-test, thử phá trên bản sao | Lượt 1 trả lại: 1 High (tiêu đề issue được đăng mà không qua kiểm public), 2 Medium (ca âm của self-test ghi trường không chứng minh "0 lệnh ghi"; chèn dấu `·` hoặc `•` giữa câu chép làm dò trùng bỏ sót), 7 Low | Sửa cả 3 lỗi chặn, thêm ca kiểm âm cho từng lỗi; lượt 2 reviewer tự lặp lại 3 thử nghiệm phá → nhận, thêm 1 Low. Các Low được sửa hoặc ghi thành giới hạn chấp nhận |
| Giữa phase chia việc | Chủ dự án (câu hỏi giữa chừng) | Số sub-issue vượt dự kiến (138 so với khoảng 80); 4 mục của PREMISE đổi hành vi hệ thống | Chủ dự án giữ số sub-issue (trần mới 150) vì màn chỉ có wireframe phải tách BE / FE; chốt 4 mục, trong đó có việc thêm khối seal vào SCR018 và tách #89 thành phần BE và FE |
| G1 — backlog trước khi public | Reviewer độc lập: chạy lại script, so 279 thân issue với file nguồn, đối chiếu 16 issue mẫu với spec LAB-4 | Nhận ngay lần đầu: 0 Critical / High / Medium, 7 Low | Sửa câu chữ một ô định nghĩa xong của #235 (vẫn đọc như việc còn chờ quyết dù đã quyết); thêm câu giải thích ký hiệu nội bộ ở đầu PREMISE.md; README và task-breakdown trỏ tới bảng chỗ hở vì thân issue không đăng mục này; kể rõ phần worker chấm thay thế ở §1 file này |
| Sau khi đăng | Script đọc ngược (tự động) | 279 / 279 issue khớp; lần dựng Project đầu bắt được lỗi script: sprint đã kết thúc bị GitHub xếp sang danh sách khác nên mất Sprint 1 | Sửa truy vấn và bản giả lập GitHub dùng cho self-test, ca cũ đỏ trước khi sửa / xanh sau khi sửa, chạy lại |
| Lần ngồi 2 (1 giờ) | Chủ dự án | 5 issue mẫu: #53 giữ 1, #131 nâng 5 → 8, #268 nâng 10 → 16, #19 giữ 10, #74 giữ 2 dù người chấm B cho 5 | Hiệu chỉnh trong skill (tổng 1048 → 1057); chủ dự án chỉnh tay thêm #102 (5 → 8) ngoài skill nên Project có 1060 SWP; cho giờ 6 neo → k = 3,00; nhận các quyết định AI tự chốt ở các phase trước |
| Xếp sprint và ghi trường | Chủ dự án (2 câu hỏi), phiên chính chạy lại script, script đọc ngược | Hai lượt xếp đầu lệch ý đồ plan; lượt ghi đầu dừng lỗi đóng issue | Chủ dự án chỉnh hướng hai lần (chi tiết ở §3); phiên chính dựng lại kế hoạch ghi và kiểm giả lập bằng script, không tin báo cáo agent; đọc ngược sau ghi 0 khác biệt |
| 6 file nộp | Script kiểm đủ phần / đủ cột, kiểm public, kiểm tổng số khớp Project | — | Mỗi file phải qua cả ba script trước cổng sau |

<!-- G2/G3: phiên chính bổ sung -->

## 3. Điểm đặc biệt khi làm với AI

**Chỗ hiệu quả.**

- **Chạy thật rồi đối chiếu ngay bắt được lỗi mà self-test không thấy.** Ba lỗi script thật chỉ lộ ra khi chạy trên GitHub thật, nhờ đọc ngược hoặc nhờ script dừng đúng chỗ thay vì ghi tiếp: sprint đã kết thúc bị chuyển sang danh sách khác nên mất Sprint 1; script khớp dòng báo cáo theo mã item (đúng với nguồn file của lượt chấm thử) nên lệch 205 / 205 dòng với báo cáo lấy từ GitHub; workflow tự đóng issue của Project đóng trước bước đóng của script. Cả ba lần, self-test vẫn xanh vì dữ liệu thử không có hình dạng đó: bản giả lập GitHub thiếu hành vi thật, còn ca thử của script khớp báo cáo chỉ có dạng nguồn file. Bài học: self-test chỉ phủ những hình dạng dữ liệu mình đã biết; đọc ngược trên hệ thống thật mới là phép kiểm cuối.
- **Người chấm thứ hai cho thấy điểm không chắc ở đâu.** B cao hơn A ở cả 15 dòng độ tin thấp (13 dòng lệch 1 bậc, 2 dòng lệch từ 2 bậc trở lên): A chỉ tính công soạn câu hỏi gửi khách, B tính cả rà spec, chờ trả lời và cập nhật issue liên quan. Nhờ vậy chủ dự án biết phải xem kỹ nhóm việc hỏi khách, và chốt rõ: rà spec là công của issue làm tính năng, thời gian chờ khách không tính điểm.
- **Chủ dự án kiểm 5 issue mẫu thay vì đọc 205 dòng.** Tổng thời gian ngồi của chủ dự án tới hết phần chấm điểm là 2,5 giờ cho một backlog 279 issue.
- **Reviewer khác model ở cổng bắt được lỗ mà người viết không thấy.** Lỗi tiêu đề issue không qua kiểm public chỉ lộ ra khi reviewer thử đổi tiêu đề sau khi đã kiểm và thấy script vẫn cho qua.
- **Chấm thử trước khi public** bắt được lá quá to (#129) khi còn sửa được ở file máy, không phải đăng lại issue.

**Chỗ chưa tốt khi làm thật (thời gian, công sức, token).**

- **Phiên làm việc bị gián đoạn.** Chủ dự án phải đóng máy giữa phase 05; hai agent đang chạy bị dừng và phải gọi lại từ đầu theo brief đã lưu. Không mất dữ liệu nhưng mất thời gian chạy lại, và phải viết brief đủ chi tiết để agent mới làm tiếp được.
- **Agent bị chặn vì lô một dòng quá dài.** Ở lượt chấm thử, lô ghi thành một dòng hơn 60 nghìn ký tự nên agent chấm không đọc được; phải ghi lại thành nhiều dòng và chấm lại. Ở lượt chấm thật, lô 1 bị skill trả lại vì 5 dòng "không rõ vai" lại ghi độ tin cao hoặc trung bình, phải sửa một lần.
- **Xếp sprint phải làm ba lượt.** Lượt 1 đọc phụ thuộc quá chặt (lá thừa kế phụ thuộc của cha): Sprint 1 chỉ dùng 180 / 300 giờ, FE và QA ngồi không, dự báo trễ bị phóng đại. Lượt 2 đúng phụ thuộc nhưng dời lõi #10 sang Sprint 2 và để #116 xong khi việc nó phụ thuộc (#36) chưa xếp. Chủ dự án phải chỉnh hướng hai lần (đọc phụ thuộc riêng của lá; lõi #10 trước, đăng nhập làm một phần); lượt 3 mới đạt. Khi ghi lên Project lại gặp lỗi tự đóng issue ở trên.
- **Skill không lan hiệu chỉnh chọn lọc được.** Chủ dự án muốn nâng điểm #131 và #268 "chỉ riêng issue này", và muốn #102 (cùng kiểu xử lý file như #268) cũng nâng; skill chỉ lan hiệu chỉnh theo nguyên nhóm, nên #102 phải chỉnh tay ngoài skill. Kết quả là HTML ghi 1057 SWP còn Project ghi 1060 SWP; estimate.md phải giải thích chênh này.
- **Token và công điều phối.** Worker chấm riêng của skill buộc dán cả lô vào prompt (5 lô, khoảng 330 nghìn ký tự), nên phải đổi sang agent đọc file. Số token thật của từng bước không được đo, nên file này không đưa ra con số. Số sub-issue vượt dự kiến (138 so với khoảng 80) làm tăng số lệnh đăng và số đợt chấm. Đăng 279 issue chạy nền khoảng 1 giờ 25 phút.

**Chỗ chưa có ý tưởng tốt.**

- Làm sao để bản giả lập GitHub dùng cho self-test bám được hành vi thật (sprint đã kết thúc, workflow tự đóng) mà không phải gặp lỗi một lần trên hệ thống thật mới biết.
- Làm sao cho skill nhận hiệu chỉnh "chỉ riêng issue này" hoặc "chỉ những issue giống thế này" mà không phải chỉnh tay ngoài skill rồi giải thích chênh.
- Việc hỏi khách: SWP đo công làm, không đo thời gian chờ, mà rủi ro trễ lại nằm ở thời gian chờ. Hiện chỉ xử lý bằng cách đưa câu hỏi mở vào rủi ro của báo cáo tiến độ; chưa có cách đưa nó vào điểm hay giờ một cách nhất quán.
- Chưa có cách đo token theo từng bước để so cách làm nào rẻ hơn.
