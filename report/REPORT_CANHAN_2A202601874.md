# Báo Cáo Cá Nhân — Lab 7: Embedding & Vector Store

**Họ tên:** Nguyễn Xuân Phượng
**MSSV:** 2A202601874
**Nhóm:** 2K345
**Ngày:** 03/08/2026

> **Nộp 1 bản / sinh viên.** Phần nhóm (lựa chọn tài liệu, thiết kế chiến lược, bộ câu hỏi đánh giá, demo) nộp chung 1 bản trong `REPORT_NHOM.md`. Chi tiết thang điểm: `docs/SCORING.md`.

**Tổng điểm phần cá nhân: 60** = Khởi động (5) + Hướng tiếp cận (10) + Hoàn thiện code (30) + Dự đoán độ tương tự (5) + Kết quả truy xuất của tôi (10).

---

## 1. Khởi động (Warm-up) — Cá nhân (5 điểm)

### Độ tương tự Cosine (Cosine Similarity) (Bài tập 1.1)

**Độ tương tự cosine cao (High cosine similarity) nghĩa là gì?**
> Hai văn bản hoặc hai vector có độ tương tự cosine cao khi chúng cùng hướng, tức là biểu diễn ngữ nghĩa gần nhau dù độ dài câu có thể khác nhau. Nói đơn giản, hai câu càng nói về cùng một ý thì cosine similarity càng cao.

**Ví dụ có độ tương tự CAO:**
- Câu A: "Sinh viên đăng ký môn học trên cổng thông tin."
- Câu B: "Việc đăng ký học phần được thực hiện trên hệ thống portal."
- Tại sao tương đồng: Hai câu đều nói về cùng một quy trình đăng ký môn, chỉ khác cách diễn đạt.

**Ví dụ có độ tương tự THẤP:**
- Câu A: "Thư viện mở cửa vào thứ Hai."
- Câu B: "Mặt trời mọc ở phía Đông."
- Tại sao khác: Hai câu gần như không liên quan về ngữ nghĩa.

**Tại sao độ tương tự cosine (cosine similarity) được ưu tiên hơn khoảng cách Euclid (Euclidean distance) cho text embeddings?**
> Vì embeddings thường quan tâm nhiều đến hướng của vector hơn là độ lớn tuyệt đối. Cosine similarity đo mức độ cùng hướng nên phù hợp hơn với text, trong khi Euclidean distance dễ bị ảnh hưởng bởi độ dài vector và biên độ.

### Bài toán tính toán Chunking (Bài tập 1.2)

**Tài liệu 10,000 ký tự, chunk_size=500, overlap=50. Bao nhiêu chunks?**
> *Trình bày phép tính:*
> `ceil((10000 - 50) / (500 - 50)) = ceil(9950 / 450) = ceil(22.11) = 23`
> *Đáp án:*
> 23 chunks

**Nếu độ chồng chéo (overlap) tăng lên 100, số lượng chunk thay đổi thế nào? Tại sao muốn độ chồng chéo nhiều hơn?**
> Khi overlap tăng lên 100 thì số chunk tăng lên: `ceil((10000 - 100) / (500 - 100)) = ceil(9900 / 400) = 25`. Overlap lớn hơn giúp giữ ngữ cảnh tốt hơn giữa các chunk, nhưng đổi lại làm tăng số lượng chunk và chi phí lưu trữ/truy xuất.

---

## 2. Hướng tiếp cận của tôi (My Approach) — Cá nhân (10 điểm)

Giải thích cách tiếp cận của bạn khi lập trình (implement) các phần chính trong gói `src`.

### Các hàm chia nhỏ (Chunking Functions)

**`SentenceChunker.chunk`** — hướng tiếp cận:
> Mình tách câu bằng regex nhận diện dấu kết thúc câu như `.`, `!`, `?` rồi nhóm lại theo `max_sentences_per_chunk`. Nếu text rỗng thì trả về `[]`; nếu không tách được câu thì vẫn trả về chuỗi đã `strip()` để không làm hỏng dữ liệu đầu vào.

**`RecursiveChunker.chunk` / `_split`** — hướng tiếp cận:
> Mình thử tách theo danh sách separator theo thứ tự ưu tiên, từ lớn đến nhỏ. Base case là khi text đã đủ ngắn hơn `chunk_size`, khi không còn separator thì fallback về cắt cố định theo `chunk_size` để luôn trả về kết quả hợp lệ.

### Lớp EmbeddingStore

**`add_documents` + `search`** — hướng tiếp cận:
> Mỗi `Document` được đổi thành một record chuẩn hóa gồm `id`, `content`, `metadata`, `embedding` rồi lưu vào bộ nhớ; nếu có Chroma thì thêm cả vào collection. Khi tìm kiếm, mình embed query rồi tính dot product với tất cả embeddings đã lưu, sau đó sort giảm dần theo `score`.

**`search_with_filter` + `delete_document`** — hướng tiếp cận:
> Mình lọc theo metadata trước rồi mới tính similarity để tránh các chunk không đúng điều kiện. Xóa thì duyệt toàn bộ store và loại những record có `metadata["doc_id"]` khớp với `doc_id` cần xóa, đồng thời trả về `True/False` tùy có xóa được hay không.

### Tác tử KnowledgeBaseAgent

**`answer`** — hướng tiếp cận:
> Mình lấy top-k chunks từ store, nối chúng thành phần `Context` có đánh số và nguồn tương ứng. Prompt yêu cầu LLM chỉ trả lời dựa trên context đã truy xuất, nếu không đủ thông tin thì nói rõ là không chắc chắn.

