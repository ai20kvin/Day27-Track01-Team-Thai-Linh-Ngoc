# 04 · Comparison Table — Bảng so sánh đầy đủ

> **Mục tiêu**: Tổng hợp tất cả số đã tính ở `03-cost-calculation.md` thành 1 bảng so sánh duy nhất — đây là artifact chính nhóm sẽ present.
>
> **Thời gian**: 10 phút (đầu phần Final)

---

## Vì sao có bảng so sánh?

Khi sếp hỏi "Nên deploy config nào?", bạn cần đặt lên bàn **1 bảng** thay vì đọc 3 báo cáo riêng. Bảng so sánh đầy đủ cho phép so sánh thẳng từng dòng, dễ nhìn ra tradeoff.

---

## Bảng chính

Điền số đã tính. Số nào chưa có → quay lại `03-cost-calculation.md` tính cho xong.

| | Config 1 | Config 2 | Config 3 | (Config 4) |
|---|---|---|---|---|
| **Tên** | __________ | __________ | __________ | __________ |
| **① Model** | | | | |
| **② Web search** | | | | |
| **③ History** | | | | |
| **Intent classifier** | Keyword/LLM | Keyword/LLM | Keyword/LLM | Keyword/LLM |
| **Cost / conv (Scenario A — 4 turns)** | $______ | $______ | $______ | $______ |
| **Cost / conv (Scenario B — 7 turns)** | $______ | $______ | $______ | $______ |
| **Monthly A** (300 conv/day × 30) | $______ | $______ | $______ | $______ |
| **Monthly B** (1,200 conv/day × 30) | $______ | $______ | $______ | $______ |
| **vs human $4,500/mo (A)** | rẻ ___× | rẻ ___× | rẻ ___× | rẻ ___× |
| **vs human $18,000/mo (B)** | rẻ ___× | rẻ ___× | rẻ ___× | rẻ ___× |
| **Savings % (A)** | ___% | ___% | ___% | ___% |
| **Savings % (B)** | ___% | ___% | ___% | ___% |
| **Quality estimate** | Low/Med/High | Low/Med/High | Low/Med/High | Low/Med/High |
| **Speed estimate** | Low/Med/High | Low/Med/High | Low/Med/High | Low/Med/High |
| **Điểm yếu chính** | (1 câu) | (1 câu) | (1 câu) | (1 câu) |
| **Best for** (khi nào nên dùng) | (1 câu) | (1 câu) | (1 câu) | (1 câu) |

---

## Quan sát nhanh từ bảng

Trước khi sang file recommendation, trả lời 4 câu — đây là material để present:

### Câu 1 — Config rẻ nhất là gì? Đắt nhất là gì?

```text
Rẻ nhất: ______________ — monthly B = $______
Đắt nhất: _____________ — monthly B = $______
Chênh: ___× lần
```

### Câu 2 — Knob nào ảnh hưởng cost nhiều nhất?

So sánh các config khác nhau ở knob nào, chênh bao nhiêu. Thường: model tier > history > web search.

```text
(điền vào đây — ví dụ: "Đổi từ cheap → strong model tăng cost ___×.
History Full vs Last 3 chênh ___% ở Turn 7. Web search bật/tắt chênh $___ /conv")
```

### Câu 3 — Tại sao Scenario B không đắt ×4 lần Scenario A?

Volume Scenario B = ×4 lần Scenario A. Turns dài hơn (7 vs 4 = ×1.75). Mong đợi monthly B ≈ A × 7. Thực tế có thể thấp hơn vì sao?

Trước khi viết, nghĩ: intent mix Scenario B có gì khác? Booking + Complaint = $0 LLM ở scenario B là bao nhiêu %?

```text
(điền 1–2 câu vào đây)
```

### Câu 4 — Có config nào AI đắt hơn human không?

So sánh monthly từng config với human baseline ($4,500 cho A, $18,000 cho B). Nếu AI rẻ hơn → savings %. Nếu đắt hơn → cần justify.

```text
(điền vào đây — nếu có config đắt hơn human, viết justify: 24/7? đa ngôn ngữ?
 scale linear? consistency tốt hơn?)
```

---

## Bảng kiểm trước khi sang file tiếp theo

- [ ] Bảng đầy đủ — không còn ô trống
- [ ] Đã có 4 câu trả lời cho 4 quan sát ở trên
- [ ] Nhóm đồng thuận về số trong bảng (đã sanity check)

Xong → mở `05-recommendation.md` để viết recommendation cuối + chuẩn bị present.
