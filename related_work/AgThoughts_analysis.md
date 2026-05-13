# Phân tích bài báo: AgThoughts / AgReason

---

## 1. Thông tin cơ bản

- **Tiêu đề:** Towards Large Reasoning Models for Agriculture
- **Tác giả:** Hossein Zaremehrjerdi, Shreyan Ganguly, Ashlyn Rairdin, Elizabeth Tranel, Benjamin Feuer, Juan Ignacio Di Salvo, Srikanth Panthulugiri, Hernan Torres Pacin, Victoria Moser, Sarah Jones, Joscif G Raigne, Yanben Shen, Heidi M. Dornath, Aditya Balu, Adarsh Krishnamurthy, Asheesh K Singh, Arti Singh, Baskar Ganapathysubramanian, Chinmay Hegde, Soumik Sarkar
- **Đơn vị:** Iowa State University, New York University
- **Năm:** 2025 (arXiv: 2505.19259v2, ngày 28/05/2025)
- **Trạng thái:** Preprint, under review

---

## 2. Tóm tắt

Bài báo giới thiệu ba đóng góp chính: (1) **AgThoughts** — dataset 44.600 cặp Q&A nông nghiệp kèm reasoning traces được tạo bởi DeepSeek-R1 dưới sự giám sát của chuyên gia nông học; (2) **AgReason** — benchmark 100 câu hỏi mở với đáp án gold-standard được chuyên gia xác nhận; (3) **AgThinker** — bộ mô hình suy luận nhỏ (Phi-3 3.7B, Mistral-7B, LLaMA-3 8B) được fine-tune trên AgThoughts. Nghiên cứu đánh giá 13 LLM trên AgReason và chứng minh Large Reasoning Models (LRM) vượt trội LLM thông thường, dù model tốt nhất (Gemini 2.5 Flash) chỉ đạt 36% pass rate.

---

## 3. Vấn đề nghiên cứu

Bài báo giải quyết các vấn đề:

1. **LLM hiện tại yếu trong suy luận nông nghiệp:** Quyết định nông nghiệp phụ thuộc nhiều yếu tố (địa lý, khí hậu, kinh tế, mùa vụ, loại đất). LLM tổng quát thường đưa ra lời khuyên chung chung, thiếu tính ngữ cảnh.
2. **Thiếu benchmark open-ended cho nông nghiệp:** Benchmark hiện có (AgXQA, AgriBench, ScienceQA) chủ yếu là trắc nghiệm hoặc factual recall, không đánh giá suy luận mở.
3. **Thiếu dataset với reasoning traces:** Không có dataset nông nghiệp nào cung cấp chuỗi suy luận (thinking traces) để fine-tune mô hình suy luận.

Câu hỏi thực tế mà nông dân đặt ra — ví dụ "Tôi nên làm gì với cỏ dại trong ruộng lúa mạch ở Colorado vào tháng 9?" — đòi hỏi suy luận đa bước dựa trên kiến thức chuyên sâu mà LLM hiện tại chưa đáp ứng được.

---

## 4. Phương pháp

### 4.1. Xây dựng dữ liệu (Dataset Construction)

**Quy trình tạo câu hỏi (Question Generation):**
1. **Base templates:** 10 danh mục nông học chính (Crop Management, Cover Crop, Plant & Seed Health, Biotic Diseases/Insects/Weeds, Abiotic Soil/Weather/Harvest, Crop Inputs).
2. **Modifiers:** Thêm biến thể ngữ cảnh: địa lý (tất cả 50 tiểu bang Mỹ), điều kiện đồng ruộng (thoát nước, xói mòn), dinh dưỡng (thiếu vi/đa lượng), thời tiết (mưa, hạn, mưa đá), thời điểm gieo (sớm/muộn), cá nhân (kinh nghiệm, hoàn cảnh).
3. **Constrained variable insertion:** Loài cây trồng được chọn dựa trên vùng địa lý + phương thức canh tác (hữu cơ/truyền thống) + quy mô trang trại (nhỏ/lớn).
4. **Paraphrasing:** Dùng Qwen-2.5-32B-Instruct để diễn đạt lại câu hỏi cho tự nhiên.
5. **Filtering:** Dùng LLaMa-4-Maverick lọc câu hỏi kém → giữ lại 53.860/80.000 (~67,3%).

