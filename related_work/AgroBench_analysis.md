# Phân tích: AgroBench — Vision-Language Model Benchmark in Agriculture

---

## 1. Thông tin cơ bản

| Mục | Chi tiết |
|-----|---------|
| **Title** | AgroBench: Vision-Language Model Benchmark in Agriculture |
| **Authors** | Risa Shinoda, Nakamasa Inoue, Hirokatsu Kataoka, Masaki Onishi, Yoshitaka Ushiku |
| **Affiliations** | University of Osaka, Kyoto University, Tokyo Institute of Technology, AIST, University of Oxford (VGG), OMRON SINIC X |
| **Venue** | ICCV 2025 (Proceedings of the IEEE/CVF International Conference on Computer Vision) |
| **Year** | 2025 |
| **ArXiv** | 2507.20519v1 |
| **Link** | https://dahlian00.github.io/AgroBenchPage/ |

---

## 2. Tóm tắt

AgroBench là benchmark toàn diện đầu tiên được thiết kế để đánh giá VLMs trên lĩnh vực nông nghiệp. Điểm cốt lõi: **tất cả QA pairs đều được annotate bởi chuyên gia nông nghiệp** (không phải synthetic từ GPT). Benchmark bao gồm 7 tasks nông nghiệp, phủ 203 loại cây trồng, 682 loại bệnh, 134 loại sâu bệnh, và 108 loại cỏ dại. Kết quả đánh giá cho thấy VLMs vẫn còn hạn chế đáng kể ở các tasks nhận dạng chi tiết (fine-grained identification), đặc biệt nhận dạng cỏ dại gần như random.

---

## 3. Phương pháp

### 3.1. Thiết kế Benchmark (7 tasks)

| Task | Mô tả | Số QA pairs | Phạm vi |
|------|--------|-------------|---------|
| **Disease Identification (DID)** | Chẩn đoán bệnh cây trồng từ hình ảnh | 1,502 | 370 loại bệnh, 160 loại cây, 682 crop-disease combinations |
| **Pest Identification (PID)** | Nhận dạng sâu bệnh/côn trùng | 544 | 134 pest categories |
| **Weed Identification (WID)** | Nhận dạng cỏ dại (có bounding box) | 609 | 108 weed species |
| **Crop Management (CMN)** | Tư vấn quản lý cây trồng (tưới, phân bón, thời điểm thu hoạch) | 411 | Đa dạng scenarios |
| **Disease Management (DMN)** | Tư vấn xử lý bệnh (thuốc, phương pháp) | 569 | 141 crop-disease combinations |
| **Machine Usage QA (MQA)** | Sử dụng máy móc nông nghiệp | 303 | 98 machine categories |
| **Traditional Management (TM)** | Phương pháp canh tác truyền thống | 404 | 77 traditional practices |

**Tổng: 4,342 QA pairs trên 3,745 images.**

### 3.2. Quy trình xây dựng dataset

- **Image Selection**: Thu thập ~50,000 ảnh ban đầu từ websites có giám sát bởi plant pathologists, ưu tiên ảnh thực tế ngoài đồng (real farm settings). Sau sàng lọc còn 4,218 ảnh chất lượng cao.
- **QA Annotation**: Tất cả do chuyên gia nông nghiệp annotate thủ công (~150 man-hours). GPT chỉ được dùng để rephrase câu hỏi, KHÔNG dùng cho knowledge generation.
- **Format**: Multiple-choice (5 lựa chọn), các đáp án sai được thiết kế có tính đánh lừa cao (diseases có triệu chứng tương tự, pests thường gặp trên cùng cây trồng).
- **Validation**: Các chuyên gia có Ph.D./M.S. nông nghiệp review lại chất lượng QA pairs.

### 3.3. Đặc điểm annotation nổi bật

- DMN và CMN có **context-dependent answers**: Cùng một bệnh nhưng ở severity khác nhau sẽ có đáp án quản lý khác nhau (ví dụ: alfalfa bacterial leaf spot ở giai đoạn đầu vs. giai đoạn nặng).
- WID sử dụng bounding box để xác định target weed khi nhiều loại cỏ mọc cùng nhau.
- Câu hỏi được thiết kế sao cho **phải nhìn ảnh mới trả lời được** (verified bằng text-only ablation).

