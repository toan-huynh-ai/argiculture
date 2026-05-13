# Phân tích: LeafNet — Large-Scale Dataset and Benchmark for Plant Disease VLMs

---

## 1. Thông tin cơ bản

| Mục | Chi tiết |
|-----|---------|
| **Title** | LeafNet: A Large-Scale Dataset and Comprehensive Benchmark for Foundational Vision-Language Understanding of Plant Diseases |
| **Authors** | Khang Nguyen Quoc, Phuong D. Dao, Luyl-Da Quach |
| **Affiliations** | Korea University (Seoul), University of Texas at Austin, FPT University (Cần Thơ, Vietnam) |
| **Venue** | arXiv preprint (under review) |
| **Year** | 2026 |
| **ArXiv** | 2602.13662v3 |
| **Dataset** | https://huggingface.co/collections/enalis/leafsight |

> **Lưu ý đặc biệt**: Paper có tác giả Việt Nam (Luyl-Da Quach, FPT University Cần Thơ) — có thể liên hệ cho collaboration hoặc tham khảo chuyên sâu.

---

## 2. Tóm tắt

LeafNet là dataset multimodal quy mô lớn nhất hiện tại cho bệnh cây trồng, bao gồm **186,000 ảnh lá** của 22 loại cây trồng và 62 loại bệnh, kèm metadata phong phú (species, disease, pathogen, symptom descriptions, taxonomic names). Song song, nhóm phát triển **LeafBench** — benchmark VQA với 13,950 QA pairs trên 6 diagnostic tasks. Benchmark đánh giá 12 VLMs state-of-the-art (GPT-4o, Gemini 2.5 Pro, CLIP, SCOLD, v.v.) và chứng minh rằng domain-specific fine-tuned VLMs (SCOLD) vượt xa generic models, khẳng định tầm quan trọng của dữ liệu chuyên ngành.

---

## 3. Phương pháp

### 3.1. LeafNet Dataset

**Quy mô và đa dạng:**

| Metric | Giá trị |
|--------|---------|
| Tổng images | 186,000+ |
| Diseased images | 137,000+ |
| Crop species | 22 |
| Disease classes | 62 (tổng 97 classes kể cả healthy) |
| Geographic coverage | 7 quốc gia, 3 châu lục |
| Image source | Ưu tiên in-situ (field conditions) |

**Top 5 crops theo số lượng ảnh**: Coffee (49,375), Maize (27,744), Tomato (25,838), Rice (13,107), Apple (12,458).

**Curation Pipeline:**
1. Thu thập từ public sources + in-house collection.
2. Metadata synthesis từ NIH, NIFA → biological taxonomies (species, disease, pathogenic agent, symptom descriptions).
3. **Human-in-the-loop validation**: Agricultural experts review metadata-image pairs, filter mislabeled/noisy samples.

**Metadata Schema (Table 2):**

| Field | Mô tả |
|-------|--------|
| Folder Name | Ground truth label (e.g., `Apple___Black_Rot`) |
| Species | Biological classification (e.g., *Malus domestica*) |
| Disease | Common name (e.g., Black Rot, Rust) |
| Pathogenic Agent | High-level category: Fungi, Bacterial, Viral, Oomycetes, Mite |
| Taxonomic Nomenclature | Scientific binomial (e.g., *Botryosphaeria spp.*) |
| Symptom Description | Qualitative visual indicators (lesion morphology, necrosis, colorimetric changes) |
| Resolution | Height × Width |
| Acquisition Environment | Controlled lab vs. in-the-wild field |

### 3.2. LeafBench Benchmark

**Quy mô:**
- **Full (All)**: 2,570 images, 13,950 QA pairs
- **Tiny subset**: 890 samples (cost-effective cho API-based models)

**6 Diagnostic Tasks:**

