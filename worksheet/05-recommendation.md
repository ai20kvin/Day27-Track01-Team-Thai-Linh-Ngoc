# 05 · Recommendation + Justification — Kết luận & Chuẩn bị Present

> **Mục tiêu**: Chọn 1 config (hoặc combo) nhóm recommend deploy, viết justification ngắn gọn, và chuẩn bị 5 phút present.
>
> **Thời gian**: 10 phút (cuối phần Final) — Pens down lúc 12:00

---

## Bảng số ai cũng tính được. PM giỏi phải **recommend** và **justify**.

Đây là phần quan trọng nhất — phần phân biệt nhóm chỉ làm xong với nhóm thực sự hiểu sản phẩm.

---

## 4 câu hỏi nhóm phải trả lời

Mỗi câu trả lời 2–4 câu. Không lan man, không clichés. Mỗi câu phải justify được bằng số trong bảng so sánh.

### Câu 1 — Recommend config nào?

Trước khi viết, thảo luận 1 phút:

- Recommend 1 config duy nhất chạy quanh năm? Hay 2 configs khác nhau cho mùa thấp / mùa cao?
- Có nên recommend "Smart Mix model theo intent" thay vì pick 1 config cố định không?
- Nếu sếp nói "chỉ deploy 1 config thôi" — chọn cái nào?

```text
(điền 2–4 câu recommend vào đây — ví dụ:
"Nhóm recommend Smart Mix cho cả 2 scenarios. Lý do: monthly cost vẫn rẻ hơn human
95% ở cả low và high season, mà quality estimate Medium-High đủ phục vụ khách
mong đợi info chính xác. Có thể cân nhắc Premium ở high season nếu booking convert
rate tăng đủ để justify cost." — đây là ví dụ, đừng copy.)
```

### Câu 2 — So với human baseline $0.50/conv → tiết kiệm bao nhiêu? Có đắt hơn human ở chỗ nào không?

```text
(điền vào đây — ví dụ:
"Tiết kiệm ___% ở Scenario A, ___% ở Scenario B. Tổng tiết kiệm $___/tháng.
Tuy nhiên cần justify thêm vì AI không thể replace human cho booking convert —
1 sales agent vẫn cần thiết. AI thắng ở 24/7 + đa ngôn ngữ + handle volume peak.")
```

### Câu 3 — Khi nào nên upgrade / downgrade config?

Trước khi viết, tự hỏi:

- Volume bao nhiêu thì cost AI scale lớn hơn benefit?
- Quality complaint rate bao nhiêu thì biết Budget Bot không đủ?
- Có signal nào báo nên chuyển sang Premium? (mùa cao điểm bắt đầu? customer feedback?)

```text
(điền vào đây — ví dụ:
"Nên upgrade lên Premium khi: monthly conv > 50,000 + quality complaint > 5%
+ approaching peak season (Tết, lễ hội). Nên downgrade về Budget khi: monthly
conv < 5,000 + low season + revenue per booking < $X.")
```

### Câu 4 — Rủi ro lớn nhất của config được chọn?

Trước khi viết, tự hỏi:

- Rủi ro về quality? (visa info outdated? language mismatch?)
- Rủi ro về cost? (provider tăng giá? volume spike?)
- Rủi ro về business? (khách bị bot trả lời sai → bad review → mất khách?)
- Có mitigation plan không?

```text
(điền vào đây — ví dụ:
"Rủi ro chính: provider tăng giá API → margin co lại. Mitigation: monitor cost
hàng tháng, có sẵn fallback sang DeepSeek V4 Pro nếu OpenAI/Anthropic tăng >30%.
Rủi ro phụ: web search OFF có thể dẫn đến visa info outdated → mitigation:
update RAG hàng tuần cho mục visa, fallback sang human nếu confidence < 0.7.")
```

---

## Final answer — Recommendation in 1 paragraph

Tổng hợp 4 câu trên thành 1 paragraph 5–7 câu — đây là phần nhóm sẽ đọc / chiếu khi present.

