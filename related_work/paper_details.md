# Detailed Paper Summaries for Related Work

---

## Paper 1: MIRAGE (NeurIPS 2025) ⭐ MOST SIMILAR WORK

- **Title:** MIRAGE: A Benchmark for Multimodal Information-Seeking and Reasoning in Agricultural Expert-Guided Conversations
- **Authors:** Dongre, Gui, Garg, Nayyeri, Tur, Hakkani-Tur, Adve
- **Venue:** NeurIPS 2025 Datasets & Benchmarks Track
- **ArXiv:** 2506.20100
- **GitHub:** https://github.com/MIRAGE-Benchmark/MIRAGE-Benchmark
- **Website:** https://mirage-benchmark.github.io/

### Summary
Built from 35,000+ real user-expert interactions on the AskExtension platform (USDA). Contains 7,000+ unique biological entities. Two tasks: MIRAGE-MMST (single-turn) and MIRAGE-MMMT (multi-turn). Evaluates entity identification, causal reasoning, recommendation generation, and dialogue state tracking.

### Key Results
- GPT-4.1: 44.6% accuracy
- GPT-4o: 40.9% accuracy
- Significant room for improvement across all models

### Differences from Our Project
| Aspect | MIRAGE | Our Project |
|--------|--------|-------------|
| Language | English | Vietnamese |
| Geography | US (USDA Extension) | Vietnam (5 ecological regions) |
| Context sensitivity | Has contextual subsets | Systematic 3-level degradation |
| Expert source | USDA Extension agents | Vietnamese agricultural experts |
| Dialect handling | Not addressed | Explicit user dialect modeling |
| Advisory feasibility | Not evaluated | Core evaluation dimension |
| Crop coverage | US crops | ~90 Vietnamese crop species |

### What to learn from MIRAGE for A* quality
- Used **real expert conversations** as data source (not synthetic)
- Clear task formulation (MMST + MMMT)
- Large scale (35K+ interactions)
- Comprehensive model evaluation (20+ models)
- Open-source benchmark with leaderboard

---

## Paper 2: AgriEval (AAAI 2026)

- **Title:** AgriEval: A Comprehensive Chinese Agricultural Benchmark for Large Language Models
- **Venue:** AAAI 2026
- **ArXiv:** 2507.21773

### Summary
14,697 MCQ + 2,167 OEQ for Chinese agriculture. 6 categories, 29 subcategories. Curated from university-level exams. Tested 51 LLMs — most struggled to achieve 60% accuracy.

### Differences from Our Project
| Aspect | AgriEval | Our Project |
|--------|----------|-------------|
| Language | Chinese | Vietnamese |
| Question source | University exams | Expert-written + synthetic |
| Format | MCQ + OEQ | Multi-turn QA with context |
| Evaluation | Accuracy on exams | Advisory quality + context sensitivity |
| Multi-turn | No | Yes |
| Regional context | Not emphasized | Core contribution |

### Takeaway
AgriEval proves there is demand for non-English agricultural benchmarks at top venues (published at AAAI). Our project adds multi-turn, context sensitivity, and advisory quality dimensions that AgriEval lacks.

---

## Paper 3: AgMMU (2025)

- **Title:** AgMMU: A Comprehensive Agricultural Multimodal Understanding and Reasoning Benchmark
- **ArXiv:** (2025)
- **Website:** https://agmmu.github.io/

### Summary
746 MCQ + 746 OEQ distilled from 116,231 real USDA expert dialogues. Development corpus AgBase: 57,387 multimodal facts. Covers 5 agricultural topics: insect ID, species ID, disease categorization, symptom description, management instruction.

### Key Results
- Fine-tuning LLaVA-1.5: +4.7% MCQ, +11.6% OEQ improvement

### Relevance
Shows value of real expert dialogues as data source. Our project should similarly emphasize real farmer interactions, not just synthetic data.

---

## Paper 4: AI AgriBench (2025-2026)

- **Title:** AI AgriBench
- **Institution:** Center for Digital Agriculture, UIUC
- **Website:** https://aiagribench.org/
- **Partners:** Bayer, John Deere, Microsoft, FAO, CGIAR

### Summary
416 expert-curated QA pairs across 31 crops, 9 agronomic categories. Public leaderboard using LLM-as-judge (Claude Opus 4.5, Gemini3-Pro-Preview, Kimi-K2-thinking). Metrics: accuracy, relevance, completeness, conciseness.

### Relevance to Our Project
- Our evaluation metrics (completeness, accuracy, relevance) align well
- Their LLM-as-judge methodology is directly applicable
- Our project should cite and compare methodology
- Smallholders Leaderboard (3,100 QA from India/Africa) validates need for regional benchmarks

---

## Paper 5: AgThoughts / AgReason (ICLR 2026 submitted)

- **Title:** Towards Large Reasoning Models for Agriculture
- **ArXiv:** 2505.19259
- **Venue:** Submitted to ICLR 2026

### Summary
44,600 QA pairs with reasoning traces for fine-tuning. 100 expert-curated evaluation questions (AgReason). 10 agronomic categories. Uses DeepSeek-R1 for reasoning trace generation. AgThinker: small reasoning models for consumer GPUs.

### Key Results
- Best baseline (Gemini): 36% accuracy on AgReason
- Fine-tuning with reasoning traces significantly improves performance

### Relevance
- AgReason has only 100 expert samples — validates that small expert-curated test sets are acceptable
- Reasoning traces could be added to our dataset design
- Shows value of fine-tuning with domain-specific reasoning data

---

## Paper 6: LocalBench (2025)

- **Title:** LocalBench: Benchmarking LLMs on County-Level Local Knowledge and Reasoning
- **ArXiv:** 2511.10459

### Summary
14,782 QA pairs across 526 US counties. Tests hyper-local knowledge. Top models: 56.8% narrative, <15.5% numerical. Web augmentation produces inconsistent results across models.

### Relevance
Directly supports our claim that LLMs struggle with local/regional knowledge. Our project extends this to agricultural advisory quality in a non-English, non-Western context.

---

## Paper 7: Digital Green DG-EVAL (2026)

- **ArXiv:** 2603.03294

### Summary
Atomic fact verification for agricultural advisory. Measures recall, precision, contradiction detection against expert ground truth. Fine-tuned models evaluated on verified agricultural knowledge recall.

### Relevance
Their fact verification approach could strengthen our evaluation pipeline. Consider adopting atomic fact decomposition for measuring advisory accuracy.

---

## Paper 8: RLHF for Agricultural Localization (2025)

- **Journal:** Advancements in Agricultural Development
- **Context:** iShamba platform, Kenya

### Summary
Used RLHF with 25,000+ expert-reviewed QA pairs to improve LLM agricultural advice. Technical accuracy: 1.53 → 1.95 (scale 0-2). Addressed gender/social bias in recommendations.

### Relevance
Shows that expert-grounded training data significantly improves advisory quality — supports our project's core hypothesis. Regional localization challenges in Kenya parallel Vietnam's situation.
