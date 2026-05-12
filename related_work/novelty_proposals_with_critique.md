# Đề xuất Novelty & Tự phản biện

> Mỗi đề xuất được đánh giá: CÓ THẬT SỰ NOVEL KHÔNG? CÓ KHẢ THI KHÔNG? CÓ CẦN THIẾT KHÔNG?
> Dựa trên evidence từ literature search thực tế, không phải suy đoán.
>
> Last updated: 2026-05-12

---

## PROPOSAL 1: Systematic Context Degradation Protocol

### Ý tưởng
Thiết kế protocol đo lường sự suy giảm chất lượng tư vấn khi thông tin ngữ cảnh bị loại
bỏ dần theo 3 cấp (hoặc mở rộng thành 5+ cấp fine-grained):
- Level 0: Full context (vùng + mùa + thời tiết + đất + giai đoạn cây)
- Level 1: Bỏ thời tiết
- Level 2: Bỏ thời tiết + mùa vụ
- Level 3: Bỏ thời tiết + mùa vụ + vùng miền
- Level 4: Chỉ có tên cây + triệu chứng (vague question)

### Kiểm chứng: CÓ AI LÀM RỒI CHƯA?

**MIRAGE** có 2 subset (contextual vs standard) nhưng KHÔNG làm systematic degradation
theo từng thành phần. Nó chỉ chia binary: có context vs không context.

**TurnWise (2026)** nghiên cứu performance gap giữa single-turn và multi-turn, nhưng
không phải context degradation cho advisory quality.

**Ablation trên context components** trong multi-turn QA có nghiên cứu (2025-2026), nhưng
tập trung vào general dialogue, KHÔNG phải domain-specific agricultural advisory.

### Tự phản biện

- **NOVEL KHÔNG?** CÓ — việc systematic loại bỏ từng component (region → season →
  weather → soil → crop stage) và đo tác động lên advisory quality chưa ai làm, đặc
  biệt trong agriculture domain.
- **KHẢ THI KHÔNG?** CÓ — dự án đã có 3 cấp, chỉ cần mở rộng thành 5+ cấp
  fine-grained hơn.
- **CẦN THIẾT KHÔNG?** RẤT CẦN — đây có thể là contribution mạnh nhất. Nó trả lời
  câu hỏi thực tiễn: "nông dân cần cung cấp bao nhiêu thông tin để LLM tư vấn đúng?"
- **RỦI RO:** Reviewer có thể nói "chỉ là ablation study, không phải contribution mới."
  Cần frame nó như một evaluation FRAMEWORK có thể tái sử dụng cho domain khác.
- **KHUYẾN NGHỊ:** ★★★★★ ĐÂY LÀ NOVEL CONTRIBUTION CHÍNH. Nên là core claim
  của paper.

---

## PROPOSAL 2: Regional Bias Quantification

### Ý tưởng
Đo lường và chứng minh rằng LLM có bias hệ thống: khi thiếu thông tin vùng miền,
model mặc định gán về Đồng bằng sông Cửu Long hoặc Đồng bằng Bắc Bộ.

### Kiểm chứng: CÓ AI LÀM RỒI CHƯA?

**AgriRegion (arXiv 2512.10114, Dec 2025)** ĐÃ chỉ ra "contextual hallucination" trong
agriculture — advice đúng ở vùng này nhưng sai ở vùng khác. Tuy nhiên nó KHÔNG đo
bias hệ thống theo hướng "model mặc định về vùng nào." AgriRegion tập trung vào RAG
solution, không phải benchmark/evaluation.

**EarthWhere (2025)** cho thấy regional accuracy gap lên đến 42.7% trong geolocation,
nhưng cho general vision-language tasks, KHÔNG phải agriculture.

**LocalBench (2025)** test county-level knowledge ở US, nhưng KHÔNG đo agricultural
advisory bias.

### Tự phản biện

- **NOVEL KHÔNG?** MỘT PHẦN — regional bias trong LLM đã được chứng minh (EarthWhere,
  LocalBench). Nhưng regional bias *cụ thể cho nông nghiệp Việt Nam* (default to Mekong
  Delta) chưa ai document.