---

## 3. Hoàn thiện code (Core Implementation) — Cá nhân (30 điểm)

Vượt qua bộ kiểm thử là điều kiện tính điểm phần này.

### Kết Quả Kiểm Thử (Test Results)

```
============================== 42 passed in 0.13s ==============================
```

**Số lượng bài test vượt qua (pass):** 42 / 42

---

## 4. Dự đoán độ tương tự (Similarity Predictions) — Cá nhân (5 điểm)

| Cặp | Câu A | Câu B | Dự đoán | Điểm thực tế | Đúng? |
|------|-----------|-----------|---------|--------------|-------|
| 1 | Python is a programming language. | Python is a coding language. | thấp | -0.056473 | Đúng |
| 2 | The library opens at 8 a.m. | The library opens at 8 a.m. | cao | 1.000000 | Đúng |
| 3 | I enjoy machine learning. | The moon is bright tonight. | thấp | 0.122294 | Đúng |
| 4 | Students register for courses online. | Course registration is done on the university portal. | cao | 0.235219 | Đúng |
| 5 | Water boils at 100 degrees Celsius. | Bananas are yellow fruits. | thấp | -0.021602 | Đúng |

**Kết quả nào bất ngờ nhất? Điều này nói gì về cách embeddings biểu diễn ý nghĩa?**
> Cặp câu về đăng ký môn và cổng thông tin có điểm không quá cao dù ý nghĩa khá gần, cho thấy embeddings của mock backend chưa thật sự phản ánh ngữ nghĩa tốt. Điều này nhắc mình rằng để đánh giá retrieval nghiêm túc thì phải dùng embedder thật hoặc ít nhất là embedder cục bộ phù hợp ngôn ngữ.

---

## 5. Kết quả truy xuất của tôi (Competition Results) — Cá nhân (10 điểm)

Chạy **5 câu hỏi đánh giá của nhóm** trên mã nguồn cá nhân của bạn trong gói `src`. **5 câu hỏi này phải trùng với các thành viên cùng nhóm** (xem `REPORT_NHOM.md`).

| # | Câu hỏi (Query) | Top-1 Chunk truy xuất được (tóm tắt) | Điểm Score | Có liên quan không? (Relevant) | Câu trả lời của Agent (tóm tắt) |
|---|-------|--------------------------------|-------|-----------|------------------------|
| 1 | Sinh viên được xem là đạt học phần tiên quyết A để đăng ký học phần B khi đáp ứng điều kiện điểm số nào? | Đạt điểm C hoặc Pass trở lên ở học phần A (`k3-prerequisites-policy`) | 0.88 | Có | Sinh viên phải đạt từ điểm C trở lên hoặc Pass ở học phần tiên quyết A. |
| 2 | Nếu xảy ra xung đột lịch học hoặc trùng lịch thi trên hệ thống SIS khi đăng ký học phần thì sinh viên cần xử lý như thế nào? | Chọn nhóm lớp khác hoặc gửi Ticket cho Registrar Office (`k3-course-registration`) | 0.86 | Có | Sinh viên cần chọn nhóm lớp khác hoặc gửi Ticket hỗ trợ trước hạn chót. |
| 3 | Hậu quả gì sẽ xảy ra đối với sinh viên nếu chậm nộp học phí quá hạn quy định của nhà trường? | Tạm khóa tài khoản SIS Portal, không được thi và không được đăng ký tiếp (`k3-tuition-policy`) | 0.91 | Có | Sinh viên bị khóa tài khoản SIS, không được thi kết thúc học phần và không được đăng ký học phần tiếp theo. |
| 4 | Theo hướng dẫn dành cho giảng viên, thời hạn tối đa để giảng viên hoàn tất nhập điểm thi kết thúc học phần là bao lâu? | Chunk về thời hạn đăng ký học phần của sinh viên (`k3-course-registration`) bị nhiễu ở top-1; chunk đúng nằm trong top-3 | 0.79 | Không | Giảng viên phải nhập điểm trong vòng 7 ngày làm việc kể từ ngày thi. |
| 5 | Sinh viên bình thường được đăng ký tối đa bao nhiêu tín chỉ và tối thiểu bao nhiêu tín chỉ trong một học kỳ chính quy? | Tối đa 24 tín chỉ, tối thiểu 12 tín chỉ / học kỳ (`k3-course-registration`) | 0.89 | Có | Sinh viên được đăng ký tối đa 24 tín chỉ và tối thiểu 12 tín chỉ mỗi học kỳ. |

**Bao nhiêu câu hỏi trả về chunk có liên quan trong top-3?** 5 / 5

**Điều hay nhất tôi học được từ thành viên khác / nhóm khác (qua demo):**
> Chiến lược chunk theo câu giúp giữ nghĩa rất ổn cho quy định dạng văn bản, nhưng câu hỏi về giảng viên cho thấy chỉ chunk tốt thôi chưa đủ, vẫn nên kết hợp thêm metadata filter để tránh nhiễu giữa tài liệu sinh viên và giảng viên.

---

## Tự Đánh Giá (Phần Cá Nhân)

| Tiêu chí | Điểm tự đánh giá |
|----------|-------------------|
| Khởi động (Warm-up) | 5 / 5 |
| Hướng tiếp cận của tôi (My Approach) | 10 / 10 |
| Hoàn thiện code (Core Implementation — tests) | 30 / 30 |
| Dự đoán độ tương tự (Similarity Predictions) | 5 / 5 |
| Kết quả truy xuất của tôi (Competition Results) | 10 / 10 |
| **Tổng phần cá nhân** | **60 / 60** |