**Quy trình tạo câu trả lời (Response Generation):**
1. DeepSeek-R1 tạo câu trả lời + reasoning traces cho 51.800 câu hỏi.
2. **Human evaluation:** 10 chuyên gia (nghiên cứu sinh ngành Agronomy, Iowa State) đánh giá 200 cặp Q-A (~20 cặp/người), mỗi cặp tốn 30-60 phút.
3. **Error taxonomy:** 57/200 câu trả lời bị đánh dấu sai → phân loại lỗi.
4. **LLM-based filtering:** GPT-4.1 đánh giá theo 5 chiều (Factual Accuracy, Contextual Relevance, Practical Feasibility, Logical Consistency, Completeness) → loại ~13,5%.
5. **Kết quả:** 44.600 cặp Q-A cuối cùng (AgThoughts).

### 4.2. Quy mô (Scale)

| Thống kê | Số lượng |
|---|---|
| Tổng Q&A pairs (AgThoughts) | 44.600 |
| Benchmark (AgReason) | 100 câu hỏi gold-standard |
| Tổng tokens | ~66,2 triệu |
| Độ dài trung bình câu hỏi | 26 từ |
| Độ dài trung bình câu trả lời | 354 từ |
| Độ dài trung bình reasoning trace | 725 từ |
| Danh mục chính | 10 |
| Phạm vi địa lý | 50 tiểu bang Mỹ |

**Phân bổ theo danh mục:**
- Plant and Seed Health: 15.811 (lớn nhất)
- Crop Management: 7.539
- General Management: 6.105
- Harvest: 4.139
- Soil: 3.896
- Weather: 3.527
- Cover Cropping: 1.969
- Diseases: 819
- Insects: 702
- Weeds: 108

### 4.3. Quy trình annotation

- **10 chuyên gia** (nghiên cứu sinh tiến sĩ ngành Agronomy, Iowa State University).
- Đánh giá theo 3 chiều: chất lượng câu hỏi, tính logic của reasoning, tính chính xác của câu trả lời.
- Sử dụng **Label Studio** để annotation.
- Thời gian: **30-60 phút mỗi cặp Q-A**.
- Tổ chức **cuộc họp hiệu chỉnh định kỳ** để giảm tính chủ quan.
- 100 câu trả lời chất lượng cao nhất → trích xuất "essential content elements" → tạo AgReason benchmark.

### 4.4. Error Taxonomy (từ đánh giá chuyên gia)

**Phân bố lỗi câu trả lời sai:**
| Loại lỗi | Tỷ lệ |
|---|---|
| Sai thực tế (factually wrong) | 55,1% |
| Câu trả lời bị cắt | 28,6% |
| Thiếu gợi ý phổ biến | 14,8% |
| Không trả lời câu hỏi chính | 4,1% |

**Mẫu lỗi thường gặp:**
| Pattern | Tỷ lệ |
|---|---|
| Khái quát hóa phân bón/thuốc trừ sâu mà không test đất | 13,9% |
| Khuyến nghị sai cho cây trồng cụ thể | 11,9% |
| Giả định mà không hỏi thông tin bổ sung | 11,6% |
| Bỏ qua tính khả thi kinh tế/lao động | 10,9% |
| Hiểu sai vòng đời cây/phù hợp vùng | 10,3% |
| Câu trả lời không liên quan | 9,9% |
| Quy kết sai hành vi sâu bệnh | 9,3% |
| Sai thời điểm/trình tự canh tác | 8,9% |
| Tư vấn hóa chất lỗi thời/bị cấm | 7,3% |
| Logic nội tại mâu thuẫn | 6,0% |

---

## 5. Evaluation

### 5.1. Thiết kế đánh giá

