# K3 — Ngày 1: Bài Tập & Phản Ánh
## Khám Phá LLM API | Phiếu Thực Hành

**Thời lượng:** 9h00–13h00
**Cách làm:** Trả lời từng câu ngay sau khi hoàn thành block tương ứng —
đừng để dồn hết về cuối buổi. Thay dòng `*Câu trả lời của bạn*` bằng câu
trả lời thật (chấm tự động sẽ đếm số câu đã trả lời).

---

## Block 1 — API Cơ Bản (trả lời sau Checkpoint 1)

### Câu 1.1 — Độ nhạy của temperature
Gọi `call_openai` với temperature 0.0, 0.5, 1.0 và 1.5 dùng prompt
**"Hãy kể cho tôi một sự thật thú vị về Việt Nam."**

**Bạn nhận thấy quy luật gì qua bốn phản hồi?** (2–3 câu)
Khi temperature càng thấp, phản hồi càng ổn định, ngắn gọn và ít biến thể; ở 0.0 model thường chọn cách trả lời an toàn, gần như lặp lại nếu gọi nhiều lần. Khi temperature tăng lên 1.0–1.5, câu trả lời trở nên sáng tạo hơn, có thể thêm ví dụ hoặc diễn đạt khác nhau nhiều hơn nhưng cũng dễ lan man hơn. Tóm lại, temperature càng cao thì độ đa dạng càng tăng, còn tính nhất quán giảm.

### Câu 1.2 — Chọn temperature cho sản phẩm
**Bạn sẽ đặt temperature bao nhiêu cho chatbot hỗ trợ khách hàng, và tại sao?**
Mình sẽ chọn temperature khoảng 0.2–0.4. Mức này giúp chatbot trả lời ổn định, nhất quán và ít “bịa” hơn, rất phù hợp với hỗ trợ khách hàng vì ưu tiên độ chính xác và tính chuyên nghiệp hơn là sự sáng tạo.

### Câu 1.3 — Đánh đổi chi phí
Kịch bản: 10.000 người dùng hoạt động mỗi ngày, mỗi người gọi API 3 lần,
mỗi lần trung bình ~350 token đầu ra.

**Ước tính GPT-4o đắt hơn GPT-4o-mini bao nhiêu lần cho workload này? Nêu một
trường hợp GPT-4o xứng đáng với chi phí và một trường hợp nên dùng mini:**
Với giá output trong bảng, GPT-4o đắt hơn GPT-4o-mini khoảng 16–17 lần cho phần token đầu ra, nên với workload lớn như vậy chênh lệch chi phí là rất đáng kể. GPT-4o xứng đáng khi cần chất lượng suy luận tốt hơn, viết nội dung tinh tế, hoặc xử lý tác vụ quan trọng cần độ tin cậy cao. GPT-4o-mini phù hợp cho chatbot FAQ, tóm tắt đơn giản, hoặc các tác vụ số lượng lớn cần tối ưu chi phí.

---

## Block 2 — System Prompt & Token (trả lời sau Checkpoint 2)

### Câu 2.1 — Sức mạnh của persona
Gọi `chat_with_system_prompt` hai lần với cùng câu hỏi
**"Giải thích blockchain là gì?"** nhưng hai system prompt khác nhau:
- "Bạn là giáo viên tiểu học, giải thích thật đơn giản cho trẻ 8 tuổi."
- "Bạn là chuyên gia tài chính, trả lời chuyên sâu bằng thuật ngữ kỹ thuật."

**Hai phản hồi khác nhau như thế nào (độ dài, từ vựng, ví dụ)? System prompt
ảnh hưởng đến hành vi model ra sao?** (3–4 câu)
Với prompt cho trẻ 8 tuổi, phản hồi thường ngắn hơn, dùng từ đơn giản như “cuốn sổ chung”, “ghi lại thông tin”, và hay kèm ví dụ đời thường. Với prompt chuyên gia tài chính, câu trả lời thường dài hơn, dùng thuật ngữ như “distributed ledger”, “consensus”, “immutability”, và có thể phân tích sâu về ứng dụng. Điều này cho thấy system prompt định hướng mạnh mẽ phong cách, độ chi tiết và mức độ chuyên môn của model. Nói cách khác, system prompt giống như “vai diễn” mà model phải tuân theo.

### Câu 2.2 — tiktoken vs đếm từ
Chọn một đoạn văn tiếng Việt ~100 từ. So sánh số token theo `count_tokens`
(tiktoken) với ước lượng `số từ / 0.75` mà Part 1 đã dùng.

