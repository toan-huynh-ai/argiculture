# Phân tích: PlantVillageVQA — A Visual Question Answering Dataset for Plant Science

---

## 1. Thông tin cơ bản

| Mục | Chi tiết |
|-----|---------|
| **Title** | PlantVillageVQA: A Visual Question Answering Dataset for Benchmarking Vision-Language Models in Plant Science |
| **Authors** | Syed Nazmus Sakib, Nafiul Haque, Mohammad Zabed Hossain, Shifat E. Arman |
| **Affiliations** | Department of Robotics and Mechatronics Engineering & Department of Botany, University of Dhaka, Bangladesh |
| **Venue** | arXiv preprint (under review) |
| **Year** | 2025 |
| **ArXiv** | 2508.17117v2 |
| **Dataset** | https://huggingface.co/datasets/SyedNazmusSakib/PlantVillageVQA |

---

## 2. Tóm tắt

PlantVillageVQA là dataset VQA quy mô lớn cho chẩn đoán bệnh cây trồng, xây dựng trên bộ ảnh PlantVillage (55,448 ảnh). Dataset chứa **193,609 QA pairs** trải rộng 14 loại cây và 38 loại bệnh, với 9 loại câu hỏi ở 3 mức độ nhận thức. Điểm đặc biệt: kết hợp **template-based generation** với **multi-stage linguistic re-engineering** và **expert validation** bởi botanists, tránh hoàn toàn dùng LLM cho content generation để loại bỏ hallucination.

---

## 3. Phương pháp

### 3.1. Pipeline xây dựng dataset (2 giai đoạn)

**Giai đoạn 1: Programming-Based Data Generation**
- Khai thác cấu trúc thư mục PlantVillage (ví dụ: `Tomato____Late_blight`) để tự động extract labels (crop + disease).
- Đặt labels vào 9 loại question templates đã thiết kế sẵn.
- Kết quả: **278,255 QA pairs** ban đầu.

**Giai đoạn 2: Data Re-engineering and Refinement**

| Phase | Mô tả | Kết quả |
|-------|--------|---------|
| **Phase A: Linguistic Diversification** | Paraphrase templates (10-15 biến thể/template), expert review loại bỏ câu sai ngữ pháp | Question vocabulary tăng 359% (178 → 818 unique words) |
| **Phase B: Structural Balancing** | Targeted stratified undersampling cho binary questions bị skew (78.6% negative) | Binary ratio cải thiện thành 40/60 (Yes/No) |

### 3.2. Question Taxonomy (3 levels, 9 categories)

**Level 1: Foundational Perception and Identification**
| Category | Mục đích | Ví dụ |
|----------|----------|-------|
| Existence & Sanity Check | Lọc ảnh không liên quan | "What is the primary subject of this image?" |
| Plant Species Identification | Phân loại species | "Is this a [Crop] leaf?" |
| General Health Assessment | Đánh giá sức khỏe binary | "Is the plant healthy?" |

**Level 2: Detailed Analysis and Verification**
| Category | Mục đích | Ví dụ |
|----------|----------|-------|
| Visual Attribute Grounding | Nhận diện triệu chứng visual cụ thể | "Does the leaf exhibit dark, concentric 'bullseye' rings?" |
| Detailed Verification | Kết hợp species + disease identification | "Is this [Crop] leaf infected with [Disease]?" |

**Level 3: Higher-Order Reasoning and Inference**
| Category | Mục đích | Ví dụ |
|----------|----------|-------|
| Specific Disease Identification | Chẩn đoán open-ended | "Please provide a diagnosis for the condition shown." |
| Comprehensive Description | Mô tả tổng hợp | "Provide a full description of the plant and its condition." |
| Causal Reasoning | Suy luận nguyên nhân | "What is the cause of the unhealthy appearance?" |
| Counterfactual Reasoning | Suy luận phản thực | "If this plant were healthy, what visual features would be different?" |

### 3.3. Validation Pipeline

