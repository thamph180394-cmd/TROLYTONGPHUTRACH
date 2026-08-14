# Danh sách lỗi/rủi ro đã sửa

| ID | Mức ban đầu | Nội dung | Cách xử lý | Test hồi quy |
|---|---|---|---|---|
| FIX-01 | High | 6 hàm top-level trùng, bản sau ghi đè bản trước | Xóa khai báo legacy, giữ một nguồn sự thật | AST: 0 hàm trùng |
| FIX-02 | High | Thiếu App ID/profile ID và metadata migration | Định danh ổn định, schema 8, chuẩn hóa giữ ID | Migration schema 3→8 |
| FIX-03 | High | Hai tab có thể cùng ghi | Web Locks; fallback lease/BroadcastChannel; tab phụ chỉ đọc | `multi-tab-read-only` |
| FIX-04 | High | Báo “đã lưu” trước khi audit hoàn tất | Một transaction; trạng thái sau `oncomplete` | core/quota/conflict |
| FIX-05 | High | Restore merge có thể ghi đè bản mới hơn | Preview và merge theo ID/revision/thời điểm | conflict + round-trip |
| FIX-06 | High | Chưa có snapshot trước thao tác rủi ro | Snapshot checksum/protected/retention | snapshot tests |
| FIX-07 | High | Backup thiếu gói năm, checksum nguồn và chunk/cancel | `TPT-BACKUP-3`, 3 scope, chunk, progress, AES | backup fault/large |
| FIX-08 | High | Báo cáo thiếu trạng thái chốt/version/checksum | Draft/finalized bất biến và package | finalized tests |
| FIX-09 | High | Năm cũ chưa chỉ đọc, chưa có quy trình đóng/mở | Wizard, checklist, package, audit lý do | year lifecycle |
| FIX-10 | Medium | Footer là hyperlink Zalo | Chữ thuần, một dòng, căn giữa | static/core |
| FIX-11 | Medium | Nội dung giới hạn 1600px gây khoảng trống | `width:100%; max-width:none` | static/core |
| FIX-12 | Medium | PWA reload cập nhật có thể làm mất nhập dở | Banner cập nhật có kiểm tra draft/save | static PWA |
| FIX-13 | Medium | License client dễ bị hiểu nhầm là thương mại | Cảnh báo demo và stub desktop không giả trạng thái | static/docs |
| FIX-14 | Medium | Không có test tải lớn/fault injection | Test 10.000 việc, 100 lớp, 200 tệp; quota/checksum/password/blob | performance/features |