- **KHẢ THI KHÔNG?** CÓ — dự án đã có preliminary data cho thấy hiện tượng này.
- **CẦN THIẾT KHÔNG?** CÓ, nhưng không nên là contribution chính. Nên là một FINDING
  thú vị hỗ trợ cho motivation.
- **RỦI RO:** Reviewer có thể nói "đây là expected behavior vì training data thiên về
  những vùng phổ biến hơn." Cần giải thích TẠI SAO bias này gây hại thực sự (ví dụ:
  khuyến cáo thuốc trừ sâu sai vùng có thể gây thiệt hại mùa màng).
- **QUAN TRỌNG:** Phải acknowledge AgriRegion trong related work. Nếu không reviewer sẽ
  hỏi "sao không cite AgriRegion?"
- **KHUYẾN NGHỊ:** ★★★★☆ Tốt như supporting finding, KHÔNG nên là main claim.

---

## PROPOSAL 3: Cross-lingual Agricultural QA Comparison

### Ý tưởng ban đầu
Dịch một subset sang tiếng Anh, so sánh performance EN vs VI trên cùng câu hỏi.

### Kiểm chứng: CÓ AI LÀM RỒI CHƯA?

**ĐÃ CÓ RẤT NHIỀU:**
- MMLU-ProX (2025): 11,829 câu hỏi giống nhau qua 29 ngôn ngữ
- XLQA (EMNLP 2025): 3,000 câu hỏi qua 8 ngôn ngữ, đã chỉ ra "locale-sensitive failures"
- MultiLoKo (2025): 31 ngôn ngữ, đã chứng minh "suboptimal knowledge transfer"

### Tự phản biện

- **NOVEL KHÔNG?** KHÔNG novel về methodology. Cross-lingual QA comparison đã là well-
  studied field. CÓ novel nếu focus vào domain-specific agricultural knowledge gap
  between languages — nhưng contribution sẽ incremental.
- **KHẢ THI KHÔNG?** CÓ, nhưng tốn thời gian dịch thuật + kiểm tra chất lượng dịch.
- **CẦN THIẾT KHÔNG?** KHÔNG BẮT BUỘC. Nếu có thì tốt, nhưng reviewer sẽ không reject
  vì thiếu cross-lingual comparison. Paper đã đủ dài nếu có các contribution khác.
- **RỦI RO:** Làm loãng focus của paper. NeurIPS D&B reviewer muốn thấy dataset
  quality, không phải thêm experiments vô tận.
- **KHUYẾN NGHỊ:** ★★☆☆☆ BỎ hoặc chỉ đề cập ngắn trong discussion. Không đáng
  đầu tư effort.

---

## PROPOSAL 4: Safety Score cho Agricultural Advisory

### Ý tưởng
Đánh giá xem khuyến nghị của LLM có gây rủi ro không (sai thuốc trừ sâu, liều lượng
sai, timing sai có thể gây hại).

### Kiểm chứng: CÓ AI LÀM RỒI CHƯA?

**ĐÃ CÓ NHIỀU CÔNG TRÌNH:**
- PestMA (Apr 2025): Multi-agent system với Validator agent kiểm tra accuracy +
  compliance + safety. Đạt 92.6% accuracy sau validation.
- DG-EVAL (2026): Atomic fact verification, đo contradiction detection.
- AgriRegion (Dec 2025): Giảm hallucination 10-20% qua region-aware retrieval.
- Digital Green fine-tuning (2026): Separate "stitching layer" cho culturally appropriate,
  safety-aware responses.

### Tự phản biện

- **NOVEL KHÔNG?** KHÔNG — safety evaluation cho agricultural LLM đã được nghiên cứu
  bởi nhiều nhóm (PestMA, DG-EVAL, AgriRegion). Nếu chỉ thêm "safety score" vào
  metrics thì KHÔNG phải contribution mới.
- **CÓ THỂ LÀM NOVEL KHÔNG?** CÓ — nếu thiết kế safety taxonomy cụ thể cho Việt Nam
  (thuốc trừ sâu bị cấm ở VN nhưng được phép ở nước khác, liều lượng phân bón theo
  tiêu chuẩn VN, etc.). Cần danh sách thuốc BVTV được phép ở VN từ Cục BVTV.
