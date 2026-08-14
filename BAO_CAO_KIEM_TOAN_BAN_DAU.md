# Báo cáo kiểm toán ban đầu — Trợ lý Tổng phụ trách Đội THCS

Thời điểm kiểm toán: 14/08/2026  
Nguồn kiểm toán: `index_tro_ly_tpt_doi_thcs_nang_cap.html` trước vòng hiệu chỉnh cuối  
SHA-256 nguồn: `81a2585391e8f4bab08c9091f571097787279adceb422edc851045ffc1bc287e`

## 1. Định danh và cấu trúc hiện tại

| Thuộc tính | Hiện trạng đã kiểm tra |
|---|---|
| Tên ứng dụng | Trợ lý Tổng phụ trách Đội THCS |
| Phiên bản | 2.0.0 |
| App ID | Chưa khai báo |
| School profile ID | Chưa khai báo độc lập |
| Cơ sở dữ liệu nghiệp vụ | `TPT_DOI_THCS_DB` |
| Schema | 7 |
| Kho giấy phép | `TPT_DOI_THCS_LICENSE_DB`, tách khỏi backup nghiệp vụ |
| Object store | 53 store |
| Điều hướng | 16 phân hệ |
| Tệp đính kèm | Blob thật trong `attachments`; metadata và phiên bản nằm trong `documents`, `file_versions`; liên kết nằm trong `document_links` |
| PWA | Có manifest, service worker, trang offline và icon; dùng đường dẫn tương đối |
| EXE | Chưa có adapter desktop, mã nguồn Tauri/Electron hoặc bản cài |

### 16 phân hệ

Tổng quan; Hôm nay; Kế hoạch; Công việc và checklist; Lịch hoạt động; Thi đua lớp; Hoạt động Đội; Tổ chức Liên đội; Rèn luyện – phong trào; Khen thưởng; Hồ sơ – minh chứng; Thiết bị Đội; Báo cáo; Trợ lý tổng hợp; Sao lưu – đồng bộ; Thiết lập.

## 2. Phát hiện chính

1. Có sáu hàm top-level khai báo trùng: `scoreContext`, `showCriteriaConfig`, `renderBackup`, `chooseBackup`, `restoreSelected`, `renderSettings`. Bản sau ghi đè bản trước; đây là rủi ro bảo trì và trái cổng kiểm tra tĩnh.
2. Chưa có `APP_ID` và `SCHOOL_PROFILE_ID` ổn định. Bản ghi mới có `revision`, `created_at`, `updated_at`, nhưng dữ liệu cũ chưa được chuẩn hóa đủ `school_profile_id`, `academic_year_id`, `source`.
3. Trạng thái lưu hiện chỉ ghi “Đang lưu…/Đã lưu/Lỗi lưu”; chưa hiển thị giờ hoàn tất, chưa phát hiện xung đột revision, chưa phân biệt lỗi quota và chưa có hàng đợi/draft.
4. Chưa có Web Locks/BroadcastChannel/lease để ngăn hai tab cùng ghi. Restore, migration và ghi hàng loạt có thể chạy đồng thời ở hai tab.
5. `audit_logs` ghi lịch sử nghiệp vụ nhưng chưa phải journal có trạng thái bắt đầu–hoàn thành–thất bại; chưa phát hiện giao dịch dang dở khi khởi động.
6. Chưa có điểm phục hồi nội bộ và chính sách 7 ngày/4 tuần/12 tháng.
7. Backup hiện có hai loại nhanh/đầy đủ; chưa có gói năm học, kiểm tra backup độc lập, thư mục backup được cấp quyền hoặc lịch nhắc/tự ghi khi ứng dụng đang mở.
8. Restore thay thế dùng transaction toàn bộ store nên có tính nguyên tử tốt; chế độ gộp mới ghi theo ID nhưng chưa xem trước xung đột revision và chưa tạo điểm phục hồi nội bộ.
9. Báo cáo lưu HTML tĩnh nên không tự đổi theo dữ liệu mới, nhưng metadata còn thiếu số phiên bản, trạng thái chốt/nộp, nơi nhận, checksum, số bản ghi nguồn và snapshot bộ lọc/cấu hình đầy đủ.
10. Quản lý năm học mới chỉ tạo năm và 40 tuần; chưa có wizard đóng năm, gói năm học, trạng thái chỉ đọc, lý do mở lại và quy tắc sao chép có chọn lọc.
11. `.page` đang giới hạn `max-width: 1600px` và căn giữa; ở màn hình rộng có thể để khoảng trống không cần thiết. Đây là lỗi đã được người dùng nhắc ở các lượt trước.
12. Dòng người phát hành đang gắn hyperlink Zalo trong footer và màn hình kích hoạt; yêu cầu gần nhất là hiển thị thông tin người phát hành bằng chữ, không biến số Zalo thành liên kết.
13. Nguồn chỉ có một URL ngoài là `https://zalo.me/0812806887`; không có CDN/API/analytics. Khi bỏ liên kết Zalo, chức năng lõi không còn request mạng ngoài.
14. License demo dùng hash của hai mã cố định phía client. Kho license đã tách đúng nhưng không đủ làm cơ chế thương mại khi công khai source.
15. Danh sách công việc có phân trang; nhiều phân hệ khác vẫn gọi `getAll()` rồi lọc trong bộ nhớ. Quy mô rất lớn có thể chậm và dùng RAM cao.
16. PWA cache app shell và dọn cache cũ, nhưng thông báo cập nhật chưa có nút cập nhật có kiểm soát và chưa kiểm tra trạng thái draft trước reload.

