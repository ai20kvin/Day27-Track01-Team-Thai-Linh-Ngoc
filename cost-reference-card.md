# D27 Cost Reference Card — AI Conversation Cost Simulator

> Reference cho lab D27. Sinh viên dùng file này để tính cost cho các config khác nhau.
> Chatbot language: English (serving international tourists to Vietnam)

## Driving Question

**"Với thiết kế này, chi phí là bao nhiêu? Có hợp lý để triển khai không?"**

---

## 1. Bối cảnh bài tập

Một travel agency Việt Nam phục vụ khách quốc tế (inbound tourism) muốn triển khai AI chatbot trên website:
- Trả lời hỏi đáp: điểm đến, visa, thời tiết, văn hóa, ẩm thực
- Tư vấn lộ trình, gợi ý tour
- Khi khách muốn đặt tour/vé/phòng → chuyển qua nhân viên sales

**Chi phí nhân viên (reference):**
- 1 nhân viên handle ~30 conversations/ngày
- Lương: ~$15/ngày
- → Chi phí người: **~$0.50 / conversation**

---

## 2. Base Flow (instructor cung cấp)

```
Tourist gửi tin nhắn
        │
        ▼
┌─ Intent Classification ────────────────────────────┐
│  Phân loại ý định khách hàng                        │
│  → Visa/Policy | Guide/Destination | Weather/Event  │
│  → Tour/Booking | Complaint                         │
└────────┬───────────────────────────────────────────┘
         │
    ┌────┼────┬──────────┬───────────┐
    ▼    ▼    ▼          ▼           ▼
  Visa  Guide  Weather  Booking   Complaint
    │    │      │         │          │
    ▼    ▼      ▼         ▼          ▼
┌──────────────────┐  ┌────────┐  ┌──────────┐
│ Knowledge Lookup │  │Handoff │  │ Escalate │
│ RAG top 5 chunks │  │ Sales  │  │ Manager  │
│ + Web search?    │  │ ($0)   │  │ ($0)     │
└────────┬─────────┘  └────────┘  └──────────┘
         │
         ▼
┌─ Context Assembly ─────────────────────────────────┐
│  System prompt + History + RAG chunks               │
│  + Web search results (nếu bật) + User message      │
└────────┬───────────────────────────────────────────┘
         │
         ▼
┌─ Response Generation ──────────────────────────────┐
│  Model tạo câu trả lời cho khách                    │
└────────────────────────────────────────────────────┘
```

**Intent routing mặc định:**

| Intent | Route mặc định |
|--------|----------------|
| Visa/Policy | RAG + có thể cần web search (policy thay đổi) |
| Guide/Destination | RAG (KB đủ) |
| Weather/Event | Web search (real-time info) |
| Tour/Booking | Chuyển sales — $0 LLM cost |
| Complaint | Escalate manager — $0 LLM cost |

---

## 3. Decision Points — 3 knobs cần chọn

### Knob 1: Model tier — "Chất lượng trả lời ở level nào?"

Chọn model cho response generation. Model mạnh hơn = trả lời tốt hơn nhưng đắt hơn.

| Tier | Model | Provider | Input / 1M tokens | Output / 1M tokens |
|------|-------|----------|-------------------|-------------------|
| **Cheap** | Gemini 2.5 Flash-Lite | Google | $0.10 | $0.40 |
| | DeepSeek V4 Flash | DeepSeek | $0.14 | $0.28 |
| | GPT-4o-mini | OpenAI | $0.15 | $0.60 |
| **Mid** | Gemini 2.5 Flash | Google | $0.30 | $2.50 |
| | Claude Haiku 4.5 | Anthropic | $1.00 | $5.00 |
| **Strong** | DeepSeek V4 Pro | DeepSeek | $1.74 | $3.48 |
| | Claude Sonnet 4.6 | Anthropic | $3.00 | $15.00 |
| **Premium** | Claude Opus 4.7 | Anthropic | $5.00 | $25.00 |
| | GPT-5.5 | OpenAI | $5.00 | $30.00 |

**Quan sát:**
- Cùng tier, provider khác nhau → giá khác nhau đáng kể (DeepSeek V4 Pro strong nhưng rẻ hơn Sonnet ~4×)
- Output tokens luôn đắt hơn input 3-5× → câu trả lời dài = tốn hơn
- Cheap → Strong chênh 20-50× → model choice là knob ảnh hưởng cost lớn nhất

**Lưu ý**: Có thể dùng model khác nhau cho intent khác nhau (ví dụ: cheap cho FAQ, strong cho visa/complaint). Đây là routing — dùng đúng mức "trí thông minh" cho đúng loại câu hỏi.

### Knob 2: Web search — "Có cần thông tin real-time không?"

| Option | Mô tả | Cost |
|--------|-------|------|
| OFF | Chỉ dùng RAG (knowledge base) | $0 |
| ON (selective) | Bật cho 1-2 intent cần real-time (visa, weather) | $0.008/query + ~800 tokens thêm vào context |
| ON (broad) | Bật cho hầu hết intent | Nhiều query hơn, cost cao hơn |

**Web search API cost:** ~$0.008 / query (Tavily)

