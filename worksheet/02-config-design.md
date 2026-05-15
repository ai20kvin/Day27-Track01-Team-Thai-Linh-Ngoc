# 02 · Configuration Design — Đặt tên + Chốt knobs cho ≥3 Configs

> **Mục tiêu**: Biến phác thảo ở `01-base-flow.md` thành ≥3 configurations chi tiết, mỗi config có tên + 3 knobs đã chốt + lý do chọn.
>
> **Thời gian**: 15 phút (đầu phần Main, trước khi tính cost)

---

## Tại sao đặt tên + viết lý do?

Khi present, nhóm sẽ nói "Config 1, Config 2, Config 3" → người nghe sẽ chán ngay. Đặt tên gợi mở (Budget Bot, Premium Concierge, Smart Mix...) giúp memorable + cho thấy nhóm hiểu rõ tradeoff. Viết lý do giúp nhóm tự kiểm tra: "Mình chọn config này vì lý do gì? Có justify được không?"

---

## Cách điền

Với mỗi config: đặt tên + chốt 3 knobs + viết 2–3 câu lý do chọn. Mỗi câu lý do phải gắn với 1 tình huống thực tế (volume thấp / khách hỏi visa nhiều / budget bị siết...).

Tham khảo bảng pricing chi tiết tại `cost-reference-card.md` mục **3. Decision Points**.

---

## Config 1

**Tên config** (gợi mở: "Budget Bot", "Bare Minimum", "Lean Mode", "Night Mode" — đặt tên có cá tính):

```text
(điền tên vào đây)
```

### 3 Knobs

**① Model tier**:

```text
Response model: __________________ → giá $_____ / $_____  per 1M tokens (input/output)
Classifier model: __________________ → giá $_____ / $_____  per 1M tokens (hoặc keyword = $0)
```

**② Web search**:

```text
□ OFF
□ ON selective — bật cho intent: __________________
□ ON broad
```

**③ History management**:

```text
□ Last 3
□ Last 5
□ Full
□ Summarize every ___ turns
```

### Lý do nhóm chọn config này

Trước khi viết, tự hỏi:

- Config này phục vụ tình huống nào tốt nhất? (mùa thấp điểm? night-time? volume cao đột biến?)
- Trade-off chính là gì? (Rẻ nhưng kém chất lượng? Đắt nhưng chính xác?)
- Khách hàng nào sẽ hài lòng nhất với config này? Khách nào sẽ thất vọng?

```text
(điền 2–3 câu lý do vào đây)
```

### Rủi ro lớn nhất của config này

```text
(điền 1 câu rủi ro — ví dụ: "Visa info có thể outdated nếu web OFF",
 "Khách quên context khi history Last 3", "Cost spike nếu volume tăng đột biến")
```

---

## Config 2

**Tên config**:

```text
(điền tên vào đây)
```

### 3 Knobs

**① Model tier**:

```text
Response model: __________________ → giá $_____ / $_____  per 1M tokens
Classifier model: __________________ → giá $_____ / $_____  per 1M tokens (hoặc keyword)
```

**② Web search**:

```text
□ OFF
□ ON selective — bật cho intent: __________________
□ ON broad
```

**③ History management**:

```text
□ Last 3
□ Last 5
□ Full
□ Summarize every ___ turns
```

### Lý do nhóm chọn config này

```text
(điền 2–3 câu lý do vào đây)
```

### Rủi ro lớn nhất của config này

```text
(điền 1 câu rủi ro)
```

---

## Config 3

**Tên config**:

```text
(điền tên vào đây)
```

### 3 Knobs

**① Model tier**:

```text
Response model: __________________ → giá $_____ / $_____  per 1M tokens
Classifier model: __________________ → giá $_____ / $_____  per 1M tokens (hoặc keyword)
```

**② Web search**:

```text
□ OFF
□ ON selective — bật cho intent: __________________
□ ON broad
```

**③ History management**:

```text
□ Last 3
□ Last 5
□ Full
□ Summarize every ___ turns
```

### Lý do nhóm chọn config này

```text
(điền 2–3 câu lý do vào đây)
```

### Rủi ro lớn nhất của config này

```text
(điền 1 câu rủi ro)
```

---

## Config 4 (optional — nếu thời gian dư)

Nhóm có thể thiết kế thêm config thứ 4 để có thêm điểm so sánh. Không bắt buộc.

**Tên config**:

```text
(điền tên vào đây)
```

### 3 Knobs

```text
Model: ___    Web: ___    History: ___
```

### Lý do

```text
(điền 1–2 câu)
```

---

## Bảng kiểm trước khi tính cost

- [ ] ≥3 configs đã đặt tên (không chỉ "Config 1/2/3")
- [ ] Mỗi config đã chốt rõ 3 knobs (không còn ô trống)
- [ ] Mỗi config có ≥2 câu lý do
- [ ] 3 configs đủ khác biệt — không phải chỉ đổi mỗi 1 knob nhỏ
- [ ] Nhóm đồng thuận đây là 3 configs đáng so sánh

**Nếu 3 configs quá giống nhau** (chỉ đổi model, knobs khác giống hệt) → quay lại tweak. Mục đích là thấy tradeoff — configs giống nhau quá → không thấy tradeoff.

Xong → mở `03-cost-calculation.md` để bắt đầu tính cost.
