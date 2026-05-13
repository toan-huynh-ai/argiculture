# Phân tích: DG-EVAL — Fine-Tuning and Evaluating Conversational AI for Agricultural Advisory

---

## 1. Thông tin cơ bản

| Mục | Chi tiết |
|-----|---------|
| **Tiêu đề** | Fine-Tuning and Evaluating Conversational AI for Agricultural Advisory |
| **Tác giả** | Sanyam Singh, Naga Ganesh, Vineet Singh, Lakshmi Pedapudi, Ritesh Kumar, SSP Jyothi, Archana Karanam, Waseem Pasha, Ekta Kumari, C. Yashoda, Mettu Vijaya Rekha Reddy, Shesha Phani Debbesa, Chandan Dash |
| **Đơn vị** | Digital Green |
| **Venue** | Preprint (arXiv:2603.03294v2) |
| **Năm** | March 2026 |

---

## 2. Tóm tắt

Paper đề xuất một **hybrid LLM architecture** tách riêng hai chức năng: (1) **factual retrieval** qua fine-tuning với LoRA trên expert-curated GOLDEN FACTS (atomic, verified units of knowledge), và (2) **conversational delivery** qua stitching layer. Đóng góp quan trọng nhất là **DG-EVAL** — một evaluation framework 3 cấp thực hiện **atomic fact verification** dựa trên expert-curated ground truth (thay vì Wikipedia hoặc retrieved documents). Fine-tuning cải thiện fact recall từ 26.2% → 50.3% và F1 từ 37.2% → 51.8% (GPT-4o Mini), đồng thời giảm chi phí 85% so với GPT-4.

---

## 3. Vấn đề nghiên cứu

Ba vấn đề hệ thống khi dùng LLM cho nông nghiệp:

1. **Hallucination risk**: LLM fabricate lời khuyên sai nhưng trông credible → hậu quả trực tiếp (sai liều thuốc, thời điểm trồng, nhận dạng bệnh)
2. **Lack of specificity**: Model cho lời khuyên generic ("bón phân hợp lý") thay vì actionable ("bón 120 kg Urea/hectare lúc 21 và 45 ngày sau cấy")
3. **Tone mismatch**: LLM tạo phản hồi quá formal, không phù hợp với nông dân quy mô nhỏ → thiếu trust

**Evaluation gap**: Các framework hiện tại (FActScore, RAGAS, TruthfulQA) verify dựa trên Wikipedia, web search, hoặc retrieved context → **không capture được expert-curated, safety-critical ground truth** cần thiết cho domain chuyên biệt.

---

## 4. Phương pháp

### 4.1. Data Curation Pipeline

**a) Human Expert Curation:**
- 25,000+ query-answer pairs reviewed across India, Kenya, Ethiopia, Nigeria
- 11,966 validated pairs sử dụng trong paper
- Platform: evaluate.farmer.chat
- 4 domain experts (5-10 năm kinh nghiệm), bilingual Hindi-English
- Quality assurance: 20% double-review, 10% control pairs, red-line protocol (ban banned pesticides, safety violations)

**b) GOLDEN FACT Extraction (3-step pipeline):**
1. **Semantic grouping**: Merge equivalent recommendations → giảm ~15% duplicates
2. **Contradiction detection**: Phát hiện conflicting dosages, timing, polarities; high-severity → expert re-review
3. **Finalization**: Tạo minimal atomic statements, mỗi fact chứa đúng 1 actionable recommendation

**Ví dụ decomposition:**
- Input: "Apply 120 kg of Urea per hectare in two split doses at 21 and 45 days after transplanting"
- Output:
  - Fact 1: Apply 120 kg of Urea per hectare for rice crop
  - Fact 2: Split Urea application into two doses
  - Fact 3: Apply first dose at 21 days after transplanting
  - Fact 4: Apply second dose at 45 days after transplanting

**c) Synthetic Data Augmentation:**
- 5 sources: Document RAG, Video RAG, LLM synthesis, Web search, Cross-source synthesis
- Quality scoring: confidence × completeness × actionability
- Source traceability preserved

### 4.2. Hybrid Engine Architecture

