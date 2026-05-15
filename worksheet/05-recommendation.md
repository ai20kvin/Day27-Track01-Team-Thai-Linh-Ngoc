# 05 · Recommendation + Justification — Kết luận & Chuẩn bị Present

> **Mục tiêu**: Chọn config recommend, justify bằng cost-quality-speed, và chuẩn bị câu trả lời khi bị hỏi khó.

---

## 4 câu hỏi nhóm phải trả lời

### Câu 1 — Recommend config nào?

```text
Nhóm recommend Smart Mix làm config default chạy quanh năm:
Gemini 2.5 Flash cho response, keyword classifier, web search ON selective cho
Visa + Weather, và Last 5 turns history.

Lý do: Smart Mix giữ monthly cost rất thấp ($137.97 ở Scenario A và $688.47 ở
Scenario B), tiết kiệm 96.93% và 96.18% so với human baseline. Quan trọng hơn,
Smart Mix không cắt mất phần accuracy nhạy cảm nhất: Visa và Weather vẫn có web
search real-time. Budget Bot rẻ hơn nhưng web OFF khiến rủi ro sai visa/thời tiết
khó chấp nhận; Premium Concierge quality cao nhất nhưng đắt hơn Smart Mix khoảng
3.1× mà không cần thiết cho phần lớn câu hỏi guide thông thường.
```

### Câu 2 — So với human baseline $0.50/conv → tiết kiệm bao nhiêu?

```text
Smart Mix tiết kiệm:
- Scenario A: $4,500 - $137.97 = $4,362.03/month, tương đương 96.93%
- Scenario B: $18,000 - $688.47 = $17,311.53/month, tương đương 96.18%

Nếu giả định 6 tháng low season và 6 tháng high season:
annual saving ≈ 6×4,362.03 + 6×17,311.53 = $130,041/year.

Không có config nào đắt hơn human. Ngay cả Premium Concierge, config đắt nhất,
vẫn rẻ hơn human 10.6× ở Scenario A và 8.4× ở Scenario B. Nhưng Premium không nên
làm default vì extra cost chủ yếu mua thêm quality cho mọi câu hỏi, kể cả những câu
guide đơn giản mà Gemini Flash đã đủ tốt.
```

### Câu 3 — Khi nào nên upgrade / downgrade config?

```text
Upgrade từ Smart Mix lên Premium Concierge hoặc dùng Sonnet selective khi:
- Khách high-value/VIP hỏi itinerary phức tạp, gia đình, dị ứng, hoặc multi-country trip
- Quality complaint của Guide/Visa vượt 3% trong 2 tuần liên tiếp
- Avg turns/conversation của Guide vượt 6, nghĩa là bot trả lời chưa đủ rõ khiến khách hỏi lại nhiều
- Booking conversion từ chatbot thấp hơn mục tiêu, ví dụ <2%, và transcript cho thấy câu trả lời thiếu thuyết phục

Downgrade về Lean Night Mode khi:
- Traffic thấp, chủ yếu là câu hỏi weather/FAQ ngoài giờ
- Team cần giảm cost nhưng vẫn muốn giữ weather real-time

Không recommend downgrade về Budget Bot làm default, trừ khi team có quy trình cập nhật
Visa RAG rất chặt. Sai visa information là rủi ro business lớn hơn phần cost tiết kiệm.
```

### Câu 4 — Rủi ro lớn nhất của config được chọn?

```text
Rủi ro chính của Smart Mix là keyword classifier có thể miss multi-intent hoặc edge case
Business/Investment từ file 00. Ví dụ khách hỏi "Which city is good for cultural travel
and investment exploration?" thì bot không nên tự tư vấn đầu tư sâu.

Mitigation:
- Thêm rule phát hiện keyword như investor, business environment, entrepreneur, investment
  để route sang handoff specialist.
- Với câu multi-intent, bot trả lời phần du lịch trước và tạo handoff cho phần booking/business.
- Monitor transcript hàng tuần: nếu nhiều câu bị route sai, nâng classifier từ keyword lên
  LLM classifier rẻ như Claude Haiku hoặc Gemini Flash-Lite.

Rủi ro phụ là web search cost/downtime. Mitigation: cache kết quả weather/visa ngắn hạn,
fallback về RAG khi web lỗi, và log rõ khi câu trả lời không có real-time verification.
```

---

## Final answer — Recommendation in 1 paragraph

```text
Nhóm recommend Smart Mix làm cấu hình default cho travel chatbot: Gemini 2.5 Flash
cho response, keyword classifier, web search ON selective cho Visa + Weather, và
Last 5 turns history. Config này chỉ tốn $137.97/month ở low season và $688.47/month
ở high season, tiết kiệm lần lượt 96.93% và 96.18% so với human baseline $0.50 per
conversation. Budget Bot rẻ hơn nhưng không bật web search, nên rủi ro sai thông tin
visa hoặc thời tiết là quá cao cho ngành du lịch. Premium Concierge cho quality cao
nhất nhưng đắt hơn Smart Mix khoảng 3.1×, phù hợp làm upgrade selective cho khách VIP
hoặc itinerary phức tạp hơn là chạy default. Rủi ro lớn nhất của Smart Mix là keyword
classifier miss multi-intent hoặc business/investment edge case; nhóm mitigate bằng
rule handoff specialist và monitor transcript để nâng lên LLM classifier khi cần.
```

