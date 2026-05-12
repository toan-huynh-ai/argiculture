# Literature Review: Context-Aware QA for Vietnamese Agriculture

> Last updated: 2026-05-12
> Target venues: ACL, EMNLP, NeurIPS Datasets & Benchmarks Track

---

## 1. Agricultural QA Benchmarks & Datasets

### 1.1 General Agricultural Benchmarks

| Paper | Venue | Year | Scale | Language | Modality | Key Contribution |
|-------|-------|------|-------|----------|----------|-----------------|
| **MIRAGE** | NeurIPS D&B | 2025 | 35K+ interactions, 7K+ bio entities | EN | Text + Image | Multi-turn expert-guided conversations from AskExtension; single-turn (MMST) + multi-turn (MMMT) tasks |
| **AgMMU** | — | 2025 | 746 MCQ + 746 OEQ, 205K knowledge facts | EN | Text + Image | Distilled from 116K real USDA expert dialogues; multimodal understanding |
| **AgroBench** | — | 2025 | 203 crop categories, 682 disease categories | EN | Text + Image | Expert-annotated; 7 agricultural topics; fine-grained weed identification |
| **AgriEval** | AAAI | 2026 | 14,697 MCQ + 2,167 OEQ | ZH | Text | Chinese agriculture; 6 categories, 29 subcategories; tested 51 LLMs |
| **AI AgriBench** | — | 2025-26 | 416 QA pairs, 31 crops, 9 categories | EN | Text | Public leaderboard; LLM-as-judge with 3 judge models; consortium-backed |
| **AgThoughts / AgReason** | ICLR (submitted) | 2026 | 44.6K QA + 100 expert eval | EN | Text | Reasoning traces for fine-tuning; 10 agronomic categories |
| **PlantVillageVQA** | arXiv | 2025 | 193,609 QA pairs, 55,448 images | EN | Text + Image | 14 crops, 38 diseases; 3 cognitive complexity levels |
| **LeafNet / LeafBench** | arXiv | 2026 | 186K images, 13,950 QA pairs | EN | Text + Image | 97 disease classes; plant disease understanding |

### 1.2 Vietnamese Agriculture Datasets

| Dataset | Platform | Scale | Description |
|---------|----------|-------|-------------|
| **VietSmartAgri** | HuggingFace | 148 KB | Vietnamese smart agriculture dataset (limited scope) |
| **batinho/vietnamese-agriculture-dataset** | HuggingFace | 8 samples | Prototype; colloquial Vietnamese farmer questions |
| **batinho/vietnam-durian-knowledge-graph** | HuggingFace | 100+ entries | Durian-specific; maps vernacular terms to scientific terminology |
| **STAR-FARM Lexicon** | CIRAD Dataverse | 553 terms | EN/VI bilingual lexicon for agroecology in Mekong Delta |
| **AgriMind AI (ngocbao220)** | GitHub | — | Fine-tuned LLM + RAG for 8 crop types in Vietnamese |
| **Rice Pest Dataset** | PMC | — | Expert-reviewed rice disease detection for Vietnam |

### 1.3 Region-Aware & Safety-Focused Agricultural AI

| Paper | Venue | Year | Key Contribution |
|-------|-------|------|-----------------|
| **AgriRegion** | arXiv | 2025 | Region-aware RAG with geospatial metadata injection; reduces hallucination 10-20%; 160 QA pairs across 12 subfields |
| **PestMA** | arXiv | 2025 | Multi-agent (Editor-Retriever-Validator) for pest management; 86.8%→92.6% accuracy; 68 scenarios, 39 pest species (UK) |
| **AgriPestDatabase-v1.0** | arXiv | 2026 | Structured insect dataset for training agricultural LLMs |

### 1.4 Multilingual / Low-Resource Agriculture

| Paper | Venue | Year | Key Contribution |
|-------|-------|------|-----------------|
| **AgriGPT-Omni** | arXiv | 2025 | Unified speech-vision-text; 6 languages; AgriBench-Omni-2K |
| **SEACrowd** | — | 2024 | Multilingual hub for ~1000 SE Asian languages; 13 NLP tasks |
| **Digital Green DG-EVAL** | — | 2025-26 | Atomic fact verification; 25K+ expert Q&A; India focus |
| **iShamba + RLHF** | CGIAR/CGSPACE | 2025 | RLHF for agriculture in Kenya; accuracy 1.53 → 1.95/2.0 |
| **FarmerChat / GAIA** | IFPRI | 2025-27 | User study with 1.5M+ queries in India/Kenya; NPS ~60 |

