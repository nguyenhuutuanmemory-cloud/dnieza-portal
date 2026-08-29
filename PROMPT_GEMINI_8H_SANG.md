# Prompt cho Gemini — cập nhật DNIEZA_News_Database lúc 8h00 hằng ngày

Dán toàn bộ phần trong khung dưới đây làm chỉ dẫn cho tác vụ tự động.
WebApp đã có lớp phòng vệ riêng, nhưng prompt càng chặt thì càng ít văn bản bị gắn nhãn
"Cần rà lại" (xem mục [Cách tự kiểm mỗi tuần](#cách-tự-kiểm-mỗi-tuần) ở cuối file).

---

## PROMPT

```
NHIỆM VỤ
Mỗi ngày, đọc chuyên mục "Triển khai Văn bản pháp luật mới" của Ban Quản lý các KCN Đồng Nai:
https://dnieza.dongnai.gov.vn/Pages/news.aspx?CatId=104

Với mỗi BÀI VIẾT MỚI chưa có trong Google Sheet, bóc tách thành các dòng dữ liệu theo chu trình
PDCA và GHI THÊM (append) vào cuối Sheet. Đối tượng phục vụ: cán bộ HSE và pháp chế của một
nhà máy sản xuất nằm trong KCN Đồng Nai.

GOOGLE SHEET ĐÍCH (trang tính đầu tiên; dòng tiêu đề ở hàng 1, dữ liệu bắt đầu từ hàng 2
và kéo dài tới hàng cuối cùng có dữ liệu — số dòng TĂNG LÊN sau mỗi lần chạy, TUYỆT ĐỐI
KHÔNG giả định một hàng cuối cố định nào):
https://docs.google.com/spreadsheets/d/1PVid8IX8djnxoa4LEUBGve9rDz3p3DCP8oSbPcwM78A/edit

QUY TẮC BẮT BUỘC — vi phạm bất kỳ mục nào thì BỎ QUA dòng đó, không được đoán:

1. CHỐNG TRÙNG — LÀM ĐÚNG 4 BƯỚC NÀY TRƯỚC KHI GHI BẤT KỲ DÒNG NÀO

   BƯỚC 1 — Đọc Sheet trước, ghi sau.
   Đọc TOÀN BỘ cột A (ID) và cột D (So_Hieu_Van_Ban) từ hàng 2 đến hàng cuối cùng có dữ
   liệu. Đếm và ghi lại số dòng đọc được. Nếu không đọc được đầy đủ hai cột này vì bất kỳ
   lý do gì: DỪNG LẠI, KHÔNG GHI GÌ CẢ, báo lỗi. Ghi mù khi chưa đọc được Sheet là lỗi
   nghiêm trọng nhất — thà bỏ lỡ một ngày còn hơn tạo dòng trùng.

   BƯỚC 2 — Chuẩn hóa trước khi so sánh số hiệu.
   Cùng một văn bản có thể được viết nhiều kiểu khác nhau. Cách chuẩn hóa: CẮT LẤY CỤM
   SỐ HIỆU BẮT ĐẦU TỪ CHỮ SỐ ĐẦU TIÊN, bỏ hết phần chữ đứng trước (như "Nghị định số",
   "Thông tư số") và bỏ hết phần đuôi phía sau (như "ngày 31/7/2026"), rồi viết HOA.
   Ví dụ tất cả các cách viết sau là MỘT văn bản:
     "Nghị định số 110/2026/NĐ-CP"  =  "110/2026/NĐ-CP"  =  "110/2026/nđ-cp"
     "Số 45/2026/TT-BKHCN ngày 31/7/2026"  =  "45/2026/TT-BKHCN"
   Lưu ý dấu chấm trong số hiệu là MỘT PHẦN của số hiệu, không được cắt bỏ:
   66.17/2026/NQ-CP và 66.19/2026/NQ-CP là HAI nghị quyết KHÁC NHAU.
   Đặc biệt cảnh giác với văn bản đổi tên cơ quan ban hành: 11/2026/TT-BNNMT và
   11/2026/TT-BTNMT là CÙNG một Thông tư (Bộ Tài nguyên Môi trường đã sáp nhập thành Bộ
   Nông nghiệp và Môi trường). Gặp trường hợp này: coi là TRÙNG, không ghi thêm.

   BƯỚC 3 — Chống trùng cả bên trong lô hôm nay.
   Bài tổng hợp dạng "Một số văn bản ... có hiệu lực từ tháng N" thường liệt kê lại văn bản
   đã có bài chi tiết riêng. Sau khi bóc tách xong toàn bộ lô, hãy so các dòng trong lô với
   NHAU trước khi so với Sheet. Nếu một văn bản xuất hiện ở cả bài chi tiết lẫn bài tổng hợp:
   GIỮ dòng từ BÀI CHI TIẾT (nội dung đầy đủ hơn), BỎ dòng từ bài tổng hợp.

   BƯỚC 4 — So và loại.
   KHÔNG ghi dòng nếu ID đã tồn tại trong cột A.
   KHÔNG ghi dòng nếu số hiệu (sau chuẩn hóa ở Bước 2) đã tồn tại trong cột D.
   NGOẠI LỆ QUAN TRỌNG: giá trị "CHƯA XÁC ĐỊNH" trong cột D KHÔNG BAO GIỜ được tính là
   trùng. Sheet đang có nhiều dòng mang giá trị này; nếu coi chúng là trùng thì mọi văn bản
   chưa rõ số hiệu sẽ bị chặn vĩnh viễn. Với các dòng này chỉ chống trùng bằng ID.
   Văn bản hợp nhất (VBHN) KHÔNG phải bản trùng của nghị định gốc — đó là văn bản riêng,
   có số hiệu riêng, vẫn ghi bình thường.

   Chỉ GHI THÊM (append) vào cuối. TUYỆT ĐỐI KHÔNG xoá, không sửa, không ghi đè dòng cũ.

   BÁO CÁO BẮT BUỘC sau mỗi lần chạy, nêu rõ 4 con số:
   số dòng đọc được ban đầu / số văn bản bóc tách được / số dòng bị loại vì trùng (kèm số
   hiệu của từng dòng bị loại) / số dòng thực sự đã ghi thêm.

2. ID PHẢI BỀN VÀ LẶP LẠI ĐƯỢC
   - Lấy NewsId trong URL bài viết: .../newsdetail.aspx?NewsId=5086&CatId=104  ->  ID = DNIEZA-5086
   - Nếu MỘT bài chứa NHIỀU văn bản (bài tổng hợp) thì đánh số hậu tố:
     DNIEZA-5061-1, DNIEZA-5061-2, DNIEZA-5061-3...
   - Chạy lại nhiều lần phải cho ra cùng một ID. KHÔNG dùng số thứ tự tăng dần theo ngày.

3. MỘT BÀI CÓ THỂ CHỨA NHIỀU VĂN BẢN
   - Các bài dạng "Một số văn bản quy phạm pháp luật ... có hiệu lực từ tháng N" là bài TỔNG HỢP.
   - Phải tách thành NHIỀU DÒNG, mỗi văn bản một dòng. Đây là loại bài dễ bị bỏ sót nhất.

4. LINK GỐC
   - Link_Goc BẮT BUỘC là link BÀI CHI TIẾT, dạng:
     https://dnieza.dongnai.gov.vn/Pages/newsdetail.aspx?NewsId=<số>&CatId=<số>
   - Phần CatId: CHÉP ĐÚNG giá trị có trong URL thật của bài. KHÔNG ép về 104.
     Đa số bài nằm ở CatId=104, nhưng có bài thuộc 99, 102, 105, 106, 108 — ép cứng
     về 104 sẽ tạo ra link hỏng, dẫn người dùng tới bài khác hoặc trang trống.
   - KHÔNG được thay bằng link trang danh mục (news.aspx?CatId=...).
   - Nếu không xác định được bài chi tiết: BỎ QUA văn bản đó, không ghi dòng.

5. LĨNH VỰC — CHỌN ĐÚNG 1 TRONG 6 GIÁ TRỊ, KHÔNG TỰ TẠO MỚI, KHÔNG ĐỔI CÁCH VIẾT:
   Môi trường
   Lao động & An toàn
   Đầu tư & Vận hành KCN
   Tiêu chuẩn & Chất lượng
   Đất đai & Xây dựng
   Hành chính & Pháp lý doanh nghiệp

6. ĐỊNH DẠNG Ô
   - Ngay_Cap_Nhat và Ngay_Hieu_Luc: đúng dạng YYYY-MM-DD, ví dụ 2026-09-25.
   - HAI Ô NGÀY PHẢI LÀ VĂN BẢN THUẦN (plain text), KHÔNG được để Google Sheets tự
     chuyển thành kiểu Ngày tháng. Nếu bị chuyển, Sheet sẽ xuất ra dạng khác (ví dụ
     9/25/2026) và WebApp sẽ tính sai hạn hiệu lực mà không báo lỗi.
     Cách bảo đảm: định dạng cột B và cột F là "Văn bản thuần túy" trước khi ghi.
   - Ngay_Cap_Nhat = ngày đăng bài trên cổng DNIEZA (KHÔNG phải ngày hôm nay).
   - TUYỆT ĐỐI KHÔNG dùng phím xuống dòng trong bất kỳ ô nào. Dùng dấu chấm phẩy để ngăn ý.
   - Không dùng ký tự < và > trong nội dung.

7. KHÔNG BỊA
   - Chỉ ghi số hiệu, cơ quan ban hành, ngày hiệu lực nếu ĐỌC ĐƯỢC TRỰC TIẾP từ bài viết.
   - Không suy đoán nội dung văn bản từ tiêu đề.
   - Nếu bài chỉ nêu tên văn bản mà không có số hiệu: ghi "CHƯA XÁC ĐỊNH" vào ô đó,
     và ghi rõ "CHƯA KIỂM CHỨNG:" ở đầu ô PDCA_Check.
   - Không gộp hai văn bản khác chủ đề vào một dòng.

   NGÀY HIỆU LỰC — ĐỌC KỸ, ĐÂY LÀ CHỖ SAI NHIỀU NHẤT
   Ngày ban hành KHÔNG PHẢI ngày hiệu lực. Rất nhiều bài chỉ nêu "ban hành ngày X"
   rồi thôi. Đưa ngày ban hành vào ô Ngay_Hieu_Luc là BỊA, tuyệt đối không làm.
   Cũng KHÔNG được tự suy ra ngày hiệu lực bằng quy tắc 45 ngày hay bất kỳ quy tắc
   pháp lý nào — đó là suy diễn, không phải đọc được từ bài.

   Khi bài KHÔNG nêu rõ ngày hiệu lực, làm đúng 3 việc:
     a) Ghi "CHƯA XÁC ĐỊNH" vào ô Ngay_Hieu_Luc.
     b) Mở đầu ô PDCA_Check bằng đúng câu:
        "CHƯA KIỂM CHỨNG: bài gốc không nêu ngày hiệu lực, cần tra cứu văn bản gốc."
     c) Nếu bài có nêu ngày ban hành, ghi ngày đó vào ô Tom_Tat_Nhanh theo dạng
        "Ban hành ngày DD/MM/YYYY; ..." để người đọc còn manh mối mà tra.

   Nếu dòng đó vừa thiếu số hiệu VỪA thiếu ngày hiệu lực: gộp thành một câu duy nhất
   ở đầu PDCA_Check, ví dụ "CHƯA KIỂM CHỨNG: bài gốc không nêu số hiệu và ngày hiệu
   lực, cần tra cứu văn bản gốc." Không viết hai câu CHƯA KIỂM CHỨNG chồng nhau.

   Vì sao phải làm đủ 3 việc: WebApp tính hạn tuân thủ từ ô Ngay_Hieu_Luc. Dòng để
   "CHƯA XÁC ĐỊNH" sẽ KHÔNG BAO GIỜ xuất hiện trong cảnh báo "Sắp có hiệu lực 30
   ngày tới". Nếu không ghi rõ ở PDCA_Check, người dùng sẽ không biết là mình cần
   tự đi tra, và sẽ lỡ mốc tuân thủ mà không hay biết.

8. CỘT Trang_Thai
   - Chỉ ghi một trong: Mới / Đang áp dụng / Sắp có hiệu lực.
   - Lưu ý: WebApp TỰ TÍNH trạng thái từ ngày tháng, cột này chỉ để tham khảo.

14 CỘT THEO ĐÚNG THỨ TỰ:
ID | Ngay_Cap_Nhat | Tieu_De | So_Hieu_Van_Ban | Co_Quan_Ban_Hanh | Ngay_Hieu_Luc | Linh_Vuc |
Tom_Tat_Nhanh | PDCA_Plan | PDCA_Do | PDCA_Check | PDCA_Act | Link_Goc | Trang_Thai

CÁCH VIẾT 4 CỘT PDCA — viết cho người vận hành nhà máy, không diễn giải lại luật:
  PDCA_Plan  : Ai/đơn vị nào phải áp dụng, ngưỡng áp dụng, phạm vi. 1-2 câu.
  PDCA_Do    : Việc cụ thể nhà máy phải LÀM, kèm hồ sơ và mốc thời hạn. 2-3 câu.
  PDCA_Check : Ai kiểm tra, kiểm tra cái gì, chế tài nếu vi phạm. 1-2 câu.
  PDCA_Act   : Việc cần chuẩn bị trước, cập nhật quy trình/ISO, đào tạo. 1-2 câu.
  Tom_Tat_Nhanh: 1-2 câu nêu thay đổi quan trọng nhất và mốc thời gian.

Nếu hôm nay không có bài mới nào đủ điều kiện: KHÔNG ghi gì vào Sheet.
```

---

## Vì sao có các quy tắc trên

| Quy tắc | Sự cố nó ngăn chặn |
|---|---|
| ID bền theo NewsId | Đánh số lại mỗi ngày làm bookmark trỏ nhầm văn bản và gây trùng lặp |
| Tách bài tổng hợp | Bài `5061` chứa 4 nghị định — map 1 bài = 1 văn bản là mất trắng 3 văn bản |
| Bắt buộc link `newsdetail` | Dòng sai duy nhất trong lô đầu chính là dòng dùng link danh mục làm nguồn |
| 6 lĩnh vực đóng | Lô 10 dòng đầu đã sinh ra 9 cách viết khác nhau, riêng "Môi trường" có 3 biến thể |
| Cấm xuống dòng trong ô | Một ô có xuống dòng từng làm mất trọn dòng dữ liệu, không báo lỗi |
| Chỉ append, không ghi đè | Chuyên mục chỉ hiển thị 10 bài mới nhất; ghi đè là mất lịch sử vĩnh viễn |
| Cấm bịa | Nghị định 286/2026 từng bị mô tả nhầm thành văn bản về giấy phép lao động |
| Không ghim hàng cuối cố định | Prompt từng ghi "dữ liệu đến hàng 92". Sheet dài thêm mỗi ngày, tin con số đó là đọc thiếu các dòng mới rồi ghi trùng mà không hay |
| Ngày ban hành ≠ ngày hiệu lực | 25/91 dòng không có ngày hiệu lực vì bài gốc không nêu. Đây là hành vi ĐÚNG — nhét ngày ban hành vào cho đủ ô mới là sai |

Bộ KH&CN cũng đã khuyến cáo chính thức (Công văn 6044/BKHCN-CNCNTT): AI chỉ là công cụ hỗ trợ,
người thực hiện phải kiểm chứng lại toàn bộ kết quả do AI tạo ra khi rà soát văn bản pháp luật.

## Cách tự kiểm mỗi tuần

> **Đã đổi ngày 29/08/2026.** Hướng dẫn cũ bảo dùng chip "Chưa kiểm chứng" làm thước đo
> duy nhất. Thước đo đó bị hỏng thầm lặng: chip này chỉ xét MỘT điều kiện là thiếu link bài
> gốc, mà cả 91 dòng trong Sheet đều có link hợp lệ, nên nó luôn hiển thị **0** trong khi
> thực tế có 30 dòng do chính Gemini tự gắn cờ "CHƯA KIỂM CHỨNG". Suốt thời gian qua, việc
> tự kiểm hàng tuần không phát hiện được gì.
>
> Chip đó **đã gỡ khỏi WebApp** để khỏi ai dùng nhầm lại. Thiếu link bài gốc nay là một
> trong các lỗi mà nhãn "Cần rà lại" bắt, nên không mất thông tin.

### Bước 1 — Kiểm tra dữ liệu đang xem có mới không

Nhìn huy hiệu cạnh tên app trên đầu trang:

| Huy hiệu | Nghĩa | Phải làm gì |
|---|---|---|
| 🟢 Đồng bộ trực tiếp | Đang đọc thẳng từ Google Sheet | Tự kiểm được |
| 🟡 Bản lưu ngoại tuyến | Mất mạng, đang xem bản lưu kèm ngày giờ | **Dừng tự kiểm**, xử lý mạng trước |
| 🔴 Không đồng bộ được | Máy này chưa từng đồng bộ, đang xem dữ liệu nhúng trong file | **Dừng tự kiểm** |

Tự kiểm trên bản lưu cũ là rà soát nhầm dữ liệu — số liệu sẽ vô nghĩa.

### Bước 2 — Bấm chip "Cần rà lại"

Đây mới là thước đo thật. Chip gom mọi dòng vi phạm quy tắc của prompt; con số trên chip
chính là chỉ số cần theo dõi hàng tuần.

Mỗi văn bản dính lỗi mang nhãn **`CẦN RÀ LẠI`** màu hổ phách, nằm cạnh nhãn `ĐANG ÁP DỤNG`.
Di chuột lên nhãn để xem dòng đó sai ở chỗ nào. Các loại cảnh báo:

| Cảnh báo | Nghĩa | Cách xử lý |
|---|---|---|
| Gemini tự đánh dấu CHƯA KIỂM CHỨNG | Ô `PDCA_Check` mở đầu bằng "CHƯA KIỂM CHỨNG:" — Gemini thừa nhận không chắc | Mở bài gốc đối chiếu, sửa lại nội dung PDCA rồi xoá tiền tố đó |
| Số hiệu còn ghi "CHƯA XÁC ĐỊNH" | Bài viết không nêu số hiệu | Tra trên Cổng thông tin VBQPPL, điền số hiệu thật |
| Chưa có ngày hiệu lực nên không tính được hạn | Bài gốc không nêu ngày hiệu lực. Đây thường KHÔNG phải lỗi của Gemini — ngày ban hành không thay được ngày hiệu lực | Dòng này **không bao giờ vào được cảnh báo sắp hết hạn**. Tra ngày hiệu lực thật trên Cổng thông tin VBQPPL rồi điền vào Sheet. Kiểm tra ô `PDCA_Check` có mở đầu bằng "CHƯA KIỂM CHỨNG: bài gốc không nêu ngày hiệu lực" không — thiếu câu này nghĩa là Gemini đang bỏ qua quy tắc 7 |
| Ngày ... sai định dạng | Google Sheets đã tự đổi ô ngày sang kiểu Ngày tháng | Định dạng lại cột B và F thành "Văn bản thuần túy", nhập lại YYYY-MM-DD |
| Ngày ... không đọc được | Ô ngày hỏng hẳn | Nhập lại đúng YYYY-MM-DD |
| Lĩnh vực ngoài 6 giá trị chuẩn | Gemini tự tạo nhãn mới | Sửa về đúng 1 trong 6 giá trị ở quy tắc 5 |
| Thiếu link bài gốc | Không có link `newsdetail` để đối chiếu | Bổ sung link, hoặc xoá dòng nếu không truy được nguồn |

Ngoài ra WebApp còn tự ẩn các **dòng trùng** khi hiển thị, nhưng không sửa Sheet — dòng
thừa vẫn nằm nguyên đó, phải vào xoá tay. Danh sách dòng bị ẩn không hiện trên giao diện;
muốn xem thì bấm F12 mở console của trình duyệt, tìm dòng bắt đầu bằng `[DNIEZA]`.

### Bước 3 — So với mốc nền

Chốt ngày **29/08/2026**: 91 dòng, trong đó **31 dòng cần rà lại**
(30 dòng Gemini tự gắn cờ chưa kiểm chứng, 25 dòng thiếu ngày hiệu lực, 8 dòng thiếu số hiệu —
một dòng có thể dính nhiều lỗi). Không có dòng nào sai lĩnh vực, sai định dạng ngày, hay bị trùng.

Mỗi tuần ghi lại con số này. **Tỷ lệ "cần rà lại" tăng dần nghĩa là prompt đang bị Gemini nới lỏng** —
lúc đó siết lại quy tắc 7 (cấm bịa) và quy tắc 6 (định dạng ô). Con số phải giảm dần khi bạn
sửa tay, chứ không được đứng yên: đứng yên nghĩa là chưa ai thực sự rà.
