# DNIEZA Legal & HSE Portal

WebApp theo dõi văn bản pháp luật mới cho doanh nghiệp trong KCN Đồng Nai, bóc tách
theo chu trình PDCA để cán bộ HSE và pháp chế biết phải làm gì, hạn nào.

Toàn bộ app nằm trong một file `index.html` duy nhất. Không cần build, không cần
server, không phụ thuộc CDN.

## Dữ liệu đi từ đâu tới đâu

```
Cổng DNIEZA  ──►  Gemini (8h00 hằng ngày)  ──►  Google Sheet  ──►  WebApp
 CatId=104        theo PROMPT_GEMINI_8H_SANG.md    14 cột         đọc CSV export
```

Nguồn: chuyên mục "Triển khai Văn bản pháp luật mới" — dnieza.dongnai.gov.vn (CatId=104).
App chỉ ĐỌC Sheet, không bao giờ ghi. Mọi chỉnh sửa dữ liệu đều làm trực tiếp trên Sheet.

## Mở app thế nào

Phải mở qua địa chỉ web (http/https) — ví dụ GitHub Pages, Google Sites. **Nhấp đúp vào
file HTML sẽ không đồng bộ được**: ở chế độ `file://` trình duyệt chặn kết nối tới Google
Sheet, app sẽ hiện cảnh báo đỏ và rơi về dữ liệu cũ.

## Có gì trong app

- **Thẻ PDCA** cho từng văn bản: Plan / Do / Check / Act, viết cho người vận hành nhà máy
- **Sắp có hiệu lực trong 30 ngày** — panel riêng đầu trang, dưới 7 ngày thì chuyển đỏ
- **Biết ngay văn bản nào mới**: mỗi thẻ hiện `Đăng: …· Hiệu lực: …`, nhãn `MỚI` cho văn bản
  đăng trong 30 ngày (`FRESH_DAYS`), nhãn `MỚI NHẤT` cho đúng một văn bản đứng đầu
- **Nhãn `CẦN RÀ LẠI`** trên văn bản có dữ liệu đáng ngờ (xem dưới)
- Lọc theo 6 lĩnh vực, tìm kiếm toàn văn kể cả trong nội dung PDCA
- Đánh dấu văn bản cần theo dõi (lưu trong trình duyệt, riêng từng máy)
- Chế độ tối, in / lưu PDF danh sách đang lọc
- **Lối vào Tạp chí An toàn, Vệ sinh lao động** — nút trên header (từ khổ `md` trở lên) và
  một thẻ trong nội dung (thấy được ở mọi khổ màn hình, kể cả điện thoại)

Trạng thái hiệu lực do app **tự tính từ ngày tháng**, không tin cột `Trang_Thai` trong
Sheet — cột đó chỉ để tham khảo.

## Ba tầng dữ liệu

Huy hiệu cạnh tên app cho biết đang xem dữ liệu nào. Nhìn nó trước khi tin bất cứ con số nào:

| Huy hiệu | Nguồn | Ý nghĩa |
|---|---|---|
| 🟢 Đồng bộ trực tiếp | Google Sheet | Dữ liệu mới nhất |
| 🟡 Bản lưu ngoại tuyến | localStorage | Mất mạng; hiện kèm thời điểm lưu gần nhất |
| 🔴 Không đồng bộ được | Dữ liệu nhúng trong file | Máy này chưa từng đồng bộ thành công lần nào |

App còn cảnh báo khi văn bản mới nhất trong Sheet đã quá **25 ngày** (`STALE_DAYS`).

Con số này chọn theo nhịp đăng bài thật của cổng DNIEZA, không phải đặt bừa: 10 bài trong
khoảng 17/06–24/08/2026 cách nhau 1, 2, 2, 4, 5, 5, 11, 17 và 21 ngày. Ngưỡng cũ là 3 ngày,
nổi cảnh báo trong 6 trên 9 khoảng nghỉ đó — kêu nhầm nhiều hơn kêu đúng, riết rồi không ai
buồn đọc. 25 ngày vượt khoảng nghỉ dài nhất từng thấy mà vẫn đủ sớm để bắt được sự cố thật.

Câu chữ cảnh báo cũng chỉ nêu điều app thực sự biết. App đọc Sheet chứ không đọc được cổng
DNIEZA (bị CORS chặn), nên nó **không thể** biết Gemini còn chạy hay không — chỉ biết Sheet
lâu rồi chưa có dòng mới. Vì vậy cảnh báo nêu cả hai khả năng và kèm link mở thẳng chuyên
mục nguồn để người đọc tự xác định là trường hợp nào.

## Biết văn bản nào mới nhất

Danh sách luôn sắp theo **ngày đăng, mới trước** — có ghi rõ ngay trên thanh lọc để khỏi
phải đoán. Trước ngày 06/09/2026 app sắp đúng như vậy nhưng **giấu ngày đăng đi**, chỉ hiện
ngày hiệu lực, nên nhìn vào tưởng danh sách lộn xộn: sắp theo một ngày vô hình mà lại hiển
thị một ngày khác.

| Dấu hiệu | Ý nghĩa |
|---|---|
| `Đăng: …` trên mỗi thẻ | Ngày cổng DNIEZA đăng bài — chính là căn cứ sắp xếp |
| Nhãn `MỚI NHẤT` | Đúng **một** văn bản, mới nhất trong cả kho, bất kể bao nhiêu ngày |
| Nhãn `MỚI` | Đăng trong vòng 30 ngày (`FRESH_DAYS`) |

