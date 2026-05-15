# 03 · Cost Calculation — Tính chi phí từng Config × 2 Scenarios

> **Mục tiêu**: Với mỗi config đã thiết kế ở `02-config-design.md`, tính cost/conversation và monthly cost cho cả low season và high season.

---

## Assumptions dùng để tính

Nhóm dùng số trong `cost-reference-card.md` làm source of truth. Các giá dưới đây là giá per 1M tokens.

```text
System prompt:              500 tokens
User message:                80 tokens
Assistant response:         180 tokens output
1 prior turn history:       260 tokens
RAG top-5 chunks:         1,250 tokens
Web search results:         800 tokens nếu bật
Web search API call:       $0.008/query (Tavily Basic)
LLM classifier:            150 input + 20 output tokens nếu dùng
```

**Lưu ý quan trọng**:

- Guide, Visa, Weather đi qua response generation.
- Booking và Complaint chỉ handoff sang người, nên **không tốn response generation**.
- Nếu dùng keyword classifier thì handoff cost = `$0`.
- Nếu dùng LLM classifier thì Booking/Complaint vẫn tốn 1 lần classifier.
- Với web ON selective, nhóm tính conservative: mỗi turn của Visa/Weather gọi web search 1 lần.

---

## Configs được tính

| Config | Response model | Classifier | Web search | History |
|---|---|---|---|---|
| Budget Bot | GPT-4o-mini — $0.15 / $0.60 | Keyword — $0 | OFF | Last 3 |
| Premium Concierge | Claude Sonnet 4.6 — $3 / $15 | Claude Haiku 4.5 — $1 / $5 | Visa + Weather | Full |
| Smart Mix | Gemini 2.5 Flash — $0.30 / $2.50 | Keyword — $0 | Visa + Weather | Last 5 |
| Lean Night Mode | GPT-4o-mini — $0.15 / $0.60 | Keyword — $0 | Weather only | Last 3 |

---

## Scenarios

### Scenario A — Low season

```text
Volume:          300 conversations/day
Turns/conv:      4
Intent mix:      Guide 50%, Visa 25%, Weather 10%, Booking 10%, Complaint 5%
Human baseline:  $0.50/conversation = $4,500/month
```

### Scenario B — High season

```text
Volume:          1,200 conversations/day
Turns/conv:      7
Intent mix:      Guide 30%, Visa 15%, Weather 10%, Booking 35%, Complaint 10%
Human baseline:  $0.50/conversation = $18,000/month
```

---

## Công thức

```text
History tokens:
- Full:   (turn - 1) × 260
- Last 3: min(turn - 1, 3) × 260
- Last 5: min(turn - 1, 5) × 260

Input tokens per AI turn =
  500 system
+ 80 user message
+ history tokens
+ 1,250 RAG chunks
+ 800 web tokens nếu web bật

Model cost =
  input_tokens × input_price / 1,000,000
+ 180 output_tokens × output_price / 1,000,000

Classifier cost nếu dùng Haiku =
  150 × $1 / 1,000,000 + 20 × $5 / 1,000,000
= $0.00025 / turn

Web cost nếu bật =
  $0.008 / turn

Average cost/conversation =
  SUM(intent mix × cost của intent đó)

Monthly cost =
  average cost/conversation × conversations/day × 30
```

---

## Cost by intent

### Config 1 — Budget Bot

| Intent cost | Scenario A: 4 turns | Scenario B: 7 turns |
|---|---:|---:|
| Guide | $0.001764 | $0.003262 |
| Visa | $0.001764 | $0.003262 |
| Weather | $0.001764 | $0.003262 |
| Booking handoff | $0.000000 | $0.000000 |
| Complaint handoff | $0.000000 | $0.000000 |

Weighted average:

```text
Scenario A = 50%×0.001764 + 25%×0.001764 + 10%×0.001764
           + 10%×0 + 5%×0
           = $0.001499 / conversation

Scenario B = 30%×0.003262 + 15%×0.003262 + 10%×0.003262
           + 35%×0 + 10%×0
           = $0.001794 / conversation
```

| Item | Scenario A | Scenario B |
|---|---:|---:|
| Cost / conversation | $0.001499 | $0.001794 |
| Monthly cost | $13.49 | $64.60 |
| Human baseline | $4,500 | $18,000 |
| Rẻ hơn human | 333.5× | 278.6× |
| Savings | 99.70% | 99.64% |

**Sanity check**:

```text
Budget Bot cực rẻ vì không có web search và không dùng LLM classifier.
Rủi ro không nằm ở cost mà nằm ở accuracy: Visa/Weather có thể outdated.
```

---

### Config 2 — Premium Concierge

| Intent cost | Scenario A: 4 turns | Scenario B: 7 turns |
|---|---:|---:|
| Guide | $0.038440 | $0.075460 |
| Visa | $0.080040 | $0.148260 |
| Weather | $0.080040 | $0.148260 |
| Booking handoff | $0.000250 | $0.000250 |
| Complaint handoff | $0.000250 | $0.000250 |

