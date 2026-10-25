# RAG tra cứu thủ tục hành chính cấp xã

Bài thực hành *Create a RAG with LLM and Qdrant using your own data* (khóa Introduction to Retrieval Augmented Generation, Duke University). Dựa trên notebook Applied RAG trong repo [alfredodeza/learn-retrieval-augmented-generation](https://github.com/alfredodeza/learn-retrieval-augmented-generation), thay dữ liệu rượu bằng danh mục thủ tục hành chính cấp xã.

## Tệp

| Tệp | Nội dung |
| --- | --- |
| `rag_thu_tuc_colab.ipynb` | Notebook chạy trên Google Colab |
| `thu_tuc_mau.csv` | 6 thủ tục minh họa (hộ tịch, chứng thực) |

Dữ liệu là dữ liệu minh họa, mã `VD-xx` tự đặt, nội dung chưa đối chiếu văn bản chính thức.

## Thành phần

- **Sentence Transformers** `paraphrase-multilingual-MiniLM-L12-v2`: tạo embedding tiếng Việt.
- **Qdrant** (chạy trong bộ nhớ): lưu vector, tìm thủ tục gần nhất.
- **Llamafile** chạy **Qwen2.5-3B-Instruct** (Q4_K_M) trên CPU, API kiểu OpenAI ở cổng 8081.
- Thư viện **openai** để gọi LLM.

## Khác với notebook gốc

1. Model embedding đa ngữ thay cho `all-MiniLM-L6-v2` (chủ yếu tiếng Anh).
2. `query_points` thay cho `search` (đã bị bỏ trong `qdrant-client` mới).
3. Tìm kiếm lai: khớp từ khóa cách nói của người dân, rồi đến độ tương đồng cosine.
4. Tài liệu đưa vào tin nhắn `user` có nhãn rõ, thay vì vai `assistant`.
5. Bước kiểm tra liên quan (CO/KHONG) trước khi trả lời; không có thủ tục phù hợp thì chuyển Bộ phận Một cửa.
6. Code tự gắn dòng "Nguồn" (mã thủ tục) vào câu trả lời.

## Nhận xét khi chạy thử

- Với Qwen 0,8B, câu ngoài phạm vi ("giấy phép xây dựng") bị gán nhầm thủ tục xác nhận tình trạng hôn nhân. Đổi sang Qwen 3B thì model từ chối đúng.
- Câu "sao y căn cước ở đâu" được xếp đúng thủ tục chứng thực, nhưng điểm cosine chỉ 0,17, dưới ngưỡng 0,35, nên bị bỏ sót. Khớp từ khóa khắc phục được. Ngưỡng điểm cố định không dùng chung được cho câu ngắn và câu dài.
- Với câu "sao y căn cước ở đâu", LLM 3B tìm đúng thủ tục nhưng viết lại sai hồ sơ ("Căn cước công dân" thay vì "Bản chính giấy tờ; Bản sao cần chứng thực") và lệ phí. Mục 8 của notebook so sánh với cách code điền thẳng các mục từ kho: LLM chỉ kiểm tra liên quan, nội dung giữ đúng từng chữ.

## Cách chạy

Mở notebook trên Google Colab, tải `thu_tuc_mau.csv` lên, chọn **Thời gian chạy → Chạy tất cả**. Lần đầu tải Llamafile và model khoảng 2,5 GB.