- **LLM-as-Judge:** Phân tách câu trả lời thành các statement đơn lẻ, so với gold-standard, gán nhãn: supported, unsupported, contradictory.
- **Metrics:** Precision, Recall, F1-score ở mức statement-level.
- **Ngưỡng pass:** F1 ≥ 0,80 (xác định qua expert review).
- **Metric chính:** Pass rate (% câu hỏi có F1 ≥ 0,80).

### 5.2. Mô hình được đánh giá

13 mô hình (10 base + 3 AgThinker fine-tuned):

**Base models:** Gemini 2.5 Flash, Grok-3 Beta, GPT-o1, DeepSeek V3, QwQ-32B, GPT-4o, LLaMA-4 Maverick, Grok-2, LLaMA-4 Scout, Mistral-24B, phi-3, Mistral-7b, LLaMA-3 8B.

**AgThinker (SFT):** Phi-3 (SFT), Mistral-7B (SFT), LLaMA-3 8B (SFT).

### 5.3. Kết quả chính (với con số)

| Mô hình | Pass Rate (F1≥0.80) | Precision | Recall |
|---|---|---|---|
| **Gemini 2.5 Flash** | **36%** | 0,727 | 0,778 |
| Grok-3 Beta | 22% | 0,583 | 0,815 |
| GPT-o1 | 20% | 0,654 | 0,710 |
| DeepSeek V3 | 7% | 0,544 | 0,644 |
| AgThinker (Phi-3 SFT) | 5% | 0,474 | 0,598 |
| QwQ-32B | 5% | 0,505 | 0,693 |
| GPT-4o | 5% | 0,554 | 0,558 |
| LLaMA-4 Maverick | 4% | 0,596 | 0,593 |
| AgThinker (LLaMA-3 8B SFT) | 3% | 0,476 | 0,595 |
| AgThinker (Mistral-7B SFT) | 3% | 0,470 | 0,678 |
| Grok-2 | 3% | 0,466 | 0,575 |
| LLaMA-4 Scout | 1% | 0,480 | 0,523 |
| Mistral-24B | 0% | 0,442 | 0,557 |
| LLaMA-3 8B | 0% | 0,409 | 0,456 |
| Mistral-7b | 0% | 0,408 | 0,520 |

**Phát hiện quan trọng:**
1. **LRM vượt trội LLM thông thường:** Gemini 2.5 Flash (36%) >> GPT-4o (5%).
2. **Ngay cả model tốt nhất cũng chỉ đạt 36%:** Chứng tỏ suy luận nông nghiệp vẫn cực kỳ khó.
3. **SFT cải thiện rõ rệt:** Phi-3: 1% → 5%; Mistral-7B: 0% → 3%; LLaMA-3 8B: 0% → 3%.
4. **Danh mục khó nhất:** Cover Crop (hầu hết mô hình ~ 0%).
5. **Danh mục dễ nhất:** Biotic Diseases, Abiotic Soil (Gemini 2.5 Flash đạt cao nhất).

---

## 6. Điểm mạnh

1. **Đóng góp đa tầng:** Ba sản phẩm liên kết chặt chẽ — dataset (AgThoughts), benchmark (AgReason), model suite (AgThinker) — tạo hệ sinh thái hoàn chỉnh.
2. **Expert-in-the-loop:** 10 chuyên gia nông học đánh giá trực tiếp, xây dựng error taxonomy chi tiết, tạo gold-standard answers — đây là tiêu chuẩn cao cho dataset nông nghiệp.
3. **Reasoning traces:** Cung cấp chuỗi suy luận chi tiết (trung bình 725 từ/câu), cho phép fine-tune mô hình suy luận — điểm mới so với các dataset chỉ có Q&A.
4. **Câu hỏi open-ended, context-rich:** Câu hỏi mô phỏng tình huống tư vấn thực tế (extension services), đòi hỏi suy luận đa bước trên nhiều yếu tố (vùng, cây trồng, thời tiết, đất, quy mô trang trại).
5. **LLM-as-Judge statement-level:** Phương pháp đánh giá chi tiết hơn accuracy/ROUGE-L, phân tách từng fact statement.
6. **Error taxonomy thực tiễn:** Bảng phân loại lỗi (10 patterns) cực kỳ hữu ích cho cộng đồng, giúp hiểu LLM sai ở đâu trong lĩnh vực nông nghiệp.

