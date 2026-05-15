# Prompt 03 — Cảm nhận Chất lượng từng Config (Optional)

> **Dùng khi**: Có thời gian dư trong Main phase, muốn kiểm tra nhanh xem các config nhóm thiết kế có thực sự khác biệt về chất lượng không — không chỉ khác về cost.
>
> **Bỏ qua khi**: Chưa tính xong cost cho tất cả configs. Prompt này là phần "bonus" — cost calculation (`prompts/01-cost-calc.md`) là ưu tiên.
>
> **Công cụ**: ChatGPT, Claude, Gemini — chạy thử trực tiếp model nhóm chọn cho config.

---

## Cách dùng

Mỗi config nhóm thiết kế đã chốt 1 generation model (Cheap / Mid / Strong / Premium / Mix). Mỗi nhóm chọn 2–3 test message từ danh sách bên dưới, gửi qua các model khác nhau, so sánh chất lượng câu trả lời.

Mục tiêu: thấy được tradeoff cost vs quality không chỉ trên giấy mà còn trên trải nghiệm thực tế.

---

## 4 test message — chọn 2–3 cái thử

### Test 1 — Câu hỏi điểm đến đơn giản

```text
What are the best places to visit in Hoi An? I have 2 days.
```

**Intent dự kiến**: Điểm đến/Guide → RAG only
**Mong đợi**: Bot đưa gợi ý lộ trình cụ thể, có địa danh
**Tiêu chí đánh giá chất lượng**:

- Có đưa ra ≥3 địa điểm cụ thể (không chung chung "Hoi An old town")?
- Có gợi ý phân bổ thời gian theo ngày 1 / ngày 2?
- Có đề cập đặc trưng địa phương (ẩm thực, văn hoá, hoạt động)?

### Test 2 — Câu hỏi visa có ngữ cảnh phức tạp

```text
I'm from the US, coming next month. Do I need a visa? I heard the rules changed recently.
```

**Intent dự kiến**: Visa/Policy → RAG + web search (vì có signal "rules changed recently")
**Mong đợi**: Bot nhận ra cần tra cứu thông tin mới nhất, không chỉ trả lời từ knowledge base cũ
**Tiêu chí đánh giá**:

- Bot có nhận diện được "rules changed recently" → cần web search không?
- Hay bot trả lời dựa hoàn toàn vào knowledge có sẵn?
- **Tradeoff quan trọng**: Classifier dùng keyword/regex thường miss tín hiệu này. LLM classifier bắt được tốt hơn.

### Test 3 — Câu hỏi nhiều intent trong 1 tin nhắn

```text
I want to visit Ha Long Bay next week. What's the weather like and can you book me a cruise?
```

**Intent dự kiến**: Thời tiết (cần web search) + Tour/Booking (chuyển sales)
**Mong đợi**: Bot xử lý được 2 intent — trả lời thời tiết và đồng thời chuyển booking cho sales
**Tiêu chí đánh giá**:

- Bot có nhận ra 2 intent không?
- Hay chỉ trả lời 1 phần (thường là thời tiết) và bỏ qua phần booking?
- Khả năng phân loại đa-intent là dấu hiệu config có chất lượng cao.

### Test 4 — Khiếu nại cần chuyển người

```text
I booked a tour through your site but the guide never showed up. I want a refund.
```

**Intent dự kiến**: Khiếu nại → Escalate manager (chứ không cố tự xử lý)
**Mong đợi**: Bot **không** cố giải quyết, chuyển ngay sang nhân viên/manager
**Tiêu chí đánh giá**:

- Bot có chuyển ngay không, hay cố trả lời (xin lỗi, đề nghị giải pháp)?
- Bot tự xử lý khiếu nại = rủi ro pháp lý + danh tiếng cho công ty.
- Đây là test quan trọng nhất về **routing accuracy** — sai một lần có thể mất khách vĩnh viễn.

---

## Quy trình test (10–15 phút)

### Bước 1 — Chọn 2 model đối lập

Để thấy rõ tradeoff:

- 1 model **cheap** (vd: GPT-4o-mini hoặc Gemini Flash-Lite)
- 1 model **strong** (vd: Claude Sonnet 4.6 hoặc DeepSeek V4 Pro)

Nếu config nhóm dùng Mix → có thể test cả 2 model nhóm dùng cho intent khác nhau.

### Bước 2 — Gửi 2–3 test message qua mỗi model

Mỗi test message gửi nguyên văn (không kèm system prompt phức tạp). Mục tiêu chỉ là cảm nhận chất lượng phản hồi cơ bản.

### Bước 3 — Đánh giá nhanh

Với mỗi cặp (model × test message), điền bảng:

| Test | Cheap model | Strong model | Chênh lệch rõ rệt? |
|---|---|---|---|
| Test 1 (Điểm đến) | _____________ | _____________ | □ Có · □ Không |
| Test 2 (Visa context) | _____________ | _____________ | □ Có · □ Không |
| Test 3 (Multi-intent) | _____________ | _____________ | □ Có · □ Không |
| Test 4 (Khiếu nại) | _____________ | _____________ | □ Có · □ Không |

**Quy ước đánh giá**:
- ✓ Đầy đủ + Chính xác + Phù hợp ngữ cảnh
- △ Đầy đủ nhưng thiếu ngữ cảnh / có lỗi nhỏ
- ✗ Sai intent / Trả lời nhầm / Bỏ qua phần quan trọng

### Bước 4 — Rút insight

Trả lời cho nhóm:

```text
(điền 1–2 câu insight — ví dụ:
"Cheap model đủ tốt cho Test 1 (Guide), nhưng Test 2 visa thiếu signal cần web search.
Strong model bắt được nuance ở Test 2, 3, 4 nhưng cost gấp 30×.
→ Nhóm sẽ Mix: cheap cho Guide, strong cho Visa + Khiếu nại.")
```

---

## Lưu ý

- **Đây là test cảm tính, không phải eval chính thức** — eval thật cần ≥100 test cases + ground truth. Lab chỉ test 2–4 message để có ý niệm.
- **Nếu nhóm thấy không có chênh lệch rõ rệt** giữa cheap và strong model → có thể recommend Budget Bot (rẻ hơn, chất lượng đủ).
- **Nếu chênh lệch rõ rệt ở 3/4 tests** → cần Premium hoặc Smart Mix để tránh rủi ro.
- **Test 4 (Khiếu nại) là test bắt buộc** nếu nhóm chọn LLM classifier — vì routing sai = rủi ro lớn nhất.

---

*Prompt 03 · Quality eval. Optional — chỉ dùng nếu nhóm còn dư thời gian sau khi xong cost calc.*
