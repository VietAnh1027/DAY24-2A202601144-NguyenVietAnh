# K4 — Ngày 1: Bài Tập & Phản Ánh
## Khám Phá LLM API | Phiếu Thực Hành

**Thời lượng:** 14h00–18h00
**Cách làm:** Trả lời từng câu ngay sau khi hoàn thành block tương ứng —
đừng để dồn hết về cuối buổi. Thay dòng `*Câu trả lời của bạn*` bằng câu
trả lời thật (chấm tự động sẽ đếm số câu đã trả lời).

---

## Block 1 — API Cơ Bản (trả lời sau Checkpoint 1)

### Câu 1.1 — Độ nhạy của temperature
Gọi `call_openai` với temperature 0.0, 0.7, 1.2 và 1.8 dùng prompt
**"Hãy kể cho tôi một sự thật thú vị về Hà Nội."**

**Bạn nhận thấy quy luật gì qua bốn phản hồi? Ở mức nào phản hồi bắt đầu
kém mạch lạc?** (2–3 câu)
> Temperature càng cao thì mô hình có xu hướng phản hồi đa dạng và hoa mỹ hơn

### Câu 1.2 — Chọn temperature cho sản phẩm
**Bạn sẽ đặt temperature bao nhiêu cho trợ lý soạn thảo hợp đồng pháp lý,
và bao nhiêu cho trợ lý viết slogan quảng cáo? Giải thích khác biệt.**
> Em sẽ đặt temperature thấp (0.0, 0.7) cho trợ lý soạn thảo hợp đồng pháp lý vì cần logic chính xác, đúng trọng tâm. Đặt temperature cao (1.2, 1.8) cho trợ lý viết slogan quảng cáo vì cần sự sáng tạo

### Câu 1.3 — Đánh đổi chi phí
Kịch bản: 20.000 người dùng hoạt động mỗi ngày, mỗi người gọi API 2 lần,
mỗi lần trung bình ~500 token đầu ra.

**Ước tính chi phí mỗi ngày của model lớn so với model nhỏ cho workload này
(dựa trên bảng giá trong template). Nêu một trường hợp model lớn xứng đáng
với chi phí và một trường hợp model nhỏ là lựa chọn đúng:**
> Chi phí token đầu ra mỗi ngày của model lớn là 20.000 * 2 * 500 / 1000 * 0.010 = $200 / ngày. Chi phí token đầu ra mỗi ngày của model nhỏ là 20.000 * 2 * 500 / 1000 * 0.0006 = $12 / ngày. Model lớn có thể sử dụng để phân tích hợp đồng pháp lý, còn model nhỏ thực hiện các tác vụ như hỏi đáp hàng ngày 

---

## Block 2 — System Prompt & Token (trả lời sau Checkpoint 2)

### Câu 2.1 — Sức mạnh của persona
Gọi `chat_with_system_prompt` hai lần với cùng câu hỏi
**"Giải thích máy học (machine learning) là gì?"** nhưng hai system prompt
khác nhau:
- "Bạn là một nhà thơ, trả lời mọi thứ bằng hình ảnh ví von, tránh thuật ngữ."
- "Bạn là kỹ sư phần mềm senior, trả lời chính xác, có ví dụ code khi phù hợp."

**Hai phản hồi khác nhau như thế nào (giọng văn, độ dài, mức kỹ thuật)?
Từ đó rút ra system prompt điều khiển được những khía cạnh nào của phản hồi?**
(3–4 câu)
> Giọng văn của phản hồi đầu tiên mang hơi hướng sáng tạo và có chút hài hước, dễ đọc nhưng không mang tính học thuật và không tạo cảm giác chuyên nghiệp. Phản hồi thứ 2 có hơi khô khan nhưng tính thuyết phục cao và chuyên nghiệp.

### Câu 2.2 — tiktoken vs đếm từ
Chọn một đoạn văn tiếng Việt ~150 từ. So sánh số token theo `count_tokens`
(tiktoken) với ước lượng `số từ / 0.75` mà Part 1 đã dùng.

