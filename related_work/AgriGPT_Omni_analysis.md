# Phân tích chi tiết: AgriGPT-Omni

---

## 1. Thông tin cơ bản

| Mục | Chi tiết |
|-----|----------|
| **Tiêu đề** | AgriGPT-Omni: A Unified Speech–Vision–Text Framework for Multilingual Agricultural Intelligence |
| **Tác giả** | Bo Yang, Lanfei Feng, Yunkui Chen, Yu Zhang, Jianyu Zhang, Xiao Xu, Nueraili Aierken, Shijian Li* |
| **Đơn vị** | Zhejiang University (Hangzhou & Ningbo, China) |
| **Năm** | 2025 (arXiv: 2512.10624v1, ngày 11/12/2025) |
| **Venue** | Preprint (arXiv), chưa rõ venue submit chính thức |

---

## 2. Tóm tắt

AgriGPT-Omni là hệ thống omni-modal đầu tiên trong lĩnh vực nông nghiệp, tích hợp **ba modality: speech, vision, và text** trong một framework thống nhất. Đóng góp chính gồm ba trụ cột:

1. **Dữ liệu:** Xây dựng bộ dữ liệu speech nông nghiệp đa ngôn ngữ lớn nhất (492K synthetic + 1.4K real speech samples) qua pipeline hybrid synthetic–human, hỗ trợ 6 ngôn ngữ.
2. **Mô hình:** Huấn luyện mô hình omni-modal đầu tiên cho nông nghiệp qua paradigm 3 giai đoạn: text knowledge injection → progressive multimodal alignment → GRPO-based reinforcement learning.
3. **Benchmark:** Đề xuất AgriBench-Omni-2K, benchmark tri-modal đầu tiên cho nông nghiệp, bao gồm 4 loại task × 6 ngôn ngữ với evaluation protocols chuẩn hóa.

---

## 3. Vấn đề nghiên cứu

Bài báo xác định **ba khoảng trống** trong AI nông nghiệp hiện tại:

1. **Thiếu dữ liệu speech nông nghiệp đa ngôn ngữ:** Không tồn tại bộ dữ liệu speech chuyên biệt cho nông nghiệp, đặc biệt cho speech QA, speech+image understanding, và multilingual agricultural instructions.

2. **Thiếu kiến trúc multimodal thống nhất:** Các hệ thống nông nghiệp hiện tại (AgriGPT-VL, AgriDoctor, AgroBench) chỉ dừng ở text–image, không tích hợp speech — mặc dù speech là giao diện tự nhiên nhất cho nông dân trên thực địa.

3. **Thiếu benchmark đánh giá toàn diện:** Không có benchmark nào đồng thời đánh giá speech input, speech–image reasoning, và multilingual capabilities trong ngữ cảnh nông nghiệp.

**Luận điểm cốt lõi:** Nông nghiệp vốn là lĩnh vực multimodal (nông dân cần hỏi bằng giọng nói, gửi ảnh cây bệnh, nhận câu trả lời text) và context-dependent — nhưng các hệ thống hiện tại không đáp ứng được yêu cầu này.

---

## 4. Phương pháp

### 4.1. Kiến trúc Unified Speech–Vision–Text Framework

**Base model:** Qwen-2.5-Omni (7B parameters)

**Pipeline huấn luyện 3 giai đoạn:**

| Giai đoạn | Nội dung | Dữ liệu | Cơ chế |
|-----------|----------|----------|--------|
| **Stage 1: Text Knowledge Injection** | (1) Continued pretraining 2.2B tokens; (2) Text-only SFT 342K QA; (3) Speech–text alignment 342K pairs | Corpus nông nghiệp + Agri-342K | Toàn bộ backbone + audio encoder + adapters được cập nhật |
| **Stage 2: Multimodal Alignment** | (1) Vision–language alignment (frozen) 600K pairs; (2) Speech–language alignment (frozen) 342K pairs; (3) Unfrozen VQA tuning 800K; (4) High-quality refinement 50K GPT-4o samples; (5) Tri-modal QA 140K | Agri-VL-3M + audio data | Freeze → unfreeze progressive strategy |
| **Stage 3: GRPO Reinforcement Learning** | Preference optimization với 3 reward sources | 2.5K speech MC + 5K speech/image MC + 1.4K transcription pairs | Clipped surrogate objective + KL penalty |