**Tradeoff:**
- OFF: rẻ, nhanh, nhưng thông tin có thể outdated (visa policy vừa đổi → chatbot trả lời sai)
- ON: đắt hơn, chậm hơn (~1-2s thêm), nhưng thông tin fresh → khách tin tưởng hơn

### Knob 3: History management — "Chatbot cần nhớ bao nhiêu context?"

| Option | Mô tả |
|--------|-------|
| Last 3 turns | Nhẹ nhất. Chỉ nhớ 3 lượt gần nhất. Rẻ nhưng dễ quên "tôi đã nói budget $500" |
| Last 5 turns | Cân bằng. Đủ context cho hầu hết conversation |
| Full history | Nhớ tất cả. Đắt nhất ở conversation dài (7-10 turns). Không mất thông tin |
| Summary every N turns | Nâng cao — tóm tắt lịch sử cũ. Tiết kiệm tokens nhưng cần 1 LLM call thêm |

---

## 4. Token Estimates (reference)

Các con số ước lượng để tính cost. Không cần chính xác tuyệt đối — đây là mô phỏng.

```
System prompt:              500 tokens
User message (avg):          80 tokens
Assistant response (avg):   180 tokens (output)
1 prior turn in history:    260 tokens (80 user + 180 assistant)

RAG: top 5 chunks:        1,250 tokens (cố định)
Web search results:         800 tokens (khi bật)

1 chunk ≈ 250 tokens ≈ 150-200 words
```

