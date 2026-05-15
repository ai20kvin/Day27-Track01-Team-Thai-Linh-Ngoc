# 02 · Configuration Design — Đặt tên + Chốt knobs cho ≥3 Configs

> **Mục tiêu**: Biến phác thảo ở `01-base-flow.md` thành ≥3 configurations chi tiết.
>
> **Thời gian**: 15 phút (đầu phần Main)

---

## Config 1

**Tên config**: 🪙 **Budget Bot** — Lean & Mean

### 3 Knobs

**① Model tier**:
```text
Response model:   GPT-4o-mini  → giá $0.15 / $0.60 per 1M tokens (input/output)
Classifier model: Keyword rule → $0 (không dùng LLM classifier)
```

**② Web search**:
```text
☑ OFF — chỉ dùng RAG, không bật web search cho bất kỳ intent nào
```

**③ History management**:
```text
☑ Last 3 turns
```

### Lý do nhóm chọn config này

Config này phục vụ tốt nhất cho mùa thấp điểm (Scenario A) hoặc giờ thấp điểm ban đêm khi volume thấp và tourist chủ yếu hỏi thông tin cơ bản về địa điểm, ẩm thực. Keyword classifier tiết kiệm hoàn toàn LLM call phân loại intent — phù hợp khi câu hỏi của tourist thường rõ ràng (có từ khoá "visa", "weather", "book"). Last 3 turns giữ cost thấp nhất trong khi vẫn đủ context cho đa số conversation ngắn.

### Rủi ro lớn nhất

Visa info có thể outdated vì web OFF + RAG không được cập nhật real-time; tourist hỏi chi tiết e-visa mới nhất có thể nhận thông tin sai.

---

## Config 2

**Tên config**: 👑 **Premium Concierge** — Quality First

### 3 Knobs

**① Model tier**:
```text
Response model:   Claude Sonnet 4.6  → giá $3.00 / $15.00 per 1M tokens
Classifier model: Claude Haiku 4.5   → giá $0.80 / $4.00 per 1M tokens (~170 tokens/call)
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

Config phục vụ mùa cao điểm (Scenario B) khi tourist có budget cao, hỏi phức tạp (itinerary, visa specific, luxury resort), và kỳ vọng câu trả lời chính xác + detailed. Sonnet 4.6 cho quality cao nhất (est. 88%+), LLM classifier tránh sai phân loại với câu hỏi ambiguous. Web search bật cho Visa + Weather đảm bảo info luôn real-time — tránh rủi ro sai visa fee/policy. Full history giúp bot nhớ preference của tourist từ đầu conversation đến cuối.

### Rủi ro lớn nhất

Monthly cost cao đáng kể ở Scenario B — nếu volume spike mà conversion booking không tăng tương ứng, ROI có thể không justify cost Premium.

---

## Config 3

**Tên config**: ⚖️ **Smart Mix** — Balanced for All Seasons

### 3 Knobs

**① Model tier**:
```text
Response model:   Claude Haiku 4.5   → giá $0.80 / $4.00 per 1M tokens
Classifier model: Keyword rule       → $0
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

Smart Mix là config "deploy quanh năm" — không cần đổi config theo mùa. Haiku 4.5 đủ tốt cho phần lớn câu hỏi tourist (quality est. ~80%), nhanh hơn Sonnet đáng kể. Web ON cho Visa + Weather giữ accuracy ở 2 intent dễ outdated nhất. Last 5 đủ nhớ context cho conversation 4–7 lượt mà không phát sinh chi phí lớn như Full history ở Scenario B (7 turns).

### Rủi ro lớn nhất

Với câu hỏi rất phức tạp (ví dụ: multi-destination itinerary 2 tuần với yêu cầu cụ thể), Haiku có thể cho câu trả lời kém chi tiết hơn Sonnet → tourist cần hỏi thêm nhiều lượt → tăng cost per conversation không mong muốn.

---

## Config 4 (optional)

**Tên config**: 🌙 **Night Mode** — Lean + Web Weather

### 3 Knobs

```text
Model: GPT-4o-mini (response) + Keyword classifier ($0)
Web: ON selective — chỉ Weather (không bật Visa)
History: Last 3
```

### Lý do

Variant của Budget Bot — thêm web search cho Weather vì đây là intent gần như bắt buộc real-time. Tắt web Visa để giữ cost thấp; tradeoff chấp nhận được nếu team cập nhật RAG visa hàng tuần. Phù hợp giờ thấp điểm hoặc khi budget bị siết.

---

## Bảng kiểm

- [x] ≥3 configs đã đặt tên (không chỉ "Config 1/2/3")
- [x] Mỗi config đã chốt rõ 3 knobs
- [x] Mỗi config có ≥2 câu lý do
- [x] 3 configs đủ khác biệt về model tier, web strategy, và history
- [x] Nhóm đồng thuận đây là 3 configs đáng so sánh