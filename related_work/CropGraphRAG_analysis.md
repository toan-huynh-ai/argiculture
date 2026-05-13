# Phân tích: Crop GraphRAG — Pest and Disease Knowledge Base Q&A System for Sustainable Crop Protection

---

## 1. Thông tin cơ bản

- **Tiêu đề:** Crop GraphRAG: pest and disease knowledge base Q&A system for sustainable crop protection
- **Tác giả:** Hao Wu, Nengfu Xie*, Xiaoli Wang, Jingchao Fan, Yonglei Li, Zhibo Meng* (Agricultural Information Institute, Chinese Academy of Agricultural Sciences / Key Laboratory of Agricultural Blockchain Application, Ministry of Agriculture and Rural Affairs, Beijing, China)
- **Venue:** Frontiers in Plant Science, Vol. 16, Article 1696872
- **Năm:** 2026 (Published 05 January 2026; DOI: 10.3389/fpls.2025.1696872)

---

## 2. Tóm tắt

Crop GraphRAG là framework tích hợp Knowledge Graph (KG) với Retrieval-Augmented Generation (RAG) để xây dựng hệ thống hỏi-đáp chuyên biệt cho lĩnh vực bệnh hại và sâu bệnh trên cây trồng. Nhóm tác giả thu thập corpus lớn về bệnh/sâu hại, trích xuất entities và relations để xây dựng knowledge graph đa quan hệ (>20,000 entities, ~60,000 relations), sau đó áp dụng Leiden community detection để tạo hierarchical summaries. Hệ thống hỗ trợ 2 chế độ truy vấn: local query (entity-centric) và global query (community-summary-based). Kết quả thí nghiệm cho thấy Crop GraphRAG đạt accuracy 89.4%, recall 86.7%, BLEU 0.68, và vượt trội so với baselines về accuracy, coverage, relevance, và traceability.

---

## 3. Phương pháp

### Xây dựng Knowledge Graph

**Nguồn dữ liệu:**
- Bài báo nghiên cứu từ tạp chí nông nghiệp Trung Quốc
- Quy định/guidelines quốc gia về phòng trừ dịch bệnh (Bộ NN & PTNT Trung Quốc, National Agro-Tech Extension Center)
- Thông tin đăng ký thuốc BVTV
- Websites nông nghiệp chuyên biệt (Chinese Crop Disease and Pest Knowledge Base)

**Quy mô corpus:**
- 3,000+ tài liệu tiếng Trung, ~15 triệu ký tự
- Sau cleaning/deduplication: ~6 triệu ký tự

**Pipeline xây dựng KG (5 bước):**
1. **Text Segmentation:** Chunk size = 1,200 tokens, overlap = 200 tokens, group theo ID
2. **Element Extraction:** Prompt engineering trên LLM để trích xuất entities (6 types) và relations (triples)
3. **Element Summarization:** Gộp descriptions của cùng entity/relation qua entity linking, truncate theo token limit
4. **Graph Community Detection:** Leiden algorithm trên weighted undirected graph G=(V,E,w), maximize modularity Q, threshold Q>0.3
5. **Community Summarization:** Hierarchical — leaf communities → higher-level → top-level, mỗi level có LLM-generated summary

**6 Entity Types:**
| Entity type | Ví dụ |
|-------------|-------|
| Crops | Lúa, lúa mì, ngô |
| Diseases | Blast (đạo ôn), wheat stem smut, northern corn leaf blight |
| Insect pests | Rice stem borer, wheat thrips |
| Symptoms | Root rot, hollow stem |
| Control measures | Biopesticides, physical control |
| Pathogen | Rice blast fungus |

**Quy mô KG:**
- 20,000+ entities
- ~60,000 semantic relations
- 40+ relation types
- Distribution: Crops (~35%), Diseases (25%), Pests (20%), Control measures (10%), Auxiliary entities (10%)
- Top relations: "disease infects crop" (~18%), "pest damages crop" (~15%), "control measure targets pest/disease" (~12%)
- Cover 30+ major crop species, 100+ common pest/disease types

### Truy vấn và Hỏi-đáp

**Local Query (entity-centric):**
1. Encode query → vector vq
2. Cosine similarity → tìm entity e* gần nhất
3. Mở rộng 1-hop adjacency subgraph G(1)_e*
4. Lấy text chunks + community summaries liên quan
5. Đưa vào prompt template → LLM generate answer

**Global Query (community-based):**
1. Top-down traversal từ top-level communities
2. LLM scoring relevance (0-5) cho mỗi community
3. Pruning: threshold = 3 (giữ score ≥ 3, cắt toàn bộ descendants nếu < 3)
4. Thu thập community summaries relevant
5. Parallel processing → aggregate → final synthesized answer
6. Consistency evaluation + conflict resolution module

### Evaluation

**Evaluation dataset:**
- 200+ domain-specific questions (jointly authored by graduate students + agricultural experts)
- 2 loại: factual questions (local query) và analytical questions (global query)

