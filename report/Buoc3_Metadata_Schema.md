# Kết Quả Bước 3 - Thiết Kế Cấu Trúc Metadata (Metadata Schema)

**Ngày thực hiện:** 03/08/2026  
**Nhóm:** 2K345

---

## 1. Mục Tiêu

Nhóm thiết kế metadata schema để:
- Truy vết được nguồn gốc tài liệu
- Hỗ trợ `search_with_filter()`
- Phân biệt đúng đối tượng sử dụng như `student` và `faculty`
- Giảm nhiễu khi đánh giá retrieval

---

## 2. Metadata Bắt Buộc

Mỗi file trong `data/k3_university/` đều có:

- `doc_id`
- `title`
- `audience`
- `department`
- `category`
- `language`
- `source_url`
- `retrieved_at`
- `document_version`

---

## 3. Mẫu Schema

```yaml
---
doc_id: k3-scholarship-policy
title: Tiêu chuẩn và Quy trình Xét Học bổng Khuyến khích Học tập (VinUniversity)
audience: student
department: student-affairs
category: scholarship
language: vi
source_url: https://vinuni.edu.vn/admission/scholarships-and-financial-aid/
retrieved_at: 2026-08-03
document_version: "2026.1"
---
```

---

## 4. Lý Do Chọn Các Trường Này

- `audience`: lọc theo đối tượng, đặc biệt hữu ích cho câu hỏi giảng viên
- `department`: chia nhóm theo đơn vị phụ trách
- `category`: gom nhóm theo chủ đề như `course-registration`, `tuition-fee`, `grading-policy`
- `language`: đảm bảo corpus đồng nhất
- `source_url`, `retrieved_at`, `document_version`: phục vụ truy vết và kiểm tra tính mới của dữ liệu

---

## 5. Kết Quả Kiểm Tra

- `sources.csv` khớp 1-1 với 6 file tài liệu
- Mỗi tài liệu đều parse được front matter
- Metadata đủ để dùng cho `search_with_filter()`

