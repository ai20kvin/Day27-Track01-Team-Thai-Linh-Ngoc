# 00 · User Journey Simulation — Đóng vai Tourist

> **Mục tiêu**: Trước khi tính chi phí, nhóm phải hình dung được khách hàng thật sự hỏi gì, hỏi như thế nào, và 1 conversation thực tế trông ra sao.
>
> **Thời gian**: 8 phút (trong 15 phút phần Setup)

---

## Tại sao phải làm bước này?

Nếu nhóm bắt đầu tính cost mà chưa biết tourist hỏi gì → mọi con số chỉ là lý thuyết. Bước này buộc nhóm "chạm" sản phẩm trước khi mở Excel.

---

## Bước 1 — Mỗi người đóng vai 1 tourist (4 phút)

### Tourist #1 (Tên thành viên: Thái — vai khách Mỹ, đi nhóm 2 người, budget ~$1,200 / 8 ngày)

```text
1. Hi, my girlfriend and I are visiting Vietnam for the first time in late June.
   Is 8 days enough for Hanoi, Ha Long Bay, and Hoi An?
2. I'm from the US. Do I need a visa if I'm staying for 8 days?
3. What is the weather usually like in Hanoi and Hoi An in late June?
   Should we expect heavy rain every day?
4. Can you suggest a relaxed itinerary? We don't want to spend the whole trip
   sitting in buses.
5. Is Ha Long Bay worth doing as a day trip, or should we stay overnight on a cruise?
6. Can your team help book a Ha Long Bay cruise and airport transfers for two people?
7. We are not used to spicy food. Are food tours still a good idea for us?
```

### Tourist #2 (Tên thành viên: Linh — vai khách Hàn, đi nhóm 4 người, budget mid-range)

```text
1. We are a group of 4 from Korea. Do you have group tour packages for Ha Long Bay?
2. How many days do we need in Vietnam to see both Hanoi and Da Nang?
3. What is the weather like in Da Nang in March? Is it good for beach?
4. Can you recommend a good mid-range hotel near Hoan Kiem Lake in Hanoi?
5. Is there a direct flight from Seoul to Da Nang or do we need to connect?
6. Are there any scams we should watch out for as tourists in Vietnam?
7. What local dishes do you recommend that are similar to Korean flavors?
```

### Tourist #3 (Tên thành viên: Ngyễn Thị Ngọc- 2A202600405 — vai người Việt, đi cùng bạn Slovakia, kết hợp du lịch & khảo sát đầu tư)

```text
1. I would like to introduce Vietnam to my friend from Slovakia — which destinations
   are best for an authentic cultural journey?
2. Which places best represent Vietnam's history, traditional craft villages,
   and local lifestyles?
3. Which cities combine strong cultural identity with dynamic economic development
   for investment exploration?
4. Are there any cultural experiences, traditional festivals, or local activities
   that we should not miss?
5. Which regions are especially known for traditional cuisine and the hospitality
   of local people?
6. Which cities are currently the most attractive for foreign investors
   and entrepreneurs?
7. Could you suggest an itinerary that combines cultural exploration, local
   experiences, and insights into Vietnam's business environment?
```

---

## Bước 2 — Gom lại và phân loại (4 phút)

Thảo luận trước khi điền bảng:
- Câu hỏi nào lặp lại? → Ha Long Bay xuất hiện ở Tourist #1 và #2; thời tiết ở #1 và #2.
- Chủ đề nào bất ngờ? → Tourist #3 hỏi về đầu tư — intent không nằm trong 5 intent mặc định → cần handoff chuyên gia.
- Câu nào chatbot trả lời được? Câu nào phải chuyển người?

| # | Câu hỏi (1 dòng) | Intent | Lượt chat | Bot hay Người? |
|---|---|---|---|---|
| 1 | 8 ngày có đủ cho Hanoi–Ha Long–Hoi An không? | Điểm đến/Guide | 3 | ☑ Bot |
| 2 | US passport — có cần visa 8 ngày không? | Visa/Policy | 2 | ☑ Bot |
| 3 | Thời tiết Hanoi & Hoi An cuối tháng 6? | Thời tiết/Sự kiện | 2 | ☑ Bot |
| 4 | Gợi ý lịch trình thư giãn, ít xe buýt? | Điểm đến/Guide | 4 | ☑ Bot |
| 5 | Ha Long — day trip hay overnight cruise? | Điểm đến/Guide | 3 | ☑ Bot |
| 6 | Đặt cruise Ha Long + airport transfer cho 2 người? | Tour/Booking | 1 | ☑ Người (sales) |
| 7 | Food tour có phù hợp người không ăn được cay không? | Điểm đến/Guide | 2 | ☑ Bot |
| 8 | Group tour Ha Long cho 4 người Hàn? | Tour/Booking | 1 | ☑ Người (sales) |
| 9 | Thời tiết Da Nang tháng 3 — có tốt cho biển không? | Thời tiết/Sự kiện | 2 | ☑ Bot |
| 10 | Điểm đến tốt nhất cho hành trình văn hoá authentic? | Điểm đến/Guide | 3 | ☑ Bot |
| 11 | Thành phố nào kết hợp văn hoá + môi trường đầu tư? | Tour/Booking* | 1 | ☑ Người (specialist) |
| 12 | Lịch trình kết hợp văn hoá + business insights? | Điểm đến/Guide | 5 | ☑ Bot (phần guide) + Người (phần invest) |