Weighted average:

```text
Scenario A = 50%×0.038440 + 25%×0.080040 + 10%×0.080040
           + 10%×0.000250 + 5%×0.000250
           = $0.047271 / conversation

Scenario B = 30%×0.075460 + 15%×0.148260 + 10%×0.148260
           + 35%×0.000250 + 10%×0.000250
           = $0.059815 / conversation
```

| Item | Scenario A | Scenario B |
|---|---:|---:|
| Cost / conversation | $0.047271 | $0.059815 |
| Monthly cost | $425.44 | $2,153.36 |
| Human baseline | $4,500 | $18,000 |
| Rẻ hơn human | 10.6× | 8.4× |
| Savings | 90.55% | 88.04% |

**Sanity check**:

```text
Premium vẫn rẻ hơn human nhiều, nhưng đắt hơn Smart Mix khoảng 3.1×.
Cost tăng mạnh vì Sonnet input/output đắt hơn và Visa/Weather có web search mỗi turn.
```

---

### Config 3 — Smart Mix

| Intent cost | Scenario A: 4 turns | Scenario B: 7 turns |
|---|---:|---:|
| Guide | $0.004464 | $0.008553 |
| Visa | $0.037424 | $0.066233 |
| Weather | $0.037424 | $0.066233 |
| Booking handoff | $0.000000 | $0.000000 |
| Complaint handoff | $0.000000 | $0.000000 |

Weighted average:

```text
Scenario A = 50%×0.004464 + 25%×0.037424 + 10%×0.037424
           + 10%×0 + 5%×0
           = $0.015330 / conversation

Scenario B = 30%×0.008553 + 15%×0.066233 + 10%×0.066233
           + 35%×0 + 10%×0
           = $0.019124 / conversation
```

| Item | Scenario A | Scenario B |
|---|---:|---:|
| Cost / conversation | $0.015330 | $0.019124 |
| Monthly cost | $137.97 | $688.47 |
| Human baseline | $4,500 | $18,000 |
| Rẻ hơn human | 32.6× | 26.1× |
| Savings | 96.93% | 96.18% |

**Sanity check**:

```text
Smart Mix rẻ hơn human trên 96% ở cả hai scenarios, nhưng vẫn bật web cho Visa/Weather.
Đây là điểm cân bằng tốt nhất giữa cost và risk.
```

---

### Config 4 — Lean Night Mode

| Intent cost | Scenario A: 4 turns | Scenario B: 7 turns |
|---|---:|---:|
| Guide | $0.001764 | $0.003262 |
| Visa | $0.001764 | $0.003262 |
| Weather | $0.034244 | $0.060103 |
| Booking handoff | $0.000000 | $0.000000 |
| Complaint handoff | $0.000000 | $0.000000 |

Weighted average:

```text
Scenario A = 50%×0.001764 + 25%×0.001764 + 10%×0.034244
           + 10%×0 + 5%×0
           = $0.004747 / conversation

Scenario B = 30%×0.003262 + 15%×0.003262 + 10%×0.060103
           + 35%×0 + 10%×0
           = $0.007478 / conversation
```

| Item | Scenario A | Scenario B |
|---|---:|---:|
| Cost / conversation | $0.004747 | $0.007478 |
| Monthly cost | $42.73 | $269.22 |
| Human baseline | $4,500 | $18,000 |
| Rẻ hơn human | 105.3× | 66.9× |
| Savings | 99.05% | 98.50% |

**Sanity check**:

```text
Lean Night Mode khắc phục phần Weather real-time nhưng vẫn bỏ rủi ro Visa stale.
Nó hợp làm fallback/low-traffic mode hơn là default production mode.
```

---

## Quality + Speed estimate

| Config | Quality | Speed | Lý do |
|---|---|---|---|
| Budget Bot | Low-Medium | High | GPT-4o-mini nhanh và rẻ, nhưng web OFF làm Visa/Weather rủi ro. |
| Premium Concierge | High | Low-Medium | Sonnet + LLM classifier + full history tốt nhất nhưng chậm hơn, nhất là khi gọi web. |
| Smart Mix | Medium-High | Medium-High | Gemini Flash đủ tốt cho guide phổ biến, web selective bảo vệ intent nhạy cảm. |
| Lean Night Mode | Medium | High | Rất nhanh và có weather real-time, nhưng Visa vẫn phụ thuộc RAG. |

---

## Bảng kiểm trước khi sang file tiếp theo

- [x] Tất cả configs có cost/conv và monthly cho cả 2 scenarios
- [x] Đã so sánh với human baseline $0.50/conversation
- [x] Có quality + speed estimate cho mỗi config
- [x] Đã sanity check các số lớn nhất/nhỏ nhất