---

## 4. Evaluation

### 4.1. Models đánh giá

- **Closed-source**: GPT-4o, GPT-4o mini, Gemini 1.5-Pro, Gemini 1.5-Flash
- **Open-source**: EMU2Chat, LLaVA-Next (8B/72B), QwenVLM (7B/72B), CogVLM-19B, LLaVA (7B/13B)
- **Human baseline**: 28 sinh viên nông nghiệp (≥ bachelor), mỗi người trả lời 20 câu

### 4.2. Metrics

- Accuracy (exact matching) trên format multiple-choice 5 lựa chọn
- Overall score = trung bình accuracy các tasks (không theo số QA)

### 4.3. Kết quả chính

| Model | DID | DMN | PID | WID | CMN | MQA | TM | Overall |
|-------|-----|-----|-----|-----|-----|-----|-----|---------|
| Random | 21.77 | 15.64 | 20.40 | 17.90 | 16.06 | 22.11 | 19.31 | 19.03 |
| Human | 25.00 | 22.50 | 45.00 | 20.00 | 36.25 | 57.50 | 51.25 | 36.79 |
| **GPT-4o** | **64.18** | **87.35** | **77.76** | 44.17 | **75.43** | **82.84** | **82.43** | **73.45** |
| Gemini 1.5-Pro | 62.92 | 81.55 | 74.45 | **55.17** | 71.05 | 82.84 | 77.72 | 72.24 |
| QwenVLM-72B | 57.99 | 87.87 | 73.35 | 34.48 | 75.91 | 80.86 | 84.16 | 70.66 |

### 4.4. Error Analysis (GPT-4o)

| Loại lỗi | Tỷ lệ | Mô tả |
|-----------|--------|-------|
| **Lack of Knowledge** | 51.92% | Không mô tả đúng triệu chứng hoặc thiếu domain knowledge |
| **Perceptual Error** | 32.69% | Không nhận ra chi tiết trong ảnh, hallucination |
| Reasoning Error | 7.6% | Mô tả đúng nhưng so sánh/kết luận sai |
| Other (Shortcut, Double Answer, Reject) | 7.79% | Các lỗi khác |

### 4.5. Ablation Studies

- **Text-only**: Performance gần random cho DID/PID/WID, nhưng DMN/CMN/TM vẫn cao hơn random → nhiều disease management strategies có pattern chung.
- **Chain-of-Thought (CoT)**: Cải thiện nhẹ nhưng bão hòa ở 3-shot. Hiệu quả nhất cho PID và WID.

---

## 5. Điểm mạnh & Điểm yếu

### Điểm mạnh

1. **Expert annotation**: Tất cả QA đều do chuyên gia nông nghiệp tạo, không phụ thuộc GPT-generated answers → đáng tin cậy cao.
2. **Phạm vi rộng nhất**: 682 disease categories, 203 crop categories — vượt xa tất cả benchmarks hiện tại.
3. **7 tasks đa dạng**: Không chỉ identification mà còn management (DMN, CMN, TM, MQA) — đánh giá khả năng tư vấn thực tế.
4. **Context-dependent answers**: DMN có đáp án thay đổi theo severity — phản ánh thực tế nông nghiệp.
5. **Error analysis chi tiết**: Phân loại lỗi giúp định hướng cải thiện VLMs rõ ràng.
6. **Text-only ablation**: Chứng minh câu hỏi thực sự cần visual information.

### Điểm yếu

1. **Quy mô nhỏ**: Chỉ 4,342 QA pairs / 3,745 images — không đủ cho training, chỉ evaluation.
2. **Không có multi-turn QA**: Chỉ single question-answer, không có follow-up hay conversation.
3. **Không có regional/geographical specificity**: Không phân biệt bệnh/giải pháp theo vùng địa lý hay khí hậu.
4. **Chỉ tiếng Anh**: Không đa ngôn ngữ, không có annotation cho ngôn ngữ địa phương.
5. **Single annotator chính**: Chỉ 1 annotator chính (Ph.D. Agriculture), validation bởi nhóm nhỏ → có thể bias.
6. **Multiple-choice format giới hạn**: Không đánh giá được khả năng sinh câu trả lời tự nhiên (open-ended).
7. **Human baseline thấp bất thường**: Human accuracy chỉ 36.79% (thấp hơn nhiều models) → có thể do thiết kế thí nghiệm (sinh viên, không được dùng internet).