**Token counting tools** (để tự verify nếu muốn):
- [OpenAI Tokenizer](https://platform.openai.com/tokenizer) — paste text, đếm ngay
- [Tiktokenizer](https://www.tiktokenizer.app/tiktoken-online) — free, nhiều model
- [Runcell Token Counter](https://www.runcell.dev/tool/token-counter) — hỗ trợ GPT, Claude, Gemini

---

## 5. Knowledge Base (cho sẵn)

KB của travel agency gồm ~40 articles, đã chunked sẵn:

| Chủ đề | Số articles | Chunks | Tokens |
|--------|------------|--------|--------|
| Destinations (Ha Long, Hoi An, Sapa, HCMC...) | 15 | 50 | 12,500 |
| Visa & Entry Policy | 5 | 15 | 3,750 |
| Food & Culture | 8 | 25 | 6,250 |
| Transport & Getting Around | 5 | 15 | 3,750 |
| Tour Packages | 5 | 10 | 2,500 |
| Safety & Health Tips | 2 | 5 | 1,250 |
| **Total** | **40** | **120** | **30,000** |

**Indexing cost (one-time):** 30,000 tokens × $0.02/1M = **$0.0006 ≈ gần như miễn phí**

→ **Insight: indexing cost không đáng kể. Cost chính nằm ở query-time (mỗi lượt chat).**

---

## 6. Embedding & Other Costs

| Item | Cost |
|------|------|
| Embedding model (text-embedding-3-small) | $0.02 / 1M tokens |
| Web search API (Tavily Basic) | ~$0.008 / query |
| Vector DB hosting | Giả sử đã có sẵn (không tính) |
| Sales handoff | $0 LLM cost (chuyển người) |
| Escalation | $0 LLM cost (chuyển người) |

---

## 7. Ví dụ tính cost — 1 conversation 5 turns (FAQ, GPT-4o-mini, no web search, last 5)

```
Turn 1: 500 (sys) + 80 (user) + 1,250 (RAG) = 1,830 input → 180 output
Turn 2: 500 + 260 (1 turn history) + 80 + 1,250 = 2,090 input → 180 output
Turn 3: 500 + 520 + 80 + 1,250 = 2,350 input → 180 output
Turn 4: 500 + 780 + 80 + 1,250 = 2,610 input → 180 output
Turn 5: 500 + 1,040 + 80 + 1,250 = 2,870 input → 180 output

Total input:  11,750 tokens
Total output:    900 tokens

Cost (GPT-4o-mini @ $0.15/$0.60 per 1M):
  Input:  11,750 × $0.15 / 1,000,000 = $0.0018
  Output:    900 × $0.60 / 1,000,000 = $0.0005
  Total: $0.0023 / conversation

vs Human: $0.50 / conversation
→ AI rẻ hơn ~217×
```

**Nếu dùng Claude Sonnet ($3/$15) thay vì GPT-4o-mini:**
```
  Input:  11,750 × $3.00 / 1,000,000 = $0.035
  Output:    900 × $15.00 / 1,000,000 = $0.014
  Total: $0.049 / conversation

→ AI vẫn rẻ hơn người ~10×, nhưng đắt hơn GPT-4o-mini ~21×
```

**Nếu thêm web search 2/5 turns:**
```
  + 2 × $0.008 (API call) = $0.016
  + 2 × 800 tokens input × $0.15/1M = $0.0002
  → Thêm ~$0.016 / conversation
```

---

## 8. Prompt gợi ý để tính cost bằng AI

Sinh viên có thể dùng ChatGPT/Claude để tính thay vì tính tay:

```
Tính cost cho 1 conversation chatbot theo config sau:

Config:
- Generation model: [tên model, input price, output price]
- Web search: [ON cho intent nào / OFF]
- History: [last N turns / full]

Giả định:
- System prompt: 500 tokens
- User message: 80 tokens
- Assistant response: 180 tokens output
- 1 prior turn history: 260 tokens
- RAG top 5 chunks: 1,250 tokens
- Web search results: 800 tokens (khi bật)
- Web search API: $0.008 / query

Conversation scenario:
- Tổng cộng [X] turns
- Intent mix: [liệt kê intent mỗi turn]
- Turns có web search: [turn nào]

Yêu cầu:
1. Tính input tokens và output tokens cho từng turn
2. Tính cost cho từng turn
3. Tính total cost / conversation
4. So sánh với chi phí nhân viên: $0.50/conversation
```

---

## 9. Deliverable — Mỗi nhóm nộp

### A. Agent Flow (cho mỗi option)
Vẽ flow dựa trên base flow, đánh dấu lựa chọn tại mỗi knob.

### B. Bảng so sánh ≥3 options (OUTPUT CHÍNH)

| | Option 1 | Option 2 | Option 3 |
|---|---|---|---|
| **Tên config** | (ví dụ: Budget) | (ví dụ: Balanced) | (ví dụ: Premium) |
| Knob 1: Model | ___ | ___ | ___ |
| Knob 2: Web search | ___ | ___ | ___ |
| Knob 3: History | ___ | ___ | ___ |
| | | | |
| **Cost / conversation** | $___ | $___ | $___ |
| **So với nhân viên ($0.50)** | Rẻ hơn ___× | Rẻ hơn ___× | Rẻ hơn ___× |
| | | | |
| **Chất lượng** | ___ | ___ | ___ |
| **Tốc độ** | ___ | ___ | ___ |
| **Điểm yếu chính** | ___ | ___ | ___ |
| **Best for** | ___ | ___ | ___ |

### C. Kết luận
- Recommend option nào?
- Vì sao? (justify bằng cost + quality + speed)
- Có thể mix options không? (ví dụ: cheap cho FAQ, strong cho visa)

---

## 10. Câu hỏi thảo luận (khi present / so sánh giữa các nhóm)

1. "Visa policy Việt Nam vừa đổi. Config nào của bạn fail đầu tiên?"
2. "Khách VIP chat 10 lượt. Config nào tốn nhất? Chênh bao nhiêu so với config rẻ nhất?"
3. "Speed 4-5 giây có chấp nhận được cho chatbot du lịch không?"
4. "Nếu dùng model rẻ cho tất cả thì quality gap nằm ở đâu?"
5. "Có nên mix — cheap model cho FAQ, strong model cho visa — không? Cost thay đổi thế nào?"
6. "So với thuê thêm 1 nhân viên, lúc nào AI chatbot trở nên đắt hơn?"

---

## 11. Nguồn tham khảo — Model Pricing (verified May 2026)

### OpenAI
- Official pricing: [developers.openai.com/api/docs/pricing](https://developers.openai.com/api/docs/pricing)
- GPT-4o-mini, GPT-5.5 pricing verified from official page
- Reference: [pricepertoken.com/pricing-page/provider/openai](https://pricepertoken.com/pricing-page/provider/openai)

### Anthropic (Claude)
- Official pricing: [platform.claude.com/docs/en/about-claude/pricing](https://platform.claude.com/docs/en/about-claude/pricing)
- Claude Haiku 4.5: $1/$5, Sonnet 4.6: $3/$15, Opus 4.7: $5/$25 per 1M tokens — verified from official docs

### Google (Gemini)
- Official pricing: [ai.google.dev/gemini-api/docs/pricing](https://ai.google.dev/gemini-api/docs/pricing)
- Gemini 2.5 Flash-Lite: $0.10/$0.40, Gemini 2.5 Flash: $0.30/$2.50 per 1M tokens — verified from official docs

### DeepSeek
- Official pricing: [api-docs.deepseek.com/quick_start/pricing](https://api-docs.deepseek.com/quick_start/pricing/)
- V4 Flash: $0.14/$0.28, V4 Pro: $1.74/$3.48 per 1M tokens (regular price) — verified from official docs
- V4 Pro promo: 75% off ($0.435/$0.87) until 2026/05/31

### Embedding
- OpenAI text-embedding-3-small: $0.02/1M tokens — [developers.openai.com/api/docs/pricing](https://developers.openai.com/api/docs/pricing)

### Web Search API
- Tavily: ~$0.008/query (Basic Search) — [tavily.com/pricing](https://www.tavily.com/pricing), [docs.tavily.com/documentation/api-credits](https://docs.tavily.com/documentation/api-credits)

### Token Counting Tools
- [OpenAI Tokenizer](https://platform.openai.com/tokenizer)
- [Tiktokenizer](https://www.tiktokenizer.app/tiktoken-online)
- [Runcell Token Counter](https://www.runcell.dev/tool/token-counter)

> **Lưu ý**: Giá API thay đổi thường xuyên. Các con số trong file này được verify ngày 14/05/2026. Trước khi dùng trong production, luôn kiểm tra lại tại trang pricing chính thức của từng provider.