- **CẦN THIẾT KHÔNG?** TÙY — nếu có dữ liệu cụ thể từ Cục BVTV thì rất mạnh. Nếu
  chỉ generic thì reviewer nói "PestMA đã làm rồi."
- **KHUYẾN NGHỊ:** ★★★☆☆ CHỈ LÀM NẾU có access vào danh sách thuốc BVTV chính thức
  của Việt Nam. Nếu không, bỏ qua.

---

## PROPOSAL 5: Vietnamese Dialect Impact on Agricultural QA

### Ý tưởng
Đo lường model performance khi nông dân hỏi bằng phương ngữ Bắc / Trung / Nam.

### Kiểm chứng: CÓ AI LÀM RỒI CHƯA?

**Vietnamese dialect NLP ĐÃ CÓ NGHIÊN CỨU:**
- ViMD (EMNLP 2024): 63 provincial dialects, 102.56 giờ audio, 19K utterances
- ViDia2Std (AAAI 2026): Parallel corpus 13K+ câu, dialect-to-standard translation cho
  cả 63 tỉnh
- Dialect-aware phonetic framework: WER 13.35%, dialect ID accuracy >95%

**NHƯNG:** Tất cả đều là general-purpose NLP. KHÔNG có ai nghiên cứu impact of dialect
on domain-specific agricultural QA.

### Tự phản biện

- **NOVEL KHÔNG?** MỘT PHẦN — dialect NLP cho tiếng Việt đã có (ViMD, ViDia2Std).
  Nhưng giao điểm dialect + agriculture QA thì CHƯA có ai làm.
- **KHẢ THI KHÔNG?** KHÓ — cần thu thập câu hỏi nông nghiệp thực tế bằng phương ngữ
  từ nông dân các vùng. Không thể fake dialect một cách tự nhiên. Nếu dùng LLM sinh
  dialect thì reviewer sẽ question authenticity.
- **CẦN THIẾT KHÔNG?** KHÔNG BẮT BUỘC cho A* acceptance. Là differentiator tốt nhưng
  effort quá cao so với impact.
- **RỦI RO LỚN:** Nếu dialect data không authentic (sinh bởi LLM), reviewer sẽ reject.
  ViDia2Std dùng real Facebook comments — tiêu chuẩn rất cao.
- **KHUYẾN NGHỊ:** ★★☆☆☆ BỎ khỏi main paper. Có thể đề cập như future work. Trừ khi
  team có access vào dữ liệu nông dân thực bằng phương ngữ.

---

## PROPOSAL 6: Fine-tuning Vietnamese Agricultural LLM

### Ý tưởng
Fine-tune Qwen/Llama trên dataset để chứng minh dataset có giá trị cho downstream task.

### Kiểm chứng: CÓ AI LÀM RỒI CHƯA?

**ĐÃ CÓ:**
- AgriMind AI (ngocbao220, Dec 2025 - Mar 2026): Fine-tuned Qwen2.5-32B-Instruct
  với LoRA cho 8 cây trồng Việt Nam. Đã có RAG pipeline.
- Digital Green (2026): LoRA fine-tuning trên expert-curated "Golden Facts" cho Mistral
  7B, Qwen 2.5 7B, LLaMA 3.1 8B. Performance >88%.
- AgThoughts (2025): Fine-tuning với reasoning traces tạo ra AgThinker.

### Tự phản biện

- **NOVEL KHÔNG?** KHÔNG về method (LoRA fine-tuning là standard). CÓ NOVEL nếu
  fine-tune trên expert-validated Vietnamese agriculture data (90 loại cây) — tức là
  novelty nằm ở DATA, không phải method.
- **KHẢ THI KHÔNG?** CÓ — straightforward với LoRA.
- **CẦN THIẾT KHÔNG?** CÓ, RẤT CẦN — NeurIPS D&B reviewer muốn thấy dataset có
  utility. Fine-tuning experiments chứng minh dataset có giá trị thực.
  Trích reviewer guidelines: "likelihood of adoption or building upon the work."
- **RỦI RO:** AgriMind AI đã fine-tune Qwen2.5 cho VN agriculture. Cần differentiate:
  "chúng tôi fine-tune trên expert-validated data với 90 crop species, không phải 8."
