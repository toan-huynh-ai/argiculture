# Phân tích bài báo: AgriEval

## 1. Thông tin cơ bản

- **Tiêu đề**: AgriEval: A Comprehensive Chinese Agricultural Benchmark for Large Language Models
- **Tác giả**: Lian Yan, Haotian Wang, Chen Tang, Haifeng Liu, Tianyang Sun, Liangliang Liu, Yi Guan, Jingchi Jiang
- **Đơn vị**: Harbin Institute of Technology; MemTensor (Shanghai) Technology Co., Ltd.
- **Năm**: 2025 (arXiv:2507.21773v1, 29 Jul 2025)
- **Venue**: Preprint, under review

## 2. Tóm tắt

AgriEval là benchmark đầu tiên và lớn nhất dành cho đánh giá LLM trong lĩnh vực nông nghiệp Trung Quốc. Benchmark bao gồm **14.697 câu hỏi trắc nghiệm** và **2.167 câu hỏi tự luận** được thu thập từ các đề thi đại học và sau đại học trong hệ thống giáo dục Trung Quốc. AgriEval đánh giá **51 mô hình LLM** (9 thương mại, 42 mã nguồn mở) trên **6 lĩnh vực chính** và **29 phân lĩnh vực** nông nghiệp, sử dụng một khung phân loại nhận thức 4 cấp (Memorization, Understanding, Inference, Generation). Kết quả cho thấy phần lớn LLM hiện tại không đạt được 60% độ chính xác, và mô hình tốt nhất (Qwen-Plus) chỉ đạt 63.21%.

## 3. Vấn đề nghiên cứu

Bài báo giải quyết vấn đề **thiếu benchmark chuyên biệt** để đánh giá năng lực LLM trong lĩnh vực nông nghiệp:

- Các benchmark hiện có (MMLU, C-Eval, CMMLU) chỉ có <1.5% câu hỏi liên quan đến nông nghiệp
- Các benchmark hiện tại thiếu câu hỏi ở mức chuyên gia, chủ yếu tập trung vào kiến thức cơ bản
- Nông nghiệp Trung Quốc có tính đặc thù cao: đa dạng vùng miền, đa dạng sinh thái, và tính đặc thù văn hóa (thảo dược truyền thống, khoa học trà)
- Cần đánh giá không chỉ kiến thức mà cả khả năng suy luận phức tạp (chẩn đoán bệnh cây, tính toán liều lượng thuốc trừ sâu)

## 4. Phương pháp

### 4.1 Xây dựng dữ liệu (Dataset Construction)

- **Nguồn dữ liệu**: Thu thập từ đề thi thực tế ở cấp đại học và sau đại học tại các trường đại học hàng đầu Trung Quốc
  - Ngân hàng đề thi mô phỏng công khai
  - Tài liệu thi tuyển sinh sau đại học từ cơ quan chính phủ
  - Đề thi lưu trữ của sinh viên các trường đại học
- **Định dạng gốc**: Word và PDF, được chuyển đổi sang JSON có cấu trúc qua OCR
- **Công cụ chú thích**: Phát triển công cụ annotation tùy chỉnh riêng

### 4.2 Quy mô (Scale)

| Thông số | Giá trị |
|----------|---------|
| Tổng câu hỏi trắc nghiệm | 14,697 |
| Tổng câu hỏi tự luận | 2,167 |
| Lĩnh vực chính | 6 (Plant Production, Forestry, Grass Science, Aquaculture, Animal Science & Technology, Traditional Chinese Herbology) |
| Phân lĩnh vực | 29 |
| Cấp độ nhận thức | 4 (Memorization, Understanding, Inference, Generation) |
| Chiều kỹ năng chi tiết | 15 |
| Độ dài trung bình câu hỏi | 76.92 tokens |
| Độ dài trung bình đáp án tự luận | 467.30 tokens |
| Mỗi phân lĩnh vực | ≥100 câu hỏi |
| Mỗi nhóm nhận thức | >2,000 mẫu |

