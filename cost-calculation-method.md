# Cost Calculation Method — Day 27

> File này giải thích cách nhóm tính chi phí chatbot trong `worksheet/03-cost-calculation.md`, `worksheet/04-comparison-table.md`, và `worksheet/05-recommendation.md`.

---

## 1. Mục tiêu tính

Mục tiêu không phải chỉ tính "một lượt gọi model tốn bao nhiêu", mà tính theo unit economics của sản phẩm:

```text
cost per turn
→ cost per conversation theo intent
→ weighted average cost/conversation theo intent mix
→ monthly cost theo volume
→ so sánh với human baseline
```

Điểm quan trọng: các intent không có cost giống nhau.

- Guide dùng RAG + model response.
- Visa có thể dùng RAG + web search + model response.
- Weather cần web search + model response.
- Booking handoff sang sales.
- Complaint escalate sang manager.

---

## 2. Nguồn số liệu

Nhóm dùng `cost-reference-card.md` làm source of truth.

### Token assumptions

```text
System prompt:              500 tokens
User message:                80 tokens
Assistant response:         180 output tokens
1 prior turn history:       260 tokens
RAG top-5 chunks:         1,250 tokens
Web search results:         800 tokens nếu bật
Web search API call:       $0.008/query
```

### Human baseline

```text
Human cost = $0.50 / conversation
```

Scenario A:

```text
300 conversations/day × 30 days × $0.50 = $4,500/month
```

Scenario B:

```text
1,200 conversations/day × 30 days × $0.50 = $18,000/month
```

---

## 3. Model pricing dùng trong bài

| Model | Input / 1M tokens | Output / 1M tokens | Dùng cho config |
|---|---:|---:|---|
| GPT-4o-mini | $0.15 | $0.60 | Budget Bot, Lean Night Mode |
| Gemini 2.5 Flash | $0.30 | $2.50 | Smart Mix |
| Claude Sonnet 4.6 | $3.00 | $15.00 | Premium Concierge response |
| Claude Haiku 4.5 | $1.00 | $5.00 | Premium Concierge classifier |

---

## 4. Configs được tính

| Config | Response model | Classifier | Web search | History |
|---|---|---|---|---|
| Budget Bot | GPT-4o-mini | Keyword = $0 | OFF | Last 3 |
| Premium Concierge | Claude Sonnet 4.6 | Claude Haiku 4.5 | Visa + Weather | Full |
| Smart Mix | Gemini 2.5 Flash | Keyword = $0 | Visa + Weather | Last 5 |
| Lean Night Mode | GPT-4o-mini | Keyword = $0 | Weather only | Last 3 |

---

## 5. Cách tính history tokens

Mỗi prior turn gồm:

```text
80 user tokens + 180 assistant tokens = 260 tokens
```

Tùy history strategy:

```text
Full history = (turn - 1) × 260

Last 3 = min(turn - 1, 3) × 260

Last 5 = min(turn - 1, 5) × 260
```

Ví dụ turn 4:

```text
Full history = (4 - 1) × 260 = 780 tokens
Last 3       = min(3, 3) × 260 = 780 tokens
Last 5       = min(3, 5) × 260 = 780 tokens
```

Ví dụ turn 7:

```text
Full history = (7 - 1) × 260 = 1,560 tokens
Last 3       = min(6, 3) × 260 = 780 tokens
Last 5       = min(6, 5) × 260 = 1,300 tokens
```

---

## 6. Cách tính input tokens mỗi AI turn

Nếu không bật web search:

```text
Input tokens =
  500 system prompt
+ 80 user message
+ history tokens
+ 1,250 RAG chunks
```

Nếu bật web search:

```text
Input tokens =
  500 system prompt
+ 80 user message
+ history tokens
+ 1,250 RAG chunks
+ 800 web search results
```

Output tokens cố định:

```text
Output tokens = 180
```

---

## 7. Cách tính model cost mỗi turn

Công thức:

```text
Model cost =
  input_tokens × input_price / 1,000,000
+ output_tokens × output_price / 1,000,000
```

Ví dụ Budget Bot, turn 4, Last 3, không web:

```text
History = 780
Input = 500 + 80 + 780 + 1,250 = 2,610 tokens
Output = 180 tokens

Input cost  = 2,610 × $0.15 / 1,000,000 = $0.0003915
Output cost = 180 × $0.60 / 1,000,000 = $0.0001080

Total model cost = $0.0004995
```

---

## 8. Cách tính web search cost

Nếu config bật web cho intent đó:

```text
Web API cost = $0.008 / turn
Web context  = +800 input tokens / turn
```

Trong bài này:

- Premium Concierge bật web cho Visa + Weather.
- Smart Mix bật web cho Visa + Weather.
- Lean Night Mode bật web cho Weather only.
- Budget Bot tắt web hoàn toàn.

Nhóm tính theo hướng conservative:

```text
Nếu conversation thuộc intent Visa/Weather và config bật web cho intent đó,
mỗi turn của conversation gọi web search 1 lần.
```

---

## 9. Cách tính classifier cost

Keyword classifier:

```text
Cost = $0
```

LLM classifier trong Premium Concierge:

```text
Model: Claude Haiku 4.5
Input: 150 tokens
Output: 20 tokens

Classifier cost =
  150 × $1 / 1,000,000
+ 20 × $5 / 1,000,000
= $0.00015 + $0.00010
= $0.00025 / turn
```

Booking/Complaint không tạo response AI, nhưng nếu config dùng LLM classifier thì vẫn tốn classifier cost để biết cần handoff.

---

## 10. Cách tính cost/conversation theo intent

### Guide