1. **Expert Review Phase 1**: Web interface cho botanists review toàn bộ dataset → phát hiện counterfactual answers bị lỗi (generic fallback 55.03%).
2. **Hierarchical Correction Pipeline**: Sửa 27,242 counterfactual QA pairs → chỉ giữ lại 9,981 verifiable pairs, xóa 17,261 non-verifiable. Generic answers giảm 90%.
3. **Automated Quality Evaluation**: 3 metrics tự động trên toàn bộ 193,609 QAs:
   - Relative Simplicity (unique words per QA vs. category mean)
   - Vagueness Score (TF-IDF weights)
   - Semantic Similarity (sentence-transformer cosine similarity)
   - → Flagged 25,418 QA pairs.
4. **Expert Review Phase 2**: Botanists review 2,837 flagged samples → chỉ discard 91 → **97.57% correctness rate**.

### 3.4. Dataset Scale

| Metric | Giá trị |
|--------|---------|
| Tổng QA pairs | 193,609 |
| Images | 55,448 |
| Crop species | 14 |
| Disease classes | 38 |
| Question categories | 9 |
| Cognitive levels | 3 |
| Format | CSV + JSON |
| Split | Train / Test |

---

## 4. Evaluation

### 4.1. Models đánh giá

- **CLIP** (contrastive learning, 400M params)
- **LXMERT** (cross-modality encoder, transformer-based)
- **FLAVA** (foundational language and vision alignment)

### 4.2. Metrics

| Metric | Áp dụng cho |
|--------|-------------|
| Accuracy | Binary/single-word answers (Existence, Health Assessment, Disease ID) |
| BLEU | Descriptive answers (Comprehensive Description, Causal/Counterfactual Reasoning) |
| METEOR | Comprehensive Description, Causal Reasoning |
| ROUGE-1/ROUGE-L | Comprehensive Description, Visual Attribute Grounding |

### 4.3. Kết quả chính

| Model | Accuracy | BLEU | METEOR | ROUGE-1 | ROUGE-L |
|-------|----------|------|--------|---------|---------|
| FLAVA | 0.3432 | 0.0909 | 0.2066 | 0.3826 | 0.3764 |
| **CLIP** | **0.6148** | **0.2452** | **0.4476** | **0.7323** | **0.7153** |
| LXMERT | 0.6034 | 0.2382 | 0.4393 | 0.7219 | 0.7061 |

**Phân tích**:
- CLIP đạt accuracy cao nhất (0.6148) — vượt xa random nhưng không hoàn hảo → dataset có learnable patterns nhưng đủ challenging.
- ROUGE-1 > 0.72: models capture keywords tốt, nhưng BLEU thấp (0.24): struggling với semantic variety.
- Models mạnh ở binary/identification questions, yếu ở causal/counterfactual reasoning.

---

## 5. Điểm mạnh & Điểm yếu

### Điểm mạnh

1. **Quy mô lớn**: 193,609 QA pairs — lớn nhất trong các agriculture VQA datasets expert-verified.
2. **No LLM dependency**: Hoàn toàn tránh dùng LLM cho content generation → eliminates hallucination risk, mọi thứ traceable và reproducible.
3. **Multi-level cognitive taxonomy**: 3 levels từ perception → analysis → reasoning cho phép đánh giá chi tiết capabilities.
4. **Rigorous validation**: 2 phases expert review + automated quality evaluation → 97.57% correctness rate.
5. **Linguistic diversity pipeline**: Re-engineering tăng vocabulary 359% → dataset không bị repetitive.
6. **Transparent methodology**: Mọi bước đều documented, reproducible, không black-box.

### Điểm yếu

1. **Chỉ dựa trên PlantVillage**: Ảnh lab-controlled, uniform background → không phản ánh điều kiện ngoài đồng thực tế.
2. **Phạm vi hạn chế**: Chỉ 14 crop species và 38 diseases (PlantVillage cố định).
3. **Chỉ leaf-based**: Chỉ ảnh lá, không có ảnh quả, thân, rễ, hay toàn cây.
4. **Template-based generation**: Dù đã paraphrase, bản chất vẫn là template → thiếu tính tự nhiên so với human-written questions.
5. **Models benchmark cũ**: CLIP, LXMERT, FLAVA — không benchmark với GPT-4o, Gemini, LLaVA-NeXT hay các VLMs mới nhất.
6. **Không có advisory/management**: Chỉ tập trung identification và reasoning, không có tư vấn xử lý bệnh.
7. **Tiếng Anh only**: Không đa ngôn ngữ.

