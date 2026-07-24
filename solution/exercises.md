# K3 — Ngày 1: Bài Tập & Phản Ánh
## Khám Phá LLM API | Phiếu Thực Hành

**Thời lượng:** 9h00–13h00


---

## Block 1 — API Cơ Bản (trả lời sau Checkpoint 1)

### Câu 1.1 — Độ nhạy của temperature
Gọi `call_openai` với temperature 0.0, 0.5, 1.0 và 1.5 dùng prompt
**"Hãy kể cho tôi một sự thật thú vị về Việt Nam."**

**Bạn nhận thấy quy luật gì qua bốn phản hồi?** (2–3 câu)
> Ở temperature = 0.0, phản hồi mang tính xác định (deterministic), nhất quán và tập trung vào các sự thật phổ biến nhất. Khi tăng temperature lên 0.5 và 1.0, văn phong trở nên tự nhiên, đa dạng hơn về góc nhìn và vốn từ. Ở mức 1.5, câu từ bắt đầu mang tính sáng tạo cao, cấu trúc câu ngẫu nhiên hơn và có thể xuất hiện dấu hiệu lan man hoặc dùng từ không tự nhiên.

### Câu 1.2 — Chọn temperature cho sản phẩm
**Bạn sẽ đặt temperature bao nhiêu cho chatbot hỗ trợ khách hàng, và tại sao?**
> Tôi sẽ đặt temperature khoảng 0.0 đến 0.2. Mức temperature thấp giúp phản hồi của chatbot có tính chính xác, nhất quán và bám sát thông tin nghiệp vụ/tri thức của doanh nghiệp, hạn chế tối đa rủi ro "ảo giác" (hallucination) hoặc trả về thông tin sai lệch cho khách hàng.

### Câu 1.3 — Đánh đổi chi phí
Kịch bản: 10.000 người dùng hoạt động mỗi ngày, mỗi người gọi API 3 lần,
mỗi lần trung bình ~350 token đầu ra.

**Ước tính GPT-4o đắt hơn GPT-4o-mini bao nhiêu lần cho workload này? Nêu một
trường hợp GPT-4o xứng đáng với chi phí và một trường hợp nên dùng mini:**
> Dựa trên bảng giá OpenAI (GPT-4o output: $10.00 / 1M tokens; GPT-4o-mini output: $0.60 / 1M tokens), GPT-4o đắt hơn khoảng 16.6 lần so với GPT-4o-mini cho cùng lượng output token.
Trường hợp xứng đáng dùng GPT-4o: Phân tích tài chính, phân tích logic mã nguồn phức tạp hoặc đưa ra quyết định kinh doanh quan trọng yêu cầu khả năng suy luận sâu và độ chính xác tuyệt đối.
Trường hợp nên dùng mini: Phân loại phản hồi khách hàng (intent classification), tóm tắt văn bản ngắn, hoặc làm chatbot CSKH giải đáp các câu hỏi thường gặp (FAQ).

---

## Block 2 — System Prompt & Token (trả lời sau Checkpoint 2)

### Câu 2.1 — Sức mạnh của persona
Gọi `chat_with_system_prompt` hai lần với cùng câu hỏi
**"Giải thích blockchain là gì?"** nhưng hai system prompt khác nhau:
- "Bạn là giáo viên tiểu học, giải thích thật đơn giản cho trẻ 8 tuổi."
- "Bạn là chuyên gia tài chính, trả lời chuyên sâu bằng thuật ngữ kỹ thuật."

**Hai phản hồi khác nhau như thế nào (độ dài, từ vựng, ví dụ)? System prompt
ảnh hưởng đến hành vi model ra sao?** (3–4 câu)
> Phản hồi dành cho trẻ 8 tuổi sử dụng từ ngữ vô cùng đơn giản, ngắn gọn, và dùng hình ảnh so sánh trực quan như "cuốn sổ nhật ký dùng chung mà ai cũng có một bản". Trong khi đó, phản hồi của chuyên gia tài chính dài hơn, sử dụng chuỗi thuật ngữ chuyên ngành như "sổ cái phân tán (distributed ledger)", "cơ chế đồng mật (consensus algorithm)", "tính bất biến (immutability)" và "mã hóa (cryptography)". System prompt đóng vai trò như một bộ lọc ngữ cảnh (contextual filter), giúp định hình persona, điều chỉnh độ sâu kiến thức, tone/mood và tập từ vựng mà model sẽ khai thác khi sinh câu trả lời.

### Câu 2.2 — tiktoken vs đếm từ
Chọn một đoạn văn tiếng Việt ~100 từ. So sánh số token theo `count_tokens`
(tiktoken) với ước lượng `số từ / 0.75` mà Part 1 đã dùng.

