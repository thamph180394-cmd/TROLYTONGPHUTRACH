# Báo cáo kiểm thử — 3.0.0-rc.1

Ngày chạy cuối: 14/08/2026 (Asia/Ho_Chi_Minh)  
Nguồn: `index.html` và `index_tro_ly_tpt_doi_thcs_nang_cap.html`  
SHA-256 hai tệp: `4dd0372505925d0bcdbcb852156e4bfbfaca3bd862173b4ccd28350086cbb554`

## 1. Phạm vi và môi trường

Chạy `npm test` bằng Node.js, JSDOM và fake-indexeddb. Bộ này kiểm tra logic/DOM/IndexedDB có thể lặp lại nhưng **không phải** benchmark Chrome/Edge thật, không chứng minh service worker offline thật, giao diện pixel-perfect, in A4 hoặc EXE.

## 2. Kết quả tự động

| Nhóm | Số kiểm tra | Kết quả | Nội dung chính |
|---|---:|---|---|
| Tĩnh/source/PWA | 14 | 14 đạt | Parse JS, 0 hàm/ID trùng, source=index, v1 nguyên vẹn, định danh, 0 URL ngoài, không eval, footer, full-width, PWA relative, update có kiểm soát |
| Core | 16 | 16 đạt | Kích hoạt, 16 phân hệ, schema/store, record contract, journal, snapshot, migration một lần, backup UI, footer, năm học, báo cáo |
| Migration | 8 | 8 đạt | Schema 3→8, giữ 3/3 ID và nội dung fixture cũ, chuẩn hóa metadata, phát hiện from_schema=3, snapshot sau migration |
| Fault/feature | 17 | 17 đạt | Conflict, quota, tính điểm/đồng hạng, draft, checksum hỏng, thiếu Blob, sai mật khẩu, full Blob, replace round-trip, báo cáo chốt bất biến, năm học, đa tab |
| Tải lớn | 7 | 7 đạt | 10.000 việc, 100 lớp, 200 hồ sơ-tệp, phân trang, bảng điểm, snapshot, quick backup |
| **Tổng** | **62** | **62 đạt, 0 thất bại** | Không có lỗi runtime/console trong môi trường tự động |

Các lệnh chạy lại:

```bash
npm ci
npm test
```

## 3. Fault injection và đối chiếu dữ liệu

| Kịch bản | Kỳ vọng | Kết quả |
|---|---|---|
| Ghi bằng revision cũ | Từ chối, giữ bản mới | Đạt |
| Object store ném `QuotaExceededError` | Không ghi, báo thiếu dung lượng | Đạt |
| Backup cấu trúc bị sửa nhưng giữ checksum cũ | Từ chối trước khi ghi | Đạt |
| Full backup khai báo attachment nhưng thiếu file | Từ chối | Đạt |
| AES-GCM sai mật khẩu | Từ chối | Đạt |
| Blob thật → base64/checksum → replace restore | Nội dung, ID, SHA-256 khớp | Đạt |
| Replace restore sau khi thêm marker | Marker ngoài backup biến mất, dữ liệu backup phục hồi | Đạt |
| Hai phiên dùng Web Locks | Phiên hai chỉ đọc | Đạt |
| Báo cáo chốt rồi đổi nguồn | HTML/checksum/revision bản chốt không đổi | Đạt |

## 4. Migration

- Database cũ giả lập: schema 3, ba bản ghi legacy (`school`, `task`, `document`).
- Database sau mở: schema 8, đủ store/index mới.
- ID legacy giữ nguyên: 3/3.
- Nội dung kiểm tra giữ nguyên: 3/3.
- Ba bản ghi cũ được bổ sung `school_profile_id`, thời gian, revision, source; task có cả `school_year_id` và `academic_year_id`.
- Nhật ký ghi đúng `from_schema: 3`, `to_schema: 8`; migration thành công một lần; có snapshot bảo vệ.

## 5. Tính điểm và báo cáo