---

## 6. So sánh với dự án Vietnamese Agriculture QA

### 6.1. Context sensitivity và regional specificity

PlantVillageVQA **hoàn toàn không** xử lý context sensitivity hay regional specificity. Dataset mang tính "universal" — một câu trả lời cho mọi vùng. Câu hỏi chỉ hỏi "bệnh gì?" chứ không hỏi "ở vùng này nên xử lý như thế nào?". Đây là **gap lớn** mà dự án có thể khai thác: context-aware answers phụ thuộc vùng miền, mùa vụ, giống cây cụ thể.

### 6.2. Multi-turn QA

PlantVillageVQA chỉ có **single-turn QA**. Mỗi image có nhiều questions nhưng chúng **independent** — không có dialogue flow hay follow-up. Không có scenario: "Bệnh gì?" → "Nên xử lý sao?" → "Thuốc nào phù hợp vùng ĐBSCL?" Dự án multi-turn QA sẽ là contribution mới hoàn toàn so với paper này.

### 6.3. Advisory quality vs. Recognition/Classification

PlantVillageVQA tập trung **hoàn toàn vào recognition/classification**:
- Level 1: Identification (species, health status)
- Level 2: Verification (visual symptoms)
- Level 3: Reasoning (diagnosis, causation)

**Không có advisory component**: Không tư vấn cách xử lý, phòng trị, hay quản lý. Dự án đánh giá advisory quality là **differentiation rõ ràng**.

### 6.4. Dataset construction quality standards

Bài học quan trọng từ PlantVillageVQA:

- **Linguistic diversification pipeline**: Ý tưởng paraphrase templates để tăng vocabulary diversity rất hay. Chúng ta có thể áp dụng: tạo nhiều biến thể câu hỏi tiếng Việt từ cùng intent.
- **Automated quality metrics**: TF-IDF vagueness score, cosine similarity check, relative simplicity — đây là framework có thể áp dụng cho QA dataset tiếng Việt.
- **Hierarchical correction**: "Fix when verifiable, delete when not" — nguyên tắc tốt khi clean data.
- **Expert review web interface**: Tạo web form cho experts review — efficient hơn review offline.
- **Vocabulary growth tracking**: Đo unique words trước và sau refinement để quantify improvement.

---

## 7. Bài học rút ra

1. **Template + Paraphrase = Scalable quality**: PlantVillageVQA chứng minh template-based generation kết hợp linguistic diversification có thể tạo ra dataset lớn (193K) mà vẫn giữ chất lượng. Áp dụng: chúng ta có thể dùng template cho câu hỏi advisory + paraphrase tiếng Việt.

2. **No-LLM policy cho ground truth**: Tránh dùng LLM cho content generation eliminates hallucination. Đặc biệt quan trọng cho dự án đánh giá LLM accuracy — ground truth PHẢI independent từ LLMs.

3. **Cognitive taxonomy là framework tốt**: 3 levels (perception → analysis → reasoning) cho phép đánh giá chi tiết. Dự án có thể mở rộng thêm level 4: **Advisory/Contextual Reasoning** — đúng gap mà paper này thiếu.

4. **Automated QA quality pipeline**: Vagueness score + Semantic similarity + Relative simplicity = framework quality assurance có thể replicate cho tiếng Việt (thay TF-IDF bằng Vietnamese tokenizer + embeddings).

5. **PlantVillage images = lab-controlled limitation**: Nếu dự án dùng image_link/image_description, nên ưu tiên mô tả ảnh thực tế ngoài đồng (field conditions), không chỉ ảnh lab. Tuy nhiên vì dự án text-focused, image_description chi tiết có thể bù đắp cho lab-only images.

6. **Multi-level evaluation cần thiết**: Không chỉ đánh giá "đúng/sai" mà cần metrics phù hợp cho từng loại câu trả lời (binary → accuracy, descriptive → BLEU/ROUGE, advisory → cần metric riêng như correctness + specificity + actionability).