- **KHUYẾN NGHỊ:** ★★★★☆ NÊN LÀM nhưng đừng claim là main contribution. Frame nó
  như "downstream utility demonstration."

---

## PROPOSAL 7: Farmer User Study

### Ý tưởng
Cho nông dân thực đánh giá câu trả lời LLM, so sánh farmer perception vs expert
evaluation.

### Kiểm chứng: CÓ AI LÀM RỒI CHƯA?

**ĐÃ CÓ NHƯNG KHÔNG CHO VIỆT NAM:**
- FarmerChat (IFPRI + Digital Green, 2025-2027): User study lớn ở India và Kenya.
  1.5M+ queries. NPS ~60. Đã test với women, youth, underserved farmers.
- Tuy nhiên KHÔNG có farmer user study nào cho nông dân Việt Nam + LLM.

### Tự phản biện

- **NOVEL KHÔNG?** MỘT PHẦN — farmer user study methodology đã có (FarmerChat). Nhưng
  cho nông dân Việt Nam thì CHƯA.
- **KHẢ THI KHÔNG?** KHÓ — cần IRB approval, recruitment logistics, có thể cần đi
  thực địa. Tuy nhiên chỉ cần 20-30 nông dân là đủ cho qualitative study.
- **CẦN THIẾT KHÔNG?** KHÔNG BẮT BUỘC cho NeurIPS D&B. Dataset papers thường không
  yêu cầu user study. NHƯNG sẽ rất impressive nếu có.
- **RỦI RO:** Tốn thời gian 2-3 tháng. Có thể delay submission.
- **KHUYẾN NGHỊ:** ★★★☆☆ NÊN LÀM NẾU có thời gian. Nếu không, đề cập như future work
  và focus vào expert evaluation.

---

## PROPOSAL 8: Atomic Fact Decomposition cho Agricultural Advisory

### Ý tưởng
Phân tách câu trả lời LLM thành từng "atomic fact" và verify từng fact riêng lẻ, thay
vì đánh giá holistic.

### Kiểm chứng: CÓ AI LÀM RỒI CHƯA?

**ĐÃ CÓ:**
- DG-EVAL (Digital Green, 2026): Chính xác method này — atomic fact verification với
  recall, precision, contradiction detection.
- FActScore (2023): General-purpose atomic fact evaluation cho LLM.

### Tự phản biện

- **NOVEL KHÔNG?** KHÔNG — DG-EVAL đã làm rồi, cụ thể cho agriculture.
- **CẦN THIẾT KHÔNG?** CÓ THỂ — adopt DG-EVAL methodology cho Vietnamese context là
  hợp lý. Nhưng KHÔNG phải novelty.
- **KHUYẾN NGHỊ:** ★★☆☆☆ Cite DG-EVAL và adopt methodology, nhưng ĐỪNG claim nó là
  contribution của mình.

---

## PROPOSAL 9: Multi-turn Clarification Strategy Evaluation

### Ý tưởng MỚI (chưa đề xuất trước đó)
Đánh giá khả năng LLM hỏi lại (clarification) khi nông dân cung cấp thông tin thiếu.
Thay vì trả lời ngay với thông tin thiếu, model tốt nên hỏi: "Bạn ở vùng nào? Mùa vụ
nào?" trước khi tư vấn.

### Kiểm chứng: CÓ AI LÀM RỒI CHƯA?

**MIRAGE-MMMT** có đánh giá "conversational decision-making" bao gồm clarification,
nhưng không systematic. Nó đánh giá dialogue state tracking chung, không đo cụ thể
"model có biết khi nào cần hỏi lại không."

**Underspecification literature**: Có nghiên cứu về handling underspecified queries
(MIRAGE gọi là "latent knowledge gaps"), nhưng không có benchmark đo cụ thể
clarification quality trong agricultural context.

### Tự phản biện

- **NOVEL KHÔNG?** CÓ — việc đo CLARIFICATION QUALITY (model biết hỏi lại vs trả lời
  bừa) trong agricultural advisory chưa có benchmark nào làm systematic.
- **KHẢ THI KHÔNG?** CÓ — thiết kế test cases: cho model câu hỏi thiếu thông tin, đo
  xem model có hỏi lại không, hỏi lại đúng thông tin cần không, và answer quality cải
  thiện bao nhiêu sau clarification.