**Baselines:**
- Traditional retrieval: BM25 + LLM
- Naive RAG: Text-only vector retrieval (FAISS)
- GraphRAG-wo-Community: Không có community summaries
- GraphRAG-wo-KnowledgeGraph: Không có graph structure
- LLM-Only: DeepSeek-R1 (không có KB)

**Metrics:**
- Ablation: Precision, Recall, F1, BLEU-4, Average response time
- Comparative: Accuracy, Coverage, Relevance, Traceability (LLM-as-judge with human oversight, ChatGPT-o3, scale 1-5, anonymized A/B/C/D, 5 randomized rounds)
- Statistical: Paired t-tests + Wilcoxon signed-rank tests, Holm-adjusted p-values, Compact Letter Displays (CLDs)

---

## 4. Kết quả chính

### Ablation Study
| Method | Accuracy | Recall | F1 | BLEU | Avg Time(s) |
|--------|----------|--------|-----|------|-------------|
| **Crop GraphRAG** | **89.4%** | **86.7%** | **88.0%** | **0.68** | 4.5 |
| GraphRAG-wo-KG | 84.1% | 80.5% | 82.2% | 0.59 | 4.1 |
| GraphRAG-wo-Community | 77.2% | 75.5% | 76.4% | 0.48 | 3.9 |
| Naive RAG | 72.6% | 68.3% | 70.3% | 0.41 | 3.2 |

### Comparative Experiment (scale 1-5)
| Method | Accuracy | Coverage | Relevance | Traceability |
|--------|----------|----------|-----------|--------------|
| **Crop GraphRAG** | **4.81** | **4.30** | **4.50** | **4.40** |
| Naive RAG | 4.00 | 4.20 | 4.40 | 3.80 |
| BM25+LLM | 3.88 | 3.61 | 4.10 | 3.70 |
| DeepSeek-R1 | 3.12 | 3.55 | 4.10 | 2.50 |

### Key Findings
- **Community summaries quan trọng nhất:** Bỏ community → accuracy giảm 12.2 percentage points (lớn nhất)
- **KG structure vẫn cần thiết:** Bỏ KG → accuracy giảm 5.3 points, recall giảm 6.2 points
- **Traceability là advantage lớn nhất:** Crop GraphRAG 4.40 vs. DeepSeek-R1 2.50 (p<0.001)
- **Response time chấp nhận được:** 3.2-4.5s across variants
- **All differences statistically significant:** p<0.05 after Holm correction

---

## 5. Điểm mạnh & Điểm yếu

### Điểm mạnh
- **End-to-end pipeline hoàn chỉnh:** Từ raw text → KG construction → community detection → hierarchical summarization → dual-mode QA — có thể reproduce
- **Domain-specific design:** Entity types, relation types, prompt engineering đều tailored cho crop pest/disease domain
- **Ablation study chặt chẽ:** Tách riêng contribution của từng component (community summaries vs. KG structure)
- **Statistical rigor:** 5 randomized rounds, paired t-tests, Holm correction, CLDs — vượt chuẩn cho applied AI papers
- **Traceability:** Unique selling point — answers có thể trace back tới evidence trong KG, quan trọng cho agricultural advice
- **Hierarchical community summaries:** Multi-granular context cho phép both local (specific entity) và global (topic synthesis) queries

### Điểm yếu
- **Evaluation dataset nhỏ:** 200+ questions — khó đánh giá coverage toàn diện
- **Chinese-only:** Toàn bộ corpus và evaluation bằng tiếng Trung — không generalize sang ngôn ngữ khác
- **LLM-as-judge bias:** Dùng ChatGPT-o3 làm judge — có thể biased towards LLM-generated answers
- **Không có human expert annotation trực tiếp:** Comparative evaluation dùng LLM-as-judge + human oversight, nhưng "oversight" không rõ ràng mức độ
- **Single modality:** Chỉ xử lý text — không xử lý images (ảnh triệu chứng bệnh), sensor data, hay weather
- **Entity extraction accuracy chưa báo cáo:** Không rõ precision/recall của extraction step → errors có thể propagate
- **Corpus bias:** Tập trung vào Chinese crops/diseases — schema phù hợp cho Trung Quốc nhưng cần adapt cho vùng khác

---

## 6. So sánh với dự án Vietnamese Agriculture QA

### Cách CropGraphRAG xây dựng Knowledge Graph

**1. Pipeline xây dựng KG có thể tham khảo trực tiếp:**

Crop GraphRAG dùng pipeline 5 bước có thể áp dụng cho dự án VN:

| Bước | CropGraphRAG | Áp dụng cho VN |
|------|-------------|----------------|
| Text Segmentation | 1,200 tokens, 200 overlap | Tương tự, nhưng tiếng Việt có thể cần chunk size khác |
| Entity Extraction | LLM + domain-specific prompts | Dùng prompt engineering cho VN entities (tên thuốc VN, giống cây VN) |
| Element Summarization | Entity linking + aggregation | Quan trọng cho VN: cùng bệnh có nhiều tên (đạo ôn = blast = Pyricularia) |
| Community Detection | Leiden algorithm | Có thể áp dụng trực tiếp — tốt hơn Louvain |
| Community Summarization | Hierarchical LLM summaries | Dùng cho overview queries ("Tổng hợp bệnh hại lúa ở ĐBSH") |

