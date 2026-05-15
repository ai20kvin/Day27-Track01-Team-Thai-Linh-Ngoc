# 00 · User Journey Simulation — Đóng vai Tourist

> **Mục tiêu**: Trước khi tính chi phí, nhóm phải hình dung được khách hàng thật sự hỏi gì, hỏi như thế nào, và 1 conversation thực tế trông ra sao.
>
> **Thời gian**: 8 phút (trong 15 phút phần Setup)

---

## Tại sao phải làm bước này?

Nếu nhóm bắt đầu tính cost mà chưa biết tourist hỏi gì → mọi con số chỉ là lý thuyết. Bước này buộc nhóm "chạm" sản phẩm trước khi mở Excel.

---

## Bước 1 — Mỗi người đóng vai 1 tourist (4 phút)
### Tourist #1 (Tên thành viên: Thái — vai khách Mỹ, đi nhóm 2 người , about 1,200 for 8 days)
```text
1. Hi, my girlfriend and I are visiting Vietnam for the first time in late June. Is 8 days enough for Hanoi, Ha Long Bay, and Hoi An?
2. I'm from the US. Do I need a visa if I'm staying for 8 days?
3. What is the weather usually like in Hanoi and Hoi An in late June? Should we expect heavy rain every day?
4. Can you suggest a relaxed itinerary? We don't want to spend the whole trip sitting in buses.
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


---

## Bước 2 — Gom lại và phân loại (4 phút)

| # | Câu hỏi (1 dòng) | Intent | Lượt chat | Bot hay Người? |
|---|---|---|---|---|
| 1 | US passport — visa on arrival? | Visa/Policy | 2 | ☑ Bot |
| 2 | Best time to visit Hanoi in November? | Điểm đến/Guide | 2 | ☑ Bot |
| 3 | Is solo travel safe in Vietnam? | Điểm đến/Guide | 2 | ☑ Bot |
| 4 | Top 3 things in Ho Chi Minh City? | Điểm đến/Guide | 3 | ☑ Bot |
| 5 | Group tour package for Ha Long Bay? | Tour/Booking | 1 | ☑ Người (sales) |
| 6 | Weather in Da Nang in March? | Thời tiết/Sự kiện | 2 | ☑ Bot |
| 7 | Hotel near Hoan Kiem Lake — mid-range? | Tour/Booking | 1 | ☑ Người (sales) |
| 8 | Tourist scams to watch out for? | Điểm đến/Guide | 2 | ☑ Bot |
| 9 | Current visa-on-arrival fee for Australians? | Visa/Policy | 3 | ☑ Bot |
| 10 | Book private driver Hue → Hoi An? | Tour/Booking | 1 | ☑ Người (sales) |

---

## Bước 3 — Rút insight cho nhóm

**Tổng số câu hỏi nhóm gom được**:

```text
21 câu hỏi (7 per tourist × 3)
```

**Phân bố intent thực tế của nhóm** (% mỗi intent):

```text
Guide:     48% (10/21)
Visa:      19%  (4/21)
Weather:   10%  (2/21)
Booking:   19%  (4/21)
Khiếu nại:  5%  (1/21)
```

**Số lượt chat trung bình để xong 1 chủ đề**:

```text
Guide: 2–3 lượt (tourist hay hỏi follow-up "còn địa điểm nào nữa không?")
Visa: 3–4 lượt (cần clarify passport type, duration of stay, e-visa vs on-arrival)
Weather: 2 lượt
Booking: 1 lượt rồi handoff ngay sang sales
Khiếu nại: 1–2 lượt rồi handoff manager
```

**Đối chiếu với đề bài** (Scenario A = 4 lượt, Scenario B = 7 lượt):

```text
Hợp lý vì: Guide + Visa chiếm ~67% intent — cả hai đều cần 2–4 lượt.
Khi tourist hỏi nhiều intent trong 1 conversation (ví dụ: hỏi visa rồi hỏi thêm
thời tiết) → tổng lượt dễ đạt 6–7, đặc biệt mùa cao điểm khi khách hỏi kỹ hơn
trước khi ra quyết định đặt tour.
```

**Insight bất ngờ — điều gì nhóm chỉ hiểu sau khi đóng vai?**

```text
1. Tourist thường bundle nhiều intent trong 1 conversation — ví dụ hỏi visa rồi
   ngay lập tức hỏi "ok vậy tháng mấy nên đi?" → 1 conv chạm 3 intent khác nhau.
2. Câu hỏi Visa phức tạp hơn tưởng: passport type → duration → e-visa hay on-arrival
   → phí hiện tại → cần 3–5 lượt mới đủ thông tin, không phải 1 câu hỏi 1 câu trả lời.
3. Booking thường ngắn gọn và rõ ý — tourist hỏi xong muốn chuyển agent ngay.
```

---

## Bảng kiểm

- [x] Mỗi người trong nhóm đã viết ≥5 câu hỏi tourist
- [x] Đã gom + phân loại intent cho ≥10 câu (bảng trên)
- [x] Đã có phân bố intent % của nhóm (so với đề bài)
- [x] Có ít nhất 1 insight về cách tourist thật sự dùng chatbot