**Stage 1: Fine-Tuning for Fact Retrieval**
- SFT + LoRA (rank r=8, α=16, targeting QKV projections)
- Training: 3 epochs, cosine LR schedule, peak lr = 2×10⁻⁴, batch size 16
- Models: GPT-4o Mini, Llama 3 8B
- Objective: output structured GOLDEN FACTS cho mỗi farmer query

**Stage 2: Stitching Layer**
- LLM riêng biệt chuyển dry facts → natural, farmer-appropriate response
- Implement FARMER.CHAT persona: culturally appropriate, safety precautions, structured
- Không thêm thông tin ngoài provided facts
- Test 3 stitching models: Llama 3.2 3B, GPT-4.1 Nano, Gemma 3n E4B

### 4.3. DG-EVAL Framework (3 Levels)

**Level 1 — Intrinsic Quality (không cần reference):**
- **Specificity**: 7 contextual anchors (Actionable, Entity, Location, Time, Quantity, Conditional, Mechanistic)
- **Conversationality**: 6-dimension LLM-as-Judge scoring (1-5): content quality, communication style, practical advice, safety, conversation flow, response format

**Level 2 — Query Alignment:**
- **Relevance**: Câu trả lời có address đúng câu hỏi của nông dân không? (1-10 scale)

**Level 3 — Ground Truth Alignment (quan trọng nhất):**
- **Fact Recall**: Bao nhiêu GOLDEN FACTS xuất hiện trong response (completeness)
- **Fact Precision**: Bao nhiêu generated facts match với GOLDEN FACTS (correctness)
- **F1**: Harmonic mean of recall & precision
- **Contradiction Detection**: Phát hiện conflicts trong numeric values, polarities, method compatibilities

---

## 5. Evaluation

### 5.1. Setup
- Train/Val/Test: 9572 / 1197 / 1197 (stratified by crop type, query category)
- 10 baseline models + fine-tuned variants
- Evaluation judge: GPT-4o

### 5.2. Kết quả chính — Ground Truth Alignment

| Model | Recall | Precision | F1 | Relevance | Conv. |
|-------|--------|-----------|-----|-----------|-------|
| GPT-4 (baseline) | 26.6% | 62.2% | 37.3% | 96.9% | 4.01 |
| GPT-4o (baseline) | 28.5% | 64.9% | 39.6% | 95.8% | 3.95 |
| GPT-4o Mini (baseline) | 26.2% | 64.3% | 37.2% | 94.4% | 3.98 |
| Llama 3 8B (baseline) | 13.4% | 94.2% | 23.5% | 98.3% | 3.72 |
| Gemini 1.5 Pro (baseline) | 42.1% | 56.2% | 48.2% | 92.6% | — |
| Qwen 3 235B | 19.2% | 31.3% | 23.8% | 55.9% | — |
| Kimi K2 | 18.7% | 27.1% | 22.1% | 50.2% | — |
| **GPT-4o Mini FT (12k)** | **50.3%** | **53.3%** | **51.8%** | 87.5% | — |
| **GPT-4o FT (130k)** | **58.7%** | **54.9%** | **56.7%** | 90.9% | — |

**Key findings:**
- Fine-tuning cải thiện recall +22-25 pp với trade-off precision giảm ~11 pp
- Recall quan trọng hơn precision trong agricultural advisory (bỏ sót safety info nguy hiểm hơn dư thông tin)
- Model lớn (Qwen 3 235B, Kimi K2) cho kết quả kém hơn fine-tuned model nhỏ → **domain-specific FT > model scale**

### 5.3. Stitching Layer Quality

| Stitching Model | Conversationality | Safety | Improvement |
|----------------|-------------------|--------|-------------|
| No stitching | — | 3.44 | — |
| Llama 3.2 3B | 4.21 | 3.72 | +8.1% |
| GPT-4.1 Nano | 4.54 | 3.98 | +15.7% |
| Gemma 3n E4B | 4.62 | 4.10 | +19.2% |

### 5.4. Cost-Performance Trade-offs

| Model | Relative Cost | F1 |
|-------|--------------|-----|
| GPT-4 | 1.00× | 37.3% |
| GPT-4o Mini FT | 0.15× | 51.8% |
| Gemma 2 9B | 0.004× | 39.1% |

→ GPT-4o Mini FT: **85% cost reduction + 14.5 pp F1 gain**

### 5.5. Ablation: Training Data Scale