**Điểm đáng chú ý về kiến trúc:**
- Sử dụng **progressive unfreezing** strategy: đầu tiên chỉ update adapters (alignment ổn định), sau đó mới unfreeze backbone.
- GRPO training dùng **exact-match reward** cho multiple-choice tasks và **edit-distance reward** (WER/CER) cho transcription tasks.
- Không huấn luyện từ đầu mà fine-tune từ Qwen-2.5-Omni, tận dụng khả năng đa ngôn ngữ sẵn có.

### 4.2. Ngôn ngữ được hỗ trợ

**6 ngôn ngữ:** Chinese (普通话), Sichuan dialect (四川话), Cantonese (粤语), English, Japanese, Korean.

| Đặc điểm | Chi tiết |
|-----------|----------|
| **Ngôn ngữ chính** | Chinese (Mandarin) |
| **Phương ngữ** | Sichuan dialect, Cantonese |
| **Ngôn ngữ Đông Á** | Japanese, Korean |
| **Ngôn ngữ phương Tây** | English |
| **Tiếng Việt** | **KHÔNG CÓ** |
| **Ngôn ngữ Đông Nam Á khác** | **KHÔNG CÓ** |

> **Quan sát quan trọng:** Mặc dù paper tuyên bố "multilingual" và nhắm đến "low-resource regions," bộ ngôn ngữ thực tế thiên hướng Đông Á (3/6 là Chinese hoặc phương ngữ Chinese). Không có ngôn ngữ nào thuộc Đông Nam Á — khu vực nông nghiệp quan trọng toàn cầu.

### 4.3. Cách xây dựng AgriBench-Omni-2K

**Quy trình xây dựng:**

1. **Nguồn dữ liệu gốc:** Agri-342K (text QA) + AgriVL-150K (image–text QA)
2. **Dịch đa ngôn ngữ:** Sử dụng Qwen2.5-72B dịch sang 6 ngôn ngữ
3. **Tổng hợp speech:** CosyVoice-0.5B (TTS) → 490K speech clips
4. **Thu âm thực tế:** 2,200 bản thu từ tình nguyện viên đa ngôn ngữ → lọc còn 1,431 (training) + 586 (evaluation)
5. **De-duplication:** ROUGE-L similarity (threshold 0.7) + GPT-4-based semantic filtering
6. **Validation:** Domain experts review + unified scoring protocol

**4 loại task trong benchmark:**

| Task | Input | Output | Mục đích đánh giá |
|------|-------|--------|-------------------|
| Audio QA | Câu hỏi bằng speech | Free-form text | Speech comprehension + generation |
| Audio+Text MC | Speech question + text choices | Chọn đáp án | Speech parsing + semantic understanding |
| Multimodal QA | Speech question + image | Descriptive text | Multimodal perception + reasoning |
| Multimodal MC | Speech + image + text choices | Chọn đáp án | Tri-modal alignment + decision-making |

### 4.4. Quy mô và thành phần dữ liệu

| Thành phần | Quy mô | Chi tiết |
|------------|--------|----------|
| **Synthesized speech** | ~492K samples | 342K từ Agri-342K + 150K từ Agri-VL-3M |
| **Real speech (training)** | 1,431 samples | Filtered từ 1,500 multiple-choice recordings |
| **Real speech (evaluation)** | 586 samples | Filtered từ 600 transcription recordings |
| **GRPO subset** | 8,931 samples | Speech MC + speech–image MC + transcription |
| **Benchmark total** | ~2,100 samples | 100 questions × 6 languages × 4 task types + 586 real speech |
| **Text pretraining** | 2.2B tokens | Agricultural domain corpus |
| **VL alignment** | 600K image–caption pairs | Vision–language stage |
| **High-quality VQA** | 50K GPT-4o curated | Refinement stage |
| **Tri-modal QA** | 140K samples | Speech + image → text |

---

## 5. Evaluation

### 5.1. Metrics