---

## 6. So sánh với dự án Vietnamese Agriculture QA

### 6.1. Context sensitivity và regional specificity

AgroBench **không** xử lý regional specificity. Các câu hỏi mang tính generic global — không phân biệt giải pháp theo vùng Đồng bằng sông Cửu Long vs. Tây Nguyên. Tuy nhiên, task DMN có **severity-dependent answers** — đây là một dạng context sensitivity đáng học hỏi. Dự án của chúng ta có lợi thế lớn: context-aware QA với regional specificity là **gap rõ ràng** mà AgroBench chưa đề cập.

### 6.2. Multi-turn QA

AgroBench chỉ có **single-turn QA** (1 question → 1 answer). Không có follow-up questions hay dialogue. Dự án của chúng ta nếu thiết kế multi-turn QA sẽ là **contribution khác biệt** so với benchmark này.

### 6.3. Advisory quality vs. Recognition/Classification

AgroBench là benchmark hiếm hoi đánh giá **cả advisory quality** (DMN, CMN, TM) chứ không chỉ recognition. Các tasks DMN/CMN yêu cầu VLMs tư vấn giải pháp quản lý bệnh, quản lý cây trồng — rất gần với mục tiêu dự án. Tuy nhiên, format multiple-choice 5 lựa chọn **giới hạn** khả năng đánh giá chất lượng tư vấn thực sự (so với open-ended evaluation). Dự án của chúng ta đánh giá advisory quality ở dạng open-ended → đánh giá sâu hơn.

### 6.4. Dataset construction quality standards

Bài học từ AgroBench:
- **Expert annotation > Synthetic**: AgroBench chứng minh expert-annotated data đáng tin cậy hơn GPT-generated data (Table 1 so sánh).
- **Misleading answer design**: Đáp án sai được thiết kế cẩn thận (diseases tương tự, pests cùng cây) → tăng tính discriminative.
- **Text-only ablation**: Quan trọng để verify câu hỏi thực sự cần context/image → chúng ta cũng nên verify câu hỏi cần context-awareness.
- **Context-dependent answers**: Cùng bệnh nhưng severity khác → đáp án khác. Chúng ta nên áp dụng: cùng bệnh nhưng vùng/mùa/giống khác → khuyến nghị khác.
- **Man-hours tracking**: 150 man-hours cho 4,342 QAs cho thấy expert annotation rất tốn kém → cần plan resource cẩn thận.

---

## 7. Bài học rút ra

1. **Expert annotation là gold standard**: AgroBench khẳng định giá trị của expert-annotated data so với synthetic data. Dự án nên duy trì chất lượng expert review, đặc biệt cho ground truth answers.

2. **Advisory tasks tạo differentiation**: DMN, CMN, TM là các tasks AgroBench nổi bật — và cũng gần nhất với dự án của chúng ta. Tuy nhiên, AgroBench bị giới hạn bởi multiple-choice format. Dự án text-based, open-ended QA có thể đánh giá advisory quality sâu hơn.

3. **Gap rõ ràng = Opportunity**: AgroBench không có regional specificity, multi-turn QA, hay ngôn ngữ địa phương. Đây đều là contributions tiềm năng của dự án.

4. **Error analysis framework**: Phân loại lỗi thành Lack of Knowledge / Perceptual Error / Reasoning Error rất hữu ích. Chúng ta có thể áp dụng framework tương tự cho text-based errors: Knowledge Error / Context Misunderstanding / Reasoning Error / Hallucination.

5. **Severity-dependent answers**: Ý tưởng cùng bệnh nhưng severity khác → tư vấn khác rất hay. Mở rộng: cùng bệnh nhưng **region khác / season khác / crop variety khác** → tư vấn khác. Đây chính là context-awareness mà dự án hướng đến.

6. **Baseline thiết kế quan trọng**: AgroBench có random baseline, human baseline, text-only baseline. Dự án nên có các baseline tương tự: random, human expert, no-context LLM, context-aware LLM.