| Training Data | Recall | Precision | F1 |
|--------------|--------|-----------|-----|
| Baseline (no FT) | 26.2% | 64.3% | 37.2% |
| 12k Human Curated | 50.3% | 53.3% | 51.8% |
| 42k Mixed | 39.3% | 54.0% | 45.5% |
| 62k Mixed | 23.6% | 54.9% | 33.0% |
| 130k Mixed | 43.8% | 66.2% | 52.7% |

→ **Quality-quantity threshold effect**: 12k curated > 62k mixed, nhưng 130k mixed ≥ 12k curated

### 5.6. Framework Comparison

| Aspect | FActScore | RAGAS | DG-EVAL |
|--------|----------|-------|---------|
| Analysis unit | Atomic facts | Sentences | GOLDEN FACTS |
| Knowledge source | Wikipedia | Retrieved context | Expert-curated |
| Safety detection | None | None | Contradiction detection |
| Agricultural test (4o Mini Vanilla) | 0.72 | 0.81 | 37.2% |
| Agricultural test (4o Mini FT) | 0.68 | 0.76 | 51.8% |

→ FActScore và RAGAS **không phát hiện cải thiện** từ fine-tuning trên agricultural domain — DG-EVAL phát hiện được.

### 5.7. Human Evaluation
- 4 agronomists, 308 queries, blind pairwise preference
- Fine-tuned GPT-4o Mini được prefer 65.9% (p < 0.001)

---

## 6. Điểm mạnh & Điểm yếu

### Điểm mạnh
- **Evaluation framework rất mạnh (DG-EVAL)**: 3 levels, atomic fact verification, contradiction detection — vượt trội hơn FActScore và RAGAS cho domain-specific tasks
- **GOLDEN FACTS concept xuất sắc**: Decomposition thành atomic, verifiable units — methodology rõ ràng, reproducible
- **Comprehensive benchmarking**: 10 baseline models, fine-tuned variants, ablation studies, human evaluation, cost analysis
- **Practical impact**: Deployed trên FARMER.CHAT (1.5M+ queries, 4 countries)
- **Open-source**: Release code (farmerchat-prompts), 2 public datasets trên HuggingFace
- **Rigorous quality assurance**: 20% double-review, 10% control pairs, red-line protocol, inter-reviewer agreement reporting
- **Cost analysis thuyết phục**: 85% cost reduction quantified rõ ràng