```text
(viết paragraph vào đây — đọc to lên để check có gọn không, có rõ không.
 Nếu lan man → cắt xuống. Nếu trống rỗng → bổ sung số cụ thể.)
```

---

## Chuẩn bị Present (5 phút)

Chia 5 phút thành 5 nhịp. 1 người trong nhóm chính phụ trách 1 nhịp. Người còn lại trả lời Q&A.

### Nhịp 0:00 – 0:30 — Base flow + 3 knobs đã chọn

Ai trình bày: __________

Nói gì:

```text
(viết 2 câu vào đây)
```

### Nhịp 0:30 – 1:00 — Config overview

Ai trình bày: __________

Nói gì (đọc nhanh tên + knobs 3 configs):

```text
(viết 3 dòng vào đây)
```

### Nhịp 1:00 – 2:00 — Cost comparison

Ai trình bày: __________

Nói gì (chiếu bảng so sánh, highlight rẻ nhất / đắt nhất):

```text
(viết 3–4 câu vào đây)
```

### Nhịp 2:00 – 3:00 — Key insight

Ai trình bày: __________

Nói gì (knob nào ảnh hưởng cost nhiều nhất + tại sao):

```text
(viết 2–3 câu vào đây)
```

### Nhịp 3:00 – 4:30 — Recommendation + justification

Ai trình bày: __________ (thường là người mạnh nhất trong nhóm)

Nói gì (đọc paragraph "Final answer" ở trên):

```text
(copy paragraph vào đây)
```

### Nhịp 4:30 – 5:00 — Hardest question prep

Ai trình bày: __________

Nhóm dự đoán câu hỏi khó nhất sẽ bị hỏi là gì?

```text
(viết câu hỏi vào đây — ví dụ:
"Tại sao không chọn Budget Bot cho high season? Premium tăng cost nhiều hơn
benefit có không?")
```

Câu trả lời sẵn:

```text
(viết câu trả lời 2–3 câu)
```

---

## Q&A — 2 phút sau khi present xong

Sẵn sàng cho 1 câu từ class + 1 câu từ instructor. Không cần lo lắng — nếu chưa biết câu trả lời, nói "đây là điểm nhóm chưa nghĩ đến — sẽ tính lại sau buổi".

**3 câu instructor thường hỏi**:

1. *"Knob nào ảnh hưởng cost nhiều nhất trong config của nhóm? Tại sao?"*
2. *"Nếu provider tăng giá API ×2 → config của nhóm còn sống được không?"*
3. *"So với nhóm X (vừa present trước) — tại sao nhóm bạn chọn khác?"*

Suy nghĩ trước câu trả lời ngắn:

```text
1. (viết câu trả lời ngắn)
2. (viết câu trả lời ngắn)
3. (viết câu trả lời ngắn — phụ thuộc nhóm X present gì, có thể skip)
```

---

## Bảng kiểm cuối cùng — trước 12:00 Pens Down

- [ ] Đã trả lời 4 câu PM (Recommend / Savings / Threshold / Risk)
- [ ] Final answer paragraph viết gọn (5–7 câu)
- [ ] Phân công 5 nhịp present cho mỗi thành viên
- [ ] Có sẵn câu trả lời cho 3 câu Q&A dự đoán
- [ ] Comparison table có sẵn để chiếu / chuyền tay khi present
- [ ] Repo đã commit + push (sẽ nộp link sau buổi học)

---

## Sau buổi học

1. **Commit + push repo** với tất cả file đã điền.
2. **Dán link repo** vào Discord `#day27-evidence-boards` trước 23:59.
3. **Chuẩn bị cho D28**: peer review giữa các nhóm — sẽ bị hỏi câu chất vấn khó hơn instructor. Polish thêm bảng + recommendation tối nay.

*Hôm nay bạn chứng minh bằng số. Ngày mai bạn bảo vệ bằng logic.*
