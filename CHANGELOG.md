# Changelog

## 3.0.0-rc.1 — 2026-08-14

### Dữ liệu và độ bền

- Giữ database `TPT_DOI_THCS_DB`, nâng schema 7→8 theo hướng chỉ thêm store/index.
- Thêm App ID, school profile ID và hợp đồng bản ghi; migration giữ nguyên ID, bổ sung metadata thiếu.
- Gộp ghi nghiệp vụ/audit/journal trong transaction; chỉ báo lưu sau `oncomplete`.
- Thêm optimistic revision conflict, lỗi quota rõ ràng, form draft và khóa một tab ghi.
- Thêm journal `started/completed/failed/cancelled/interrupted` cho thao tác lớn.

### Snapshot, backup và restore

- Thêm snapshot nội bộ không nhân Blob, checksum và retention 7 ngày/4 tuần/12 tháng.
- Chuẩn hóa `TPT-BACKUP-3`: nhanh, đầy đủ, gói năm học; manifest, source checksum và metadata tệp.
- Mã hóa AES-GCM xác nhận mật khẩu hai lần; xử lý theo chunk, tiến trình và hủy.
- Thêm thư mục File System Access và sao lưu lúc ứng dụng đang mở khi quyền còn hiệu lực.
- Restore kiểm App ID/profile/schema/checksum/Blob, preview xung đột, staging, merge theo revision hoặc replace transaction.

### Năm học và báo cáo

- Wizard tạo năm có chọn lọc; 2 học kỳ/40 tuần; không sao chép dữ liệu kỳ cũ sai phạm vi.
- Đóng năm có checklist, snapshot, báo cáo tổng kết chốt, gói năm và chế độ chỉ đọc.
- Mở sửa năm cũ theo phiên, bắt buộc lý do và audit.
- Báo cáo có draft/finalized, version, nơi nhận, trạng thái gửi, filter/config/source count/checksum; gói báo cáo chốt.

### PWA, giao diện và kiến trúc

- Xóa 6 khai báo hàm top-level trùng.
- Nội dung dùng toàn chiều rộng; footer chữ thuần đúng yêu cầu; thêm reduced motion và trạng thái UI.
- PWA dùng `./index.html`, cache RC1, fallback 404/offline và cập nhật có kiểm tra draft.
- Thêm BrowserPlatformAdapter, data contract và stub/hợp đồng desktop trung thực.
- Không còn URL ngoài, CDN, analytics hoặc telemetry trong runtime.

### Kiểm thử

- 62 kiểm tra tự động đạt; có migration, fault injection, Blob round-trip, tính điểm/xếp hạng đồng hạng, báo cáo bất biến, năm học, đa tab và tải lớn.
- Chưa thực hiện browser/PWA/GitHub/EXE thật; xem `LOI_CON_LAI.md`.
