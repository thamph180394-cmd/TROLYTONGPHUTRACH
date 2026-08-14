# Hướng dẫn bàn giao — Trợ lý Tổng phụ trách Đội THCS 3.0 RC1

## 1. Mở ứng dụng

- Dùng nhanh một máy: mở `index.html` hoặc `index_tro_ly_tpt_doi_thcs_nang_cap.html` bằng Chrome/Edge.
- Dùng như PWA: triển khai cả thư mục qua HTTPS hoặc GitHub Pages, mở trực tuyến một lần rồi chọn cài ứng dụng nếu trình duyệt hỗ trợ.
- Mã trải nghiệm demo: `thayhieu` (120 giờ). Mã vĩnh viễn demo: `admin@`.
- Hai mã trên chỉ phục vụ demo/bàn giao nội bộ. Không dùng cơ chế hash trong HTML làm giấy phép thương mại.

## 2. Thiết lập lần đầu

Trình hướng dẫn gồm 9 bước: trường, cơ sở, năm học, lớp, giáo viên chủ nhiệm, tiêu chí, công việc định kỳ, chế độ dữ liệu và kiểm tra sẵn sàng. Dữ liệu mẫu có nhãn riêng và có thể xóa mà không xóa bản ghi tự tạo.

`APP_ID` là `vn.giaoducso40.tpt.thcs.standard`; database là `TPT_DOI_THCS_DB`; schema hiện tại là 8. Không đổi các định danh này khi cập nhật cùng một sản phẩm/trường.

## 3. Dữ liệu được lưu ở đâu

- Nghiệp vụ và Blob tệp: IndexedDB của đúng trình duyệt, đúng hồ sơ người dùng và đúng origin.
- Giấy phép demo: database riêng `TPT_DOI_THCS_LICENSE_DB`; không nằm trong backup nghiệp vụ.
- Cấu hình trường thật không nằm trong source phát hành; được nhập trên thiết bị.
- Xóa dữ liệu trang, đổi trình duyệt/hồ sơ/origin hoặc thiếu dung lượng có thể làm mất dữ liệu nếu chưa có backup ngoài.

## 4. Phân biệt bốn cơ chế

| Cơ chế | Nằm ở đâu | Dùng để làm gì | Có bảo vệ khi mất dữ liệu trình duyệt? |
|---|---|---|---|
| Tự lưu | IndexedDB hiện tại | Ghi từng thay đổi sau khi transaction hoàn tất | Không |
| Điểm khôi phục nội bộ | Cùng IndexedDB | Quay lại trước thao tác rủi ro; không nhân đôi Blob | Không |
| Sao lưu ngoài | Tệp tải xuống/thư mục đã cấp quyền | Chuyển máy, cài sạch, lưu trữ độc lập | Có, nếu tệp được cất an toàn |
| Báo cáo chốt | Kho báo cáo nghiệp vụ | Giữ phiên bản nội dung, bộ lọc và checksum bất biến | Không thay thế backup |

Thanh trạng thái chỉ báo “Đã lưu lúc…” sau `transaction.oncomplete`; có trạng thái riêng cho nháp, xung đột revision, hết dung lượng và lỗi lưu.

## 5. Điểm khôi phục nội bộ

Vào **Sao lưu – đồng bộ → Tạo điểm ngay**. Mặc định giữ 7 bản ngày, 4 bản tuần, 12 bản tháng; điểm bảo vệ trước migration/restore/khóa điểm/chốt báo cáo/đóng năm không tự xóa. Snapshot chứa dữ liệu cấu trúc và SHA-256, không chứa bản sao Blob để tránh tăng dung lượng.

## 6. Sao lưu ngoài và kiểm tra

- **Nhanh:** dữ liệu cấu trúc, không có nội dung Blob; dùng thường xuyên.
- **Đầy đủ:** cấu trúc và Blob thật, metadata tệp, kích thước, SHA-256; dùng trước khi đổi máy/cài sạch.
- **Gói năm học:** dữ liệu năm đang chọn, cấu hình dùng chung, báo cáo chốt và tệp liên quan.
- Có thể mã hóa AES-GCM; phải nhập mật khẩu hai lần. Quên mật khẩu thì ứng dụng không thể khôi phục.
- Nút **Kiểm tra bản đã ghi nhận** đối chiếu nhật ký/checksum lúc tạo. Muốn kiểm tra lại chính tệp đã tải, phải chọn lại tệp vì web không tự đọc thư mục Tải xuống.

Chrome/Edge hỗ trợ File System Access có thể chọn thư mục. Tự động hằng ngày chỉ chạy khi ứng dụng đang mở và quyền thư mục còn hiệu lực; web/PWA không bảo đảm chạy nền sau khi đóng.

## 7. Phục hồi và chuyển máy