- **CẦN THIẾT KHÔNG?** RẤT CẦN — đây là real-world scenario. Nông dân thực tế hỏi vắn
  tắt. Model tốt phải biết hỏi lại, không phải đoán bừa.
- **RỦI RO:** Phải thiết kế careful — metric đo clarification quality cần definition rõ.
- **KHUYẾN NGHỊ:** ★★★★★ ĐÂY LÀ NOVELTY RẤT MẠNH. Kết hợp với Proposal 1 (context
  degradation) tạo thành bộ đánh giá hoàn chỉnh:
  "Khi context thiếu → model có biết hỏi lại không → nếu hỏi lại thì quality cải thiện
  bao nhiêu?"

---

## PROPOSAL 10: Region-Aware Advisory Feasibility Score

### Ý tưởng MỚI
Đánh giá xem khuyến nghị có khả thi về mặt thực tiễn tại địa phương không:
- Thuốc/phân bón được khuyến cáo có bán ở vùng đó không?
- Kỹ thuật canh tác có phù hợp với quy mô nông hộ nhỏ không?
- Khuyến cáo có phù hợp với điều kiện kinh tế nông dân không?

### Kiểm chứng: CÓ AI LÀM RỒI CHƯA?

**AgriRegion (2025)** giảm hallucination qua region-aware retrieval, nhưng KHÔNG đo
feasibility for smallholders.

**FarmerChat** phát hiện rằng trust tăng khi advice "locally relevant and specific" —
nhưng KHÔNG có metric đo feasibility.

**AI AgriBench Smallholders Leaderboard** có 3,100 QA từ India/Africa nhưng KHÔNG có
feasibility score.

### Tự phản biện

- **NOVEL KHÔNG?** CÓ — advisory feasibility (thuốc có bán không? nông dân đủ tiền
  không? kỹ thuật có áp dụng được với ruộng 500m² không?) là dimension chưa ai đo
  systematic.
- **KHẢ THI KHÔNG?** KHÓ VỪA — cần expert annotation cho feasibility. Cần biết thị
  trường nông nghiệp từng vùng. Expert hiện tại có thể đánh giá được.
- **CẦN THIẾT KHÔNG?** CÓ — đây là real-world impact lớn. LLM khuyến cáo thuốc đắt
  tiền hoặc không bán ở vùng đó thì advice vô dụng.
- **RỦI RO:** Feasibility là subjective — cần rubric rõ ràng.
- **KHUYẾN NGHỊ:** ★★★★☆ RẤT TỐT cho differentiation với MIRAGE/AgriEval. Nên đưa
  vào evaluation framework.

---

## PROPOSAL 11: Reasoning Trace Analysis

### Ý tưởng
Yêu cầu LLM giải thích reasoning khi trả lời, phân tích xem reasoning chain đúng hay sai.

### Kiểm chứng:

**AgThoughts (ICLR 2026 submitted)** ĐÃ LÀM CỤ THỂ ĐIỀU NÀY — 44.6K QA với reasoning
traces. AgReason benchmark đo reasoning quality.

### Tự phản biện

- **NOVEL KHÔNG?** KHÔNG — AgThoughts đã làm rồi, rất thorough.
- **KHUYẾN NGHỊ:** ★☆☆☆☆ BỎ. Cite AgThoughts thay vì cố replicate.

---

## PROPOSAL 12: Multimodal Component (Thêm hình ảnh)

### Ý tưởng
Thêm hình ảnh cây bệnh vào dataset.

### Kiểm chứng:

**ĐÃ CÓ RẤT NHIỀU:**
- MIRAGE: multimodal
- AgMMU: multimodal
- AgroBench: multimodal, 682 disease categories
- PlantVillageVQA: 193K QA pairs + 55K images
- LeafNet: 186K images, 97 disease classes

### Tự phản biện

- **NOVEL KHÔNG?** KHÔNG về concept. Nhưng hình ảnh bệnh cây VIỆT NAM (cụ thể cho
  điều kiện khí hậu nhiệt đới gió mùa) có thể khác với US/EU.
