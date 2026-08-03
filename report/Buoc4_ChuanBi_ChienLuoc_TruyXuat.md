# Kết Quả Bước 4 - Chuẩn Bị Chiến Lược Truy Xuất

**Ngày thực hiện:** 03/08/2026  
**Nhóm:** 2K345

---

## 1. Mục Tiêu

Nhóm chuẩn bị phần truy xuất để so sánh các chiến lược chunking khác nhau trên cùng một corpus.

Mục tiêu gồm:
- Có baseline rõ ràng
- Có chiến lược riêng cho từng thành viên
- Có cơ sở để benchmark công bằng

---

## 2. Hướng Chuẩn Bị

- Chọn cùng một corpus: `data/k3_university/`
- Dùng cùng 5 câu benchmark ở Bước 5
- Mỗi thành viên thử một chiến lược chunking khác nhau
- Dùng metadata filter khi câu hỏi yêu cầu đúng đối tượng

---

## 3. Chia Sơ Bộ Cho 3 Thành Viên

- Nguyễn Xuân Phượng: `SentenceChunker`
- Lê Nguyễn Minh Đức: `RecursiveChunker`
- Nguyễn Đào Nam Hải: `CustomSectionHeaderChunker`

---

## 4. Đường Cơ Sở

Nhóm dùng `ChunkingStrategyComparator().compare()` để có baseline trước khi đánh giá chiến lược riêng.

Baseline giúp so sánh:
- số lượng chunk
- độ dài trung bình chunk
- mức độ giữ ngữ cảnh

---

## 5. Cách Đánh Giá

- Top-3 phải chứa chunk liên quan
- Câu trả lời của agent phải đúng với gold answer
- Câu hỏi về giảng viên nên ưu tiên `metadata_filter={"audience": "faculty"}`

