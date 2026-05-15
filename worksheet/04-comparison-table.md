# 04 · Comparison Table — Bảng so sánh đầy đủ

> **Mục tiêu**: Tổng hợp số từ `03-cost-calculation.md` thành một bảng so sánh duy nhất để present.

---

## Bảng chính

| | Config 1 | Config 2 | Config 3 | Config 4 |
|---|---|---|---|---|
| **Tên** | Budget Bot | Premium Concierge | Smart Mix | Lean Night Mode |
| **Model** | GPT-4o-mini | Claude Sonnet 4.6 | Gemini 2.5 Flash | GPT-4o-mini |
| **Web search** | OFF | Visa + Weather | Visa + Weather | Weather only |
| **History** | Last 3 | Full history | Last 5 | Last 3 |
| **Intent classifier** | Keyword | LLM: Claude Haiku 4.5 | Keyword | Keyword |
| **Cost/conv A** | $0.001499 | $0.047271 | $0.015330 | $0.004747 |
| **Cost/conv B** | $0.001794 | $0.059815 | $0.019124 | $0.007478 |
| **Monthly A** | $13.49 | $425.44 | $137.97 | $42.73 |
| **Monthly B** | $64.60 | $2,153.36 | $688.47 | $269.22 |
| **vs human A** | rẻ 333.5× | rẻ 10.6× | rẻ 32.6× | rẻ 105.3× |
| **vs human B** | rẻ 278.6× | rẻ 8.4× | rẻ 26.1× | rẻ 66.9× |
| **Savings A** | 99.70% | 90.55% | 96.93% | 99.05% |
| **Savings B** | 99.64% | 88.04% | 96.18% | 98.50% |
| **Quality estimate** | Low-Medium | High | Medium-High | Medium |
| **Speed estimate** | High | Low-Medium | Medium-High | High |
| **Điểm yếu chính** | Visa/Weather stale | Cost và latency cao | Keyword miss multi-intent | Visa vẫn stale |
| **Best for** | Cost floor, low risk FAQ | Premium customers, complex trips | Default production config | Night/low-traffic fallback |

---

## Quan sát nhanh từ bảng

### Câu 1 — Config rẻ nhất là gì? Đắt nhất là gì?

```text
Rẻ nhất: Budget Bot — monthly B = $64.60
Đắt nhất: Premium Concierge — monthly B = $2,153.36
Chênh: 2,153.36 / 64.60 ≈ 33.3×
```

Nếu chỉ nhìn cost, Budget Bot thắng tuyệt đối. Nhưng nó thắng bằng cách tắt web hoàn toàn, nên rủi ro sai Visa/Weather cao nhất. Nếu chỉ xét các config có web cho Visa + Weather, Smart Mix rẻ hơn Premium khoảng `2,153.36 / 688.47 ≈ 3.1×` ở Scenario B.

### Câu 2 — Knob nào ảnh hưởng cost nhiều nhất?

```text
1. Web search ảnh hưởng lớn ở intent Visa/Weather vì mỗi query thêm $0.008.
   Ví dụ Smart Mix A:
   - Guide 4 turns = $0.004464
   - Visa 4 turns = $0.037424
   → Visa đắt hơn Guide ~8.4× chủ yếu do web API.

2. Model tier ảnh hưởng lớn trên mọi intent.
   Guide A:
   - Budget Bot GPT-4o-mini = $0.001764
   - Smart Mix Gemini Flash = $0.004464
   - Premium Sonnet = $0.038440
   → Premium đắt hơn Budget ~21.8× cho cùng Guide 4 turns.

3. History length ảnh hưởng ít hơn web và model trong lab này.
   Full history làm input tăng đều theo turn, nhưng tác động nhỏ hơn $0.008/query của web search.
```

Ranking thực tế trong bảng của nhóm:

```text
Web search on high-frequency intent > Model tier > History length > Classifier
```

### Câu 3 — Tại sao Scenario B không đắt ×7 lần Scenario A?

Scenario B có volume gấp 4 và turns/conversation gấp 1.75, nên nếu mọi intent đều dùng AI thì có thể kỳ vọng monthly cost gần `4 × 1.75 = 7×` Scenario A.

Thực tế bảng chỉ tăng khoảng 4.8-5.1×:

```text
Budget Bot: 64.60 / 13.49 = 4.8×
Premium:    2,153.36 / 425.44 = 5.1×
Smart Mix:  688.47 / 137.97 = 5.0×
Lean:       269.22 / 42.73 = 6.3×
```

Lý do chính: Scenario B có **45% Booking + Complaint**, tức là gần một nửa conversation được handoff sớm và không chạy response generation. AI-served ratio giảm từ 85% ở Scenario A xuống 55% ở Scenario B, nên workload không tăng đúng 7×.

### Câu 4 — Có config nào AI đắt hơn human không?

```text
Không. Tất cả configs đều rẻ hơn human baseline rất nhiều.

Human baseline:
- Scenario A: $4,500/month
- Scenario B: $18,000/month

Config đắt nhất là Premium Concierge:
- Scenario A: $425.44/month → vẫn rẻ hơn human 10.6×
- Scenario B: $2,153.36/month → vẫn rẻ hơn human 8.4×
```

Tuy vậy, "rẻ hơn human" chưa đủ để chọn Premium làm default. Premium chỉ nên dùng khi quality thật sự tạo thêm booking conversion hoặc giảm rủi ro ở khách high-value. Với default production, Smart Mix hợp lý hơn vì vẫn tiết kiệm trên 96% nhưng giữ web search cho Visa/Weather.

---

## Bảng kiểm

- [x] Bảng đầy đủ, không còn ô trống
- [x] Đã có 4 câu trả lời quan sát
- [x] Số monthly được tính lại đúng từ cost/conversation × volume × 30
- [x] Nhóm đồng thuận về tradeoff trước khi viết recommendation