## 3. Ma trận xử lý

| Hạng mục | Hiện trạng | Rủi ro | Cách sửa | Kiểm thử xác nhận |
|---|---|---|---|---|
| Nguồn sự thật | 6 hàm trùng | Ghi đè ngầm | Xóa bản legacy, giữ một khai báo duy nhất | Phân tích AST: 0 hàm trùng |
| Định danh | Thiếu App ID/profile ID | Nhầm sản phẩm/trường khi restore | Khai báo hằng bất biến; ghi vào manifest/backup/bản ghi | Migration và restore sai App ID |
| Schema | Schema 7, 53 store | Thiếu snapshot/journal/draft/staging | Nâng schema tuần tự, chỉ thêm store/index | Mở dữ liệu schema 3 và 7, đối chiếu ID/số bản ghi |
| Tự lưu | Chỉ báo chung | Báo “đã lưu” thiếu căn cứ thời điểm | Chỉ cập nhật sau `transaction.oncomplete`; thêm giờ/lỗi/xung đột | Lưu thành công, quota, abort, revision conflict |
| Nhiều tab | Chưa có khóa | Ghi đè/restore đồng thời | Web Locks; fallback BroadcastChannel + lease; tab phụ chỉ đọc | Mô phỏng hai phiên ghi |
| Journal | Chỉ có audit | Không biết thao tác lớn dở dang | Store journal, trạng thái start/complete/fail/interrupted | Reload giữa thao tác |
| Điểm phục hồi | Chưa có | Không quay lại trước thao tác lớn | Snapshot không nhân Blob, checksum, retention, preview/restore phạm vi thật | Tạo/xem/khôi phục/dọn phiên bản |
| Backup ngoài | Nhanh/đầy đủ | Thiếu gói năm học và xác minh riêng | Định dạng TPT-BACKUP-3, 3 loại, kiểm tra checksum, thư mục tùy chọn | Quick/full/year, mã hóa, sai mật khẩu, tệp hỏng |
| Restore | Replace nguyên tử; merge đơn giản | Xung đột revision chưa rõ | Preview theo ID/revision/checksum, snapshot trước restore, staging/journal | Merge/replace/cancel/fault injection |
| Năm học | Tạo năm đơn giản | Sao chép sai hoặc sửa năm cũ | Wizard mở/đóng, checklist, lựa chọn copy, read-only và audit lý do | Đóng năm, mở năm, không mang điểm cũ |
| Báo cáo chốt | HTML tĩnh | Thiếu metadata và phiên bản chốt | Draft/final/submitted, version, checksum, filters, source count | Sửa nguồn không đổi bản chốt |
| PWA | App shell cơ bản | Reload khi còn draft | Banner cập nhật có nút và kiểm tra lưu | Cài lần đầu, update, offline, subpath |
| EXE | Chưa có | Chưa thể đóng gói trực tiếp | Tách hợp đồng adapter và hồ sơ sẵn sàng Tauri; không tuyên bố đã có EXE | Kiểm tra interface và tài liệu chuyển dữ liệu |
| Giao diện | Căn giữa, giới hạn rộng | Khoảng trống lớn | Cho nội dung dùng toàn bộ chiều rộng; rà footer/modal/table/zoom | 1366, 1440, 1920, tablet, mobile, zoom |
| Footer | Zalo là hyperlink | Sai yêu cầu người phát hành | Hiển thị đúng một dòng chữ thuần, 11px, không lặp modal | DOM/text/print |

## 4. Kết quả baseline trước sửa

- JavaScript nội tuyến parse thành công.
- Bộ test nâng cấp: đạt.
- Bộ test migration schema 3: đạt.
- Bộ test nâng cao về trường tùy chỉnh, Blob/phiên bản/liên kết tài liệu, công việc lặp và 100 lớp: đạt.
- Không có ID trùng trong DOM tĩnh.
- Giới hạn: baseline tự động chưa chứng minh EXE, GitHub Pages thực tế, thời gian chạy nhiều giờ hoặc giao diện pixel-perfect trên máy người dùng.
