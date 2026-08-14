# Trợ lý Tổng phụ trách Đội THCS 3.0 RC1

Ứng dụng HTML/PWA local-first cho công tác Đội, thi đua lớp, hồ sơ Blob và báo cáo. Đây là **release candidate**, chưa phải bản phát hành chính thức vì chưa có nghiệm thu trình duyệt thật/GitHub Pages thực tế và chưa có EXE.

## Chạy nhanh

Mở `index.html` bằng Chrome/Edge. Để cài PWA/service worker, phục vụ cả thư mục qua HTTPS hoặc máy chủ HTTP cục bộ. Không tách `index.html`, `manifest.webmanifest`, `sw.js`, `offline.html`, `404.html` và `assets/`.

## Định danh ổn định

- App ID: `vn.giaoducso40.tpt.thcs.standard`
- Database: `TPT_DOI_THCS_DB`
- Schema: `8`
- School profile mẫu: `thcs-local-profile-001`
- Backup contract: `TPT-BACKUP-3`

## Kiểm thử

```bash
npm ci
npm test
```

Bộ test gồm phân tích tĩnh, khởi động/16 phân hệ, migration schema 3→8, xung đột revision, quota, draft, backup lỗi, Blob round-trip, báo cáo chốt, vòng đời năm học, khóa đa tab và tải 10.000 công việc/100 lớp/200 hồ sơ-tệp.

## Cấu trúc

- `index.html`: entry deploy PWA.
- `index_tro_ly_tpt_doi_thcs_nang_cap.html`: nguồn HTML độc lập giống hệt `index.html`.
- `core/`: hợp đồng dữ liệu và adapter trình duyệt.
- `school-profile/`: hồ sơ trường mẫu, không chứa dữ liệu thật.
- `desktop/`: hợp đồng/stub Tauri; không phải EXE.
- `tests/`: test có thể chạy lại bằng Node.js.
- `BAO_CAO_*.md`, `CHANGELOG.md`, `LOI_CON_LAI.md`: chứng cứ và giới hạn phát hành.

## Quyền riêng tư

Không có CDN/API/analytics/telemetry trong runtime HTML. Dữ liệu nghiệp vụ lưu trên thiết bị. Không commit backup, dữ liệu thật, tên học sinh hoặc tài liệu nội bộ vào repository.

Xem [HUONG_DAN_BAN_GIAO.md](HUONG_DAN_BAN_GIAO.md) trước khi dùng dữ liệu thật.
