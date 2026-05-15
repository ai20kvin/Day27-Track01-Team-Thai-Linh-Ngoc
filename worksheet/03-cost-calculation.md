# 05 · Recommendation + Justification — Kết luận & Chuẩn bị Present

> **Mục tiêu**: Chọn 1 config (hoặc combo) nhóm recommend deploy, viết justification ngắn gọn, và chuẩn bị 5 phút present.

---

## 4 câu hỏi nhóm phải trả lời

### Câu 1 — Recommend config nào?

```text
Nhóm recommend Smart Mix (Config 3 — Claude Haiku 4.5, Web ON selective Visa+Weather,
Last 5 turns) làm config chạy quanh năm. Đây là lựa chọn duy nhất trong 3 configs
duy trì savings >95% ở CẢ HAI scenarios mà không hy sinh quality quá nhiều:
monthly $147 (A) và $740 (B) so với human $4,500/$18,000.

Không recommend Budget Bot vì web OFF → Visa info có thể stale → rủi ro sai thông tin
chính sách nhập cảnh, hậu quả business lớn. Không recommend Premium làm default vì
Scenario A chỉ tiết kiệm 15.6% so với human — gần như không có ROI ở mùa thấp điểm.

Khuyến nghị thêm: khi volume vượt 800 conv/ngày liên tiếp (peak season) có thể
upgrade response model sang Sonnet 4.6 cho Visa/Guide intent, giữ nguyên web + history.
```

### Câu 2 — So với human baseline → tiết kiệm bao nhiêu?

```text
Smart Mix tiết kiệm 96.7% ở Scenario A ($4,353/tháng) và 95.9% ở Scenario B
($17,260/tháng). Tổng tiết kiệm trong 1 năm nếu 6 tháng thấp điểm + 6 tháng cao điểm:
≈ (4,353×6) + (17,260×6) = $26,118 + $103,560 = ~$129,678/năm.

Không có config nào đắt hơn human, nhưng Premium Concierge Scenario A chỉ tiết kiệm
15.6% ($703/tháng) — gần như không có ROI khi tính thêm overhead vận hành AI hệ thống.
AI vẫn justify ở Premium vì: phục vụ 24/7 (human agent = 8h/ngày), đa ngôn ngữ tức thì,
và consistency — không trả lời sai vì mệt mỏi.
```

### Câu 3 — Khi nào nên upgrade / downgrade config?

```text
Upgrade lên Premium (hoặc Smart Mix + Sonnet) khi:
- Monthly conversation vượt 40,000/tháng (mùa cao điểm) và quality complaint > 3%
- Booking conversion rate từ chatbot thấp (<2%) → có thể do quality trả lời kém
- Approaching peak season (trước Tết 4–6 tuần, trước mùa hè)
- Có feedback khách hàng cụ thể về sai thông tin visa hoặc itinerary

Downgrade về Budget Bot khi:
- Monthly conv < 3,000 (mùa thấp điểm sâu, Jan sau Tết)
- Budget bị siết tạm thời, chấp nhận quality thấp hơn
- Team cam kết cập nhật RAG visa ít nhất 2 lần/tuần để bù đắp web OFF

Default: giữ Smart Mix quanh năm, không cần đổi config theo mùa.
```

### Câu 4 — Rủi ro lớn nhất của config được chọn (Smart Mix)?

```text
Rủi ro chính: Haiku 4.5 có thể cho câu trả lời thiếu chi tiết với câu hỏi phức tạp
(ví dụ: lập itinerary 14 ngày multi-city với yêu cầu cụ thể) → tourist phải hỏi thêm
nhiều lượt → tăng turns/conv → tăng cost ngoài dự kiến.
Mitigation: monitor avg turns/conv hàng tuần; nếu avg vượt 6 turns cho Guide intent
→ xem xét upgrade model hoặc improve RAG chunks.

Rủi ro phụ: Provider (Anthropic) tăng giá Haiku → margin co lại.
Mitigation: có sẵn fallback sang GPT-4o-mini (giảm cost ×10) hoặc Gemini Flash
($0.075/$0.30 per 1M) — đổi model mà không cần thay đổi architecture.

Rủi ro phụ 2: Web search Tavily tăng giá hoặc downtime → Visa/Weather turn fail.
Mitigation: fallback về RAG khi web search error, alert team update RAG ngay.
```

---

## Final answer — Recommendation in 1 paragraph

```text
Nhóm recommend triển khai Smart Mix (Claude Haiku 4.5, web search ON cho Visa và
Weather, Last 5 turns history) làm cấu hình duy nhất chạy quanh năm. Đây là config
duy nhất đạt savings >95% ở cả mùa thấp điểm lẫn cao điểm — tiết kiệm khoảng $4,353
và $17,260 mỗi tháng so với baseline nhân viên $0.50/conversation — trong khi vẫn đảm
bảo accuracy cho 2 intent nhạy cảm nhất (Visa và Weather) nhờ web search real-time.
Budget Bot rẻ hơn nhưng rủi ro sai thông tin visa là không chấp nhận được trong ngành
du lịch; Premium Concierge gần bằng cost nhân viên ở mùa thấp điểm, không có ROI.
Khi volume vượt 800 conv/ngày liên tiếp trong peak season, nhóm khuyến nghị nâng model
lên Sonnet 4.6 chỉ cho intent Guide và Visa để cải thiện quality, giữ nguyên web
strategy và history — tổng cost tăng khoảng 2.5× nhưng vẫn rẻ hơn human 10× ở
Scenario B. Rủi ro lớn nhất là provider tăng giá API; mitigation đã có sẵn fallback
sang GPT-4o-mini hoặc Gemini Flash mà không cần thay đổi kiến trúc hệ thống.
```

