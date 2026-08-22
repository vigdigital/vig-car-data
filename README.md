# VIG Car Data — Kho dữ liệu xe tập trung

Kho JSON chuẩn hoá **giá · phiên bản · thông số** xe theo hãng, để các website cài
**VIG Car Sync** pull về (thay vì mỗi site tự scrape web).

## Cấu trúc
```
sources.json          # DANH SÁCH URL model theo hãng (VIG curate — "muốn có xe nào")
data/<brand>.json     # OUTPUT: dữ liệu đã scrape+chuẩn hoá (sinh bởi generator)
schema.json           # JSON Schema của 1 file brand
```

## Quy trình cập nhật (VIG chạy định kỳ)
1. Sửa `sources.json` (thêm/bớt URL model).
2. Chạy generator trên 1 WordPress có plugin VIG Car Sync:
   `wp vig-car build --sources=/path/vig-car-data/sources.json --out=/path/vig-car-data/data`
   → scrape qua đúng Source (Honda/VnExpress) của plugin → ghi `data/<brand>.json`.
3. Kiểm tra diff (git) → commit. Lịch sử git = provenance.

## Thêm hãng mới (vd BYD)
Kho **đa hãng** sẵn: mỗi hãng 1 file `data/<hãng>.json`, `index.json` liệt kê tất cả.
1. Tạo `data/byd.json` theo **schema.json** — bắt buộc `brand:"byd"`, `brand_name:"BYD"`, `models[]`
   (mỗi model: `slug, name, price, versions[{name,price,status,specs[{label,value}]}], specs[{label,value}]`).
   Dùng bộ nhãn chuẩn (Động cơ, Công suất, Số chỗ ngồi…); xe điện thêm nhãn mới (Dung lượng pin, Tầm hoạt động…) thoải mái — plugin generic, không đổi theme.
2. Regenerate `index.json` (sinh `rev`+`status`): chạy `wp vig-car build …`, hoặc script gen-index khi curate tay.
3. Commit + push → site BYD pull về.

> Site BYD: cài plugin → **Xe → Cài đặt đồng bộ** → tick **BYD** → thêm xe chọn nguồn từ dropdown (chỉ hiện BYD) → **Đồng bộ**. Mỗi site chỉ thấy/sync hãng mình chọn (option `vcs_brands`).

## Website dealer tiêu thụ thế nào
Plugin thêm 1 Source = "VIG Car Hub" đọc `data/<brand>.json` (qua URL raw) →
tìm model → đưa vào luồng diff/duyệt/ghi Carbon Fields như cũ. Không scrape ở site.

## Hosting (CHƯA chốt — quyết định kinh doanh)
- **Public** (git raw / CDN): đơn giản, free, không cần key — NHƯNG lộ kho curated (đối thủ lấy free).
- **Private + key**: gate được (kho curated = gói trả phí) — cần 1 endpoint nhỏ (serverless / WP REST + key, pattern Connection của vig-sync).
