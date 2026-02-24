🤖 # GenAI vs AI Agent (So sánh nhanh, dễ hiểu)

## 🧠 1) Định nghĩa
- **GenAI (Generative AI / LLM Chat)**: AI tập trung **tạo nội dung** (trả lời, tóm tắt, viết email, giải thích, tạo code snippet…). Thường “đúng/sai” dựa vào chất lượng câu trả lời.
- **AI Agent**: AI tập trung **hoàn thành mục tiêu** bằng cách **lập kế hoạch → gọi công cụ (tools) → kiểm tra → lặp lại** đến khi xong việc. “Đúng/sai” dựa vào việc đã hoàn thành nhiệm vụ và kiểm chứng được.

---

## 📊 2) So sánh

> Ghi chú: Bảng 3 cột dài thường bị “xấu” khi xem ở terminal hoặc cửa sổ hẹp. Dưới đây là bản tóm tắt ngắn + so sánh theo từng tiêu chí để dễ đọc.

### ✨ Tóm tắt 10 giây
| Bạn cần… | Chọn |
|---|---|
| 🧠 Giải thích, tóm tắt, viết nội dung | **GenAI (Chat)** |
| ✅ Kết quả có thể kiểm chứng (file/PR/query/test) | **AI Agent** |

### 🟦 GenAI (Chat) — “Trả lời & tạo nội dung”
- Mạnh: giải thích, tóm tắt, viết, brainstorm.
- Output thường là: đoạn trả lời, dàn ý, bảng, hướng dẫn, snippet.
- Lưu ý: nếu thiếu dữ liệu/nguồn → dễ sai.

### 🟩 AI Agent — “Giao việc & hoàn thành”
- Mạnh: làm theo bước, dùng tool, tạo artefacts (file/PR/ticket), có kiểm tra.
- Output thường là: thay đổi trong repo, báo cáo có số liệu, kết quả test/query.
- Lưu ý: cần phân quyền vì có thể gây side effects (ghi/xoá/sửa).

### 🧩 So sánh theo tiêu chí (dễ nhìn)

#### 🎯 Mục tiêu
- **GenAI**: Trả lời / tạo nội dung.
- **AI Agent**: Hoàn thành việc end-to-end.

#### 🧠 Cách làm
- **GenAI**: Sinh văn bản dựa trên prompt + context.
- **AI Agent**: Lập kế hoạch + hành động nhiều bước (có thể lặp lại).

#### 🛠️ Tooling
- **GenAI**: Có thể không cần tool.
- **AI Agent**: Thường cần tool (file/web/DB/git/ticket…).

#### 🧪 Kiểm chứng
- **GenAI**: Hay dừng ở mức “giải thích/đề xuất”.
- **AI Agent**: Có bước check/verify (test, lint, query, đối chiếu…).

#### 📌 Dữ liệu
- **GenAI**: Dễ thiếu dữ liệu → dễ sai nếu prompt mơ hồ.
- **AI Agent**: Dùng tool kéo dữ liệu từ nguồn → ít đoán hơn.

#### ⏱️ Thời gian
- **GenAI**: Nhanh (1–2 lượt chat).
- **AI Agent**: Lâu hơn (vì chạy nhiều bước).

#### 🧱 Output
- **GenAI**: Bài viết, bảng, hướng dẫn, code snippet.
- **AI Agent**: PR, file đã sửa, báo cáo có số liệu, ticket…

#### ⚠️ Rủi ro
- **GenAI**: Hallucination (bịa), thiếu dẫn chứng.
- **AI Agent**: Side effects (lỡ ghi/xoá), cần phân quyền.

#### ✅ Khi nào dùng
- **GenAI**: Học lý thuyết, soạn nội dung, brainstorming.
- **AI Agent**: Sửa bug, refactor, báo cáo từ dữ liệu, tự động hoá workflow.

---

## 🧪 3) Ví dụ “cùng 1 việc” nhưng 2 kiểu
### Ví dụ: “Tôi muốn hiểu và xử lý lỗi đăng nhập”

**GenAI prompt (học/hiểu):**
- “Giải thích các nguyên nhân phổ biến gây lỗi đăng nhập (JWT/Session), cách debug từng bước, và checklist kiểm tra.”

**Agent prompt (làm việc):**
- “Trong repo này, tìm nguyên nhân lỗi đăng nhập X (kèm stacktrace), sửa lỗi nhỏ nhất, thêm test, chạy test và báo kết quả.”

---

## 🧰 4) Prompt nên khác nhau ở đâu?
### ✅ GenAI: nhấn vào *format + độ rõ*
- Goal + Context + Output format + Constraints

### ✅ Agent: nhấn vào *phạm vi + bước làm + kiểm tra*
- Goal + Scope + Tasks + Deliverables + Checks

---

## 🧭 5) Quy tắc chọn nhanh
- Nếu bạn cần **câu trả lời/giải thích** → dùng **GenAI**.
- Nếu bạn cần **kết quả cụ thể có thể kiểm chứng** (file đổi, test pass, query có số liệu) → dùng **AI Agent**.

---

### 📝 Ghi chú
- Nhiều hệ thống thực tế là “hybrid”: GenAI để brainstorm/thiết kế, Agent để triển khai và kiểm tra.