---

## Chuẩn bị Present (5 phút)

### Nhịp 0:00 – 0:30 — Base flow + 3 knobs

Ai trình bày: **Thành viên A**

```text
"Chatbot của chúng tôi có 4 bước: phân loại intent → route → assemble context →
generate response. 3 knobs nhóm tweak là model tier, web search, và history length.
Booking và Complaint handoff ngay sang người — $0 LLM cost."
```

### Nhịp 0:30 – 1:00 — Config overview

Ai trình bày: **Thành viên B**

```text
Config 1 — Budget Bot: GPT-4o-mini, web OFF, Last 3 turns. Rẻ tuyệt đối.
Config 2 — Premium Concierge: Sonnet 4.6, web ON selective, Full history. Quality cao nhất.
Config 3 — Smart Mix: Haiku 4.5, web ON selective (Visa+Weather), Last 5 turns.
          → Đây là config nhóm recommend.
```

### Nhịp 1:00 – 2:00 — Cost comparison

Ai trình bày: **Thành viên C**

```text
"Chiếu bảng so sánh. Budget Bot: $140/tháng (A), $71/tháng (B) — rẻ 32× và 254× so
với human, nhưng web OFF. Premium: $3,797 (A) và $1,926 (B) — ở Scenario A chỉ tiết
kiệm 15.6% so với human agent. Smart Mix: $147 (A) và $740 (B) — tiết kiệm ~97% cả
hai scenarios. Điểm thú vị: Scenario B thường RẺ HƠN Scenario A tuyệt đối vì 45%
conversation là handoff = $0 LLM."
```

### Nhịp 2:00 – 3:00 — Key insight

Ai trình bày: **Thành viên A**

```text
"Knob ảnh hưởng cost nhiều nhất là model tier — Budget Bot vs Smart Mix chênh 10×,
Smart Mix vs Premium chênh 2.6×. Web search ON selective chỉ thêm ~$6/tháng ở A
nhưng giúp Visa accuracy tăng đáng kể. History Last 5 vs Full ảnh hưởng ít nhất —
chênh <8% cost/conv. Insight bất ngờ: Premium gần bằng human cost ở mùa thấp điểm,
nhưng ở mùa cao điểm AI thắng hơn 9× vì handoff ratio cao."
```

### Nhịp 3:00 – 4:30 — Recommendation + justification

Ai trình bày: **Thành viên B** (người mạnh nhất trong nhóm)

```text
[Đọc paragraph Final answer ở trên]
```

### Nhịp 4:30 – 5:00 — Hardest question prep

Ai trình bày: **Thành viên C**

Câu hỏi dự đoán khó nhất:
```text
"Tại sao không dùng Budget Bot — nó rẻ hơn Smart Mix 5× mà vẫn tiết kiệm 96% so
với human? Smart Mix có đáng hơn không?"
```

Câu trả lời:
```text
"Budget Bot web OFF có nghĩa là thông tin Visa lấy từ RAG — có thể stale nếu
policy thay đổi. Trong du lịch quốc tế, sai visa info có thể khiến khách bị từ
chối nhập cảnh hoặc công ty bị kiện — rủi ro business cao hơn nhiều so với khoản
tiết kiệm $7/tháng (Smart Mix $147 vs Budget Bot $140 ở Scenario A). Đây là
quyết định risk management, không phải chỉ cost optimization."
```

---

## Q&A — 3 câu instructor thường hỏi

```text
1. "Knob nào ảnh hưởng cost nhiều nhất?"
   → Model tier: chênh 10–27× giữa cheap và premium. Web search và history ảnh
     hưởng thứ 2 và 3, nhưng nhỏ hơn nhiều so với model tier.

2. "Nếu provider tăng giá API ×2 — config còn sống được không?"
   → Smart Mix: monthly A tăng từ $147 → $294, monthly B từ $740 → $1,480.
     Vẫn rẻ hơn human 15× (A) và 12× (B) → vẫn có ROI tốt. Nếu Anthropic tăng
     giá, có thể switch sang Gemini Flash ($0.075/$0.30 per 1M) mà không đổi
     architecture — fallback sẵn sàng.

3. "So với nhóm X — tại sao nhóm bạn chọn khác?"
   → [Phụ thuộc nhóm X present gì — nếu họ chọn Premium: nhóm mình ưu tiên
     consistency savings qua cả 2 scenarios thay vì quality tối đa. Nếu họ
     chọn Budget: nhóm mình ưu tiên accuracy Visa vì rủi ro business cao hơn
     khoản tiết kiệm nhỏ.]
```

---

## Bảng kiểm cuối cùng

- [x] Đã trả lời 4 câu PM (Recommend / Savings / Threshold / Risk)
- [x] Final answer paragraph viết gọn (7 câu)
- [x] Phân công 5 nhịp present cho các thành viên
- [x] Có sẵn câu trả lời cho 3 câu Q&A dự đoán
- [x] Comparison table có sẵn để chiếu khi present
- [ ] Repo đã commit + push (thực hiện sau buổi học)

---

## Sau buổi học

1. Commit tất cả file worksheet đã điền vào repo.
2. Push lên GitHub.
3. Dán link repo vào Discord `#day27-evidence-boards` trước 23:59.
4. Chuẩn bị D28 peer review: polish bảng so sánh, sẵn sàng câu chất vấn khó hơn.

*Hôm nay bạn chứng minh bằng số. Ngày mai bạn bảo vệ bằng logic.*