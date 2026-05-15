# Glossary — Ngày 27

> Giải thích các thuật ngữ tiếng Anh xuất hiện trong lab. Đọc khi gặp từ lạ — không cần học thuộc hết.

---

## Kinh tế học AI

### Usage unit
Hành động nhỏ nhất mà AI làm trong 1 lần chạy. Ví dụ: 1 ticket được phân loại, 1 câu trả lời được tạo, 1 đoạn tóm tắt. Mỗi usage unit có chi phí riêng (token + model). *Trong lab này, usage unit chính là "1 lượt chat" — 1 lần model AI nhận message và trả lời.*

### Job to be Done
Việc người dùng thực sự cần xong, không phải 1 hành động AI riêng lẻ. Thường 1 job = 3–5 usage units xếp chuỗi. *Ví dụ: tourist hỏi "do I need a visa?" — job = "tìm hiểu xong visa requirement". Để xong job có thể cần: phân loại intent + tra cứu RAG + web search + tạo câu trả lời = 4 usage units.*

### Variable cost vs Fixed cost
Chi phí biến đổi (variable) thay đổi theo lượng dùng — phần mềm AI là dạng này, mỗi lần model chạy = tốn tiền. Chi phí cố định (fixed) không đổi theo lượng dùng — phần mềm truyền thống chủ yếu là dạng này. **Sự khác biệt này là nền tảng của AI Product Economics.**

### Cost of Revenue
Chi phí để phục vụ 1 đồng doanh thu. AI products thường có Cost of Revenue cao hơn SaaS truyền thống vì phải trả tiền cho mỗi inference (token + API).

### AI margin gap
Sự chênh lệch margin: SaaS truyền thống 80–90% gross margin, AI products thường chỉ 50–60%. Lý do: cost biến đổi cao + 1 power user có thể phá unit economics.

### ROI (Return on Investment)
Tỷ lệ giá trị / chi phí. Ngưỡng quyết định trong stress test: **>3:1 = GO**, **1–3:1 = CONDITIONAL**, **<1:1 = NO-GO**.

### Reality discount
Hệ số nhân giảm khi tính giá trị thật của AI feature. Công thức tham khảo: adoption ~70% × quality ~80% ≈ 56% → làm tròn xuống **50%** cho an toàn (cho người mới triển khai chưa có data thực tế).

### Stress test
Kiểm tra ROI ở 3 kịch bản: Base (kỳ vọng), Cost ×2 (provider tăng giá hoặc overuse), Value ½ (adoption thấp hoặc AI kém chất lượng), và combined (cả hai cùng lúc). Mục đích: phơi bày giả định nào yếu nhất.

---

## Pricing & monetization

### Cost-Capability-Speed Triangle *(Reforge)*
Tam giác đánh đổi 3 trục — model rẻ thường không mạnh, model mạnh thường chậm, model nhanh thường đắt. **Chọn 2 trong 3**. *Dùng để quyết định model nào cho task nào.*

### Monetization Triad *(Reforge)*
Bộ ba ràng buộc của pricing: **Customer Value × Growth Loop × Cost of Revenue**. Cả 3 phải align — đổi 1 góc → cascade cả 3. Pricing nằm ở giao điểm.

### Value Metric Spectrum *(Reforge)*
Phổ 5 cách định giá: **Seat → Flat → Usage → Outcome → Hybrid**. Đi từ "dễ làm, weak alignment" sang "khó làm, strong alignment". *Hybrid đang chiếm 41% thị trường (tăng từ 27% trong 12 tháng).*

### Attribution × Autonomy Matrix *(Madhavan Ramanujam)*
Ma trận 2 trục để chọn pricing model:
- **Autonomy**: AI tự làm (high) hay AI hỗ trợ (low)?
- **Attribution**: Kết quả quy cho AI (high) hay team effort (low)?

Mỗi quadrant gợi ý 1 pricing model: Seat / Subscription / Usage / Outcome.

### CAMP Framework *(Kyle Poyar)*
4 tiêu chí để outcome-based pricing khả thi:
- **C**onsistency — outcome có nhất quán không?
- **A**ttribution — có rõ AI gây ra outcome không?
- **M**easurability — đo được real-time không?
- **P**redictability — duy trì được unit economics đa dạng khách không?