| Task type | Metrics |
|-----------|---------|
| Open-ended QA (text generation) | BLEU, METEOR, ROUGE-1-f, ROUGE-2-f, ROUGE-L-f |
| Open-ended VQA | Pairwise win rate (GPT-4-based judging) |
| Multiple choice | Accuracy (Exact Match) |
| Transcription (ASR) | WER (English), CER (các ngôn ngữ khác) |
| Real-world robustness | Win/Tie/Loss comparison: synthetic vs. human speech |

### 5.2. Baseline models

Tổng cộng 15 baselines, chia thành:

- **Vision-language models:** InternVL-3-8B, LLaVA-1.5-7B, MiniCPM-V-2.6-8B, Yi-VL-6B, Yi-VL-34B, Qwen2.5-VL-7B-Instruct, QVQ-72B-Preview, DeepSeek-VL2
- **Production systems:** Gemini-2.5-Flash, Gemini-2.5-Pro
- **Omni-modal models:** Qwen2.5-Omni-3B, Qwen2.5-Omni-7B, Qwen2-Audio-7B-Instruct, Megrez-Omni-3B, Step-Audio-2-mini

### 5.3. Kết quả chính

**Text-only generation (AgriBench-13K):**
- AgriGPT-Omni đạt **tốt nhất** trên tất cả metrics: BLEU 9.69, METEOR 30.37, ROUGE-1-f 26.88 — vượt Qwen2.5-VL-7B (BLEU 7.69) và Gemini-2.5-Flash (BLEU 6.12).

**Image-text generation (AgriBench-VL-4K):**
- AgriGPT-Omni đạt **BLEU 16.89**, **ROUGE-1-f 42.25** — vượt đáng kể tất cả baselines bao gồm cả Gemini-2.5-Pro.

**Speech-based tasks:**
- Speech open QA: Win rate **75.77–98.74%** so với tất cả baselines. Đặc biệt mạnh trên phương ngữ (Sichuanese, Cantonese).
- Speech+Text MC: Accuracy **92.67%** (so với 83.83% của Qwen2.5-Omni-7B).
- Speech+Image QA: Win rate **89.89–94.85%** so với tất cả baselines.
- Speech+Image+Text MC: Accuracy **75.67%** (so với 59.17% của Qwen2.5-Omni-7B).

**Generalization (không mất khả năng general-domain):**
- MMLU: 62.81% (tăng +12.8 so với base Qwen2.5-Omni-7B)
- OpenBookQA: 80.74% (tăng +20.4)
- Vision benchmarks (MMBench, MMMU, SeedBench): giảm nhẹ ~1-3 điểm

**Real-world robustness:**
- Synthetic vs. Human speech: 234 wins / 111 ties / 232 losses → gần ngang nhau, cho thấy mô hình không phụ thuộc vào audio studio-quality.

---

## 6. Điểm mạnh & Điểm yếu

### Điểm mạnh

1. **Tiên phong:** Là mô hình omni-modal đầu tiên cho nông nghiệp, tích hợp speech–vision–text trong một framework duy nhất. Đây là contribution rõ ràng và có novelty cao.

2. **Pipeline huấn luyện 3 giai đoạn có hệ thống:** Progressive unfreezing + GRPO reinforcement learning cho kết quả cải thiện nhất quán (monotonic improvement) qua từng stage, được chứng minh bằng ablation study chi tiết.

3. **Benchmark toàn diện:** AgriBench-Omni-2K cover 4 task types × 6 ngôn ngữ, có de-duplication nghiêm ngặt (ROUGE-L + GPT-4 semantic filtering), expert validation, và reproducible evaluation scripts.

4. **Không mất khả năng general-domain:** Domain-specific fine-tuning thậm chí cải thiện MMLU (+12.8) và OpenBookQA (+20.4), chứng minh không overfitting.

5. **Thực tiễn cao:** Đánh giá trên cả synthetic và human speech, cho thấy khả năng triển khai thực tế.

6. **Cam kết open-source:** Toàn bộ models, datasets, benchmarks, và code sẽ được release.

### Điểm yếu

1. **"Multilingual" thiên lệch Đông Á:** 6 ngôn ngữ nhưng 3/6 là Chinese hoặc phương ngữ (Mandarin, Sichuanese, Cantonese). Thiếu hoàn toàn Đông Nam Á (Việt Nam, Thái Lan, Indonesia), Nam Á (Hindi, Bengali), và châu Phi — những vùng nông nghiệp trọng điểm toàn cầu.