```text
Guide cost =
sum(model cost từng turn)
+ classifier cost từng turn nếu dùng LLM classifier
```

Không web search.

### Visa

```text
Visa cost =
sum(model cost từng turn)
+ web API cost từng turn nếu config bật web cho Visa
+ classifier cost từng turn nếu dùng LLM classifier
```

### Weather

```text
Weather cost =
sum(model cost từng turn)
+ web API cost từng turn nếu config bật web cho Weather
+ classifier cost từng turn nếu dùng LLM classifier
```

### Booking

```text
Booking cost =
$0 nếu keyword classifier
$0.00025 nếu LLM classifier
```

Không tính response generation vì handoff sang sales.

### Complaint

```text
Complaint cost =
$0 nếu keyword classifier
$0.00025 nếu LLM classifier
```

Không tính response generation vì escalate sang manager.

---

## 11. Scenario A formula

Scenario A:

```text
Volume:       300 conversations/day
Turns/conv:   4
Intent mix:
- Guide:     50%
- Visa:      25%
- Weather:   10%
- Booking:   10%
- Complaint:  5%
```

Weighted average:

```text
Avg cost A =
  50% × cost_guide_4turn
+ 25% × cost_visa_4turn
+ 10% × cost_weather_4turn
+ 10% × cost_booking_handoff
+  5% × cost_complaint_handoff
```

Monthly:

```text
Monthly A = Avg cost A × 300 × 30
```

---

## 12. Scenario B formula

Scenario B:

```text
Volume:       1,200 conversations/day
Turns/conv:   7
Intent mix:
- Guide:     30%
- Visa:      15%
- Weather:   10%
- Booking:   35%
- Complaint: 10%
```

Weighted average:

```text
Avg cost B =
  30% × cost_guide_7turn
+ 15% × cost_visa_7turn
+ 10% × cost_weather_7turn
+ 35% × cost_booking_handoff
+ 10% × cost_complaint_handoff
```

Monthly:

```text
Monthly B = Avg cost B × 1,200 × 30
```

---

## 13. Cách tính savings so với human

Human monthly:

```text
Human A = $4,500/month
Human B = $18,000/month
```

Savings:

```text
Savings % =
(human_monthly - AI_monthly) / human_monthly × 100
```

Rẻ hơn human bao nhiêu lần:

```text
Human multiple =
human_monthly / AI_monthly
```

---

## 14. Ví dụ đầy đủ: Smart Mix Scenario A

Smart Mix:

```text
Model: Gemini 2.5 Flash = $0.30 input / $2.50 output
Classifier: Keyword = $0
Web: Visa + Weather
History: Last 5
Turns: 4
```

Cost theo intent đã tính:

```text
Guide 4 turns:   $0.004464
Visa 4 turns:    $0.037424
Weather 4 turns: $0.037424
Booking:         $0
Complaint:       $0
```

Weighted average:

```text
Avg cost A =
  50% × 0.004464
+ 25% × 0.037424
+ 10% × 0.037424
+ 10% × 0
+  5% × 0

= 0.002232
+ 0.009356
+ 0.003742
= $0.015330 / conversation
```

Monthly:

```text
Monthly A =
0.015330 × 300 × 30
= $137.97/month
```

Savings:

```text
Savings A =
(4,500 - 137.97) / 4,500 × 100
= 96.93%
```

Human multiple:

```text
4,500 / 137.97 = 32.6× cheaper than human
```

---

## 15. Ví dụ đầy đủ: Smart Mix Scenario B

Cost theo intent:

```text
Guide 7 turns:   $0.008553
Visa 7 turns:    $0.066233
Weather 7 turns: $0.066233
Booking:         $0
Complaint:       $0
```

Weighted average:

```text
Avg cost B =
  30% × 0.008553
+ 15% × 0.066233
+ 10% × 0.066233
+ 35% × 0
+ 10% × 0

= 0.002566
+ 0.009935
+ 0.006623
= $0.019124 / conversation
```

Monthly:

```text
Monthly B =
0.019124 × 1,200 × 30
= $688.47/month
```

Savings:

```text
Savings B =
(18,000 - 688.47) / 18,000 × 100
= 96.18%
```

Human multiple:

```text
18,000 / 688.47 = 26.1× cheaper than human
```

---

## 16. Vì sao Scenario B không tăng đúng 7 lần?

Nếu chỉ nhìn volume và turns:

```text
Scenario B volume = 4× Scenario A
Scenario B turns  = 7 / 4 = 1.75× Scenario A

Expected naive multiplier = 4 × 1.75 = 7×
```

Nhưng thực tế thấp hơn vì intent mix thay đổi:

```text
Scenario A handoff = Booking 10% + Complaint 5% = 15%
Scenario B handoff = Booking 35% + Complaint 10% = 45%
```

High season có nhiều conversation booking/complaint hơn, mà các intent này được handoff sớm nên không chạy response generation.

Vì vậy monthly cost tăng khoảng 4.8-5.1× với các config chính, không phải 7×.

---

## 17. Kết luận từ cách tính

Từ bảng cost:

```text
Budget Bot:
- Rẻ nhất
- Nhưng web OFF → rủi ro Visa/Weather stale

Premium Concierge:
- Quality cao nhất
- Vẫn rẻ hơn human nhiều
- Nhưng đắt hơn Smart Mix khoảng 3.1×

Smart Mix:
- Cost thấp
- Savings >96% ở cả hai scenarios
- Có web search cho Visa/Weather
- Phù hợp làm default

Lean Night Mode:
- Rẻ và nhanh
- Có weather real-time
- Nhưng Visa vẫn stale nên chỉ hợp làm fallback
```

Recommendation cuối:

```text
Chọn Smart Mix làm default production config.
```