Chỉ ~5% AI products đáp ứng được cả 4 → chọn được outcome-based pricing.

### Exploration Penalty
Hậu quả tâm lý khi user bị tính phí metered trong giai đoạn khám phá sản phẩm — user self-censor, không bao giờ tới aha moment. *Đó là lý do free tier thường cần có, dù tốn cost.*

---

## Pricing models

### Tiered pricing
Các tier giá cố định, ceiling rõ. **Ví dụ**: GitHub Copilot $10 → $19 → $39. Predictability cao, nhưng power user hit ceiling.

### Progressive pricing
Bắt đầu free hoặc rẻ, scale dần lên paid khi user value tăng. **Ví dụ**: Grammarly Free → $12 → Enterprise. Low barrier, nhưng revenue delay.

### Hybrid pricing
Base subscription + credit/usage component. **Ví dụ**: Clay $149/mo + 2,000 credits, Miro $8/seat + AI credits. Captures all usage levels, nhưng billing complex.

### Outcome-based pricing
Tính phí theo kết quả AI giao được, không tính theo input/usage. **Ví dụ**: Intercom Fin $0.99/resolution. Chỉ work nếu đủ CAMP — Intercom Fin build đến $100M+ AI agent revenue.

### Seat-based pricing
Tính phí theo user, không theo usage. **Ví dụ**: Notion AI $10/user/month. Predictable nhưng đang giảm thị phần (21% → 15% trong 12 tháng — Kyle Poyar 2025).

---

## Thuật ngữ kỹ thuật trong lab

### RAG (Retrieval-Augmented Generation)
AI tra cứu knowledge base (kho tài liệu) trước khi trả lời. Trong lab: top-5 chunks được lấy ra từ KB ≈ 1,250 tokens cố định, đẩy vào input cho mỗi lượt chat.

### Chunk
1 đoạn nhỏ tài liệu sau khi cắt knowledge base ra. Trong lab: 1 chunk ≈ 250 tokens ≈ 150–200 từ.

### Token
Đơn vị nhỏ AI dùng để tính. ~3–4 ký tự tiếng Anh = 1 token. Giá API tính theo $/M tokens (1 triệu tokens).

### Inference
1 lần AI tính toán + phản hồi. Mỗi inference = 1 lần tốn tiền API.

### Embedding
Chuyển text thành vector số để AI search. Trong lab: dùng OpenAI `text-embedding-3-small` ($0.02/M tokens). Indexing cost cho 30K tokens KB = $0.0006 — gần như miễn phí.

### Web search API
Dịch vụ tìm kiếm real-time để bổ sung context cho AI. Trong lab: Tavily ~$0.005–$0.008/query. Bật khi cần info mới (visa policy, thời tiết, sự kiện).

### History management
Cách quản lý lịch sử conversation đẩy vào context mỗi turn. 3 options trong lab:
- **Full**: giữ toàn bộ — tokens tăng mỗi turn.
- **Last N**: chỉ giữ N lượt gần nhất — cap tokens.
- **Summarize**: tóm tắt lịch sử cũ thành ~150 tokens — tiết kiệm nhưng cần 1 LLM call phụ.

### Intent classification
Phân loại ý định của user message để route đúng. 2 options trong lab:
- **Keyword/regex**: rule-based, $0/message, miss được nuance.
- **LLM classifier**: model cheap (~170 tokens), hiểu context tốt hơn nhưng tốn $.

### Handoff
Chuyển conversation từ AI sang con người (sales hoặc manager). Trong lab: Booking → sales, Khiếu nại → manager. Cost AI = $0 cho phần handoff (nhưng có thể tốn classifier nếu dùng LLM intent).

---

## Verdict (quyết định cuối)

### GO
ROI base > 3:1 + ROI worst case (combined stress) > 1:1. Triển khai pilot.

### CONDITIONAL
ROI 1–3:1 hoặc < 1:1 ở 1 stress scenario. Triển khai có điều kiện — phải fix cái yếu nhất trước.

### NO-GO
ROI < 1:1 ở base case. Thiết kế lại hoặc dừng.

---

*Glossary D27 · cập nhật ngày dạy. Nếu thấy thuật ngữ khác chưa có trong list này → hỏi giảng viên hoặc TA.*