### 1.5 Vietnamese Dialect NLP

| Paper | Venue | Year | Key Contribution |
|-------|-------|------|-----------------|
| **ViMD** | EMNLP | 2024 | 63 provincial dialects, 102.56h audio, 19K utterances |
| **ViDia2Std** | AAAI | 2026 | First parallel corpus dialect→standard VN; 13K+ sentences from 63 provinces |

---

## 2. Context-Aware & Geographically-Grounded QA

| Paper | Venue | Year | Key Finding |
|-------|-------|------|-------------|
| **LocalBench** | arXiv | 2025 | 14,782 QA pairs across 526 US counties; best model 56.8% on narrative, <15.5% on numerical; web augmentation inconsistent |
| **MapEval** | ICML | 2025 | 700 questions, 180 cities, 54 countries; best model 67%, 20%+ below humans |
| **EarthWhere** | arXiv | 2025 | Vision-language geolocation; up to 42.7% regional accuracy gap (bias) |
| **MAPVERSE** | arXiv | 2026 | 11,837 QA pairs on 1,025 real maps; VLMs fail on spatial reasoning |
| **GeoVQA** | ACL | 2025 | Geographic VQA requiring cultural/landscape knowledge beyond visual content |

---

## 3. LLM-as-Judge & Expert Alignment

| Paper | Venue | Year | Key Contribution |
|-------|-------|------|-----------------|
| **AI AgriBench LLM-as-Judge** | — | 2025-26 | 3 judge models (Claude Opus 4.5, Gemini3-Pro, Kimi-K2); 4 metrics; no self-eval bias |
| **Digital Green DG-EVAL** | arXiv | 2026 | Atomic fact verification: recall + precision + contradiction detection |
| **RLHF for Agricultural Localization** | Adv. Agri. Dev. | 2025 | RLHF with expert Q&A; gender/social bias mitigation |
| **AIEP Initiative** | arXiv | 2026 | Technical learnings from building AI advisory for smallholder farmers |

---

## 4. Context Injection & Multi-turn Evaluation

| Paper | Venue | Year | Key Finding |
|-------|-------|------|-------------|
| **Instruction Tuning with/without Context** | EACL | 2026 | Context-augmented training reduces hallucination; separate models with input routing outperforms mixed training |
| **InteGround** | EMNLP Findings | 2025 | LLMs rationalize with internal knowledge when external info incomplete; premise abduction helps |
| **Domain-Specific Knowledge Injection Survey** | EMNLP Findings | 2025 | 4 approaches: dynamic injection, static embedding, modular adapters, prompt optimization |
| **TurnWise** | arXiv | 2026 | Measures gap between single- and multi-turn LLM capabilities; up to 73% performance degradation |
| **MMLU-ProX** | arXiv | 2025 | 11,829 questions across 29 languages; proves cross-lingual knowledge transfer gaps |

---

## 5. Research Gap Analysis

### 5.1 Gaps This Project Fills

| Gap | Evidence | How This Project Addresses It |
|-----|----------|-------------------------------|
| **No Vietnamese agriculture QA benchmark** | VietSmartAgri has 148KB; batinho dataset has 8 samples; no peer-reviewed VN agriculture benchmark exists | First comprehensive, expert-validated Vietnamese agriculture QA dataset |
| **No context sensitivity evaluation** | MIRAGE has contextual subsets but doesn't systematically degrade context; LocalBench tests knowledge not advisory quality | 3-level context degradation protocol measuring advisory quality change |
| **No multi-turn advisory QA in non-English agriculture** | MIRAGE (EN only); AgriEval (ZH but single-turn MCQ); no multi-turn advisory in low-resource languages | Multi-turn QA with expert-grounded answers in Vietnamese |
| **Regional bias in LLMs undocumented for agriculture** | EarthWhere shows geographic bias in geolocation; no study shows LLM regional bias in agricultural advice | Explicit documentation of LLM defaulting to Mekong Delta / Northern Delta |
| **Expert-in-the-loop for non-Western agriculture** | AI AgriBench, DG-EVAL focus on US/India/Kenya; no expert annotation for Vietnamese farming context | 3-stage expert pipeline with Vietnamese agricultural expert(s) |
| **Vernacular/dialect gap** | DurianExpert-VN addresses vernacular for 1 crop; no systematic handling of Vietnamese regional dialects | User profile with dialect and communication style for ~90 crop species |

