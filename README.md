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

## Website dealer tiêu thụ thế nào
Plugin thêm 1 Source = "VIG Car Hub" đọc `data/<brand>.json` (qua URL raw) →
tìm model → đưa vào luồng diff/duyệt/ghi Carbon Fields như cũ. Không scrape ở site.

## Hosting (CHƯA chốt — quyết định kinh doanh)
- **Public** (git raw / CDN): đơn giản, free, không cần key — NHƯNG lộ kho curated (đối thủ lấy free).
- **Private + key**: gate được (kho curated = gói trả phí) — cần 1 endpoint nhỏ (serverless / WP REST + key, pattern Connection của vig-sync).
