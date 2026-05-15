# 01 · Base Flow + Chốt 3 Knobs

> **Mục tiêu**: Hiểu chatbot hoạt động ra sao ở mức base (không chọn config gì) — và xác định 3 knobs nhóm sẽ tweak ở các bước sau.
>
> **Thời gian**: 7 phút (trong 15 phút phần Setup)

---

## Bước 2 — Vẽ lại flow theo cách hiểu của nhóm

```text
┌─────────────────────────────────────────────────────────────────────┐
│                        TOURIST gửi tin nhắn                         │
└───────────────────────────┬─────────────────────────────────────────┘
                            ▼
┌─────────────────────────────────────────────────────────────────────┐
│  BƯỚC 1 · INTENT CLASSIFICATION                                     │
│  (Keyword match HOẶC LLM classifier ~170 tokens)                    │
│  → Xác định thuộc 1 trong 5 intent:                                 │
│    Guide / Visa / Weather / Booking / Complaint                      │
└───────────────────────────┬─────────────────────────────────────────┘
                            ▼
┌─────────────────────────────────────────────────────────────────────┐
│  BƯỚC 2 · ROUTE THEO INTENT                                         │
│                                                                     │
│  Guide ──────→ RAG (knowledge base)                                 │
│  Visa ───────→ RAG + (Web search nếu bật)                           │
│  Weather ────→ Web search (real-time)                               │
│  Booking ────→ HANDOFF → Sales agent ($0 LLM)                       │
│  Complaint ──→ ESCALATE → Manager ($0 LLM)                          │
└───────────────────────────┬─────────────────────────────────────────┘
                            │ (chỉ Guide / Visa / Weather tiếp tục)
                            ▼
┌─────────────────────────────────────────────────────────────────────┐
│  BƯỚC 3 · CONTEXT ASSEMBLY                                          │
│  System prompt      : 500 tokens (cố định)                          │
│  Conversation history: (T-1) × 260 tokens (tuỳ History knob)        │
│  RAG top-5 chunks   : 1,250 tokens (cố định khi dùng RAG)           │
│  Web search results : 800 tokens (khi bật)                          │
│  User message       : 80 tokens                                     │
│  → Tổng input tokens để đưa vào LLM                                 │
└───────────────────────────┬─────────────────────────────────────────┘
                            ▼
┌─────────────────────────────────────────────────────────────────────┐
│  BƯỚC 4 · RESPONSE GENERATION                                       │
│  LLM (model tuỳ knob Model Tier) generate câu trả lời              │
│  Output ≈ 180 tokens                                                │
│  → Gửi về tourist                                                   │
└─────────────────────────────────────────────────────────────────────┘
```

**Key insight từ flow**:
- Booking + Complaint → handoff ngay → **$0 LLM cost** cho 2 intent này
- Mỗi turn cộng thêm 260 tokens vào history → turn càng về sau càng đắt
- Web search bật thêm 800 tokens input + $0.005 API call mỗi lần gọi

---

## Bước 3 — Xác định 3 Knobs

### Knob 1 — Model tier

```text
Suy nghĩ của nhóm:
- Mục tiêu chính: cân bằng cost + quality — không phải rẻ nhất hay mạnh nhất.
- Tourist hỏi cả câu đơn giản (weather) lẫn phức tạp (visa policy, itinerary).
- Chiến lược mix có vẻ hợp lý: dùng cheap model cho classifier + medium/strong
  cho response — tối ưu cost mà không mất quality ở phần quan trọng.
- Premium chỉ đáng nếu conversion rate booking tăng đủ để justify cost cao hơn.
```

### Knob 2 — Web search

```text
Suy nghĩ của nhóm:
- Visa policy thay đổi thường xuyên → RAG alone không đủ, dễ stale → bật web cho Visa.
- Weather là real-time tự nhiên → bắt buộc bật web cho Weather.
- Guide (top places, food, tips) ít thay đổi → RAG đủ, không cần web.
- ON selective (Visa + Weather) là điểm cân bằng: tránh bật web bừa bãi
  mà vẫn đảm bảo accuracy ở 2 intent critical nhất.
- Web search tốn $0.005/call + 800 tokens — bật cho Guide là overkill.
```

### Knob 3 — History management

```text
Suy nghĩ của nhóm:
- Scenario A: 4 turns → full history = 3 prior turns × 260 = 780 tokens extra → chấp nhận được.
- Scenario B: 7 turns → full history = 6 × 260 = 1,560 tokens extra → cộng dồn đáng kể.
- Tourist hay đặt budget/preference ở turn đầu rồi refer lại → cắt history quá ngắn
  (Last 3) → bot quên → trải nghiệm tệ, có thể mất booking.
- Last 5 là sweet spot: đủ nhớ context trong phần lớn conversation, không quá đắt.
- Summarize every 5 nâng cao hơn nhưng cần thêm 1 LLM call → phức tạp hơn.
```

---

## Bước 4 — Phác thảo combo

**Combo 1 (định hướng cheap)**:
```text
Model: Cheap (Gemini Flash-Lite / GPT-4o-mini)
Web: OFF
History: Last 3
Tên dự kiến: "Budget Bot"
```

**Combo 2 (định hướng premium)**:
```text
Model: Strong (Claude Sonnet 4.6 / DeepSeek V4 Pro)
Web: ON selective (Visa + Weather)
History: Full history
Tên dự kiến: "Premium Concierge"
```

**Combo 3 (định hướng balanced / smart mix)**:
```text
Model: Mid (Gemini Flash / Claude Haiku 4.5) — response; Cheap cho classifier
Web: ON selective (Visa + Weather)
History: Last 5
Tên dự kiến: "Smart Mix"
```

**Combo 4** (optional — lean với web):
```text
Model: Cheap — response; Keyword classifier ($0)
Web: ON selective (chỉ Weather)
History: Last 3
Tên dự kiến: "Lean Night Mode"
```

---

## Bảng kiểm

- [x] Đã vẽ flow base có đủ 4 bước (Intent → Route → Context → Response)
- [x] Hiểu Booking + Khiếu nại = $0 LLM cost (handoff)
- [x] Đã phác thảo ≥3 combo khác nhau
- [x] Nhóm đồng thuận về hướng đi mỗi combo