### 5.2 Potential Weaknesses (to address for A* acceptance)

| Weakness | Risk | Mitigation Strategy |
|----------|------|---------------------|
| **Single expert** | Reviewer concern about annotation bias and inter-annotator agreement | Recruit 2-3 more experts; report IAA (Cohen's kappa or Krippendorff's alpha) |
| **Dataset scale** | 100-500 test samples may seem small vs AgriEval (14K+) or MIRAGE (35K+) | Emphasize quality over quantity; compare with AgReason (100 expert samples) and AI AgriBench (416) |
| **Synthetic data reliance** | Questions about data authenticity | Include real farmer questions from Vietnamese farming forums/hotlines |
| **Limited multimodality** | MIRAGE, AgMMU, AgroBench all include images | Include crop disease images from Vietnamese sources |
| **Evaluation depth** | Need stronger baselines and analysis | Test more models; include Vietnamese-specialized LLMs; ablation studies |

---

## 6. Competitive Positioning Map

```
                        Multi-turn          Single-turn
                    ┌───────────────────┬───────────────────┐
                    │                   │                   │
    Expert-         │   THIS PROJECT    │   AI AgriBench    │
    grounded        │   (Vietnamese)    │   AgReason        │
                    │                   │   AgriEval        │
                    │                   │                   │
                    ├───────────────────┼───────────────────┤
                    │                   │                   │
    LLM-            │   MIRAGE          │   AgMMU           │
    generated/      │                   │   AgroBench       │
    Automated       │                   │   PlantVillageVQA │
                    │                   │   AgThoughts      │
                    │                   │                   │
                    └───────────────────┴───────────────────┘
                    
    Context-aware:  THIS PROJECT, MIRAGE, LocalBench
    Vietnamese:     THIS PROJECT (only one)
    Advisory focus: THIS PROJECT, AI AgriBench, DG-EVAL, iShamba
```

---

## 7. Key Papers to Cite (Priority Order)

### Must-cite (directly competing or foundational)
1. **MIRAGE** (NeurIPS 2025) — Most similar work; multi-turn agricultural expert conversations
2. **AgriEval** (AAAI 2026) — Chinese agricultural benchmark; shows non-English need
3. **AgMMU** (2025) — Multimodal agricultural benchmark from real expert dialogues
4. **AI AgriBench** (2025-26) — LLM-as-judge methodology for agriculture
5. **AgThoughts/AgReason** (ICLR 2026 submitted) — Agricultural reasoning with expert curation

### Should-cite (supporting context)
6. **AgroBench** (2025) — Vision-language agriculture benchmark
7. **LocalBench** (2025) — County-level knowledge; geographic context sensitivity
8. **PlantVillageVQA** (2025) — Large-scale agricultural VQA
9. **LeafNet** (2026) — Plant disease multimodal understanding
10. **SEACrowd** (2024) — Southeast Asian language resources
11. **AgriGPT-Omni** (2025) — Multilingual agricultural AI
12. **Digital Green DG-EVAL** (2026) — Fact verification for agriculture

### Consider-citing (methodology)
13. **MapEval / EarthWhere** — Geographic bias in LLMs
14. **Instruction Tuning with/without Context** (EACL 2026) — Context injection theory
15. **RLHF for Agricultural Localization** (2025) — Expert alignment methods

---

## 8. Recommended Venue Strategy

| Venue | Track | Deadline | Fit | Notes |
|-------|-------|----------|-----|-------|
| **NeurIPS 2026** | Datasets & Benchmarks | ~Jun 2026 | ★★★★★ | MIRAGE was accepted here; perfect fit for dataset papers |
| **EMNLP 2026** | Main + Resource | May 25, 2026 (ARR) | ★★★★☆ | Strong NLP venue; resource papers welcome |
| **ACL 2027** | Main | ~Jan 2027 | ★★★★☆ | Top NLP venue; needs strong methodology contribution |
| **AAAI 2027** | Main | ~Sep 2026 | ★★★☆☆ | AgriEval published here; AI applications track |
| **ICLR 2027** | Main | ~Oct 2026 | ★★★☆☆ | AgThoughts submitted here; needs learning contribution |
