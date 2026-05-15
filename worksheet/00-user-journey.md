# 00 · User Journey Simulation — Đóng vai Tourist

> **Mục tiêu**: Trước khi tính chi phí, nhóm phải hình dung được khách hàng thật sự hỏi gì, hỏi như thế nào, và 1 conversation thực tế trông ra sao.
>
> **Thời gian**: 8 phút (trong 15 phút phần Setup)

---

## Tại sao phải làm bước này?

Nếu nhóm bắt đầu tính cost mà chưa biết tourist hỏi gì → mọi con số chỉ là lý thuyết. Bước này buộc nhóm "chạm" sản phẩm trước khi mở Excel.

---

## Bước 1 — Mỗi người đóng vai 1 tourist (4 phút)

Tưởng tượng mình là 1 khách du lịch nước ngoài đang plan trip Việt Nam. Bạn vừa mở website công ty du lịch, thấy có chatbot ở góc màn hình. Bạn sẽ hỏi gì?

Trước khi viết, tự hỏi:

- Mình từ đâu đến? Mỹ, Anh, Hàn, Nhật, Úc?
- Đi 1 mình hay đi nhóm? Budget khoảng bao nhiêu?
- Đã biết gì về Việt Nam? Lần đầu đến hay đã đến rồi?
- Mình lo lắng điều gì nhất? (visa, an toàn, ngôn ngữ, thời tiết, ẩm thực, lừa đảo...)

Viết **5–7 câu hỏi bằng tiếng Anh** mình sẽ thật sự gửi cho chatbot. Viết câu hỏi tự nhiên, đúng giọng tourist — không phải đặt câu hỏi "nghe có vẻ technical".

→ Mỗi người viết vào ô dưới (chưa có gì sẵn — đừng nhìn người bên cạnh):

### Tourist #1 (Tên thành viên: Thái)

```text
1. Hi there, do US citizens need a visa for a 14-day trip to Vietnam?
2. What's the weather typically like in Hanoi and Da Nang around late November?
3. We are a family of four. Can you recommend a kid-friendly itinerary for 2 weeks?
4. Are credit cards widely accepted, or do we need to bring a lot of cash?
5. We'd love to book a 2-day cruise in Ha Long Bay, what are our options?
6. Is it safe to drink tap water in the hotels there?
```

### Tourist #2 (Tên thành viên: Linh)

```text
1. Hello! I'm planning to visit Sapa this December. Is there a direct train from Hanoi?
2. What are some must-try local street foods in the Hanoi Old Quarter?
3. How much does a typical 3-star hotel cost per night in Da Nang?
4. I've heard about Grab. Is it safe for a solo female traveler to use Grab bike at night?
5. I want to complain about a tour guide I booked last time, how do I do that?
6. Where can I buy a cheap SIM card with good 4G data at Noi Bai airport?
```

### Tourist #3 (Tên thành viên: Ngọc)

```text
1. Hey! My partner and I want to do the Ha Giang loop. Should we rent motorbikes or hire easy riders?
2. Do we need an international driving permit to ride a motorbike legally in Vietnam?
3. What is the best month to visit the Phong Nha caves without worrying about floods?
4. Can you help me book a domestic flight from Da Nang to Ho Chi Minh City?
5. Are there many vegetarian options for traditional food like Pho?
6. We want to extend our tourist visa for another 15 days, is that possible?
```

---

## Bước 2 — Gom lại và phân loại (4 phút)

Cả nhóm chụm vào, gom tất cả câu hỏi lại. Trước khi điền bảng, thảo luận 1 phút:

- Có câu hỏi nào lặp lại giữa các tourist không?
- Có chủ đề nào không ai trong nhóm nghĩ tới ban đầu nhưng quan trọng?
- Câu nào chatbot có thể trả lời được? Câu nào cần chuyển sang nhân viên thật?

5 intent có sẵn (tham khảo `cost-reference-card.md` mục 2):

- **Visa/Policy** — chính sách, thủ tục nhập cảnh
- **Điểm đến/Guide** — gợi ý đi đâu, làm gì, ăn gì
- **Thời tiết/Sự kiện** — info real-time
- **Tour/Booking** — đặt vé, đặt tour, đặt phòng → chuyển sales
- **Khiếu nại** — phàn nàn → chuyển manager

