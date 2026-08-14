# Hạng mục còn lại / cổng phát hành chưa đạt

Không phát hiện lỗi Blocker/Critical/High trong 62 kiểm tra tự động. Các mục dưới đây là **khoảng trống bằng chứng hoặc sản phẩm chưa được build**, nên vẫn chặn tuyên bố phát hành chính thức.

| ID | Loại | Mức chặn phát hành | Ảnh hưởng | Cách tránh hiện tại | Việc cần làm |
|---|---|---|---|---|---|
| RG-01 | Nghiệm thu trình duyệt | Blocker cổng phát hành | Chưa chứng minh layout 1366/1440/1920/tablet/mobile, zoom, modal, in A4 và memory leak trên Chrome/Edge thật | Chỉ dùng dữ liệu thử; chưa phát hành toàn trường | Chạy ma trận viewport/zoom/in và soak ≥8 giờ, lưu ảnh/kết quả |
| RG-02 | GitHub/PWA thực tế | Blocker cổng phát hành | Chưa chứng minh deployment subpath, cài PWA, offline lần hai và update giữ IndexedDB trên site thật | Deploy thử riêng, backup trước | Deploy staging HTTPS, chạy online→offline→update→đối chiếu dữ liệu |
| RG-03 | EXE | Blocker nếu phạm vi bắt buộc có EXE | Không có installer/portable, storage path, auto-backup native, signature hoặc test nâng cấp | Dùng web/PWA và backup `TPT-BACKUP-3` | Chọn Tauri/Electron, triển khai adapter, build/sign/test Windows |
| RG-04 | License thương mại | Blocker nếu bán/triển khai có cấp phép | Hai mã hash phía client có thể bị phân tích; không phải DRM | Chỉ dùng demo nội bộ | Dùng dịch vụ cấp phép/kho khóa hệ điều hành và quy trình thu hồi |
| RG-05 | File System Access thật | Medium bằng chứng | Quyền thư mục và lịch lúc mở phụ thuộc Chrome/Edge/hệ điều hành | Luôn giữ tệp tải xuống thứ hai | Test cấp/thu hồi quyền, đường dẫn tiếng Việt, disk full và file lock |
| RG-06 | Dữ liệu thật | Medium bằng chứng | Fixture không bao quát mọi tên dài, file Office lớn và quy trình riêng trường | Pilot trên bản sao đã ẩn danh | Nghiệm thu với dữ liệu đã ẩn danh và rubric nghiệp vụ của trường |

Môi trường đã có gói Playwright nhưng không có browser executable; không tải/cài được Chromium trong phiên làm việc này. Không có ảnh giả lập thay cho ảnh chụp thật.

Chủ sản phẩm chỉ nên đổi nhãn từ RC sang bản phát hành chính thức sau khi RG-01 và RG-02 đạt; nếu EXE nằm trong phạm vi bàn giao thì RG-03/RG-04 cũng phải đạt.