**Hai con số chênh nhau bao nhiêu phần trăm? Vì sao tiếng Việt thường tốn
nhiều token hơn tiếng Anh cùng độ dài?**
Với một đoạn tiếng Việt khoảng 100 từ, ước lượng thô `số từ / 0.75` cho ra khoảng 133 token, còn `count_tokens` thường có thể cao hơn hoặc thấp hơn tùy cách tách token; chênh lệch thực tế thường vào khoảng 10–30%. Tiếng Việt hay tốn nhiều token hơn vì có dấu thanh, nhiều âm tiết viết cách nhau bằng dấu cách nhưng không phải lúc nào cũng là “từ” theo nghĩa ngữ nghĩa, nên bộ mã hóa có thể tách nhỏ hơn so với tiếng Anh. Ngoài ra, tên riêng, dấu câu và ký tự Unicode cũng làm số token tăng lên.

---

## Block 3 — Streaming & Độ Bền (trả lời sau Checkpoint 3)

### Câu 3.1 — Trải nghiệm người dùng với streaming
**Streaming quan trọng nhất trong trường hợp nào, và khi nào thì
non-streaming lại phù hợp hơn?** (1 đoạn văn)
Streaming quan trọng nhất khi người dùng cần cảm giác phản hồi ngay lập tức, như chat hỗ trợ, sinh câu trả lời dài, hoặc ứng dụng realtime vì nó cải thiện trải nghiệm rõ rệt. Non-streaming phù hợp hơn khi đầu ra ngắn, khi cần xử lý toàn bộ kết quả trước rồi mới hiển thị, hoặc khi hệ thống cần đơn giản hóa việc log, kiểm thử và xử lý lỗi. Nói ngắn gọn: streaming tối ưu trải nghiệm, còn non-streaming tối ưu sự đơn giản và tính nhất quán ở các tác vụ nhỏ.

### Câu 3.2 — Vì sao backoff theo cấp số nhân?
**So với delay cố định (ví dụ luôn chờ 1 giây), exponential backoff có lợi
thế gì khi API bị quá tải? Điều gì xảy ra nếu hàng nghìn client cùng retry
với delay cố định giống nhau?**
Exponential backoff giúp giảm áp lực lên API bằng cách tăng dần thời gian chờ sau mỗi lần lỗi, cho hệ thống có thời gian hồi phục và giảm số request dồn vào cùng lúc. Nếu hàng nghìn client cùng retry với delay cố định giống nhau, chúng dễ tạo ra “thundering herd”, tức là tất cả cùng quay lại một thời điểm và làm API quá tải nặng hơn. Backoff ngẫu nhiên hóa nhịp retry tốt hơn và công bằng hơn cho cả client lẫn server.

---

## Block 4 — Mini-Project (trả lời sau Checkpoint 4)

### Câu 4.1 — Thiết kế persona
**Bạn chọn persona gì cho trợ lý của mình? Viết lại system prompt đó và giải
thích 1–2 lựa chọn từ ngữ quan trọng trong prompt (ví dụ: vì sao yêu cầu
"trả lời ngắn gọn", vì sao chỉ định ngôn ngữ...):**
Mình chọn persona là: “Bạn là trợ giảng thân thiện của khóa AI, trả lời ngắn gọn bằng tiếng Việt, ưu tiên chính xác và dễ hiểu.” Từ “trợ giảng thân thiện” giúp giọng điệu gần gũi, còn “trả lời ngắn gọn” để tránh lan man và phù hợp với nhu cầu học tập nhanh. Chỉ định “bằng tiếng Việt” giúp phản hồi đồng nhất, dễ đọc và đúng ngữ cảnh của lớp học.

### Câu 4.2 — Hạn chế & cải thiện
**Trợ lý của bạn hiện có hạn chế lớn nhất là gì (ví dụ: history chỉ 3 lượt,
không có bộ nhớ dài hạn, không kiểm duyệt nội dung...)? Đề xuất một cải
thiện cụ thể và mô tả ngắn cách triển khai:**
Hạn chế lớn nhất là history chỉ giữ 3 lượt gần nhất nên trợ lý dễ quên ngữ cảnh cũ và không duy trì được cuộc hội thoại dài. Một cải thiện cụ thể là thêm bộ nhớ tóm tắt: sau mỗi vài lượt, tóm tắt các ý quan trọng của cuộc trò chuyện và lưu vào một biến hoặc file nhỏ, rồi chèn bản tóm tắt đó vào system/history ở lượt sau. Cách này giúp giảm mất ngữ cảnh mà vẫn kiểm soát được độ dài prompt.

---

## Danh Sách Kiểm Tra Nộp Bài

- [ ] `python grade.py` — xem điểm tự động, mục tiêu ≥ 75/100
- [ ] Cả 4 checkpoint pytest đều pass
- [ ] Tất cả 9 câu trong file này đã được trả lời
- [ ] Đã copy bài làm vào folder `solution/` và zip theo hướng dẫn README