2. **Speech data chủ yếu synthetic:** 492K synthetic vs. chỉ 1.4K real speech. Tỉ lệ >99% synthetic. Câu hỏi: liệu TTS-generated speech có đại diện cho giọng nói thực tế của nông dân (có nhiễu, accent mạnh, ngữ pháp không chuẩn)?

3. **Dịch bằng LLM, không phải native data:** Dữ liệu đa ngôn ngữ được tạo bằng cách dịch từ tiếng Trung qua Qwen2.5-72B. Translation artifacts có thể làm giảm chất lượng, đặc biệt với thuật ngữ nông nghiệp địa phương.

4. **Không đánh giá context sensitivity:** Paper không đề cập đến khả năng mô hình phân biệt đặc thù vùng miền (cùng một bệnh nhưng cách xử lý khác nhau ở vùng khác nhau) hay mùa vụ.

5. **Benchmark quy mô nhỏ:** 100 questions × 6 languages = 600 câu cơ bản, cộng 586 real speech → tổng ~2K. So với các benchmark text-based (AgriBench-13K, AgMMU), quy mô này khá khiêm tốn.

6. **Evaluation metrics thiên hướng surface matching:** BLEU, ROUGE đo overlap text, không đo chất lượng nội dung (factual accuracy, feasibility of recommendations). Thiếu expert-based evaluation cho nội dung lời khuyên nông nghiệp.

7. **Thiếu safety evaluation:** Không đánh giá rủi ro khi mô hình đưa ra lời khuyên sai (sử dụng thuốc bị cấm, liều lượng sai, thời điểm phun không phù hợp).

---

## 7. So sánh với dự án Vietnamese Agriculture QA

### 7.1. AgriGPT-Omni có hỗ trợ tiếng Việt không?

**KHÔNG.** 6 ngôn ngữ được hỗ trợ là Chinese (Mandarin), Sichuan dialect, Cantonese, English, Japanese, Korean. Không có ngôn ngữ Đông Nam Á nào. Điều này tạo ra **khoảng trống rõ ràng** mà dự án của chúng ta lấp đầy.

Mặc dù base model Qwen-2.5-Omni có thể hiểu tiếng Việt ở mức cơ bản (nhờ multilingual pretraining), AgriGPT-Omni:
- Không có dữ liệu fine-tuning nào bằng tiếng Việt
- Không có thuật ngữ nông nghiệp Việt Nam
- Không có expert validation cho nội dung tiếng Việt
- Không có phương ngữ Việt Nam (Bắc, Trung, Nam)

### 7.2. AgriGPT-Omni có đề cập context sensitivity hoặc regional specificity không?

**KHÔNG đáng kể.** Paper đề cập rằng nông nghiệp là "context-dependent" nhưng:

| Khía cạnh | AgriGPT-Omni | Dự án của chúng ta |
|-----------|--------------|-------------------|
| **Regional specificity** | Không đánh giá. Dữ liệu dựa trên Agri-342K (Chinese-centric) | 5 vùng sinh thái Việt Nam, mỗi vùng có đặc thù riêng |
| **Context degradation** | Không có thí nghiệm nào | Context degradation protocol: full → partial → minimal → zero |
| **Mùa vụ** | Không phân biệt | Calendar-aware: vụ Đông Xuân, Hè Thu, Mùa |
| **Điều kiện khí hậu** | Không evaluate | Soil type, temperature, humidity, rainfall ảnh hưởng đến khuyến cáo |
| **Tính khả thi** | Không đánh giá | Feasibility score: thuốc có được phép ở VN không? liều lượng đúng không? |

**Nhận xét:** Đây là **điểm khác biệt lớn nhất** giữa hai dự án. AgriGPT-Omni tập trung vào **modality** (speech–vision–text integration), trong khi dự án của chúng ta tập trung vào **content quality** (context sensitivity, regional accuracy, expert validation).

### 7.3. So sánh multilingual approach