### 4.3 Quy trình Annotation

- **Nhân sự**: 2 chuyên gia nông nghiệp (trình độ tiến sĩ) từ phòng thí nghiệm đại học đối tác
- **Quy trình**:
  1. Thu thập >500 tài liệu, lọc còn 400 tài liệu dựa trên độ khó, độ phù hợp, và tính thực tế
  2. Chuyển đổi PDF → Word (OCR) → JSON có cấu trúc
  3. Phân loại bởi chuyên gia nông nghiệp
  4. Xác nhận chất lượng: lấy mẫu ngẫu nhiên 5% dữ liệu cho 2 chuyên gia chú thích độc lập
  5. Đo Cohen's Kappa = 0.85 cho phân loại danh mục
  6. Độ nhất quán câu hỏi/đáp án đạt 99.7%
  7. Chỉ bắt đầu chú thích quy mô lớn khi đồng thuận >90%
- **Nâng cao độ khó**: Sử dụng GPT-4 tạo thêm lựa chọn gây nhiễu (từ 4 lên 7 đáp án), được chuyên gia xác nhận
- **Thời gian**: ~1 tháng, annotator trả 50 CNY/giờ

## 5. Evaluation

### 5.1 Metrics

- **Trắc nghiệm**: Accuracy (câu nhiều đáp án: đúng khi chọn đúng tất cả)
- **Tự luận**: ROUGE-L
- **Thiết lập đánh giá**: Zero-shot, Few-shot (5-shot), Chain-of-Thought (CoT), RAG

### 5.2 Mô hình được đánh giá

- Tổng cộng **51 LLM**: 9 thương mại + 42 mã nguồn mở
- Thương mại: GPT-3.5-Turbo, GPT-4o, GPT-4o-mini, Claude-3.5-Sonnet, Gemini-2.0-Flash, Qwen-Plus, Qwen-Turbo, GLM-4-Air, GLM-4-Flash
- Mã nguồn mở: Qwen2.5 (3B–72B), Llama2/3, DeepSeek-V3, Baichuan2, InternLM, Yi, Phi-3, Mistral, ChatGLM, Marco-o1, KwooLa (nông nghiệp chuyên biệt)

### 5.3 Kết quả chính (với con số cụ thể)

| Mô hình | Accuracy (Overall) |
|---------|--------------------|
| Qwen-Plus | **63.21%** (tốt nhất) |
| Qwen2-72B-Instruct | 62.72% |
| Qwen2.5-72B-Instruct | 60.32% |
| DeepSeek-V3 | 57.43% |
| Qwen2.5-32B-Instruct | 56.35% |
| Qwen-Turbo | 55.76% |
| Gemini-2.0-Flash | 55.33% |
| Claude-3.5-Sonnet | 54.92% |
| GPT-4o | 50.01% |
| GPT-4o-mini | 48.19% |
| Human Expert (avg.) | **70.62%** |

**Phát hiện chính:**

1. **Mô hình trung bình chỉ đạt 41.27%** — đa số không vượt 60%
2. **Khoảng cách với chuyên gia**: Mô hình tốt nhất kém chuyên gia 4.84% (63.21% vs 70.62%)
3. **Inference là thách thức lớn nhất**: Numerical reasoning và genetic inference có điểm thấp nhất
4. **Bias vị trí**: Xáo trộn vị trí đáp án làm giảm trung bình 6.95% accuracy
5. **CoT không luôn hiệu quả**: Giảm trung bình 3.51%, nhưng tăng 9.81% cho numerical reasoning
6. **Few-shot không ổn định**: Kết quả không nhất quán, phụ thuộc vào chất lượng ví dụ
7. **RAG hiệu quả**: Tăng trung bình ~4.0%, đặc biệt giúp mô hình nhỏ
8. **Mô hình <7B**: Chỉ đạt trung bình 34.15%
9. **Mô hình mã nguồn mở vượt mô hình thương mại**: Qwen2-72B (62.72%) > GPT-4o (50.01%)