*Intent đầu tư không có trong 5 intent mặc định → route về Booking/Handoff gần nhất.

---

## Bước 3 — Rút insight cho nhóm

**Tổng số câu hỏi nhóm gom được**:

```text
21 câu hỏi (7 per tourist × 3)
```

**Phân bố intent thực tế của nhóm** (% mỗi intent):

```text
Guide (địa điểm, lịch trình, ẩm thực, tips):  52%  (11/21)
Visa/Policy:                                    10%   (2/21)
Thời tiết/Sự kiện:                             14%   (3/21)
Tour/Booking (đặt dịch vụ, handoff):           19%   (4/21)
Khiếu nại:                                      0%   (0/21)
Không phân loại được (đầu tư/business):         5%   (1/21)
```

**Số lượt chat trung bình để xong 1 chủ đề**:

```text
Guide (lịch trình, gợi ý): 3–4 lượt
  → Tourist hay hỏi follow-up: "còn gì nữa không?", "hợp với người không ăn cay không?",
    "nên đi mấy ngày ở đó?"
Visa: 2–3 lượt
  → Cần clarify passport type, duration of stay, e-visa vs on-arrival, phí hiện tại
Thời tiết: 2 lượt
  → 1 hỏi, 1 trả lời; thỉnh thoảng 1 follow-up ("có nên mang áo mưa không?")
Booking: 1 lượt rồi handoff ngay sang sales
Khiếu nại: không xuất hiện trong nhóm này
```

**Đối chiếu với đề bài** (Scenario A = 4 lượt, Scenario B = 7 lượt):

```text
Hợp lý vì: Guide chiếm 52% intent — và câu hỏi Guide của nhóm đều cần 3–5 lượt
(ví dụ Tourist #1 hỏi lịch trình 8 ngày → cần clarify phương tiện di chuyển,
mức độ hoạt động, ưu tiên thiên nhiên hay văn hoá → dễ đạt 4–5 lượt).
Tourist #3 có combo Guide + Business rất phức tạp → dễ đạt 6–7 lượt ở Scenario B
khi khách hỏi kỹ trước khi đặt tour hoặc lên kế hoạch chuyến đi dài ngày.
```

**Insight bất ngờ — điều gì nhóm chỉ hiểu sau khi đóng vai?**

```text
1. Cùng 1 điểm đến (Ha Long Bay) xuất hiện ở 2 tourist với intent khác nhau hoàn toàn:
   - Tourist #1 hỏi "nên đi day trip hay overnight?" → Guide (bot trả lời được)
   - Tourist #2 hỏi "có group tour không?" → Booking (handoff sales ngay)
   → Chatbot phải phân loại đúng sub-intent trong cùng chủ đề địa lý, không chỉ
     nhận ra keyword "Ha Long Bay".

2. Tourist #3 tạo ra intent lai (du lịch + đầu tư) mà 5 intent mặc định không cover.
   Cần quyết định: route về Guide (trả lời phần văn hoá) + Handoff (phần business)?
   Hay thêm intent thứ 6 "Investment/Business"? Đây là edge case quan trọng cần
   define rõ trong thiết kế sản phẩm thực tế.

3. Tourist thường bundle nhiều intent trong 1 câu mở đầu — câu 1 của Tourist #1
   chứa implicit Guide (lịch trình) + implicit Visa (US citizen) + implicit Weather
   (late June) cùng lúc → classifier phải detect primary intent trước, không phải
   cố gắng trả lời tất cả cùng 1 lúc.
```

---

## Bảng kiểm trước khi sang file tiếp theo

- [x] Mỗi người trong nhóm đã viết ≥5 câu hỏi tourist
- [x] Đã gom + phân loại intent cho ≥10 câu (bảng trên)
- [x] Đã có phân bố intent % của nhóm (so với đề bài)
- [x] Có ít nhất 1 insight về cách tourist thật sự dùng chatbot

Xong → mở `01-base-flow.md`.