| Tiêu chí | AgriGPT-Omni (Multilingual) | Dự án của chúng ta (Vietnamese-focused) |
|-----------|----------------------------|----------------------------------------|
| **Số ngôn ngữ** | 6 | 1 (tiếng Việt, bao gồm phương ngữ) |
| **Depth vs. Breadth** | Breadth: cover nhiều ngôn ngữ nhưng nông | Depth: 1 ngôn ngữ nhưng sâu (90 crops × 5 regions × mùa vụ) |
| **Cách tạo dữ liệu đa ngôn ngữ** | Dịch từ Chinese bằng LLM | Native Vietnamese data từ expert |
| **Thuật ngữ chuyên ngành** | Chinese-centric | Vietnamese-specific (tên thuốc VN, tên bệnh VN, giống cây VN) |
| **Phương ngữ** | 2 phương ngữ Chinese | Phương ngữ Bắc/Trung/Nam Việt Nam |
| **Expert validation** | Manual review cho benchmark | Expert panel validation cho toàn bộ dataset |

**Luận điểm positioning:** AgriGPT-Omni chứng minh rằng approach multilingual thông qua translation (dịch từ 1 ngôn ngữ gốc) có thể đạt kết quả tốt trên benchmark. Tuy nhiên, approach này **không đảm bảo chất lượng cho ngôn ngữ/khu vực cụ thể** — đặc biệt khi thuật ngữ nông nghiệp, regulations (thuốc BVTV được phép sử dụng), và best practices khác nhau hoàn toàn giữa các quốc gia.

### 7.4. So sánh benchmark

| Tiêu chí | AgriBench-Omni-2K | Benchmark của chúng ta (proposed) |
|-----------|-------------------|-----------------------------------|
| **Quy mô** | ~2K samples | Target: 2K–5K samples |
| **Modality** | Tri-modal (speech + vision + text) | Text-based (focus vào content quality) |
| **Đa ngôn ngữ** | 6 ngôn ngữ | 1 ngôn ngữ (Vietnamese) |
| **Task types** | 4 types (Audio QA, Audio MC, Multimodal QA, Multimodal MC) | Context-aware QA, context degradation, safety, feasibility |
| **Context sensitivity** | Không đánh giá | **Core contribution:** đánh giá LLM khi context thay đổi |
| **Expert validation** | Manual review | Expert panel + atomic fact verification |
| **Safety evaluation** | Không có | Đánh giá rủi ro lời khuyên sai (thuốc cấm, liều lượng) |
| **Evaluation metrics** | BLEU, ROUGE, EM, WER/CER, Win rate | Factual accuracy, feasibility, context sensitivity scores |
| **Regional specificity** | Không có | 5 vùng sinh thái, province-level |
| **Crop coverage** | Không rõ (dựa trên Agri-342K, chủ yếu Chinese crops) | ~90 loại cây trồng Việt Nam |
| **Rigor** | De-duplication tốt (ROUGE-L + semantic filtering); nhưng quy mô nhỏ, metrics surface-level | Expert-validated ground truth, atomic facts, context degradation protocol |

**Kết luận:** AgriBench-Omni-2K nghiêm ngặt hơn về **de-duplication** (dùng 2 tầng lọc) và **multimodal coverage** (speech + vision + text). Benchmark của chúng ta nghiêm ngặt hơn về **content quality evaluation** (context sensitivity, feasibility, safety) — đây là hai chiều đánh giá khác nhau, bổ sung chứ không loại trừ nhau.

---

## 8. Bài học rút ra

### 8.1. Về multilingual/low-resource

1. **Translation-based approach có thể scale nhanh nhưng có giới hạn:** AgriGPT-Omni dùng Qwen2.5-72B dịch → 6 ngôn ngữ. Approach này nhanh nhưng không capture thuật ngữ đặc thù vùng miền. Cho dự án của chúng ta: **cần nhấn mạnh** rằng Vietnamese agriculture cần native data, không phải translated data, vì thuật ngữ nông nghiệp Việt Nam (tên thuốc, tên bệnh, giống cây) không thể dịch chính xác từ Chinese.

2. **TTS-based speech synthesis là viable strategy:** 492K synthetic speech samples cho 6 ngôn ngữ — approach này rẻ và scalable. Nếu dự án muốn mở rộng sang speech trong tương lai, CosyVoice-0.5B là tool tham khảo tốt.