---

## 7. Điểm yếu / Limitations

1. **Chỉ tập trung vào Mỹ:** Tất cả câu hỏi dựa trên ngữ cảnh 50 tiểu bang Mỹ. Không bao gồm nông nghiệp nhiệt đới, Đông Nam Á, châu Phi, hay bất kỳ quốc gia đang phát triển nào.
2. **Benchmark nhỏ:** Chỉ 100 câu cho AgReason — quá nhỏ để kết luận vững chắc, dễ bị bias bởi phân phối câu hỏi.
3. **Dữ liệu tổng hợp (synthetic):** 44.600 câu trả lời từ DeepSeek-R1, ước tính ~15% có thể sai. Không có câu hỏi thực từ nông dân.
4. **Single-turn only:** Thừa nhận đã loại Claude vì model này ưu tiên multi-turn. Chỉ đánh giá single-turn prompting.
5. **Chỉ tiếng Anh:** Không đánh giá khả năng đa ngôn ngữ.
6. **Expert verification không toàn diện:** Chỉ 200/51.800 cặp Q-A được chuyên gia kiểm tra trực tiếp (~0,4%), phần còn lại phụ thuộc LLM-based filtering.
7. **Thiếu multimodal:** Không có hình ảnh (triệu chứng bệnh, sâu hại) dù nông nghiệp rất cần visual diagnosis.

---

## 8. So sánh với dự án Vietnamese Agriculture QA

### 8.1. Điểm tương đồng

| Khía cạnh | AgThoughts/AgReason | Dự án của chúng ta |
|---|---|---|
| **Loại câu hỏi** | Open-ended, tình huống thực | Open-ended, context-aware |
| **Expert validation** | 10 experts, gold-standard | Vietnamese expert validation |
| **Context-rich questions** | Có (location, weather, soil, crop) | Có (vùng sinh thái, cây trồng, mùa vụ) |
| **Đánh giá nhiều LLM** | 13 mô hình | 7 mô hình |
| **Tập trung vào advisory** | Mô phỏng extension services | Tư vấn cho nông dân Việt |
| **Nhận thấy LLM yếu** | 36% pass rate (tốt nhất) | Kỳ vọng thấp tương tự |

### 8.2. Điểm khác biệt

| Khía cạnh | AgThoughts/AgReason | Dự án của chúng ta |
|---|---|---|
| **Ngôn ngữ** | Tiếng Anh | **Tiếng Việt** |
| **Vùng địa lý** | 50 tiểu bang Mỹ (ôn đới) | **5 vùng sinh thái Việt Nam (nhiệt đới)** |
| **Context sensitivity** | Không đánh giá systematically | **3-level degradation** (full → partial → no context) |
| **Multi-turn QA** | Không (thừa nhận là hạn chế) | **Có** — single-turn + multi-turn |
| **Regional bias** | Không đánh giá | **Đánh giá bias giữa các vùng** |
| **Reasoning traces** | Có (725 từ/câu) | Không (tập trung vào chất lượng câu trả lời) |
| **Quy mô dataset** | 44.600 (tổng hợp) | Nhỏ hơn nhưng expert-validated |
| **Cây trồng** | Theo vùng Mỹ | **~90 loài cây trồng Việt Nam** |
| **Nông nghiệp nhiệt đới** | Không | **Có** (lúa nước, cà phê, tiêu, điều, cây ăn trái nhiệt đới) |

### 8.3. Khoảng trống mà dự án chúng ta lấp đầy

1. **Nông nghiệp nhiệt đới & Đông Nam Á:** AgThoughts/AgReason hoàn toàn dựa trên ngữ cảnh Mỹ. Nông nghiệp nhiệt đới Việt Nam (lúa nước 2-3 vụ, cây công nghiệp, cây ăn trái) có đặc thù hoàn toàn khác — mùa mưa/khô, đất phèn, đất bạc màu, sâu bệnh nhiệt đới. Dự án chúng ta là benchmark đầu tiên cho ngữ cảnh này.