`FRESH_DAYS` để 30 ngày cùng lý do với `STALE_DAYS`: nguồn đăng thưa. Đặt 7 ngày như bản cũ
thì gần như quanh năm không thẻ nào có nhãn — ngày 06/09/2026 đo trên dữ liệu thật: ngưỡng
7 ngày cho **0/91** văn bản, ngưỡng 30 ngày cho **15/91**.

Nhãn `MỚI NHẤT` thay chỗ `MỚI` trên chính văn bản đó chứ không đứng cạnh. Nó bám theo thứ
hạng nên luôn tồn tại, kể cả khi nguồn nghỉ đăng lâu hơn 30 ngày và không còn nhãn `MỚI` nào.

## Lớp phòng vệ dữ liệu

Prompt ràng buộc Gemini, nhưng prompt có thể bị nới lỏng mà không ai hay. App tự kiểm tra
lại và gắn nhãn **`CẦN RÀ LẠI`** lên văn bản dính một trong các lỗi sau:

- Thiếu link bài gốc để đối chiếu
- Số hiệu trống hoặc còn ghi "CHƯA XÁC ĐỊNH"
- Gemini tự đánh dấu "CHƯA KIỂM CHỨNG" ở đầu ô `PDCA_Check`
- Ngày trống, không đọc được, hoặc sai chuẩn `YYYY-MM-DD`
- Lĩnh vực nằm ngoài 6 giá trị chuẩn

Nhãn nằm cạnh `ĐANG ÁP DỤNG` và `MỚI`, chỉ để nhắc nhở khi đọc — di chuột lên nhãn sẽ hiện
đúng dòng đó sai ở chỗ nào. Muốn xem hết một lượt thì bấm chip **"Cần rà lại"** ở thanh lọc.

Riêng phần ngày tháng đáng nói: Google Sheets hay tự đổi ô ngày sang kiểu `M/D/YYYY`.
Chuỗi `9/25/2026` trông vẫn hợp lệ nhưng nếu đọc theo kiểu Việt Nam sẽ thành ngày 9 tháng
25 và tràn sang năm 2028 — sai âm thầm, không báo lỗi. App dựng ngày rồi đọc ngược lại để
đối chiếu, không khớp thì từ chối và gắn cờ.

App cũng chống trùng theo đúng luật của prompt: bỏ tiền tố "Nghị định số", bỏ dấu, và coi
`TT-BTNMT` ≡ `TT-BNNMT` (Bộ TN&MT đã sáp nhập vào Bộ NN&MT). Gặp trùng thì giữ dòng đầy
đủ hơn, và **chỉ ẩn khi hiển thị** — dòng thừa vẫn nằm trong Sheet, phải vào xoá tay.
Danh sách dòng bị ẩn được ghi ra console của trình duyệt (F12), không hiện trên giao diện.

Quy trình tự kiểm hàng tuần: xem mục cuối [PROMPT_GEMINI_8H_SANG.md](PROMPT_GEMINI_8H_SANG.md).

## Tạp chí ATVSLĐ — chỉ dẫn link, không lưu file

App **không lưu và không phục vụ** bất kỳ file PDF tạp chí nào. Hai lối vào đều trỏ thẳng
sang [trang tạp chí in của Tạp chí Lao động và Công đoàn](https://laodongdoanthe.vn/tap-chi-in/an-toan-ve-sinh-lao-dong),
nơi có đủ các số đã phát hành và người đọc xem ngay trên đó.

Đây là lựa chọn có chủ đích, không phải làm tạm:

- **Bản quyền** — tạp chí là ấn phẩm có bản quyền; dẫn link về nguồn thì không phát sinh
  vấn đề gì, còn tự đăng lại bản PDF đầy đủ thì có.
- **Dung lượng** — mỗi số nặng 57–84 MB. GitHub Pages giới hạn mềm 1 GB dung lượng site và
  100 GB băng thông/tháng, tức khoảng 1.500 lượt mở là chạm trần. Thêm ~65 MB mỗi tháng thì
  chưa đầy một năm là hết chỗ.
- **Không gỡ lại được** — git giữ lịch sử vĩnh viễn. Commit nhầm một file PDF thì xoá ở
  commit sau cũng không giảm được dung lượng repo.

Vì vậy `.gitignore` chặn sẵn `*.pdf` và thư mục `Tap_chi_ATVSLĐ/`. Bản PDF gốc cứ để trên
máy / Google Drive, đừng đưa vào repo.

## Sửa giao diện — đọc trước kẻo mất công

CSS Tailwind trong file là **bản build tĩnh**, không còn CDN sinh lớp tại chỗ. Thêm một lớp
Tailwind mới vào HTML sẽ **không có tác dụng và không báo lỗi gì cả**.

Quy trình build lại nằm trong khối chú thích đầu `<head>` của `index.html`. Cách kiểm nhanh
sau khi sửa: mở một thẻ để hiện modal PDCA — nếu 4 ô P/D/C/A mất màu nền thì safelist đang thiếu.

Icon dùng Font Awesome Free 6.4.0, nhúng dạng CSS mask, theo giấy phép CC BY 4.0.

## Lưu ý quan trọng

Nội dung PDCA do AI bóc tách, **chỉ mang tính tham khảo**. Luôn đối chiếu văn bản gốc trước
khi ra quyết định. Bộ KH&CN đã khuyến cáo chính thức tại Công văn 6044/BKHCN-CNCNTT: AI chỉ
là công cụ hỗ trợ, người thực hiện phải kiểm chứng lại toàn bộ kết quả do AI tạo ra khi rà
soát văn bản pháp luật.