- **KHẢ THI KHÔNG?** TRUNG BÌNH — cần nguồn ảnh chất lượng. Cục BVTV có thể có.
  Hoặc partner với trường đại học nông nghiệp.
- **CẦN THIẾT KHÔNG?** CÓ nếu target NeurIPS D&B (5/8 top benchmarks đều multimodal).
  KHÔNG bắt buộc nếu focus text-only.
- **RỦI RO LỚN:** Nếu chỉ thêm ảnh "cho có" mà không có enough images hoặc image
  quality thấp, reviewer sẽ đánh thấp. PlantVillageVQA có 55K images — khó cạnh tranh
  về quy mô.
- **KHUYẾN NGHỊ:** ★★★☆☆ NÊN CÂN NHẮC KỸ. Nếu có nguồn ảnh chất lượng (>1000 ảnh
  verified), thêm vào. Nếu không, tốt hơn là focus text-only và nói rõ "text-focused
  benchmark" thay vì thêm ảnh yếu.

---

## PROPOSAL 13: Seasonal Temporal Evaluation

### Ý tưởng MỚI
Đánh giá xem LLM có advice đúng theo THỜI ĐIỂM TRONG NĂM không. Ví dụ: khuyến cáo gieo
lúa vào tháng 10 ở Đồng bằng Bắc Bộ thì đúng (vụ Đông Xuân), nhưng nếu model khuyến
cáo gieo cùng thời điểm ở Mekong Delta thì sai (đang mùa lũ).

### Kiểm chứng:

Không tìm thấy benchmark nào đo temporal accuracy of agricultural advice. AgriRegion
đo region-awareness nhưng KHÔNG đo temporal/seasonal awareness.

### Tự phản biện

- **NOVEL KHÔNG?** CÓ — seasonal/temporal accuracy cho agricultural advice chưa được
  benchmark hóa.
- **KHẢ THI KHÔNG?** CÓ — expert có thể annotate correct timing cho từng vùng.
- **CẦN THIẾT KHÔNG?** CÓ — timing sai trong nông nghiệp = mất mùa. Đây là real-world
  critical.
- **RỦI RO:** Có thể overlap với context degradation protocol (bỏ season info). Cần
  differentiate rõ: context degradation đo "performance khi thiếu info," còn temporal
  evaluation đo "model có biết timing đúng không KHI CÓ ĐỦ info."
- **KHUYẾN NGHỊ:** ★★★★☆ TỐT — nhưng integrate vào Proposal 1 (context degradation)
  thay vì tách riêng. Season là một axis trong degradation framework.

---

## TỔNG HỢP: XẾP HẠNG THEO PRIORITY

| Rank | Proposal | Novel? | Khả thi? | Impact | Rating | Recommendation |
|------|----------|--------|----------|--------|--------|----------------|
| 1 | **P1: Context Degradation Protocol** | ★★★★★ | ★★★★★ | ★★★★★ | ★★★★★ | MAIN CONTRIBUTION #1 |
| 2 | **P9: Clarification Strategy Eval** | ★★★★★ | ★★★★☆ | ★★★★★ | ★★★★★ | MAIN CONTRIBUTION #2 |
| 3 | **P10: Feasibility Score** | ★★★★☆ | ★★★☆☆ | ★★★★★ | ★★★★☆ | EVALUATION FRAMEWORK |
| 4 | **P6: Fine-tuning Experiments** | ★★☆☆☆ | ★★★★★ | ★★★★☆ | ★★★★☆ | UTILITY DEMONSTRATION |
| 5 | **P2: Regional Bias** | ★★★☆☆ | ★★★★★ | ★★★★☆ | ★★★★☆ | SUPPORTING FINDING |
| 6 | **P13: Temporal/Seasonal Eval** | ★★★★☆ | ★★★★☆ | ★★★★☆ | ★★★★☆ | INTEGRATE INTO P1 |
| 7 | **P12: Multimodal** | ★★☆☆☆ | ★★★☆☆ | ★★★☆☆ | ★★★☆☆ | ONLY IF GOOD DATA |
| 8 | **P7: Farmer User Study** | ★★★☆☆ | ★★☆☆☆ | ★★★★☆ | ★★★☆☆ | IF TIME ALLOWS |
| 9 | **P4: Safety Score** | ★★☆☆☆ | ★★★☆☆ | ★★★☆☆ | ★★★☆☆ | ONLY WITH VN BVTV DATA |
| 10 | **P3: Cross-lingual** | ★☆☆☆☆ | ★★★★☆ | ★★☆☆☆ | ★★☆☆☆ | DROP |
| 11 | **P5: Dialect Analysis** | ★★★☆☆ | ★☆☆☆☆ | ★★★☆☆ | ★★☆☆☆ | FUTURE WORK |
| 12 | **P8: Atomic Fact Decomposition** | ★☆☆☆☆ | ★★★★☆ | ★★★☆☆ | ★★☆☆☆ | ADOPT, DON'T CLAIM |
| 13 | **P11: Reasoning Traces** | ★☆☆☆☆ | ★★★★☆ | ★★★☆☆ | ★☆☆☆☆ | DROP — AgThoughts did it |

