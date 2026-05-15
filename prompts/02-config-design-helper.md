# Prompt 02 — Brainstorm Config Names + Tradeoffs

> **Dùng khi**: Nhóm đang ở `worksheet/02-config-design.md` và bí ý tưởng — không biết nên đặt tên gì cho config, hoặc 3 configs nhóm phác thảo trông giống nhau quá.
>
> **Công cụ gợi ý**: ChatGPT, Claude, Gemini.

---

## Trước khi dán — 3 câu nhóm tự hỏi

1. Nhóm đã có sẵn ý tưởng tổng quát chưa (1 cheap, 1 premium, 1 mix)?
2. Có constraint cụ thể nào không (budget cap, latency cap, quality requirement)?
3. Đã đọc `cost-reference-card.md` mục 3 (3 knobs) chưa?

Nếu chưa có ý tưởng tổng quát → đọc reference card trước. Nếu đã đọc nhưng vẫn bí → dùng prompt này.

---

## Câu lệnh — copy phần dưới

````text
Tôi đang thiết kế chatbot AI cho travel agency Việt Nam phục vụ khách quốc tế.
Tôi cần thiết kế ít nhất 3 configurations khác nhau để so sánh chi phí.

3 knobs tôi có thể tweak:

1. Model tier: Cheap / Mid / Strong / Premium / Mix theo intent
2. Web search: OFF / ON selective (cho 1-2 intent) / ON broad
3. History: Last 3 / Last 5 / Full / Summarize

Tôi đã có 1 config sơ bộ:

- [VD: Cheap model + Web OFF + History Last 3 — đặt tên "Budget Bot"]

Giúp tôi:

1. Brainstorm 4-5 cái tên config có cá tính (không chỉ "Config 1", "Premium" — đặt tên gợi mở persona, ví dụ "Night Owl Mode", "VIP Concierge", "Backpacker Helper").

2. Đề xuất 2 configs khác đủ khác biệt so với "Budget Bot":
   - 1 config extreme premium (model mạnh nhất + tất cả tính năng bật)
   - 1 config "smart" — mix khôn ngoan theo intent (cheap classify, strong cho visa/complaint)

3. Cho mỗi config đề xuất, viết:
   - 3 knobs chốt rõ
   - 1 câu mô tả persona ("config này phục vụ tình huống X")
   - 1 câu trade-off chính ("đánh đổi gì để đạt được gì")
   - 1 rủi ro lớn nhất

4. Cảnh báo tôi nếu 3 configs (Budget + Premium + Smart Mix) trông giống nhau quá — gợi ý cách làm chúng khác biệt rõ hơn.
````

---

## Sau khi AI trả về

Đừng copy nguyên xi. Đọc nhanh, lấy 2-3 ý hay, gắn vào context cụ thể của nhóm. Ví dụ:

- AI gợi ý "VIP Concierge" → nhóm thấy hợp với case travel agency → đổi thành "First-Class Concierge" (gắn với ngành du lịch).
- AI gợi ý mix "Strong cho visa, Cheap cho weather" → nhóm hỏi lại: "Tại sao không phải Cheap cho weather + Strong cho complaint?" — đây là lúc nhóm reasoning.

---

## Mẫu config thường gặp (tham khảo)

Đây là 4 pattern phổ biến — nhóm có thể bắt đầu từ đây, không nhất thiết phải nghĩ lại từ đầu:

| Pattern | Model | Web | History | Persona |
|---|---|---|---|---|
| **Budget / Bare Minimum** | Cheap toàn bộ | OFF | Last 3 | Tiết kiệm tối đa, chấp nhận quality thấp |
| **Premium / Luxury** | Strong toàn bộ | ON broad | Full | Chất lượng tối đa, không sợ cost |
| **Smart Mix** | Cheap classify + Strong respond | ON selective (visa, weather) | Last 5 | Cân bằng — dùng đúng "trí thông minh" cho đúng task |
| **Volume-First** | Mid model | OFF | Last 3 | Tối ưu cho volume cao, chấp nhận quality medium |
| **Quality-First** | Strong + classifier | ON broad | Full | Khách VIP, không có handoff dễ → AI phải làm tốt |
| **Hybrid Selective** | Mid base, escalate Strong nếu confidence thấp | ON selective | Last 5 | Production-realistic — pattern các công ty thật hay dùng |

Nhóm có thể chọn 3 pattern khác nhau từ bảng này, hoặc kết hợp.

---

## Câu hỏi tự kiểm — sau khi có 3 configs

Trước khi đi tính cost (file 03), kiểm 4 điểm:

1. **3 configs có khác nhau ở ≥2 knobs không?** Nếu chỉ khác 1 knob (vd: cùng cheap, cùng web OFF, chỉ history khác) → kết quả so sánh sẽ rất nhỏ, không thấy tradeoff. Quay lại tweak.

2. **Mỗi config có persona / use case rõ không?** Nếu không hình dung được "khi nào dùng config này" → có thể nhóm chọn knobs ngẫu nhiên. Quay lại nghĩ lý do.

3. **Có config nào extreme không?** Ít nhất 1 trong 3 nên là "rẻ tối đa" hoặc "đắt tối đa" — để có baseline so sánh.

4. **Tên config có catchy + memorable không?** Nếu vẫn là "Config A / B / C" — đặt lại tên. Khi present, tên hay sẽ giúp class nhớ.

Nếu cả 4 điểm pass → sẵn sàng đi tính cost.

---

*Prompt 02 · Config design helper. Dùng khi bí ý tưởng — không phải lúc nào cũng cần.*