**2. Entity types phù hợp nhưng cần mở rộng cho VN:**

CropGraphRAG có 6 entity types. Dự án VN cần thêm:

| CropGraphRAG | Thêm cho VN |
|-------------|-------------|
| Crops | Giống cây (variety) — quan trọng ở VN |
| Diseases | Giữ nguyên |
| Insect pests | Giữ nguyên |
| Symptoms | Giữ nguyên |
| Control measures | Tách riêng: Chemical / Biological / Physical |
| Pathogen | Giữ nguyên |
| *(chưa có)* | **Region** (ĐBSH, ĐBSCL, Tây Nguyên...) |
| *(chưa có)* | **Season** (Đông Xuân, Hè Thu, Mùa) |
| *(chưa có)* | **Soil type** (Phù sa, Phèn, Bazan...) |
| *(chưa có)* | **Approved status** (Cục BVTV approved Y/N) |

**3. Relation types cần contextualize cho VN:**

Top relations trong CropGraphRAG:
- "disease infects crop" (~18%) → "bệnh_gây_hại_cây" + thêm condition (season, region)
- "pest damages crop" (~15%) → "sâu_gây_hại_cây" + growth stage
- "control measure targets pest/disease" (~12%) → "thuốc_trị_bệnh" + dosage + method

**4. Community detection cho Vietnamese agriculture:**
- Leiden algorithm tạo communities như "major diseases affecting rice" hoặc "insecticides for fruit trees"
- Cho VN: communities tự nhiên sẽ là: "Bệnh lúa ĐBSH", "Sâu hại cà phê Tây Nguyên", "Thuốc BVTV group 4A"
- Hierarchy: Crop → Region → Disease/Pest → Treatment

**5. Dual-mode query phù hợp cho benchmark:**
- **Local query:** Test LLMs trên factual questions ("Thuốc nào trị đạo ôn?") → entity-centric
- **Global query:** Test LLMs trên analytical questions ("Tổng hợp chiến lược phòng trừ tổng hợp cho lúa vụ Đông Xuân") → community-based
- → Dự án VN có thể thiết kế 2 loại questions tương tự cho benchmark

### Relevance cho đề xuất KB 3 tầng (knowledge_base_analysis.md)

CropGraphRAG validate trực tiếp kiến trúc đã đề xuất:

| Tầng KB (dự án VN) | Tương ứng CropGraphRAG | Validation |
|--------------------|-----------------------|------------|
| Tầng 1: Scenario Templates | Text chunks grouped by ID | ✅ Crop GraphRAG cũng group texts by ID |
| Tầng 2: Knowledge Graph | Core KG (entities + relations) | ✅ 20K entities, 60K relations → chứng minh KG hoạt động tốt cho agriculture |
| Tầng 3: Golden Facts | Community summaries + reference answers | ⚠️ Crop GraphRAG dùng summaries thay vì atomic facts — approach khác nhưng cùng mục đích |

**Kết luận:** CropGraphRAG chứng minh KG + RAG cho agricultural QA là approach hiệu quả (accuracy 89.4% vs. naive RAG 72.6%). Dự án VN nên xây lightweight KG (Tầng 2) nhưng KHÔNG cần reach quy mô 20K entities — chỉ cần đủ cho coverage analysis và consistency checking.

---

## 7. Bài học rút ra

1. **Community summaries là key innovation:** Ablation cho thấy bỏ community summaries → giảm accuracy 12.2 points (nhiều nhất). Dự án VN nên tạo summaries cho mỗi crop-region combination để hỗ trợ global queries.

2. **Traceability quan trọng cho agricultural advice:** CropGraphRAG đạt 4.40/5.0 traceability vs. DeepSeek-R1 chỉ 2.50 — KG cho phép trace back answers tới evidence cụ thể. Đây là feature quan trọng khi advice sai có thể gây thiệt hại cho nông dân.

3. **Entity extraction bằng LLM + domain prompts khả thi:** Không cần NER model riêng — dùng LLM với carefully designed prompts là đủ. Dự án VN có thể dùng approach tương tự với Vietnamese-specific prompts.

4. **Leiden > Louvain cho community detection:** CropGraphRAG chọn Leiden vì: internally well-connected communities, higher partition quality, better scalability. Dự án VN nên dùng Leiden nếu xây KG.

5. **Quy mô KB phù hợp:** 3,000 documents → 6M characters → 20K entities → 60K relations → performance tốt. Dự án VN với target ~90 crops × 5 regions sẽ có quy mô nhỏ hơn nhiều — hoàn toàn khả thi.

6. **Dual-mode query design:** Local (factual) + Global (analytical) queries cover 2 loại questions khác nhau. Benchmark VN nên include cả hai: factual questions (test precision) + analytical questions (test comprehensiveness).

7. **Chinese agriculture schema có thể adapt:** 6 entity types + top relation types phù hợp cho domain crop pest/disease nói chung. Cần customize thêm entities cho Vietnamese context (region, season, variety, approved status).
