# 02 · Configuration Design — Đặt tên + Chốt knobs cho ≥3 Configs

> **Mục tiêu**: Biến phác thảo ở `01-base-flow.md` thành ít nhất 3 configurations rõ knobs, rõ use case, và đủ khác biệt để so sánh cost-quality-speed.
>
> **Thời gian**: 15 phút (đầu phần Main)

---

## Config 1

**Tên config**: **Budget Bot** — Lean FAQ Mode

### 3 Knobs

**① Model tier**:

```text
Response model:   GPT-4o-mini  → giá $0.15 / $0.60 per 1M tokens (input/output)
Classifier model: Keyword rule → $0
```

**② Web search**:

```text
☑ OFF — chỉ dùng RAG, không bật web search cho intent nào
```

**③ History management**:

```text
☑ Last 3 turns
```

### Lý do nhóm chọn config này

Config này là baseline rẻ nhất để thấy "sàn chi phí" của chatbot. Nó phù hợp với mùa thấp điểm hoặc giờ thấp điểm, khi phần lớn câu hỏi là guide cơ bản như nên đi đâu, ăn gì, itinerary đơn giản. Keyword classifier giúp tiết kiệm chi phí phân loại, và Last 3 đủ dùng cho các conversation ngắn 2-4 lượt.

### Rủi ro lớn nhất

Web OFF làm visa và weather dễ stale. Nếu khách hỏi thông tin nhập cảnh hoặc thời tiết gần ngày đi, bot có thể trả lời thiếu cập nhật; đây là rủi ro business lớn hơn phần tiền tiết kiệm được.

---

## Config 2

**Tên config**: **Premium Concierge** — Quality First

### 3 Knobs

**① Model tier**:

```text
Response model:   Claude Sonnet 4.6 → giá $3.00 / $15.00 per 1M tokens
Classifier model: Claude Haiku 4.5  → giá $1.00 / $5.00 per 1M tokens (~170 tokens/call)
```

**② Web search**:

```text
☑ ON selective — bật cho intent: Visa + Weather
```

**③ History management**:

```text
☑ Full history
```

### Lý do nhóm chọn config này

Config này đại diện cho hướng "quality trước, cost sau". Nó phù hợp với khách premium hoặc conversation phức tạp: itinerary nhiều thành phố, yêu cầu gia đình, dị ứng đồ ăn, visa, hoặc câu hỏi multi-intent. LLM classifier tốt hơn keyword khi câu hỏi không nói thẳng intent, ví dụ "Ha Long next week, weather and can you book a cruise?" vừa có Weather vừa có Booking.

### Rủi ro lớn nhất

Cost và latency cao nhất. Nếu dùng làm default quanh năm, nhiều câu guide đơn giản cũng bị xử lý bằng model mạnh, khiến ROI kém hơn cần thiết.

---

## Config 3

**Tên config**: **Smart Mix** — Balanced for All Seasons

### 3 Knobs

**① Model tier**:

```text
Response model:   Gemini 2.5 Flash → giá $0.30 / $2.50 per 1M tokens
Classifier model: Keyword rule     → $0
```

**② Web search**:

```text
☑ ON selective — bật cho intent: Visa + Weather
```

**③ History management**:

```text
☑ Last 5 turns
```

### Lý do nhóm chọn config này

Smart Mix là option thực tế nhất để deploy quanh năm. Gemini Flash rẻ hơn strong model nhưng vẫn đủ tốt cho phần lớn guide/itinerary thông thường; web search chỉ bật ở Visa và Weather để bảo vệ accuracy ở những intent dễ outdated. Last 5 giữ được các preference quan trọng như budget, đi nhóm mấy người, không ăn cay, muốn ít di chuyển mà không tăng cost như Full history.

### Rủi ro lớn nhất

Keyword classifier vẫn có thể miss câu multi-intent hoặc edge case Business/Investment trong file 00. Mitigation là rule rõ: nếu phát hiện từ khóa như "investor", "business environment", "entrepreneur", bot chỉ trả lời phần du lịch ở mức nhẹ và handoff phần business cho specialist.

---

## Config 4 (optional)

**Tên config**: **Lean Night Mode** — Cheap + Weather Safety

### 3 Knobs

```text
Response model:   GPT-4o-mini → giá $0.15 / $0.60 per 1M tokens
Classifier model: Keyword rule → $0
Web search:       ON selective — chỉ Weather
History:          Last 3 turns
```

### Lý do

Đây là biến thể của Budget Bot cho giờ thấp điểm: vẫn rất rẻ nhưng không bỏ qua weather real-time, vì thời tiết là câu hỏi phổ biến và khách có thể cần quyết định nhanh trước ngày đi. Config này không phù hợp làm default vì Visa vẫn không có web search.

---

## Bảng kiểm trước khi tính cost

- [x] ≥3 configs đã đặt tên rõ ràng
- [x] Mỗi config đã chốt đủ 3 knobs
- [x] Mỗi config có use case và rủi ro riêng
- [x] 3 configs khác nhau ở ít nhất 2 knobs
- [x] Có một baseline rẻ, một option premium, và một option balanced để so sánh
