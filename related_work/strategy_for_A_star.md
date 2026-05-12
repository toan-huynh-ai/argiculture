# Strategy to Reach A* Conference Level

---

## Current State Assessment

### Strengths
- Clear research gap (no Vietnamese agriculture QA benchmark exists)
- Novel context sensitivity framework (3-level degradation)
- Expert involvement (Vietnamese agricultural expert)
- Multi-model evaluation (7 frontier LLMs)
- Preliminary results already show interesting findings

### Weaknesses to Address
- Single expert annotator (IAA cannot be computed)
- Small dataset scale (100-500 test samples)
- Heavily synthetic data (no real farmer interactions)
- Limited multimodality
- No comparison with MIRAGE (most similar prior work)
- No open-source release plan mentioned

---

## Action Items by Priority

### CRITICAL (Must-do for A* acceptance)

#### 1. Recruit More Expert Annotators
- **Why:** Reviewers at NeurIPS/ACL/EMNLP will reject a dataset paper with a single annotator
- **Target:** Minimum 3 experts (different regions of Vietnam)
- **Metric:** Report Inter-Annotator Agreement (Krippendorff's alpha >= 0.67)
- **Suggested experts:**
  - 1 expert from Northern Vietnam (current)
  - 1 expert from Central Vietnam / Central Highlands
  - 1 expert from Mekong Delta / Southern Vietnam
- **Timeline:** Recruit within 1-2 months

#### 2. Include Real Farmer Questions
- **Why:** MIRAGE used 35K+ real interactions; AgMMU used 116K real dialogues
- **Sources:**
  - Vietnamese agricultural hotlines (1900-xxxx)
  - Facebook farming groups (Hội Nông Dân, etc.)
  - Cục Bảo vệ Thực vật forums
  - Provincial agricultural extension centers
  - Existing QA from Vietnamese agri-apps
- **Target:** At least 1,000-2,000 real farmer questions
- **Benefit:** Dramatically strengthens data authenticity claims

#### 3. Scale Up Dataset
- **Current:** 100-500 test samples
- **Target for A*:**
  - Training/dev set: 5,000-10,000 samples
  - Test set: 500-1,000 expert-validated samples
- **Comparison:**
  - AgReason: 100 expert test (ICLR submitted) — minimum viable
  - AI AgriBench: 416 QA pairs — reasonable
  - MIRAGE: 35K+ — gold standard
- **Recommendation:** At minimum match AI AgriBench (400+), ideally reach 1K+ test

#### 4. Direct Comparison with MIRAGE
- **Why:** MIRAGE is the closest competitor (NeurIPS 2025)
- **How:**
  - Run MIRAGE evaluation protocol on your data
  - Show performance differences between English and Vietnamese agricultural QA
  - Highlight gaps MIRAGE doesn't address (dialect, regional specificity, advisory feasibility)
  - Cross-lingual comparison: same questions in EN vs VI

### HIGH PRIORITY (Strongly recommended)

#### 5. Add Multimodal Component
- **Why:** 5 of 8 major benchmarks include images; reviewers expect it
- **How:**
  - Include crop disease images from Vietnamese agricultural sources
  - Partner with Vietnamese crop protection agencies for image datasets
  - Add image_link and image_description fields (already in schema!)
- **Target:** At least 30-50% of samples should include images

#### 6. Strengthen Evaluation Framework
- **Current metrics:** Completeness, Accuracy, Relevance, Task Identification
- **Add:**
  - **Safety score:** Does the recommendation pose risks? (wrong pesticide, harmful dosage)
  - **Locality score:** How well-adapted is advice to Vietnamese conditions?
  - **Atomic fact verification:** Adopt DG-EVAL approach for fine-grained accuracy
  - **Reasoning traces:** Adopt AgThoughts approach for understanding model reasoning
- **LLM-as-judge alignment:** Report correlation between LLM judges and human experts (like AI AgriBench)

#### 7. Ablation Studies on Context
- **Why:** Your 3-level context degradation is the most novel contribution
- **Design rigorous ablations:**
  - Full context → remove region → remove season → remove weather → remove crop stage
  - Measure degradation curve for each model
  - Compare degradation patterns across models
  - Statistical significance tests (bootstrap, paired t-test)
- **Visualization:** Heatmaps showing per-model, per-context-level performance

#### 8. Open-Source Release Plan
- **Why:** NeurIPS D&B and EMNLP Resource tracks strongly prefer open datasets
- **Release:**
  - Dataset on HuggingFace with clear documentation
  - Evaluation code on GitHub
  - Leaderboard (even simple Google Sheets or GitHub-based)
  - Datasheet for dataset (Gebru et al.) or Data Statement (Bender & Friedman)

### RECOMMENDED (Differentiators)

#### 9. Cross-lingual Evaluation
- Translate a subset of your dataset to English
- Compare LLM performance on same questions in Vietnamese vs English
- Hypothesis: performance gap reveals language-specific knowledge gaps
- This would be a strong empirical contribution

#### 10. Fine-tuning Experiments
- Fine-tune an open-source LLM (e.g., Llama, Qwen) on your training data
- Show improvement on Vietnamese agricultural advisory quality
- Compare with zero-shot and few-shot baselines
- This moves beyond "just a dataset paper" to a "dataset + method paper"

#### 11. User Study with Real Farmers
- Have actual Vietnamese farmers evaluate LLM responses
- Compare farmer preferences vs expert evaluations
- This would be extremely compelling for reviewers
- Even 20-30 farmers would be impactful

#### 12. Dialect Analysis
- Quantify how model performance varies across Vietnamese dialects
- Northern Vietnamese vs Central Vietnamese vs Southern Vietnamese
- Dialect-specific vocabulary mapping
- This is unique and not addressed by any existing work

---

## Suggested Paper Structure for A*

```
1. Introduction
   - Problem: LLMs fail at Vietnamese agricultural advisory
   - Gap: No VN agriculture benchmark; no context sensitivity evaluation
   - Contribution: Dataset + Evaluation framework + Findings

2. Related Work
   - Agricultural QA benchmarks (MIRAGE, AgriEval, AgMMU, AgroBench)
   - Context-aware QA (LocalBench, MapEval)
   - LLM evaluation for advisory systems (AI AgriBench, DG-EVAL)
   - Vietnamese NLP resources

3. Dataset Construction
   3.1 Scenario Design (region, crop, weather, issue, user profile)
   3.2 Expert Annotation Pipeline (3-stage)
   3.3 Quality Control (IAA, rejection criteria)
   3.4 Dataset Statistics & Analysis

4. Evaluation Framework
   4.1 Context Sensitivity Protocol (3 levels)
   4.2 Metrics (completeness, accuracy, relevance, safety, locality)
   4.3 LLM-as-Judge Setup & Alignment with Experts

5. Experiments
   5.1 Models Evaluated (7+ frontier LLMs)
   5.2 Single-turn Results
   5.3 Multi-turn Results
   5.4 Context Degradation Analysis
   5.5 Regional Bias Analysis
   5.6 Ablation Studies

6. Analysis & Discussion
   6.1 Why LLMs Fail at Vietnamese Agriculture
   6.2 Context vs Advisory Quality Trade-off
   6.3 Dialect Impact
   6.4 Comparison with MIRAGE (cross-lingual gap)

7. Conclusion & Future Work
```

---

## Timeline Suggestion

| Phase | Duration | Deliverable |
|-------|----------|-------------|
| Expert recruitment | Month 1-2 | 3+ annotators confirmed |
| Real farmer data collection | Month 1-3 | 1,000+ real questions |
| Dataset expansion to 90 crops | Month 2-4 | Full dataset draft |
| Expert annotation (Stage 1) | Month 3-5 | Gold standard answers |
| Model evaluation | Month 4-5 | All 7+ models evaluated |
| LLM review + expert alignment (Stage 2) | Month 5-6 | IAA reports |
| Final benchmark validation (Stage 3) | Month 6-7 | 500-1K validated test set |
| Paper writing | Month 6-8 | Full draft |
| Internal review + revision | Month 8-9 | Camera-ready |

### Target Submission
- **NeurIPS 2026 D&B Track** (~June 2026 deadline) — if timeline allows
- **EMNLP 2026** (ARR May 25, 2026) — backup option
- **ACL 2027** (~January 2027) — if more time needed