### Điểm yếu
- **Không so sánh trực tiếp với RAG**: Tự nhận limitation — chỉ test fine-tuning path, không có head-to-head FT vs. RAG
- **Bias trong evaluation**: Dùng GPT-4o làm judge khi GPT-4o variants cũng được evaluate
- **Single geography**: Focus Bihar, India — chưa chứng minh transferability
- **Human evaluation design**: Forced-choice (không có "no preference"), không compute inter-annotator agreement (Cohen's κ)
- **Contradiction detection hạn chế**: Chỉ detect explicit conflicts (dosage, polarity), có thể miss subtle semantic contradictions
- **Temporal knowledge decay**: Không model knowledge freshness — lời khuyên có thể outdated

---

## 7. So sánh với dự án Vietnamese Agriculture QA

### Có thể áp dụng atomic fact verification methodology không?

**Rất phù hợp — đây là paper có methodology closest với nhu cầu của dự án:**

| Khía cạnh | DG-EVAL | Dự án của chúng ta |
|-----------|---------|-------------------|
| **Paper type** | Hybrid (systems + evaluation) | Benchmark paper (evaluation focus) |
| **Evaluation approach** | Atomic fact verification vs. expert ground truth | Expert evaluation + automated metrics |
| **Ground truth** | GOLDEN FACTS (expert-curated) | Expert annotations (Vietnamese agriculture) |
| **Geographic focus** | Bihar, India (smallholder farmers) | Vietnam (diverse regions) |
| **Language** | English, Hindi | Vietnamese |
| **Scale** | 11,966 validated QA pairs | Target 1000+ questions |
| **Safety mechanism** | Contradiction detection | Feasibility score |

### So sánh expert evaluation pipeline:

| DG-EVAL Pipeline | Dự án chúng ta |
|-----------------|----------------|
| 4 domain experts | Target 3+ experts (multi-region) |
| evaluate.farmer.chat platform | Custom annotation tool |
| 5-point Likert × 5 dimensions | Multi-dimensional scoring |
| 20% double-review, 10% control | Inter-Annotator Agreement (Krippendorff's α) |
| Red-line protocol (banned pesticides) | Feasibility score (broader safety concept) |
| Pairwise preference evaluation | Planned but not yet implemented |

### Tương đồng quan trọng:
1. **Expert-curated ground truth**: Cả hai đều nhấn mạnh rằng Wikipedia/web search **không đủ** cho domain chuyên biệt
2. **Atomic decomposition**: DG-EVAL phân tách câu trả lời thành atomic facts → chúng ta có thể áp dụng tương tự
3. **Safety as explicit dimension**: DG-EVAL có contradiction detection; chúng ta có feasibility score
4. **Regional specificity matters**: DG-EVAL nhận ra Bihar-specific knowledge khác India general → tương tự Bắc-Trung-Nam Việt Nam

### Điểm khác biệt tạo novelty cho dự án chúng ta:
1. **Context degradation protocol**: DG-EVAL không có concept này — chúng ta đánh giá LLMs dưới các mức context khác nhau (full → partial → none). Đây là **novel contribution** rõ ràng.
2. **Feasibility score vs. contradiction detection**: DG-EVAL chỉ detect contradictions (binary: có/không mâu thuẫn). Feasibility score rộng hơn: đánh giá tính khả thi thực tế (chi phí, kỹ thuật, thời vụ, điều kiện địa phương). Đây là **novel contribution** thứ hai.
3. **Vietnamese language**: Không có benchmark nào cho Vietnamese agriculture QA → language gap rõ ràng.
4. **Multi-region evaluation**: DG-EVAL chỉ Bihar; chúng ta target nhiều vùng → regional variation analysis.

### Cơ hội áp dụng từ DG-EVAL:
1. **GOLDEN FACTS methodology** → áp dụng cho Vietnamese Golden Facts
2. **7 contextual anchors cho specificity** → có thể dùng trực tiếp hoặc adapt
3. **3-level evaluation hierarchy** → framework tham khảo cho evaluation pipeline
4. **Contradiction detection prompts** → adapt cho pesticide/fertilizer recommendations tiếng Việt
5. **Quality-quantity trade-off findings** → inform data curation strategy

---

## 8. Bài học rút ra

### Nên áp dụng (ưu tiên cao):
1. **Atomic fact decomposition**: Phân tách ground truth answers thành GOLDEN FACTS → evaluation chính xác hơn holistic scoring. Đây có thể là phương pháp evaluation chính cho benchmark.
2. **3-level evaluation framework**: Intrinsic quality → Query alignment → Ground truth alignment. Cấu trúc rõ ràng, dễ explain cho reviewers.
3. **Specificity scoring (7 contextual anchors)**: Đặc biệt phù hợp cho nông nghiệp — đánh giá xem lời khuyên có actionable, entity-specific, location-specific, time-specific, quantity-specific không.
4. **Contradiction detection**: Phát hiện lời khuyên mâu thuẫn (liều lượng, phương pháp, thời điểm) — safety-critical cho nông nghiệp Việt Nam.
5. **Cost-performance analysis**: Rất thuyết phục cho reviewers — chứng minh practical impact.
6. **Open-source release**: Release dataset + code + prompts → tăng impact và reproducibility.

### Nên áp dụng (ưu tiên trung bình):
7. **Quality assurance mechanisms**: Double-review, control pairs, adjudication protocol → strengthen annotation quality claims
8. **Crop-topic analysis**: Phân tích F1 theo crop × topic matrix → reveal strengths/weaknesses per domain
9. **Human pairwise preference evaluation**: Bổ sung cho automated metrics

### Nên tránh:
1. **Không so sánh FT vs. RAG**: Paper tự nhận là limitation → chúng ta nên include cả hai approaches (hoặc justify rõ tại sao chỉ focus một approach)
2. **LLM-as-Judge bias**: Dùng GPT-4o judge GPT-4o variants → cần dùng judge khác hoặc acknowledge bias
3. **Forced-choice preference**: Nên có "no preference" option trong human evaluation
4. **Single-geography**: Chúng ta cần multi-region từ đầu, không để thành limitation