| Task | Viết tắt | Mô tả |
|------|----------|--------|
| Healthy-Diseased Classification | HDC | Binary: plant có bệnh hay không |
| Disease Identification | DI | Nhận dạng bệnh cụ thể |
| Crop Species Identification | CSI | Nhận dạng loại cây |
| Symptom Identification | SI | Nhận dạng triệu chứng chi tiết (lesion morphology, chlorosis) |
| Pathogen Classification | PC | Phân loại tác nhân gây bệnh (Fungi/Bacteria/Virus/Mite) |
| Scientific Name Classification | SNC | Nhận dạng tên khoa học của pathogen |

**Thiết kế:**
- **Label-constrained prompting**: Multiple-choice format, tránh open-ended generation (prone to hallucination).
- **Visual-dependent questions**: Câu trả lời đúng KHÔNG thể suy ra chỉ từ text prompt, BẮT BUỘC phải phân tích ảnh.
- Câu hỏi sắp xếp theo **hierarchical diagnostic complexity**: HDC (dễ) → DI/CSI → SI/SNC/PC (khó).

### 3.3. Evaluation Protocols (3 protocols)

1. **Visual Recognition & Data Efficiency**: Supervised + few-shot classification trên LeafNet (vision-only models).
2. **Zero-Shot Semantic Alignment**: CLIP-based models trên LeafBench, không fine-tuning.
3. **Diagnostic Reasoning**: VQA protocol cho instruction-based VLMs.

### 3.4. Models đánh giá

**VLMs (12 models):**
- Closed-source: GPT-4o (~200B), Gemini 2.5 Pro (~288B)
- Open-source general: LLaVA 1.5, LLaVA-NeXT, Qwen 2.5 VL, BLIP-2, InternVL3.5, CLIP, SigLIP, SigLIP2, ALIGN
- Domain-specific: BioCLIP (biology), **SCOLD** (plant pathology, trained on LeafNet)

**Vision-only (7 models):** VGG16, SwinT, MobileNetV3, ViT, EfficientNetB0, EfficientNetV2S, DenseNet121.

---

## 4. Evaluation

### 4.1. Image Classification (Vision-only, LeafNet)

| Model | Fine-tuning Acc | Linear Probing Acc |
|-------|----------------|-------------------|
| **DenseNet121** | **94.27%** | 70.74% |
| SwinT | 93.41% | 31.75% |
| EfficientNetV2S | 93.47% | 25.79% |
| VGG16 | 90.99% | 77.48% |

**Key insight**: Gap lớn giữa fine-tuning vs. linear probing (13.5% - 67%) → plant pathology features rất khác ImageNet features → cần domain-specific training data.

### 4.2. Zero-Shot VQA (LeafBench)

| Model | HDC | DC | CSI | SNC | PC | SI |
|-------|-----|-----|-----|-----|-----|-----|
| Random | 50.47 | 24.07 | 26.20 | 25.51 | 26.14 | 26.18 |
| **GPT-4o** | **92.48** | **85.27** | **85.58** | **65.27** | 56.47 | 51.64 |
| Gemini 2.5 Pro | 88.25 | 78.54 | 83.21 | 64.89 | 51.23 | 48.99 |
| **SCOLD** | **96.28** | **95.85** | 84.73 | 41.64 | 37.83 | **77.92** |
| Qwen 2.5 VL | 81.05 | 52.60 | 63.06 | 41.50 | 60.92 | 43.14 |
| CLIP | 21.20 | 46.51 | 48.99 | 32.56 | 20.43 | 32.32 |
| LLaVA-NeXT | 88.33 | 33.64 | 48.82 | 27.10 | 70.82 | 32.09 |

### 4.3. Vision-Only vs. VLMs (Fine-tuned, LeafBench)

| Type | Model | HDC | DC | CSI | SNC | PC | SI |
|------|-------|-----|-----|-----|-----|-----|-----|
| Vision | DenseNet121 | 94.27 | 94.12 | 92.45 | 61.88 | 93.50 | 70.23 |
| VLM | **SCOLD** | **99.15** | **98.85** | **96.73** | **89.64** | **97.83** | **94.92** |