1. Ở máy cũ, tạo backup **đầy đủ** và cất bản thứ hai trên ổ/đám mây do đơn vị quản lý.
2. Mở bản ứng dụng cùng hoặc mới hơn ở máy mới.
3. Vào **Sao lưu – đồng bộ → Chọn tệp phục hồi**.
4. Kiểm tra App ID, hồ sơ trường, schema, số bản ghi/tệp, checksum và bảng xung đột.
5. Chọn **Hợp nhất** để chỉ lấy bản mới hơn theo ID/revision/thời điểm, hoặc **Thay thế** để ghi dữ liệu nghiệp vụ trong một transaction.
6. Ứng dụng tạo snapshot bảo vệ trước khi ghi. Sau phục hồi, đối chiếu lớp, công việc, điểm, báo cáo và mở thử tệp.

Không phục hồi tệp có nguồn không rõ. Nếu cảnh báo khác hồ sơ trường, chỉ tiếp tục khi thực sự chuyển đúng bộ dữ liệu.

## 8. Đóng năm học và mở năm mới

Vào **Thiết lập → Cơ sở – năm học – học kỳ – tuần**.

- Tạo năm mới: nhập thời gian; chọn sao chép lớp và/hoặc bộ tiêu chí. Hệ thống tạo ID mới, 2 học kỳ, 40 tuần; không sao chép điểm, xếp hạng, hoạt động, việc phát sinh hay báo cáo cũ.
- Đóng năm: xử lý hết bảng thi đua chưa khóa, việc chưa hoàn thành và báo cáo nháp. Ứng dụng tạo snapshot bảo vệ, báo cáo tổng kết chốt, gói năm học rồi đặt năm cũ chỉ đọc.
- Sửa năm đã đóng: chọn **Mở sửa có lý do**, nhập ít nhất 10 ký tự. Quyền chỉ tồn tại trong phiên/thẻ hiện tại và được ghi nhật ký.

## 9. Báo cáo cấp trên

Chọn loại, phạm vi, nơi nhận và trạng thái gửi. **Lưu nháp** và **Chốt báo cáo** là hai thao tác khác nhau. Báo cáo chốt có version, trạng thái, bộ lọc, số bản ghi nguồn, cấu hình, checksum nội dung/nguồn và không tự đổi khi dữ liệu nguồn thay đổi. Nếu cần sửa, tạo phiên bản mới. Nút **Gói báo cáo chốt** xuất báo cáo cùng tệp minh chứng của năm.

## 10. Cập nhật phiên bản

1. Tạo backup đầy đủ trước cập nhật.
2. Giữ nguyên App ID, database name và school profile ID.
3. Thay bộ tệp PWA/source bằng bản mới; không xóa dữ liệu trang.
4. Khi banner “Có phiên bản mới” xuất hiện, lưu/đóng nháp rồi chọn **Cập nhật khi an toàn**.
5. Kiểm tra nhật ký migration, số lớp/công việc/tệp và báo cáo sau mở lại.

## 11. Web/PWA sang EXE

Bản bàn giao này **chưa có EXE**. Khi có bản desktop đã nghiệm thu, dùng `TPT-BACKUP-3` để chuyển dữ liệu. Adapter desktop phải lưu dữ liệu ngoài thư mục cài đặt, có ghi tệp nguyên tử/kho khóa/lịch backup và qua test cài–nâng cấp–gỡ. Xem `desktop/ADAPTER_CONTRACT.md`.

## 12. Giới hạn GitHub Pages, trình duyệt và giấy phép

- GitHub Pages chỉ là hosting tĩnh; không đồng bộ hai máy, không giữ backup thay người dùng và lần mở đầu vẫn cần mạng.
- IndexedDB quota, lưu bền vững, File System Access và hành vi PWA phụ thuộc trình duyệt/hệ điều hành/origin.
- Origin đổi (tên miền, giao thức hoặc cổng) tạo kho dữ liệu khác.
- Bản license hiện tại là demo phía client, không phải DRM/chống sao chép tuyệt đối.

## 13. Xử lý lỗi thường gặp

- **Thẻ chỉ đọc:** đóng thẻ khác đang có quyền ghi rồi tải lại.
- **Thiếu dung lượng:** tạo backup đầy đủ, dọn tệp không cần thiết, kiểm tra quota; không tiếp tục nhập lớn khi thanh trạng thái báo lỗi.
- **Backup sai mật khẩu/checksum/thiếu Blob:** không cố phục hồi; dùng bản sao khác.
- **Không thấy PWA offline:** mở online ít nhất một lần, kiểm tra HTTPS và service worker; xóa cache chỉ sau khi đã backup.
- **Không xem Word/Excel/PowerPoint:** tải tệp và mở bằng phần mềm phù hợp; ứng dụng không giả lập trình xem Office.
- **Bảng điểm khóa:** chỉ mở bằng lý do cụ thể; báo cáo liên quan được đánh dấu cần đối chiếu.

## 14. Bảo vệ dữ liệu và hỗ trợ

Ứng dụng không tải analytics/telemetry và không gửi dữ liệu nghiệp vụ ra mạng mặc định. Nhà trường/người dùng chịu trách nhiệm phân quyền thiết bị, mật khẩu backup, nơi lưu tệp, thời hạn lưu, dữ liệu cá nhân và kiểm tra nội dung trước khi nộp.

Phát triển bởi: Thầy Hiếu (Giáo dục số 4.0) - Zalo: 0812806887