---

## PROPOSED NOVELTY STORY (4 PILLARS)

Dựa trên phân tích trên, paper nên có 4 contribution claims:

### Contribution 1: ViAgri-Bench — First Expert-Validated Vietnamese Agriculture QA Benchmark
- ~90 crop species, 5 ecological regions
- 3-stage expert pipeline
- Multi-turn format

**Phản biện:** "First X for language Y" có thể bị xem là incremental. CẦN kết hợp với
contributions 2-3 để có enough depth.

### Contribution 2: Context Sensitivity Framework (CSF)
- Systematic protocol đo advisory quality degradation khi context bị loại bỏ
- Fine-grained ablation: region → season → weather → soil → crop stage
- KHÔNG chỉ có/không có context (binary như MIRAGE) mà là GRADIENT degradation
- Reusable framework: có thể áp dụng cho agriculture domain ở bất kỳ quốc gia nào

**Phản biện:** Reviewer có thể nói "just ablation study." ĐỐI PHÓ: frame nó như
evaluation methodology contribution, tương tự như cách FActScore tạo ra atomic fact
evaluation methodology.

### Contribution 3: Clarification-Aware Evaluation (MỚI)
- Đánh giá: khi context thiếu, model có biết HỎI LẠI không hay đoán bừa?
- Metric: Clarification Precision (hỏi đúng thông tin cần), Clarification Recall
  (nhận biết khi nào cần hỏi), Answer Improvement After Clarification
- Kết hợp với multi-turn: Turn 1 = vague question → Model hỏi lại → Turn 2 = farmer
  trả lời → Model tư vấn

**Phản biện:** MIRAGE cũng đo conversational decision-making. ĐỐI PHÓ: MIRAGE đo
dialogue state tracking chung, KHÔNG có specific metrics cho clarification quality.

### Contribution 4: Advisory Feasibility Evaluation
- Metric mới: khuyến nghị có khả thi tại địa phương không?
- Dimensions: availability (thuốc/phân bón có bán ở vùng đó?), affordability
  (nông dân nhỏ có đủ khả năng?), applicability (có áp dụng được với quy mô
  nông hộ nhỏ?)

**Phản biện:** Subjective metric, khó standardize. ĐỐI PHÓ: Expert annotation +
IAA score cho feasibility dimension.

---

## PAPERS CẦN CẬP NHẬT VÀO RELATED WORK

Từ research lần này, phát hiện thêm papers quan trọng chưa có trong literature review:

| Paper | Tại sao quan trọng |
|-------|--------------------|
| **AgriRegion** (arXiv 2512.10114) | Region-aware RAG cho agriculture — PHẢI cite |
| **PestMA** (arXiv 2504.09855) | Multi-agent agricultural safety — liên quan P4 |
| **ViDia2Std** (AAAI 2026) | Vietnamese dialect parallel corpus — nếu làm P5 |
| **ViMD** (EMNLP 2024) | Vietnamese multi-dialect dataset — background |
| **FarmerChat / GAIA** (IFPRI 2025) | User study methodology cho nông dân |
| **TurnWise** (arXiv 2026) | Single vs multi-turn gap — liên quan P1 |
| **MMLU-ProX** (2025) | Cross-lingual benchmark — nếu làm P3 |
| **AgriPestDatabase-v1.0** (2026) | Structured insect dataset cho agricultural LLM |