Sau khi gom, điền bảng phân loại:

| # | Câu hỏi (1 dòng) | Intent thuộc loại nào | Cần bao nhiêu lượt chat để xong? | Bot trả lời hay chuyển người? |
|---|---|---|---|---|
| 1 | Do US citizens need a visa for a 14-day trip to Vietnam? | Visa/Policy | 1-2 lượt | ☑ Bot · □ Người |
| 2 | What's the weather typically like in Hanoi and Da Nang around late November? | Thời tiết/Sự kiện | 1-2 lượt | ☑ Bot · □ Người |
| 3 | Can you recommend a kid-friendly itinerary for 2 weeks? | Điểm đến/Guide | 3-4 lượt | ☑ Bot · □ Người |
| 4 | We'd love to book a 2-day cruise in Ha Long Bay, what are our options? | Tour/Booking | 4-5 lượt | □ Bot · ☑ Người |
| 5 | I'm planning to visit Sapa this December. Is there a direct train from Hanoi? | Điểm đến/Guide | 2-3 lượt | ☑ Bot · □ Người |
| 6 | I want to complain about a tour guide I booked last time, how do I do that? | Khiếu nại | 1-2 lượt | □ Bot · ☑ Người |
| 7 | Should we rent motorbikes or hire easy riders for the Ha Giang loop? | Điểm đến/Guide | 2-3 lượt | ☑ Bot · □ Người |
| 8 | Do we need an international driving permit to ride a motorbike legally in Vietnam? | Visa/Policy | 1-2 lượt | ☑ Bot · □ Người |
| 9 | Can you help me book a domestic flight from Da Nang to Ho Chi Minh City? | Tour/Booking | 3-4 lượt | □ Bot · ☑ Người |
| 10 | We want to extend our tourist visa for another 15 days, is that possible? | Visa/Policy | 2-3 lượt | ☑ Bot · □ Người |

---

## Bước 3 — Rút insight cho nhóm (cuối phần Setup)

Trả lời nhanh 4 câu — sẽ dùng lại ở các bước sau:

**Tổng số câu hỏi nhóm gom được**:

```text
18 câu hỏi
```

**Phân bố intent thực tế của nhóm** (% mỗi intent):

```text
Guide: 45% (8/18)
Visa: 22% (4/18)
Weather: 11% (2/18)
Booking: 17% (3/18)
Khiếu nại: 5% (1/18)
```

**Số lượt chat trung bình để xong 1 chủ đề**:

```text
2-3 lượt cho info đơn giản (Visa, Thời tiết, Phương tiện đi lại), 4-5 lượt cho tư vấn lộ trình và đặt dịch vụ (Booking/Guide).
```

**Đối chiếu với đề bài** (Scenario A = 4 lượt, Scenario B = 7 lượt):

```text
Hợp lý vì một khách hàng thực tế hiếm khi chỉ hỏi đúng 1 câu rồi đi, họ thường kết hợp nhiều intent trong một phiên chat (ví dụ: hỏi thời tiết -> hỏi địa điểm -> nhờ đặt xe). Con số 4-7 lượt phản ánh đúng thực tế của một conversation đa chủ đề.
```

**Insight bất ngờ — điều gì nhóm chỉ hiểu sau khi đóng vai?**

```text
Khách du lịch thường có xu hướng gộp nhiều intent vào cùng một tin nhắn ban đầu (vd "Tôi muốn đi Hà Nội vào tháng 12, thời tiết thế nào và có tour gì không?"). Điều này đòi hỏi chatbot phải xử lý đa ý định tốt chứ không chỉ trả lời theo từng intent đơn lẻ. Đồng thời, những câu hỏi mang lại doanh thu (Booking) thường yêu cầu ngữ cảnh và trao đổi rất dài trước khi khách chốt.
```

---

## Bảng kiểm trước khi sang file tiếp theo

- [x] Mỗi người trong nhóm đã viết ≥5 câu hỏi tourist
- [x] Đã gom + phân loại intent cho ≥10 câu (bảng trên)
- [x] Đã có phân bố intent % của nhóm (so với đề bài)
- [x] Có ít nhất 1 insight về cách tourist thật sự dùng chatbot

Xong → mở `01-base-flow.md`.