**Hai con số chênh nhau bao nhiêu phần trăm? Vì sao tiếng Việt thường tốn
nhiều token hơn tiếng Anh cùng độ dài?**
> Với một đoạn văn tiếng Việt 100 từ, ước lượng 100 / 0.75 ra khoảng 133 token. Tuy nhiên, tiktoken thực tế đếm được khoảng 200–230 token (chênh lệch từ 50% đến 70%). Lý do là các thuật toán tokenizer (như BPE) được huấn luyện chủ yếu trên dữ liệu tiếng Anh, nơi mỗi token thường tương ứng với một từ hoặc từ ghép ngắn. Với tiếng Việt, do có dấu thanh và nhiều ký tự Unicode đặc biệt, tokenizer thường phải tách một từ tiếng Việt thành 2–3 sub-word tokens, dẫn đến chi phí token cao hơn đáng kể

---

## Block 3 — Streaming & Độ Bền (trả lời sau Checkpoint 3)

### Câu 3.1 — Trải nghiệm người dùng với streaming
**Streaming quan trọng nhất trong trường hợp nào, và khi nào thì
non-streaming lại phù hợp hơn?** (1 đoạn văn)
> Streaming cực kỳ quan trọng trong các giao diện hội thoại tương tác thời gian thực (như ChatGPT CLI, Web Chatbot), nơi phản hồi dài có thể mất vài giây để sinh xong; việc stream từng token giúp giảm Time-to-First-Token (TTFT), tạo cảm giác ứng dụng phản hồi tức thì và cải thiện trải nghiệm người dùng (UX). Nguồn ngược lại, non-streaming phù hợp hơn cho các tác vụ xử lý ngầm (background jobs), gọi API giữa các service (service-to-service), phân tích dữ liệu hàng loạt (batch processing), hoặc khi cần validate toàn bộ định dạng dữ liệu (JSON schema) trước khi trả về cho hệ thống phía sau.

### Câu 3.2 — Vì sao backoff theo cấp số nhân?
**So với delay cố định (ví dụ luôn chờ 1 giây), exponential backoff có lợi
thế gì khi API bị quá tải? Điều gì xảy ra nếu hàng nghìn client cùng retry
với delay cố định giống nhau?**
> Exponential backoff giúp giảm áp lực tăng dần lên hệ thống máy chủ đang bị sự cố bằng cách giãn rộng khoảng thời gian chờ giữa các lần thử lại. Nếu hàng nghìn client cùng retry với delay cố định (ví dụ 1 giây), nó sẽ tạo ra hiện tượng Thundering Herd Problem (hoặc Retry Storm) — các request liên tục dội ngược lại server theo chu kỳ 1 giây, khiến server vừa khôi phục đã ngay lập tức bị quá tải và sập lại.

---

## Block 4 — Mini-Project (trả lời sau Checkpoint 4)

### Câu 4.1 — Thiết kế persona
**Bạn chọn persona gì cho trợ lý của mình? Viết lại system prompt đó và giải
thích 1–2 lựa chọn từ ngữ quan trọng trong prompt (ví dụ: vì sao yêu cầu
"trả lời ngắn gọn", vì sao chỉ định ngôn ngữ...):**
> System prompt: "Bạn là trợ giảng thân thiện của khóa AI, trả lời ngắn gọn bằng tiếng Việt."
Lựa chọn 1 ("trả lời ngắn gọn"): Giúp tiết kiệm ngân sách token, đồng thời tối ưu tốc độ phản hồi trên giao diện dòng lệnh (CLI).
Lựa chọn 2 ("bằng tiếng Việt"): Khóa định hướng ngôn ngữ đầu ra, tránh trường hợp model tự động chuyển sang tiếng Anh khi gặp các từ khóa kỹ thuật (như "temperature", "prompt", "LLM").

### Câu 4.2 — Hạn chế & cải thiện
**Trợ lý của bạn hiện có hạn chế lớn nhất là gì (ví dụ: history chỉ 3 lượt,
không có bộ nhớ dài hạn, không kiểm duyệt nội dung...)? Đề xuất một cải
thiện cụ thể và mô tả ngắn cách triển khai:**
> Hạn chế lớn nhất: Bộ nhớ ngắn hạn bị giới hạn cứng ở 3 lượt hội thoại gần nhất (history[-6:]), khiến chatbot nhanh chóng "quên" các thông tin quan trọng được cung cấp ở đầu phiên làm việc.

Đề xuất cải thiện: Triển khai cơ chế Summarization / Memory Buffer.

Cách triển khai: Khi độ dài history vượt quá 6 message, thay vì xóa bỏ hoàn toàn các tin nhắn cũ, ta gọi một hàm phụ (sử dụng GPT-4o-mini) để tóm tắt các thông tin quan trọng của các lượt hội thoại trước đó thành một đoạn văn ngắn (summary_context), sau đó đính kèm đoạn tóm tắt này vào ngay sau System Prompt ở mỗi lần gọi API tiếp theo.


## Danh Sách Kiểm Tra Nộp Bài

- [ ] `python grade.py` — xem điểm tự động, mục tiêu ≥ 75/100
- [ ] Cả 4 checkpoint pytest đều pass
- [ ] Tất cả 9 câu trong file này đã được trả lời
- [ ] Đã copy bài làm vào folder `solution/` và zip theo hướng dẫn README