2. **Context sensitivity (3-level degradation):** AgThoughts tạo câu hỏi context-rich nhưng không đánh giá LLM phản ứng thế nào khi context bị thiếu. Dự án chúng ta hệ thống hóa điều này — đây là đóng góp phương pháp luận mới, ứng dụng rộng hơn nông nghiệp Việt Nam.

3. **Multi-turn QA:** AgThoughts thừa nhận single-turn là hạn chế và phải loại Claude vì model này ưu tiên multi-turn. Dự án chúng ta bao gồm multi-turn, phản ánh đúng quy trình tư vấn nông nghiệp thực tế (nông dân hỏi → chuyên gia hỏi lại → nông dân bổ sung → chuyên gia tư vấn).

4. **Regional bias evaluation:** AgThoughts dùng 50 tiểu bang làm modifier nhưng không phân tích bias giữa các vùng. Dự án chúng ta đánh giá trực tiếp xem LLM có thiên vị vùng nào (ví dụ: trả lời tốt cho Đồng bằng sông Cửu Long nhưng kém cho Tây Nguyên).

5. **Low-resource language:** Tiếng Việt là ngôn ngữ ít tài nguyên hơn tiếng Anh rất nhiều. Đánh giá LLM trong ngữ cảnh này phơi bày vấn đề nghiêm trọng hơn (hallucination, thiếu kiến thức domain) mà benchmark tiếng Anh không thấy được.

---

## 9. Bài học rút ra

1. **Question generation pipeline:** Cách AgThoughts xây dựng câu hỏi (base template + location modifier + crop modifier + contextual modifier) rất có hệ thống. Chúng ta có thể áp dụng framework tương tự: base question + vùng sinh thái + loài cây + mùa vụ + điều kiện đặc biệt.

2. **Error taxonomy là đóng góp quan trọng:** Bảng phân loại 10 pattern lỗi của AgThoughts được reviewer đánh giá cao. Chúng ta nên xây dựng error taxonomy tương tự cho tiếng Việt — đặc biệt các lỗi liên quan đến nông nghiệp nhiệt đới, thuốc bảo vệ thực vật Việt Nam, và kiến thức bản địa.

3. **LLM-as-Judge + statement-level evaluation:** Phương pháp phân tách câu trả lời thành statement đơn lẻ rồi so với gold-standard tốt hơn ROUGE-L/accuracy rất nhiều. Chúng ta nên cân nhắc áp dụng framework đánh giá này (FACTS Grounding approach) cho dự án.

4. **Expert time investment:** Mỗi cặp Q-A tốn 30-60 phút expert time. Cần lên kế hoạch thực tế cho annotation budget. Với 3 experts × N câu hỏi, đây là chi phí đáng kể.

5. **Reasoning model vs. standard LLM:** AgThoughts chứng minh LRM (Gemini 2.5 Flash, o1) vượt trội LLM thông thường (GPT-4o) trong suy luận nông nghiệp. Chúng ta nên đảm bảo bộ 7 mô hình của mình bao gồm cả reasoning models (o1/o3, Gemini Flash Thinking, DeepSeek-R1).

6. **Ngưỡng 36% chỉ pass rate:** Ngay cả model tốt nhất cũng chỉ đạt 36% ở ngưỡng F1 ≥ 0.80. Điều này cho thấy nông nghiệp là domain cực kỳ khó cho LLM. Kết quả tương tự (hoặc thấp hơn) có thể xảy ra với tiếng Việt — cần chuẩn bị narrative phù hợp.

7. **Dataset tổng hợp cần thận trọng:** AgThoughts ước tính ~15% câu trả lời có thể sai dù đã qua filtering. Đây là bài học quan trọng: nếu dùng LLM tạo dữ liệu, cần expert review tỷ lệ đủ lớn để ước tính error rate.

8. **Cần lập AgReason Leaderboard:** AgThoughts tạo leaderboard công khai — tăng visibility và cho phép cộng đồng đóng góp. Chúng ta có thể tạo Vietnamese Agriculture QA Leaderboard tương tự.
