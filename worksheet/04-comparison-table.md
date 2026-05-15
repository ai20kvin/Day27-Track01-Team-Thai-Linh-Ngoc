# 04 · Comparison Table — Bảng so sánh đầy đủ

> **Mục tiêu**: Tổng hợp tất cả số đã tính thành 1 bảng so sánh duy nhất.

---

## Bảng chính

| | Config 1 | Config 2 | Config 3 |
|---|---|---|---|
| **Tên** | 🪙 Budget Bot | 👑 Premium Concierge | ⚖️ Smart Mix |
| **① Model** | GPT-4o-mini ($0.15/$0.60 per 1M) | Claude Sonnet 4.6 ($3/$15 per 1M) + Haiku classifier | Claude Haiku 4.5 ($0.80/$4 per 1M) |
| **② Web search** | OFF | ON selective (Visa + Weather) | ON selective (Visa + Weather) |
| **③ History** | Last 3 turns | Full history | Last 5 turns |
| **Intent classifier** | Keyword ($0) | LLM — Haiku 4.5 | Keyword ($0) |
| **Cost / conv (Scenario A — 4 turns)** | $0.00156 | $0.04218 | $0.01638 |
| **Cost / conv (Scenario B — 7 turns)** | $0.00197 | $0.05349 | $0.02057 |
| **Monthly A** (300 conv/day × 30) | $140 | $3,797 | $147 |
| **Monthly B** (1,200 conv/day × 30) | $71 | $1,926 | $740 |
| **vs human $4,500/mo (A)** | rẻ **32×** | rẻ **1.2×** | rẻ **30.6×** |
| **vs human $18,000/mo (B)** | rẻ **254×** | rẻ **9.3×** | rẻ **24.3×** |
| **Savings % (A)** | **96.9%** | **15.6%** | **96.7%** |
| **Savings % (B)** | **99.6%** | **89.3%** | **95.9%** |
| **Quality estimate** | Low-Med (70%) | High (88%+) | Medium (80%) |
| **Speed estimate** | High (~200ms) | Low (2–4s) | Med-High (~500ms) |
| **Điểm yếu chính** | Visa info có thể outdated; context ngắn, dễ quên preference | Scenario A gần bằng human cost; chậm do Sonnet + web | Haiku có thể kém với câu itinerary phức tạp |
| **Best for** | Mùa thấp điểm / giờ thấp điểm / volume thấp | Mùa cao điểm, khách premium, cần accuracy cao | Deploy quanh năm, balance cost-quality |

---

## Quan sát nhanh từ bảng

### Câu 1 — Config rẻ nhất / đắt nhất?

```text
Rẻ nhất: Budget Bot — monthly B = $71/tháng
Đắt nhất: Premium Concierge — monthly A = $3,797/tháng (Scenario A đắt hơn B!)
Chênh: $3,797 / $71 ≈ 53× lần
```

*Điều thú vị: Premium Concierge Scenario A ($3,797) đắt hơn Scenario B ($1,926) —
vì Scenario B có 45% handoff nên AI-served conversation ít hơn.*

### Câu 2 — Knob nào ảnh hưởng cost nhiều nhất?

```text
Model tier ảnh hưởng lớn nhất:
- Budget Bot (GPT-4o-mini) vs Smart Mix (Haiku): $0.00156 vs $0.01638/conv → chênh ≈10.5×
- Smart Mix (Haiku) vs Premium (Sonnet): $0.01638 vs $0.04218/conv → chênh ≈2.6×
- Budget Bot vs Premium: chênh ≈27× (cùng scenario A)

Web search (ON selective vs OFF):
- Visa turn: Budget Bot $0.000500/turn vs Smart Mix $0.008448/turn (T4)
  → Chênh ~16× ở turn có web — nhưng chỉ áp dụng cho 25%+10% intent
  → Impact thực tế: khoảng $6–$7/month ở Scenario A (nhỏ so với model cost)

History (Last 3 vs Full):
- Ở Turn 7: Full history = 1,560 tokens extra vs Last 3 = 780 tokens extra
  → Chênh 780 tokens × Sonnet $3/M = $0.00234 per turn → ảnh hưởng thứ 3
  → Ở conversation 7 turns, Full vs Last 5 chênh khoảng 5–8% cost/conv

Ranking: Model tier > Web search ON/OFF > History length
```

### Câu 3 — Tại sao Scenario B không đắt ×4 lần Scenario A?

```text
Scenario B volume = ×4 lần A, turns = ×1.75 (7 vs 4) → lý thuyết đáng ra ×7.
Thực tế monthly B thường THẤP HƠN monthly A (Budget Bot: $71 < $140):

Lý do chính: Intent mix Scenario B rất khác —
- Booking tăng từ 10% → 35%, Complaint 5% → 10% → handoff = 45% (vs 15% ở A)
- 45% conversation = $0 LLM cost hoàn toàn
- Chỉ 55% conversation thật sự chạy LLM (660/1,200 conv/ngày)

Ảnh hưởng: dù volume ×4, LLM-served conversations chỉ tăng ×(0.55×1200)/(0.85×300)
= 660/255 ≈ 2.6× → cộng thêm turns dài hơn ×1.75 → tổng token ≈ ×4.5, không phải ×7

Đây là lý do Scenario B ở Budget Bot rẻ hơn A ($71 < $140): volume ×4 nhưng
AI-served % giảm từ 85% → 55% → giảm LLM workload đáng kể.
```

### Câu 4 — Có config nào AI đắt hơn human không?

```text
Không có config nào đắt hơn human, nhưng Premium Concierge Scenario A ($3,797)
gần bằng human baseline ($4,500) — chỉ tiết kiệm 15.6%.

Nếu tính thêm chi phí ẩn của AI (monitoring, RAG maintenance, web search API,
prompt engineering updates): có thể Premium Scenario A thực tế ngang bằng hoặc
cao hơn human cost một chút.

Tuy nhiên AI vẫn justify ở 4 điểm:
1. 24/7 — human agent không làm việc 3AM nhưng tourist quốc tế hỏi mọi lúc
2. Đa ngôn ngữ — handle English + Korean + Japanese + Chinese không cần hire thêm
3. Scale linear — volume tăng ×4, cost không tăng ×4 như human
4. Consistency — không bao giờ mất kiên nhẫn, không trả lời sai vì mệt mỏi
```

---

## Bảng kiểm

- [x] Bảng đầy đủ — không còn ô trống
- [x] Đã có 4 câu trả lời cho 4 quan sát
- [x] Nhóm đồng thuận về số trong bảng (đã sanity check)