**Key insight**: Fine-tuned VLMs vượt vision-only models **+27.76% SNC, +24.69% SI** → integrating linguistic representations significantly enhances diagnostic precision.

### 4.4. Key Findings

1. **HDC dễ nhất** (>90%), **SNC/SI khó nhất** (<65% cho generic models) → hierarchical difficulty confirmed.
2. **Domain-specific models (SCOLD)** vượt xa generic VLMs: 96.28% HDC, 95.85% DC, 77.92% SI.
3. **Generic open-source VLMs** (CLIP, SigLIP2) performance gần random cho nhiều tasks → web-scale pre-training không đủ cho agriculture.
4. **Tiny subset** representative cho full benchmark → cost-effective evaluation strategy viable.

---

## 5. Điểm mạnh & Điểm yếu

### Điểm mạnh

1. **Quy mô lớn nhất**: 186,000 images — vượt xa PlantVillage (55K), CDDM (137K) với diversity cao hơn.
2. **Geographic diversity**: 7 quốc gia, kết hợp lab + field images → generalization tốt hơn.
3. **Rich metadata**: Không chỉ labels mà có symptom descriptions, pathogenic agents, taxonomic names → multimodal annotations phong phú.
4. **Comprehensive benchmarking**: 12 VLMs + 7 vision models, 3 evaluation protocols → so sánh toàn diện nhất.
5. **Domain-specific model validation**: Chứng minh SCOLD (trained on LeafNet) vượt xa generic models → validates dataset utility.
6. **Hierarchical task design**: 6 tasks từ binary screening → expert-level taxonomy → đánh giá diagnostic depth.
7. **Tiny subset strategy**: Cost-effective proxy cho API-based evaluation.
8. **Expert-annotated metadata**: Verified against NIH, NIFA authoritative sources.

### Điểm yếu

1. **Geographic bias**: Chủ yếu USA và India, thiếu Southeast Asian crops (lúa nước VN, sầu riêng, cà phê VN varieties).
2. **Static images only**: Không có temporal dynamics (disease progression over time).
3. **Visible spectrum only**: Thiếu multispectral/thermal imaging.
4. **Limited text annotations**: Symptom descriptions ngắn, không có treatment/management recommendations.
5. **No advisory component**: Chỉ identification/classification, không có tư vấn xử lý.
6. **Multiple-choice VQA**: Giới hạn đánh giá khả năng generate tự nhiên.
7. **LeafBench QA pairs ít hơn expected**: 13,950 QA pairs cho 186K images — chỉ benchmark subset nhỏ.
8. **Tiếng Anh only**: Không đa ngôn ngữ mặc dù có tác giả Vietnam.

---

## 6. So sánh với dự án Vietnamese Agriculture QA

### 6.1. Context sensitivity và regional specificity

LeafNet có **geographic metadata** (7 countries) nhưng **không sử dụng** nó như context cho QA. Câu hỏi không phân biệt "bệnh này ở Ấn Độ xử lý khác so với ở Mỹ". Metadata chỉ dùng cho dataset curation, không inject vào questions/answers.

Paper nhận ra limitation này trong Future Work: *"expanding text annotations to encompass structured ontologies, such as crop, disease severity scales, and growth stage metadata"* — nhưng chưa thực hiện.

Dự án của chúng ta xử lý **đúng gap này**: context-aware QA với Vietnamese regional specificity (ĐBSCL, Tây Nguyên, v.v.).

### 6.2. Multi-turn QA

LeafNet/LeafBench chỉ có **single-turn multiple-choice QA**. Không có follow-up hay conversation. Future Work đề xuất *"instruction-tuning dataset, wherein each example pairs an image with detailed, natural-language explanations and step-by-step diagnostic reasoning"* — nhưng vẫn chưa có multi-turn.

### 6.3. Advisory quality vs. Recognition/Classification

LeafNet **hoàn toàn tập trung classification/identification**:
- HDC: binary health screening
- DI: disease name
- CSI: crop species
- SI: symptom type
- PC: pathogen class
- SNC: scientific name