### 5.4 Phân tích lỗi (Error Analysis trên GPT-4o-mini, 200 mẫu)

| Loại lỗi | Tỷ lệ |
|-----------|--------|
| Thiếu kiến thức (Lack of Knowledge) | 83% |
| Lỗi hiểu (Understanding Error) | 8% |
| Lỗi suy luận (Reasoning Error) | 9% |

## 6. Điểm mạnh

1. **Quy mô lớn nhất hiện có**: 16,864 câu hỏi (14,697 MC + 2,167 QA) — vượt xa các benchmark nông nghiệp khác
2. **Phân loại nhận thức tinh tế**: Framework 4 cấp, 15 chiều kỹ năng — dựa trên Bloom's Taxonomy, cho phép đánh giá chi tiết từ nhớ → hiểu → suy luận → sinh
3. **Dữ liệu chất lượng cao**: Từ đề thi thực tế cấp đại học, không phải dữ liệu tổng hợp
4. **Đánh giá toàn diện**: 51 mô hình, nhiều thiết lập (zero-shot, few-shot, CoT, RAG, position bias)
5. **Bao phủ đa dạng**: 6 lĩnh vực chính, 29 phân lĩnh vực — bao gồm cả các lĩnh vực đặc thù Trung Quốc (thảo dược, khoa học trà)
6. **Nâng cao độ khó**: Tăng lên 7 lựa chọn đáp án, tạo bởi GPT-4 và xác nhận bởi chuyên gia
7. **Phiên bản đa ngôn ngữ**: Có bản dịch tiếng Anh qua GPT-4o-mini
8. **Công khai mã nguồn**: Dataset và code đánh giá trên HuggingFace/GitHub

## 7. Điểm yếu / Limitations

1. **Chỉ tập trung vào Trung Quốc**: Dữ liệu hoàn toàn từ hệ thống giáo dục và nông nghiệp Trung Quốc, không đại diện cho nông nghiệp nhiệt đới, Đông Nam Á, châu Phi
2. **Định dạng thi cử**: Câu hỏi dạng đề thi (trắc nghiệm, tự luận) — không phản ánh các tình huống tư vấn thực tế ngoài đồng ruộng
3. **Thiếu ngữ cảnh (context)**: Câu hỏi không có thông tin bối cảnh cụ thể (điều kiện thời tiết, loại đất, vùng địa lý cụ thể trong câu hỏi)
4. **Đánh giá tự luận bằng ROUGE-L**: Metric này đo sự trùng khớp từ ngữ, không đánh giá chất lượng tư vấn nông nghiệp thực tế
5. **Không có multi-turn**: Chỉ đánh giá single-turn, không kiểm tra khả năng hỏi thêm thông tin hoặc tương tác nhiều lượt
6. **Không đánh giá context sensitivity**: Không kiểm tra mô hình phản ứng thế nào khi ngữ cảnh bị thay đổi hoặc suy giảm
7. **Bias tiếng Trung**: Mô hình tiếng Anh (Llama) bị bất lợi tự nhiên do dữ liệu hoàn toàn bằng tiếng Trung
8. **Thiếu đánh giá chất lượng tư vấn thực tế**: Không đánh giá tính khả thi, an toàn, hoặc phù hợp của lời khuyên nông nghiệp

## 8. So sánh với dự án Vietnamese Agriculture QA

### 8.1 Điểm tương đồng

| Khía cạnh | AgriEval | Vietnamese Agriculture QA |
|-----------|----------|--------------------------|
| Lĩnh vực | Nông nghiệp | Nông nghiệp |
| Đánh giá LLM | 51 mô hình | 7 frontier LLM |
| Chuyên gia tham gia | Có (PhD nông nghiệp) | Có (expert validation) |
| Ngôn ngữ đặc thù | Tiếng Trung | Tiếng Việt |
| Đa dạng lĩnh vực | 6 lĩnh vực, 29 phân lĩnh vực | ~90 loài cây trồng, 5 vùng sinh thái |

