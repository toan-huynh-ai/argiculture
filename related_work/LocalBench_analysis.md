# Phân tích: LOCALBENCH — Benchmarking LLMs on County-Level Local Knowledge and Reasoning

---

## 1. Thông tin cơ bản

- **Tiêu đề:** LOCALBENCH: Benchmarking LLMs on County-Level Local Knowledge and Reasoning
- **Tác giả:** Zihan Gao, Yifei Xu (UCLA), Jacob Thebault-Spieker (University of Wisconsin-Madison)
- **Venue:** AAAI 2026
- **Năm:** 2025 (arXiv: 2511.10459v2, Nov 2025)
- **Dataset:** https://github.com/MadCollab/LocalBench

---

## 2. Tóm tắt

LOCALBENCH là benchmark đầu tiên đánh giá khả năng LLMs xử lý kiến thức địa phương cấp county (huyện) tại Mỹ. Dựa trên Localness Conceptual Framework, dataset gồm 14,782 cặp QA đã validate trên 526 counties thuộc 49 bang, tích hợp dữ liệu Census, subreddit địa phương và tin tức khu vực. Benchmark đánh giá 13 LLMs trong cả chế độ closed-book và web-augmented. Kết quả cho thấy: model tốt nhất chỉ đạt 56.8% accuracy cho narrative questions và <15.5% cho numerical reasoning. Web augmentation giúp Gemini (+13.6%) nhưng làm giảm hiệu suất GPT (-11.4%).

---

## 3. Phương pháp

### Conceptual Framework
- Dựa trên **Localness Conceptual Framework** với 3 domain:
  - **Physical:** Tương tác trực tiếp với nơi ở (Place Interaction, Temporal Presence)
  - **Cognitive:** Hiểu biết văn hóa và môi trường (Cultural Understanding, Environmental Cognition, Local Knowledge)
  - **Relational:** Kết nối cảm xúc và cộng đồng (Emotional Connection, Social/Community Engagement)
- 7 dimensions, 88 subcomponents

### Data Sources (3 nguồn)
1. **U.S. Census + USDA + National Register:** 34 localness indicators → 6,120 QA pairs (numerical + comparison)
2. **Local Subreddits:** Jan 2024 - Mar 2025, top-50 comments per thread → 4,000 QA pairs (narrative)
3. **NELA-Local corpus:** 1.4M+ local news articles (2020-2021) → 4,662 QA pairs (governance, civic)

### QA Generation Pipeline (4 bước)
1. **Raw Generation:** OpenAI o3 (temp=0.7, top-p=0.9, max 200 tokens), 1-3 QAs/document
2. **Multi-Rule Filter:** GPT-4o-mini fine-tuned via DPO (473 human-annotated samples, κ=0.84), 9 quality criteria
3. **Feedback-Driven Refinement:** Lặp lại tối đa 3 lần cho QA pairs bị reject → retain 95.2% non-census pairs
4. **Localness Attribute Classification:** o3 model gán labels theo 4 hierarchical levels (domain → dimension → component → subcomponent)

### Human Verification
- Quality filter validation: 500 samples, κ=0.78, F1=0.94
- Classification validation: 200 samples, κ=0.87, precision 94.2% (domain-level: 98.5%)

### Evaluation Setup
- **13 LLMs:** GPT-4o, GPT-4.1, Gemini-2.5-Pro/Flash, Claude-4-Sonnet, Claude-3.7-Sonnet, Qwen3 (8B/14B/32B/30B-A3B/235B-A22B)
- **Web-augmented:** GPT-4.1+Web, Gemini-2.5-Pro+Grounding
- **Metrics:** Exact Match, ROUGE-1 F1, Semantic Match (embedding cosine), GPT Judge (GPT-4o-mini binary), Numerical Accuracy (<2% relative error), Answer Rate

---

## 4. Kết quả chính

### Non-Numerical QA
| Model | GPT Judge Acc. | Ans Rate |
|-------|---------------|----------|
| Gemini-2.5-Pro+Grounding | **56.8%** | 91.7% |
| Gemini-2.5-Pro | 52.5% | 100% |
| GPT-4.1 | 47.0% | 100% |
| Claude-3.7-Sonnet | 43.7% | 100% |
| GPT-4o | 32.8% | 99.6% |
| Qwen3-30B-A3B | 28.0% | 99.7% |

### Numerical QA
| Model | Accuracy | Ans Rate |
|-------|----------|----------|
| GPT-4.1+Web | **15.5%** | 92.0% |
| Gemini-2.5-Pro | 12.8% | 100% |
| Claude-Sonnet-4 | 7.1% | 97.3% |
| GPT-4.1 | 6.2% | 100% |
| Qwen3-30B-A3B | 2.2% | 100% |

### Key Findings
- **Web augmentation paradox:** Gemini +13.6% nhưng GPT -11.4% → model-dependent
- **Scaling không giúp:** Qwen3-235B (MoE) kém hơn Qwen3-32B (non-MoE) cho local knowledge
- **Numerical reasoning cực yếu:** Tất cả models <15.5% — architectural limitation, không phải knowledge gap
- **Answer rate giảm với web search:** Cả GPT-4.1+Web (92.9%) và Gemini+Grounding (91.7%) giảm so với base (100%)

---

## 5. Điểm mạnh & Điểm yếu

### Điểm mạnh
- **Framework-grounded:** Dựa trên Localness Conceptual Framework (không ad-hoc) — có lý thuyết backing
- **Multi-source data:** Census (structured) + Reddit (unstructured) + News (semi-structured) → phong phú
- **Robust validation:** Human verification ở cả 2 stages (quality filter: F1=0.94, classification: precision=94.2%)
- **Comprehensive evaluation:** 13 models × closed/web-augmented × multiple metrics × 3 runs
- **Insightful findings:** Web augmentation paradox và scaling failure là contributions mạnh
- **Reproducible:** Dataset public trên GitHub, evaluation protocol rõ ràng
- **Geographic equity lens:** Stratified sampling (urban/suburban/rural) đảm bảo diversity, Moran's I test cho spatial autocorrelation