**Không có management/advisory task nào**. Paper Future Work đề xuất: *"articulate biological mechanisms and management recommendations in a manner analogous to expert agronomists"* — nhưng chưa hiện thực hóa. Dự án của chúng ta đánh giá advisory quality → contribution khác biệt rõ ràng.

### 6.4. Dataset construction quality standards

Bài học quan trọng từ LeafNet:

- **Authoritative source verification**: Metadata verified against NIH, NIFA — không tự bịa. Dự án nên verify agricultural knowledge against Cục Bảo vệ Thực vật, Viện Khoa học Nông nghiệp VN.
- **Human-in-the-loop curation**: Expert review TRƯỚC khi data vào benchmark, không chỉ sau. Áp dụng: expert review Vietnamese QA pairs ở giai đoạn construction.
- **Structured metadata schema**: Species → Disease → Pathogenic Agent → Symptom → Taxonomic Name. Dự án nên có schema tương tự cho Vietnamese context: Cây trồng → Bệnh → Tác nhân → Triệu chứng → Vùng → Mùa → Giải pháp.
- **Multiple evaluation protocols**: 3 protocols (supervised, zero-shot, VQA) cho cái nhìn toàn diện. Dự án nên có multiple evaluation dimensions: accuracy, specificity, actionability, context-awareness.
- **Domain-specific model development**: SCOLD (trained on LeafNet) vượt generic models 27%+ → chứng minh domain-specific training rất quan trọng. Implication: nếu dự án build Vietnamese agriculture corpus đủ lớn, fine-tuned models sẽ vượt xa generic LLMs.
- **Tiny subset strategy**: Tạo subset nhỏ representative cho expensive API evaluations → áp dụng cho dự án khi evaluate GPT-4o/Gemini.

---

## 7. Bài học rút ra

1. **Domain-specific data là then chốt**: LeafNet chứng minh rõ ràng nhất: SCOLD (trained on LeafNet) đạt 99.15% disease ID vs. generic CLIP 46.51%. Implication cho dự án: Vietnamese agriculture-specific training data sẽ tạo ra models vượt trội. Dataset chất lượng cao là **contribution có giá trị lâu dài**.

2. **Rich metadata > Simple labels**: LeafNet vượt PlantVillage nhờ metadata phong phú (symptom descriptions, pathogenic agents). Dự án nên đầu tư vào metadata phong phú cho mỗi QA pair: không chỉ question-answer mà còn context fields (region, season, crop variety, soil type, v.v.).

3. **Hierarchical task complexity**: 6 tasks từ binary → fine-grained → taxonomic. Dự án có thể thiết kế hierarchy tương tự cho advisory: Basic identification → Context-aware diagnosis → Region-specific treatment → Integrated management recommendation.

4. **Gap rõ ràng nhất**: LeafNet explicitly thừa nhận trong Future Work rằng họ thiếu (a) management recommendations, (b) severity scales, (c) growth stage context, (d) instruction-tuning data. Dự án Vietnamese Agriculture QA đang xây dựng **đúng những thứ mà LeafNet thiếu** → positioning rất mạnh.

5. **Vietnamese connection**: Tác giả Luyl-Da Quach (FPT University Cần Thơ) → potential collaboration opportunity. Có thể tham khảo LeafNet framework khi xây dựng Vietnamese agricultural dataset.

6. **In-situ vs. Lab data**: LeafNet nhấn mạnh field conditions images quan trọng hơn lab images cho generalization. Cho dự án text-based: **real farmer scenarios** (câu hỏi thực tế từ nông dân) quan trọng hơn câu hỏi sách giáo khoa.

7. **Evaluation cost management**: Tiny subset strategy cho phép evaluate expensive models (GPT-4o, Gemini) cost-effectively. Dự án nên có stratified sampling cho evaluation subset, đảm bảo cover đủ regions, crop types, và difficulty levels.
