# Research Hub

Web tự động thu thập, lưu trữ và trực quan hóa báo cáo phân tích / nghiên cứu của các công ty chứng khoán và quỹ đầu tư.

Chạy hoàn toàn trên GitHub, **không cần cài gì trên máy**:

| Thành phần | Vai trò |
|---|---|
| **GitHub Actions** | Chạy bộ thu thập lúc 8h, 12h, 17h (thứ Hai đến thứ Sáu), và mỗi khi có file mới trong `inbox/` |
| **Repo** | Lưu trữ: PDF trong `docs/files/`, dữ liệu trong `docs/data/` |
| **GitHub Pages** | Hiển thị dashboard tại `https://<tài-khoản>.github.io/<tên-repo>/` |

## Cài đặt (khoảng 10 phút)

1. Tạo tài khoản tại github.com nếu chưa có.
2. Tạo repo mới: **New repository** → đặt tên, ví dụ `research-hub` → chọn **Private** hoặc **Public** → **Create**.
   (Repo Private cần gói GitHub Pro/Team để bật Pages.)
3. Tải code lên: trong repo vừa tạo, chọn **uploading an existing file**, rồi kéo thả **toàn bộ nội dung** thư mục `research-hub`,
   kể cả thư mục ẩn `.github`, sau đó bấm **Commit changes**.
   > Nếu Windows ẩn thư mục `.github`: vào **Add file → Create new file**, gõ tên `.github/workflows/update.yml`, rồi dán nội dung file đó vào.
4. Bật quyền ghi cho bot: **Settings → Actions → General → Workflow permissions → Read and write permissions** → Save.
5. Bật trang web: **Settings → Pages → Source: Deploy from a branch → Branch: `main`, thư mục `/docs`** → Save.
   Sau khoảng 1 phút, trang chạy tại địa chỉ GitHub hiển thị.
6. Chạy lần đầu: **Actions → Cap nhat bao cao → Run workflow**.

## Thêm nguồn báo cáo

Sửa file `sources.yaml` (bấm vào file trên GitHub, rồi bấm biểu tượng bút chì). Có 3 kiểu nguồn:

- `html`: trang danh sách báo cáo. Cần CSS selector cho dòng báo cáo, tiêu đề, link và ngày.
- `rss`: trang có RSS feed.
- `json`: trang trả dữ liệu dạng JSON.

**Kiểm tra một nguồn trước khi bật:** vào **Actions → Cap nhat bao cao → Run workflow**, rồi nhập `id` của nguồn vào ô *test_source*.
Log sẽ liệt kê các báo cáo tìm được mà không lưu gì. Khi kết quả đúng, đặt `enabled: true` cho nguồn đó.

> Các nguồn mẫu trong `sources.yaml` (Vietstock, CafeF…) **chưa được kiểm tra selector**, nên cần chỉnh theo giao diện thực tế của từng trang.
> Một số trang có thể chặn máy chủ nước ngoài của GitHub. Khi đó, bạn dùng cách tải PDF thủ công bên dưới.
> Hãy tôn trọng điều khoản sử dụng của từng website.

## Tải PDF thủ công

Kéo thả PDF vào thư mục `inbox/` trên GitHub (**Add file → Upload files**). Hệ thống tự:
- trích mã cổ phiếu, khuyến nghị (Mua / Khả quan / Nắm giữ / Kém khả quan / Bán) và giá mục tiêu từ nội dung PDF;
- phân loại báo cáo (Doanh nghiệp, Ngành, Vĩ mô, Chiến lược, Trái phiếu, Kỹ thuật, Quỹ);
- đưa báo cáo vào thư viện và xóa file khỏi `inbox/`.

Tên file gợi ý: `CTCK__MA__YYYY-MM-DD__Tieu de.pdf`. Xem thêm tại [inbox/README.md](inbox/README.md).

## Giá hiện tại (để tính upside)

Giá nằm trong `docs/data/prices.json`. Có thể cập nhật theo một trong ba cách:
- sửa tay file này;
- nhờ Claude cập nhật bằng dữ liệu FiinX;
- nếu có API giá riêng, thêm 2 secrets `FIINX_PRICE_URL` và `FIINX_API_KEY` trong **Settings → Secrets → Actions**.
  Bộ thu thập sẽ gọi `URL?tickers=AAA,BBB` và đọc JSON dạng `{"AAA": 12345}`.

## Xem thử trên máy (không bắt buộc)

```powershell
powershell -ExecutionPolicy Bypass -File serve.ps1
```
Sau đó mở http://localhost:8080.

## Cấu trúc

```
.github/workflows/update.yml   lịch chạy tự động
sources.yaml                   danh sách nguồn
scripts/update.py              bộ thu thập
scripts/extract.py             đọc PDF, nhận diện mã / khuyến nghị / giá mục tiêu
inbox/                         nơi thả PDF thủ công
docs/                          trang web (GitHub Pages)
  index.html, app.js, style.css
  data/reports.json            kho dữ liệu báo cáo
  data/prices.json             giá hiện tại
  data/data.js                 bản sao dữ liệu cho trang web
  files/                       PDF đã lưu
```