---

## Chuẩn bị Present (5 phút)

### Nhịp 0:00–0:30 — Base flow + 3 knobs

Ai trình bày: **Thái**

```text
Chatbot flow có 4 bước: intent classification, route theo intent, context assembly,
và response generation. Ba knobs nhóm tweak là model tier, web search, và history.
Booking/Complaint được handoff, không tốn response generation.
```

### Nhịp 0:30–1:00 — Config overview

Ai trình bày: **Linh**

```text
Budget Bot: GPT-4o-mini, web OFF, Last 3 — rẻ nhất nhưng risk cao.
Premium Concierge: Sonnet, LLM classifier, web Visa+Weather, Full history — quality cao nhất.
Smart Mix: Gemini Flash, keyword classifier, web Visa+Weather, Last 5 — config nhóm recommend.
Lean Night Mode: GPT-4o-mini, web Weather only, Last 3 — fallback ngoài giờ.
```

### Nhịp 1:00–2:00 — Cost comparison

Ai trình bày: **Ngọc**

```text
Budget Bot là rẻ nhất: $13.49/month ở Scenario A và $64.60/month ở Scenario B.
Premium Concierge đắt nhất: $425.44/month và $2,153.36/month, nhưng vẫn rẻ hơn
human 10.6× và 8.4×. Smart Mix nằm giữa: $137.97/month và $688.47/month, tiết kiệm
trên 96% so với human trong cả hai scenarios.
```

### Nhịp 2:00–3:00 — Key insight

Ai trình bày: **Thái**

```text
Insight chính: cheapest không đồng nghĩa best. Web search làm Visa/Weather đắt hơn
Guide nhiều lần, nhưng đó là cost đáng trả vì sai visa hoặc thời tiết có impact thật.
Scenario B không tăng 7× so với Scenario A vì high season có 45% Booking/Complaint
handoff sớm, nên AI-served workload thấp hơn tưởng tượng.
```

### Nhịp 3:00–4:30 — Recommendation + justification

Ai trình bày: **Linh**

```text
Đọc paragraph Final answer ở trên.
```

### Nhịp 4:30–5:00 — Hardest question prep

Ai trình bày: **Ngọc**

**Câu hỏi khó nhất dự đoán**:

```text
Nếu Budget Bot rẻ hơn Smart Mix hơn 10× ở Scenario A, tại sao không chọn Budget Bot?
```

**Câu trả lời chuẩn bị trước**:

```text
Vì Budget Bot rẻ bằng cách tắt web search hoàn toàn. Với chatbot du lịch quốc tế,
sai visa hoặc thời tiết gần ngày đi có thể làm khách mất chuyến, mất niềm tin, hoặc
đánh giá xấu. Smart Mix chỉ tốn $137.97/month ở low season nhưng giữ web real-time
cho Visa/Weather, nên khoản cost thêm là bảo hiểm rủi ro hợp lý.
```

---

## Q&A — 3 câu instructor thường hỏi

```text
1. Knob nào ảnh hưởng cost nhiều nhất?
   → Trong bảng của nhóm, web search ảnh hưởng rất mạnh ở Visa/Weather vì mỗi turn
     thêm $0.008 API call. Model tier là knob lớn thứ hai: cùng Guide 4 turns,
     Premium Sonnet đắt hơn Budget GPT-4o-mini khoảng 21.8×.

2. Nếu provider tăng giá API ×2 thì config còn sống được không?
   → Smart Mix monthly A có thể tăng từ $137.97 lên khoảng $275.94, monthly B từ
     $688.47 lên khoảng $1,376.94 nếu nhân đôi toàn bộ cost. Vẫn rẻ hơn human
     khoảng 16.3× ở A và 13.1× ở B, nên vẫn sống được.

3. Vì sao không chọn Premium nếu vẫn rẻ hơn human?
   → Premium đúng là vẫn rẻ hơn human, nhưng default production cần ROI tốt chứ
     không chỉ "rẻ hơn người". Smart Mix tiết kiệm trên 96% và đủ accuracy cho
     Visa/Weather nhờ web search; Premium nên dùng selective cho VIP hoặc case phức tạp.
```

---

## Bảng kiểm cuối cùng

- [x] Đã trả lời 4 câu PM: Recommend / Savings / Threshold / Risk
- [x] Final answer paragraph gọn và có số cụ thể
- [x] Có outline present 5 phút
- [x] Có câu trả lời cho 3 Q&A dự đoán
- [x] Recommendation khớp với bảng cost mới ở `04-comparison-table.md`