**Hai con số chênh nhau bao nhiêu phần trăm? Nếu dùng ước lượng thô để dự
toán ngân sách API cho ứng dụng tiếng Việt, bạn sẽ dự toán thiếu hay thừa —
và vì sao?**
> Con số thực tế cao hơn 25-75% so với ước lượng thô. Nếu sử dụng ước lượng thô để dự toán ngân sách API cho ứng dụng tiếng việt, em sẽ dự toán thiếu, vì tiếng việt có nhiều kí tự đặc biệt nên sẽ tốn token hơn trong khi 1 token = 0.75 từ là quy chuẩn do openai đặt ra cho tiếng anh.

---

## Block 3 — Streaming & Độ Bền (trả lời sau Checkpoint 3)

### Câu 3.1 — Trải nghiệm người dùng với streaming
**Xét ba ứng dụng: (a) chatbot văn bản, (b) trợ lý giọng nói đọc to phản hồi,
(c) pipeline dịch tài liệu chạy ngầm ban đêm. Ứng dụng nào hưởng lợi nhiều
nhất từ streaming, ứng dụng nào không cần — và tại sao?** (1 đoạn văn)
> a và b hưởng lợi nhất từ streaming, vì người dùng muốn được nhìn thấy ứng dụng đang chạy, nên khi streaming sẽ giúp người dùng không phải chờ lâu và biết rằng ứng dụng không bị lag. Trong khi đó c thì không cần streaming vì nhu cầu người dùng không yêu cầu phản hồi ngay và họ sẵn sàng chờ đợi để nó chạy ngầm.

### Câu 3.2 — Vì sao backoff theo cấp số nhân?
**Khi API quá tải và hàng nghìn client cùng retry, exponential backoff giúp
gì so với delay cố định? Tra cứu thêm: kỹ thuật "jitter" (thêm độ trễ ngẫu
nhiên) giải quyết vấn đề gì còn sót lại?**
> Khi API quá tải, nếu dùng delay cố định, hàng nghìn client sẽ gửi request lại vào cùng một thời điểm, khiến lưu lượng ồ ạt tràn vào tiếp tục đánh sập server. Backoff theo cấp số nhân giúp kéo dãn khoảng cách giữa các lần thử lại, từ từ giảm áp lực để hệ thống có thể xử lý từ từ.

---

## Block 4 — Mini-Project (trả lời sau Checkpoint 4)

### Câu 4.1 — Thiết kế persona
**Viết lại system prompt bạn dùng cho trợ lý của mình. Chỉ ra 2 chỗ trong
prompt mà nếu xóa đi, hành vi trợ lý sẽ thay đổi rõ rệt — và mô tả thay đổi
đó:**
> System prompt: "Bạn là một kỹ sư hệ thống (System Engineer) dày dặn kinh nghiệm. Nhiệm vụ của bạn là hỗ trợ phân tích log, chẩn đoán lỗi hạ tầng và cung cấp giải pháp tối ưu hóa". Nếu chỉnh sửa "kỹ sư hệ thống" thành "thực tập sinh đang đi học" thì câu trả lời sẽ không được chuyên nghiệp và thuyết phục, Nếu chỉnh sửa nhiệm vụ thành "viết lại hệ thống khác" thì model sẽ tập trung vào việc xây dựng lại thay vì phân tích, chẩn đoán.

### Câu 4.2 — Hạn chế & cải thiện
**Trợ lý của bạn giữ history 4 lượt cuối. Hãy mô tả một tình huống hội thoại
cụ thể mà giới hạn này khiến trợ lý trả lời sai/mất ngữ cảnh, và đề xuất một
cách khắc phục (ví dụ: tóm tắt các lượt cũ, tăng giới hạn có chọn lọc...):**
> Tình huống mà giới hạn khiến trợ lý trả lời sai: Người dùng hỏi trợ lý giải 5 bài tập, bài tập số 5 lấy dữ liệu từ bài tập 1, vì không còn dữ liệu của bài 1 nên trả lời fail. Cách khắc phục: Ta có thể thêm 1 model llm chạy song song có tác dụng tóm tắt ngữ cảnh khi ngữ cảnh đầy.

---

## Danh Sách Kiểm Tra Nộp Bài

- [ ] `python grade.py` — xem điểm tự động, mục tiêu ≥ 75/100
- [ ] Cả 4 checkpoint pytest đều pass
- [ ] Tất cả 9 câu trong file này đã được trả lời
- [ ] Đã copy bài làm vào folder `solution/`, push lên GitHub cá nhân và nộp link repo vào vlearn (theo hướng dẫn README)
