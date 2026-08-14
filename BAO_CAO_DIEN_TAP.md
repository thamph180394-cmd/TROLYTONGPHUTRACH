# Báo cáo diễn tập dữ liệu tự động

## Chuỗi đã chạy

1. Tạo database schema 3 có trường, công việc và hồ sơ cũ.
2. Mở 3.0 RC1, nâng lên schema 8, đối chiếu ID/nội dung/metadata.
3. Tạo dữ liệu Blob thật và full backup `TPT-BACKUP-3` có SHA-256.
4. Thêm một marker sau thời điểm backup.
5. Phục hồi chế độ replace; xác nhận marker biến mất, Blob/nội dung/hash phục hồi đúng.
6. Tạo và chốt báo cáo; thay đổi dữ liệu nguồn; xác nhận bản chốt không đổi.
7. Tạo năm mới không sao chép lớp/tiêu chí; xác nhận 40 tuần và log.
8. Đóng năm; xác nhận read-only, báo cáo tổng kết chốt, package checksum và log.
9. Mở quyền sửa năm cũ bằng lý do; xác nhận audit.
10. Mở phiên thứ hai; xác nhận chỉ đọc.

## Kết quả

- Migration: 3/3 ID và nội dung fixture cũ giữ nguyên.
- Full Blob round-trip: nội dung và SHA-256 khớp.
- Replace transaction: đạt trong fake-indexeddb.
- Báo cáo chốt bất biến: đạt.
- Tạo/đóng/mở sửa năm học: đạt.
- Hai phiên: một writer, phiên sau read-only: đạt.

Giới hạn: đây là diễn tập tự động, không thay thế cài sạch bằng Chrome/PWA/EXE thật trên hai máy.