### Điểm yếu
- **US-only:** Chỉ cho US counties — không generalize sang các quốc gia khác (tác giả thừa nhận)
- **English-only:** Bỏ qua multilingual communities — hạn chế cho immigrant/Indigenous populations
- **Temporal mismatch risk:** Data 2020-2025 nhưng LLM knowledge cutoffs khác nhau
- **Census QA quá khô khan:** Numerical questions ("What is the median household income in County X?") không test reasoning thực sự
- **Reddit data biased:** Counties không có subreddit active bị underrepresented
- **GPT Judge dependency:** 96% agreement với human annotators nhưng vẫn có 4% sai — đặc biệt cho culturally nuanced questions

---

## 6. So sánh với dự án Vietnamese Agriculture QA

### Cách LocalBench xử lý county-level local knowledge

**1. Stratified sampling theo geography:**
- LOCALBENCH chia counties thành urban (RUCC 1-3), suburban (4-6), rural (7-9), sample đều 60 counties/group
- **Áp dụng cho dự án VN:** Chia câu hỏi theo 5 macro regions (ĐBSH, ĐBSCL, Tây Nguyên, Bắc Trung Bộ, Đông Nam Bộ) + stratified sampling đảm bảo coverage cân bằng

**2. Multi-source data integration:**
- Census (structured) + Reddit (narrative) + News (events) → 3 loại knowledge khác nhau
- **Áp dụng cho dự án VN:** 
  - Structured: Thống kê nông nghiệp MARD, danh mục thuốc BVTV Cục BVTV
  - Narrative: Forum nông dân (agriviet.com, nongdan.vn), Facebook groups
  - Events: Tin tức về dịch bệnh, thời tiết cực đoan, chính sách mới

**3. QA Generation Pipeline có thể tham khảo:**
- Raw Generation (reasoning model) → Multi-Rule Filter (DPO-tuned classifier) → Feedback-Driven Refinement → Classification
- **Áp dụng cho dự án VN:** Dùng pipeline tương tự nhưng thay Census metrics bằng agricultural indicators (tỷ lệ sử dụng phân bón, diện tích lúa, mật độ canh tác)

**4. 9 Quality Criteria đáng tham khảo:**
1. Single Factual Answer
2. Geographic Grounding (→ phải gắn với province/region cụ thể)
3. Subjectivity Detection
4. Privacy Compliance
5. Safety Compliance (→ đặc biệt quan trọng cho advice thuốc BVTV)
6. Temporal Consistency (→ mùa vụ phải đúng)
7. Difficulty Assessment
8. Question Clarity
9. Answer Completeness

**5. Evaluation framework cho regional knowledge:**
- LOCALBENCH test 3 loại: numerical (statistics), comparison (2 counties), narrative (cultural/events)
- **Áp dụng cho dự án VN:** 
  - Factual: "Thuốc nào trị bệnh đạo ôn trên lúa?" 
  - Regional: "So sánh lịch mùa vụ lúa giữa ĐBSH và ĐBSCL"
  - Contextual: "Giống lúa nào phù hợp đất phèn ở ĐBSCL?"
  - Practical: "Liều lượng Tricyclazole cho 1 hecta ruộng?"

**6. Web augmentation paradox:**
- LOCALBENCH cho thấy web search KHÔNG nhất thiết cải thiện performance — phụ thuộc model architecture
- **Ý nghĩa cho dự án VN:** Khi test LLMs với RAG/web access, không nên assume rằng more context = better answers. Cần test cả với và không với RAG.

**7. Numerical reasoning gap:**
- Tất cả LLMs <15.5% cho numerical questions — architectural limitation
- **Ý nghĩa cho dự án VN:** Câu hỏi về liều lượng thuốc, diện tích, năng suất, tỷ lệ phân bón sẽ là thách thức lớn cho LLMs. Đây có thể là contribution: "LLMs fail at agricultural dosage calculations"

---

## 7. Bài học rút ra

1. **Framework-based benchmark design:** LOCALBENCH thành công vì dựa trên lý thuyết (Localness Framework) chứ không phải ad-hoc question collection. Dự án VN nên có framework rõ ràng cho "agricultural knowledge dimensions" (crop knowledge, regional knowledge, seasonal knowledge, treatment knowledge, safety knowledge).

2. **Multi-source diversity là critical:** 3 nguồn data (Census + Reddit + News) cho 3 loại knowledge khác nhau. Dự án VN cũng cần nhiều nguồn: official guidelines (Cục BVTV), expert interviews, real farmer questions.

3. **Quality pipeline > manual annotation:** DPO-tuned filter (F1=0.94) gần bằng human annotation nhưng scalable hơn nhiều. Dự án VN có thể dùng approach tương tự: train small classifier trên expert-annotated samples → filter remaining data.

4. **Stratification prevents bias:** Chia đều urban/suburban/rural ensures không bị dominated bởi 1 loại county. Dự án VN cần stratify theo: region × crop type × difficulty level × question type.

5. **Publish dataset for reproducibility:** LOCALBENCH public trên GitHub → community có thể reproduce và extend. Dự án VN nên plan cho public release (sau khi paper accepted).

6. **Scaling ≠ local knowledge:** LOCALBENCH chứng minh model lớn hơn không nhất thiết biết nhiều hơn về local knowledge. Relevant cho Vietnamese agricultural context — LLMs lớn có thể biết ít về nông nghiệp Việt Nam hơn specialized models.