- Bộ điểm chuẩn 100, ba lớp, năm tiêu chí; hai lớp tổng 100 đồng hạng 1, lớp tổng 98 hạng 3; đủ dữ liệu: đạt.
- Báo cáo chốt có version, nơi nhận, trạng thái gửi, checksum nguồn/nội dung, config snapshot và immutable flag: đạt.
- Thay đổi dữ liệu nguồn sau chốt không đổi phiên bản báo cáo: đạt.

## 6. Kết quả tải lớn

Môi trường: Node.js + JSDOM + fake-indexeddb; số đo chỉ dùng so sánh hồi quy trong môi trường này.

| Thao tác | Thời gian cuối (ms) |
|---|---:|
| Seed 10.000 việc + 100 lớp + 200 hồ sơ/tệp | 819,7 |
| Render trang công việc có phân trang | 153,0 |
| Render bảng điểm ≥100 lớp | 101,6 |
| Render 200 hồ sơ/tệp | 93,2 |
| Mở trang backup với >10.000 bản ghi | 333,2 |
| Tạo snapshot >10.000 bản ghi | 251,5 |
| Quick backup >10.000 bản ghi | 1.310,1 |

Không phát hiện lỗi runtime trong lượt đo. Chưa đo RAM/leak nhiều giờ trên trình duyệt thật.

## 7. PWA/GitHub và giao diện thật

- Manifest JSON hợp lệ; `id/scope/start_url` đều tương đối; service worker cache `index.html`, dọn cache cũ, fallback offline và nhận `SKIP_WAITING`: kiểm tra tĩnh đạt.
- Runtime HTML không có URL ngoài, fetch/API/CDN/analytics/telemetry: kiểm tra tĩnh đạt.
- Đã chuẩn bị `404.html`, `offline.html`, icon và cấu trúc deploy subpath.
- Đã thử khởi chạy Playwright để chụp/kiểm tra Chrome thật nhưng môi trường không có Chromium/Firefox executable; do đó **chưa có bằng chứng PWA offline/GitHub Pages và ảnh giao diện thật**.

## 8. Cổng chất lượng

| Tiêu chí | Trạng thái | Bằng chứng/ghi chú |
|---|---|---|
| App ID/database ổn định | Đạt | Static + source |
| Migration không mất fixture cũ | Đạt trong test | 3/3 ID/nội dung |
| Tự lưu theo transaction | Đạt | Core/conflict/quota |
| Snapshot và giới hạn đúng | Đạt | Snapshot tests/docs |
| Quick/full/year backup | Đạt trong test | Fault + close year + large |
| Blob/checksum/restore sạch | Đạt trong test | Full round-trip |
| Chuyển năm đúng quy tắc | Đạt trong test | New/close year |
| Báo cáo chốt bất biến | Đạt | Source-change test |
| Tính điểm/xếp hạng | Đạt với fixture | Ranking test |
| Không secret/analytics/network mặc định | Đạt tĩnh | 0 URL ngoài |
| Footer đúng | Đạt | DOM + static |
| GitHub Pages thực tế/subpath | **Chưa nghiệm thu** | Chưa có deployment thật |
| PWA offline/update thật | **Chưa nghiệm thu** | Thiếu browser executable |
| Ma trận viewport/zoom/in A4 | **Chưa nghiệm thu** | Chưa có browser/screenshot |
| Chạy nhiều giờ/memory leak | **Chưa nghiệm thu** | Chưa có soak test thật |
| EXE cài/nâng cấp/gỡ | **Không có** | Chỉ adapter contract/stub |
| License thương mại | **Không có** | License client chỉ demo |

## 9. Quyết định

Trong phạm vi 62 kiểm tra tự động: 0 lỗi Blocker/Critical/High đã biết, 0 thất bại. Tuy nhiên cổng phát hành chính thức theo yêu cầu vẫn **HOLD** vì thiếu nghiệm thu GitHub/PWA/browser thật, ma trận hình ảnh/in/soak và toàn bộ EXE. Bản này được gắn đúng nhãn **RC1**, không tuyên bố hoàn thiện chính thức.