### 8.2 Điểm khác biệt cốt lõi

| Khía cạnh | AgriEval | Vietnamese Agriculture QA |
|-----------|----------|--------------------------|
| Định dạng | Đề thi (MC + QA) | Hỏi-đáp tư vấn thực tế (single-turn + multi-turn) |
| Ngữ cảnh | Không có context cụ thể | Context-aware (3 mức suy giảm ngữ cảnh) |
| Multi-turn | Không | Có |
| Đánh giá bias vùng miền | Không | Có (regional bias) |
| Đối tượng sử dụng | Đánh giá mô hình trong học thuật | Tư vấn cho nông dân Việt Nam |
| Sensitivity testing | Position bias chỉ | Context sensitivity (3-level degradation) |
| Thực tiễn | Kiến thức lý thuyết | Tư vấn khuyến nông (advisory quality) |

### 8.3 Khoảng trống dự án chúng ta lấp đầy

1. **Context Sensitivity**: AgriEval không đánh giá khả năng suy luận dựa trên ngữ cảnh — dự án chúng ta kiểm tra 3 mức suy giảm ngữ cảnh (full context → partial → no context)
2. **Multi-turn QA**: AgriEval chỉ có single-turn — dự án chúng ta bao gồm cả multi-turn, phản ánh thực tế tương tác giữa nông dân và hệ thống tư vấn
3. **Regional Bias cho vùng nhiệt đới**: AgriEval tập trung vào nông nghiệp ôn đới Trung Quốc — dự án chúng ta là benchmark ĐẦU TIÊN cho nông nghiệp nhiệt đới Việt Nam
4. **Tư vấn thực tế (Advisory Quality)**: AgriEval dùng accuracy/ROUGE-L — dự án chúng ta đánh giá chất lượng tư vấn khuyến nông phù hợp với điều kiện thực tế
5. **Tiếng Việt — ngôn ngữ ít tài nguyên**: Đây là lĩnh vực chưa được khai thác, khác với tiếng Trung đã có nhiều LLM chuyên biệt
6. **5 vùng sinh thái Việt Nam**: Đánh giá sự phù hợp của tư vấn theo từng vùng sinh thái cụ thể

## 9. Bài học rút ra

1. **Cognitive taxonomy là framework tốt**: Phân loại 4 cấp (Memorization → Understanding → Inference → Generation) từ Bloom's Taxonomy là cách tiếp cận hữu ích — có thể áp dụng tương tự khi phân loại câu hỏi trong dataset của chúng ta
2. **Chuyên gia là then chốt**: Quy trình annotation có chuyên gia PhD, đo Cohen's Kappa, và iterative training cho annotator — đây là tiêu chuẩn vàng mà dự án chúng ta nên duy trì
3. **RAG cải thiện đáng kể**: Tăng ~4% cho tất cả mô hình — gợi ý rằng việc cung cấp context phù hợp (đúng vùng, đúng cây trồng) có thể cải thiện đáng kể chất lượng phản hồi
4. **Position bias cần lưu ý**: Nếu dự án có phần đánh giá trắc nghiệm, cần xáo trộn vị trí đáp án
5. **Quy mô mô hình không phải tất cả**: Diminishing returns sau 14B tham số — quan trọng hơn là domain adaptation
6. **83% lỗi do thiếu kiến thức**: Điều này củng cố tầm quan trọng của việc cung cấp context chất lượng cao trong dataset QA của chúng ta
7. **Công khai dataset**: AgriEval công khai trên HuggingFace — dự án chúng ta cũng nên hướng tới việc công khai để tạo impact trong cộng đồng nghiên cứu
8. **Cần vượt xa dạng đề thi**: AgriEval bị giới hạn bởi định dạng thi cử — dự án chúng ta với dạng QA tư vấn thực tế có giá trị ứng dụng cao hơn
