# Triển khai thử trên GitHub Pages

## Tệp cần đưa lên cùng một thư mục

`index.html`, `index_tro_ly_tpt_doi_thcs_nang_cap.html`, `manifest.webmanifest`, `sw.js`, `offline.html`, `404.html`, `assets/`.

Các đường dẫn đều tương đối để chạy dưới subpath dạng `https://ten-tai-khoan.github.io/ten-repo/`. Không đổi `scope` sang `/` khi repo không nằm ở domain root.

## Nghiệm thu bắt buộc trước phát hành

1. Mở site online lần đầu, kiểm tra manifest/icon/service worker.
2. Nhập fixture, tạo Blob và full backup.
3. Đóng tab, ngắt mạng, mở lại PWA và đối chiếu dữ liệu.
4. Đưa một bản mới lên, chờ banner cập nhật, giữ một form draft rồi xác nhận app không reload; lưu/đóng draft và cập nhật.
5. Đối chiếu IndexedDB, báo cáo và Blob sau update.
6. Thử URL repo có dấu gạch, refresh ở route/fallback và thiết bị mới chưa cache.

GitHub Pages không đồng bộ dữ liệu, không chạy backend, không giữ backup và không thể mở lần đầu khi thiết bị chưa tải app mà đang offline.