3. **Real speech rất quan trọng nhưng quy mô nhỏ:** Chỉ 1.4K real speech — cho thấy thu thập speech thực tế rất tốn kém. Tuy nhiên, chính sự so sánh synthetic vs. real speech (234/111/232) cho thấy mô hình khá robust.

### 8.2. Về training methodology

4. **Progressive 3-stage training có hiệu quả rõ ràng:** Ablation study cho thấy mỗi stage đều cải thiện monotonic. Đặc biệt, Stage 3 (GRPO) cho gains lớn nhất trên multiple-choice tasks. Bài học: nếu dự án có fine-tune mô hình, nên áp dụng progressive approach tương tự.

5. **GRPO với exact-match reward đơn giản nhưng hiệu quả:** Reward function rất đơn giản (r = 2.0 nếu match, 0.0 nếu không). Không cần reward model phức tạp cho agricultural tasks có ground truth rõ ràng.

6. **Domain fine-tuning không nhất thiết giảm general capability:** AgriGPT-Omni thậm chí tăng MMLU (+12.8). Điều này gợi ý rằng domain data chất lượng có thể cải thiện cả general reasoning.

### 8.3. Về benchmark design

7. **De-duplication 2 tầng (surface + semantic) là best practice:** ROUGE-L threshold 0.7 + GPT-4 semantic filtering. Dự án của chúng ta nên áp dụng phương pháp tương tự để đảm bảo benchmark không bị data leakage.

8. **Surface metrics (BLEU, ROUGE) không đủ cho agricultural advisory:** AgriGPT-Omni chỉ dùng text overlap metrics — không đo factual accuracy hay safety. Đây là **cơ hội positioning** cho dự án: chúng ta đề xuất metrics đo chất lượng nội dung (feasibility, context sensitivity, safety) — chiều đánh giá mà AgriGPT-Omni bỏ qua.

### 8.4. Về positioning chiến lược

9. **AgriGPT-Omni tạo narrative "multilingual = inclusive":** Paper claim hướng đến "inclusive agricultural intelligence" và "low-resource regions." Tuy nhiên, 6 ngôn ngữ (3 Chinese variants + English + Japanese + Korean) **không thực sự inclusive** — thiếu toàn bộ khu vực Đông Nam Á, Nam Á, và châu Phi. Dự án của chúng ta có thể phản biện: *"True inclusivity requires depth, not just breadth — serving Vietnamese farmers well requires understanding Vietnamese agricultural context, not just translating Chinese content."*

10. **Khoảng trống rõ ràng cho Vietnamese agriculture:** Không có hệ thống nào (AgriGPT-Omni, AgroGPT, AgriLLM) thực sự phục vụ nông dân Việt Nam. Dự án có thể tuyên bố là **đầu tiên** cho Vietnamese agriculture với depth thực sự.

---

## Tổng kết vị trí so với dự án

```
AgriGPT-Omni                          Dự án Vietnamese Agriculture QA
─────────────                          ──────────────────────────────
Modality depth (speech+vision+text)    Content depth (context, region, safety)
Breadth (6 languages, shallow)         Depth (1 language, deep)
Surface metrics (BLEU, ROUGE)          Quality metrics (feasibility, context sensitivity)
No regional specificity                5 ecological regions
Translated data                        Native Vietnamese data
No safety evaluation                   Expert-validated safety checks
~2K benchmark                          Target 2K-5K benchmark

Hai dự án COMPLEMENT chứ không COMPETE nhau.
AgriGPT-Omni hỏi: "LLM có thể nghe + nhìn + đọc cho nông nghiệp không?"
Dự án của chúng ta hỏi: "LLM có thể đưa lời khuyên ĐÚNG cho nông dân VIỆT NAM không?"
```

**Câu positioning trong paper:** *"While recent multilingual agricultural systems like AgriGPT-Omni [Yang et al., 2025] advance modality integration across six languages, they do not address the fundamental question of content quality — whether the advice is regionally appropriate, seasonally correct, and safe for the farmer. Our work fills this gap by providing the first context-sensitive evaluation framework for Vietnamese agriculture, covering 5 ecological regions, ~90 crop species, and expert-validated ground truth."*
