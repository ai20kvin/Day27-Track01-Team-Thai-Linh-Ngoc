# 01 · Base Flow + Chốt 3 Knobs

> **Mục tiêu**: Hiểu chatbot hoạt động ra sao ở mức base trước khi chọn config, và xác định 3 knobs nhóm sẽ tweak ở bước sau.
>
> **Thời gian**: 7 phút (trong 15 phút phần Setup)

---

## Bước 1 — Đọc base flow trong cost reference card

Nhóm đọc lại base flow và thống nhất cách hiểu:

- Chatbot không trả lời ngay. Nó cần phân loại intent trước.
- Chỉ các intent **Guide / Visa / Weather** tiếp tục đi qua RAG, web search, context assembly và response generation.
- **Booking** chuyển sales, **Complaint** chuyển manager, nên không tốn LLM response cost.
- Từ file `00-user-journey.md`, nhóm thấy có thêm edge case: câu hỏi **business/investment** không nằm trong 5 intent mặc định. Trong lab này nhóm route edge case đó sang **handoff specialist** thay vì để bot tự trả lời quá sâu.

---

## Bước 2 — Vẽ lại flow theo cách hiểu của nhóm

```text
┌─────────────────────────────────────────────────────────────────────┐
│                        Tourist gửi tin nhắn                         │
└───────────────────────────┬─────────────────────────────────────────┘
                            ▼
┌─────────────────────────────────────────────────────────────────────┐
│  1. Intent Classification                                           │
│                                                                     │
│  Input: user message + short conversation context                   │
│  Output: primary intent                                             │
│                                                                     │
│  5 intent chính:                                                    │
│  - Visa/Policy                                                      │
│  - Destination/Guide                                                │
│  - Weather/Event                                                    │
│  - Tour/Booking                                                     │
│  - Complaint                                                        │
│                                                                     │
│  Edge case từ nhóm: Business/Investment → Handoff specialist        │
└───────────────────────────┬─────────────────────────────────────────┘
                            ▼
┌─────────────────────────────────────────────────────────────────────┐
│  2. Route theo intent                                               │
│                                                                     │
│  Destination/Guide ──→ RAG knowledge base                           │
│  Visa/Policy ───────→ RAG + web search nếu config bật               │
│  Weather/Event ─────→ Web search vì cần real-time                   │
│  Tour/Booking ──────→ Handoff sales agent                           │
│  Complaint ─────────→ Escalate manager                              │
│  Business/Investment → Handoff specialist                           │
└───────────────────────────┬─────────────────────────────────────────┘
                            │
                            │ Chỉ Guide / Visa / Weather tiếp tục tạo response AI
                            ▼
┌─────────────────────────────────────────────────────────────────────┐
│  3. Context Assembly                                                │
│                                                                     │
│  System prompt:              500 tokens                             │
│  User message:                80 tokens                              │
│  Conversation history:        phụ thuộc history knob                 │
│  RAG top-5 chunks:         1,250 tokens nếu dùng RAG                 │
│  Web search results:         800 tokens nếu bật web                  │
│                                                                     │
│  → Tổng input tokens là phần làm cost tăng theo từng turn            │
└───────────────────────────┬─────────────────────────────────────────┘
                            ▼
┌─────────────────────────────────────────────────────────────────────┐
│  4. Response Generation                                             │
│                                                                     │
│  Model tạo câu trả lời tiếng Anh cho tourist                        │
│  Output trung bình: ~180 tokens                                     │
│  Model mạnh hơn → quality tốt hơn nhưng cost và latency cao hơn      │
└─────────────────────────────────────────────────────────────────────┘
```

**Key insight từ flow**:

- Cùng keyword chưa đủ để route đúng. Ví dụ "Ha Long Bay" có thể là Guide nếu hỏi day trip vs overnight, nhưng là Booking nếu hỏi group tour package.
- Multi-intent xuất hiện khá tự nhiên. Bot nên chọn **primary intent** trước, rồi trả lời phần bot làm được và handoff phần cần người.
- Booking, Complaint và Business/Investment edge case không nên để bot tự xử lý sâu vì rủi ro business cao hơn phần cost tiết kiệm được.

---

## Bước 3 — Xác định 3 Knobs

### Knob 1 — Model tier

```text
Suy nghĩ của nhóm:
- Không nên chỉ tối ưu cost thấp nhất, vì sai visa hoặc route nhầm booking có thể gây hậu quả lớn.
- Guide chiếm tỷ trọng cao nhất trong user journey, nhưng đa số là câu hỏi tư vấn du lịch thông thường.
- Visa và câu hỏi multi-intent cần model/classifier hiểu ngữ cảnh tốt hơn keyword đơn giản.
- Vì vậy nhóm cần so sánh 3 hướng: cheap toàn bộ, premium chất lượng cao, và smart mix cân bằng.
```

### Knob 2 — Web search

```text
Suy nghĩ của nhóm:
- Weather bắt buộc cần real-time, vì hỏi thời tiết theo tháng/ngày và khả năng mưa bão.
- Visa có rủi ro outdated nếu chỉ dùng RAG, đặc biệt khi khách hỏi "rules changed recently" hoặc e-visa.
- Guide thường có thể dùng RAG vì thông tin địa điểm, food, culture ít thay đổi hơn.
- ON selective cho Visa + Weather là lựa chọn đáng thử vì tăng accuracy đúng chỗ, không bật web bừa bãi cho mọi câu guide.
```

### Knob 3 — History management

```text
Suy nghĩ của nhóm:
- Tourist hay đặt preference ở đầu conversation: budget, đi cùng ai, không ăn cay, muốn ít di chuyển.
- Last 3 rẻ nhưng dễ quên preference khi conversation dài 6-7 turns.
- Full history giữ context tốt nhất nhưng cost tăng dần theo mỗi turn, đặc biệt ở Scenario B.
- Last 5 có vẻ là điểm cân bằng: đủ nhớ hầu hết context quan trọng nhưng không đắt như Full.
```

---

## Bước 4 — Phác thảo combo

**Combo 1 — Cheap / baseline cost**

```text
Tên dự kiến: Budget Bot
Model: Cheap model cho response, keyword classifier
Web: OFF
History: Last 3
Use case: mùa thấp điểm, câu hỏi guide đơn giản, cần cost thấp nhất
```

**Combo 2 — Quality first**

```text
Tên dự kiến: Premium Concierge
Model: Strong model cho response, LLM classifier
Web: ON selective cho Visa + Weather
History: Full history
Use case: khách premium, conversation dài, cần câu trả lời chắc và ít sai route
```

**Combo 3 — Balanced / production realistic**

```text
Tên dự kiến: Smart Mix
Model: Mid model cho response, keyword hoặc cheap classifier
Web: ON selective cho Visa + Weather
History: Last 5
Use case: deploy quanh năm, cân bằng cost-quality-speed
```

**Combo 4 — Optional**

```text
Tên dự kiến: Lean Night Mode
Model: Cheap model, keyword classifier
Web: ON selective chỉ Weather
History: Last 3
Use case: giờ thấp điểm, giảm rủi ro thời tiết real-time nhưng vẫn giữ cost thấp
```

---

## Bảng kiểm trước khi sang file tiếp theo

- [x] Đã vẽ flow base có đủ 4 bước: Intent → Route → Context → Response
- [x] Hiểu Booking + Complaint = $0 LLM response cost vì handoff
- [x] Có xử lý edge case Business/Investment từ file 00 bằng handoff specialist
- [x] Đã phác thảo ≥3 combo khác nhau
- [x] Nhóm đồng thuận về hướng đi mỗi combo
