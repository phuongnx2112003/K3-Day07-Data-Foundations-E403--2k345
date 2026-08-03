# Kết Quả Bước 2-4 - Thu Thập, Chuẩn Hóa Và Kiểm Kê Corpus

## Bước 2 - Thu thập và làm sạch tài liệu

Nhóm đã chuyển corpus K3 từ các file template sang bộ tài liệu công khai thật, tập trung vào hai cụm nội dung:
- đăng ký học phần và quy định học vụ
- dịch vụ và quy định thư viện

Bộ dữ liệu hiện có 5 tài liệu, đều là nguồn công khai từ hệ thống chính thức của VinUni.

### Data inventory

| # | Tên tài liệu | Nguồn (Source URL) | Ngày lấy / Phiên bản | Số ký tự | Metadata đã gán |
|---|---|---|---|---|---|
| 1 | Hướng dẫn đăng ký học phần | https://registrar.vinuni.edu.vn/academics/class-schedule-course-registration/ | 2026-08-03 / 2026-08-03 | ~900 | `doc_id`, `title`, `audience`, `department`, `language`, `source_url`, `retrieved_at`, `document_version` |
| 2 | Quy định đăng ký, thêm, bỏ và rút học phần | https://policy.vinuni.edu.vn/all-policies/academic-regulations-for-full-time-undergraduate-programs/ | 2026-08-03 / 2026-08-03 | ~900 | `doc_id`, `title`, `audience`, `department`, `language`, `source_url`, `retrieved_at`, `document_version` |
| 3 | Quy trình lập lịch học và mở đăng ký | https://policy.vinuni.edu.vn/all-policies/university-academic-scheduling-procedures/ | 2026-08-03 / 2021-11-22 | ~500 | `doc_id`, `title`, `audience`, `department`, `language`, `source_url`, `retrieved_at`, `document_version` |
| 4 | Quyền mượn thư viện cho sinh viên bậc đại học | https://library.vinuni.edu.vn/services/borrow-and-request/undergraduate-and-staff/ | 2026-08-03 / 2026-08-03 | ~700 | `doc_id`, `title`, `audience`, `department`, `language`, `source_url`, `retrieved_at`, `document_version` |
| 5 | Quy định truy cập và sử dụng thư viện | https://policy.vinuni.edu.vn/all-policies/library-policies-for-users/ | 2026-08-03 / 2026-08-03 | ~800 | `doc_id`, `title`, `audience`, `department`, `language`, `source_url`, `retrieved_at`, `document_version` |

## Bước 3 - Chuẩn hóa metadata

Nhóm thống nhất schema metadata chung cho mọi tài liệu:

| Trường metadata | Kiểu | Ví dụ | Tác dụng |
|---|---|---|---|
| `doc_id` | string | `vinuni-course-registration` | Định danh duy nhất cho từng tài liệu |
| `title` | string | `Hướng dẫn đăng ký học phần` | Giúp đọc kết quả retrieval dễ hơn |
| `source_url` | string | URL trang gốc | Truy vết nguồn gốc nội dung |
| `retrieved_at` | date string | `2026-08-03` | Kiểm tra độ mới của dữ liệu |
| `document_version` | string | `2026-08-03` | Theo dõi phiên bản/hiệu lực |
| `audience` | string | `student` / `all` | Hỗ trợ filter theo đối tượng |
| `department` | string | `registrar` / `library` | Hỗ trợ filter theo đơn vị |
| `language` | string | `vi` | Hỗ trợ lọc ngôn ngữ |

## Bước 4 - Kiểm kê nguồn

File `sources.csv` đã được cập nhật để khớp 1-1 với 5 file tài liệu trong `data/k3_university/`.

Đánh giá nhanh:
- Corpus đã có đủ 5 tài liệu tối thiểu
- Có cả tài liệu về registration và library
- Metadata đủ để dùng `search_with_filter()`
- Ít nhất một tài liệu có `audience: student` để phục vụ benchmark query cần lọc

## Kết luận

Bước 2-4 đã hoàn tất ở mức corpus chuẩn bị:
- bộ tài liệu đã sạch hơn và không còn là template mẫu
- metadata đồng nhất
- kiểm kê nguồn khớp với file thật

Nhóm có thể chuyển sang bước benchmark và so sánh retrieval strategy ở giai đoạn tiếp theo.
