# RAG Web App — A to Z (Offline/Local or API)

Một dự án mẫu **end-to-end** giúp bạn:
- Đọc & index tài liệu (PDF/DOCX/TXT/HTML)
- Tìm ngữ nghĩa bằng FAISS
- RAG (Retrieval-Augmented Generation) với LLM **local (llama.cpp)** hoặc **OpenAI API**
- Giao diện chat qua **web browser** (Gradio) và **REST API** (FastAPI)

## 0) Yêu cầu hệ thống
- Python 3.10+ (khuyến nghị)
- Windows/Mac/Linux đều chạy được
- (Tùy chọn) File model `.gguf` nếu dùng backend `llama_cpp`

## 1) Cài đặt
```bash
python -m venv .venv
# Windows:
.\.venv\Scripts\activate
# macOS/Linux:
# source .venv/bin/activate

pip install -r requirements.txt
```

## 2) Cấu hình
- Sao chép `settings.example.toml` ➜ `settings.toml`
- Sửa `backend`:
  - `llama_cpp`: đặt `model_path` trỏ tới file `.gguf`
  - `openai`: đặt `OPENAI_API_KEY` (env) hoặc nhập trong file, chọn `model`
- Đặt `input_dir` chứa tài liệu của bạn (mặc định `data/input_docs`)

## 3) Thêm tài liệu
Thả file vào `data/input_docs`:
- Hỗ trợ: `.pdf`, `.docx`, `.txt`, `.md`, `.html`

## 4) Ingest (tạo index)
```bash
python app/ingest.py
```
Kết quả sẽ nằm trong `data/index/` (FAISS + metadata).

## 5) Chạy Web UI (Gradio) hoặc API (FastAPI)
- Web UI (nhanh nhất để thử):
```bash
python app/webui.py
# Mở http://127.0.0.1:7860
```
- REST API (nếu muốn tách frontend):
```bash
python app/api.py
# API ở http://127.0.0.1:8000/docs
```

## 6) Luồng hoạt động
1. `ingest.py` đọc toàn bộ file trong `input_dir`, parse text, chunk theo cấu hình, embed bằng `SentenceTransformer` (mặc định `intfloat/multilingual-e5-base`), lưu vào FAISS.
2. Khi bạn hỏi, hệ thống:
   - Embed câu hỏi
   - Tìm Top-k đoạn liên quan (FAISS cosine)
   - Ghép vào prompt cùng chỉ dẫn
   - Gọi LLM backend (`llama_cpp` hoặc `openai`)
   - Trả về câu trả lời + nguồn

## 7) Cú pháp Prompt & Trả lời
- Hệ thống ưu tiên trích dẫn chính xác. Nếu không tìm thấy bằng chứng, sẽ nói rõ.
- Bạn có thể chỉnh prompt trong `rag.py`.

## 8) Tips
- Tài liệu dài ➜ tăng `chunk_size` và `n_ctx` (nếu model cho phép).
- Nếu tiếng Việt, dùng embedding `intfloat/multilingual-e5-base` hoặc `BAAI/bge-m3` (sửa trong `settings.toml`).
- Muốn chạy nhanh hơn: dùng GPU build của `llama-cpp-python` (tuỳ máy).

---

**Made for: đọc tài liệu local, trả lời qua web, đơn giản – gọn – chuẩn.**
