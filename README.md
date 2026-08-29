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
- **Nhãn `CẦN RÀ LẠI`** trên văn bản có dữ liệu đáng ngờ (xem dưới)
- Lọc theo 6 lĩnh vực, tìm kiếm toàn văn kể cả trong nội dung PDCA
- Đánh dấu văn bản cần theo dõi (lưu trong trình duyệt, riêng từng máy)
- Chế độ tối, in / lưu PDF danh sách đang lọc

Trạng thái hiệu lực do app **tự tính từ ngày tháng**, không tin cột `Trang_Thai` trong
Sheet — cột đó chỉ để tham khảo.

## Ba tầng dữ liệu

Huy hiệu cạnh tên app cho biết đang xem dữ liệu nào. Nhìn nó trước khi tin bất cứ con số nào:

| Huy hiệu | Nguồn | Ý nghĩa |
|---|---|---|
| 🟢 Đồng bộ trực tiếp | Google Sheet | Dữ liệu mới nhất |
| 🟡 Bản lưu ngoại tuyến | localStorage | Mất mạng; hiện kèm thời điểm lưu gần nhất |
| 🔴 Không đồng bộ được | Dữ liệu nhúng trong file | Máy này chưa từng đồng bộ thành công lần nào |

App còn cảnh báo khi văn bản mới nhất trong Sheet đã quá 3 ngày — dấu hiệu tiến trình
cập nhật 8h sáng có thể đã dừng.

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
