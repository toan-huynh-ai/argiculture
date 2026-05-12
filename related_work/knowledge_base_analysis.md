# Phân tích: Xây Knowledge Base hỗ trợ Prompting

> Câu hỏi cốt lõi: Knowledge base phục vụ MỤC ĐÍCH GÌ trong dự án?
> Dự án này là BENCHMARK paper, không phải systems paper.
> KB phải phục vụ: (1) sinh data, (2) context injection cho experiments, (3) ground truth cho evaluation.

---

## CÁC APPROACH HIỆN TẠI TRÊN THẾ GIỚI

### Approach 1: Knowledge Graph (KG)

**Đại diện:**
- Crop GraphRAG (Frontiers, 2025): KG cho crop diseases + RAG retrieval
- CropDP-KG (PMC, 2025): 13,840 entities, 8 entity types, 21,961 relationships (Trung Quốc)
- DurianExpert-VN: KG nhỏ cho sầu riêng Việt Nam (100+ entries)

**Cấu trúc điển hình:**
```
Entities: Disease, Pest, Crop, Symptom, Pesticide, Region, Season, Treatment
Relations: causes, treats, affects, occurs_in, suitable_for, banned_in
```

**Ưu:** Quan hệ rõ ràng giữa entities; hỗ trợ multi-hop reasoning; có thể query chính xác.
**Nhược:** Tốn thời gian xây dựng; cần domain expert liên tục; khó maintain.

### Approach 2: RAG với Vector Database

**Đại diện:**
- AgriMind AI: FAISS + bge-m3 embeddings cho Vietnamese agriculture
- AgriRegion: Geospatial metadata injection + region-prioritized re-ranking
- Đom Đóm AI: Vietnamese agricultural chatbot (đã deploy thực tế)

**Cấu trúc:**
```
Documents → Chunking → Embedding → Vector DB (FAISS/ChromaDB)
Query → Embed → Similarity Search → Top-K chunks → LLM generates answer
```

**Ưu:** Nhanh, scale tốt, dễ thêm documents mới.
**Nhược:** Semantic search có thể miss thông tin quan trọng; không capture relationships.

### Approach 3: Golden Facts (Atomic Knowledge Units)

**Đại diện:**
- Digital Green DG-EVAL (2026): Atomic facts verified bởi expert
- FActScore methodology

**Cấu trúc:**
```
Mỗi "fact" = 1 câu đơn, verifiable, có source.
Ví dụ: "Bệnh đạo ôn (Pyricularia oryzae) trên lúa ở ĐBSCL thường xuất hiện
        vào vụ Đông Xuân, tháng 12-2, khi nhiệt độ 20-25°C và ẩm độ >90%."
```

**Ưu:** Dễ verify; dùng trực tiếp cho evaluation (so sánh LLM output vs facts).
**Nhược:** Tốn effort annotation; khó cover hết domain.

### Approach 4: Structured Scenario Templates

**Đại diện:**
- DỰ ÁN NÀY đã có: scenario blocks (region_info, crop_info, weather_info,
  issue_info, user_profile)
- PestMA: structured attributes (pest type, severity, crop, growth stage,
  environmental conditions)

**Cấu trúc:**
```json
{
  "region_info": { "macro_region": "...", "province": "...", "agro_ecology": "..." },
  "crop_info": { "crop_type": "...", "growth_stage": "...", "common_issues": "..." },
  "weather_info": { "terrain": "...", "soil": "...", "temperature": "...", ... },
  "issue_info": { "classification": "...", "symptoms": "..." },
  "user_profile": { "role": "...", "experience": "...", "dialect": "..." }
}
```

**Ưu:** Đã có; rất structured; hỗ trợ trực tiếp cho context degradation experiments.
**Nhược:** Không có relationships giữa entities; khó query cross-scenario.

---

## PHÂN TÍCH: CÁI NÀO PHÙ HỢP VỚI DỰ ÁN NÀY?

### Mục đích 1: Sinh Data (Question Generation)

Bạn cần KB để sinh câu hỏi đa dạng, cover đủ 90 crops × 5 regions × seasons.

| Approach | Phù hợp? | Lý do |
|----------|----------|-------|
| KG | ★★★★☆ | Có thể enumerate tổ hợp (crop × region × disease × season) |
| RAG | ★★☆☆☆ | Tốt cho retrieval, KHÔNG tốt cho systematic enumeration |
| Golden Facts | ★★★☆☆ | Mỗi fact cho ra 1 câu hỏi, nhưng khó cover systematic |
| Scenario Templates | ★★★★★ | ĐÃ CÓ, chỉ cần mở rộng. Trực tiếp generate scenarios |

**Kết luận:** Scenario Templates (đã có) + KG bổ trợ cho enumeration = approach tốt nhất.

### Mục đích 2: Context Injection cho Experiments

Bạn cần KB để inject context theo levels (full → partial → minimal) cho
context degradation protocol.

| Approach | Phù hợp? | Lý do |
|----------|----------|-------|
| KG | ★★★☆☆ | Có thể remove nodes/edges theo levels, nhưng overhead |
| RAG | ★☆☆☆☆ | KHÔNG phù hợp — RAG retrieves, không degrade |
| Golden Facts | ★★☆☆☆ | Có thể remove facts, nhưng granularity thô |
| Scenario Templates | ★★★★★ | HOÀN HẢO — bỏ từng field một |

**Kết luận:** Scenario Templates là approach tự nhiên nhất cho context degradation.

### Mục đích 3: Ground Truth cho Evaluation

Bạn cần KB để verify câu trả lời LLM có đúng không.

| Approach | Phù hợp? | Lý do |
|----------|----------|-------|
| KG | ★★★★☆ | Có thể check: "thuốc X có treat disease Y không?" |
| RAG | ★★☆☆☆ | Similarity search không đủ cho fact verification |
| Golden Facts | ★★★★★ | HOÀN HẢO — so sánh trực tiếp LLM output vs verified facts |
| Scenario Templates | ★★☆☆☆ | Templates mô tả context, KHÔNG mô tả đáp án đúng |

**Kết luận:** Golden Facts tốt nhất cho evaluation.

---

## ĐỀ XUẤT: KIẾN TRÚC KB 3 TẦNG

Thay vì chọn 1 approach, kết hợp 3 tầng phục vụ 3 mục đích khác nhau:

```
┌─────────────────────────────────────────────────────┐
│           TẦNG 3: GOLDEN FACTS REGISTRY             │
│  Expert-verified atomic facts cho evaluation        │
│  "Bệnh X trên cây Y ở vùng Z dùng thuốc W,        │
│   liều lượng A, thời điểm B, cách dùng C"          │
│  → Dùng cho: ground truth verification,             │
│    feasibility checking, safety validation          │
├─────────────────────────────────────────────────────┤
│           TẦNG 2: KNOWLEDGE GRAPH                   │
│  Entities + Relationships                           │
│  Crop ──affects──▶ Region                           │
│  Disease ──causes──▶ Symptom                        │
│  Pesticide ──treats──▶ Disease                      │
│  Region ──has_season──▶ Season                      │
│  → Dùng cho: systematic enumeration,                │
│    consistency checking, coverage analysis           │
├─────────────────────────────────────────────────────┤
│           TẦNG 1: SCENARIO TEMPLATES (ĐÃ CÓ)       │
│  Structured JSON blocks                             │
│  region_info + crop_info + weather_info +            │
│  issue_info + user_profile                           │
│  → Dùng cho: question generation,                   │
│    context degradation experiments                   │
└─────────────────────────────────────────────────────┘
```

---

## TỰ PHẢN BIỆN

### Câu hỏi 1: Có cần xây KG riêng không hay dùng KG có sẵn?

**CropDP-KG** (Trung Quốc) có 13,840 entities — nhưng cho nông nghiệp TRUNG QUỐC,
không có crops/diseases đặc thù Việt Nam. STAR-FARM Lexicon có 553 terms nhưng chỉ
cho agroecology Mekong Delta.

**Kết luận:** Không có KG nào cover đủ nông nghiệp Việt Nam. CẦN xây mới, nhưng có
thể dùng CropDP-KG làm schema reference.

### Câu hỏi 2: Effort xây KG có xứng đáng cho benchmark paper không?

**Thẳng thắn:** Nếu mục tiêu là submit NeurIPS D&B, KG KHÔNG phải contribution
chính. Reviewer đánh giá dataset quality + evaluation methodology, KHÔNG đánh giá
infrastructure.

**Tuy nhiên:** KG giúp đảm bảo dataset COVERAGE (không bỏ sót crop-region-season
combinations) và CONSISTENCY (cùng disease, cùng region → cùng treatment). Đây là
internal tool, không cần publish riêng.

**Kết luận:** Xây KG ở mức VỪA ĐỦ để hỗ trợ dataset construction. Không cần hoàn
chỉnh 100%.

### Câu hỏi 3: Golden Facts có trùng với expert_explanation đã có không?

Dự án ĐÃ CÓ field expert_explanation và final_answer trong schema. Đây BẢN CHẤT là
Golden Facts rồi.

**Vấn đề:** expert_explanation hiện tại có dạng narrative (đoạn văn dài). Golden Facts
cần ATOMIC (mỗi fact = 1 câu verifiable).

**Kết luận:** Cần DECOMPOSE expert_explanation thành atomic facts. Ví dụ:
- Expert explanation: "Bệnh đạo ôn trên lúa vụ Đông Xuân ở ĐBBB thường xuất hiện
  khi nhiệt độ 20-25°C, ẩm độ >90%. Phun Tricyclazole 75WP liều 0.5kg/ha vào giai
  đoạn đẻ nhánh, phun lúc chiều mát."
- Atomic facts:
  1. "Đạo ôn do nấm Pyricularia oryzae gây ra"
  2. "Điều kiện thuận lợi: nhiệt độ 20-25°C, ẩm độ >90%"
  3. "Vụ Đông Xuân ở ĐBBB thường gặp đạo ôn"
  4. "Thuốc: Tricyclazole 75WP, liều 0.5kg/ha"
  5. "Thời điểm phun: giai đoạn đẻ nhánh"
  6. "Cách phun: phun lúc chiều mát"

### Câu hỏi 4: RAG có cần không?

**Thẳng thắn:** KHÔNG cho benchmark paper. RAG là systems contribution, không phải
dataset contribution. AgriMind AI và Đom Đóm AI đã làm RAG cho Vietnamese agriculture.

**Nếu muốn thêm RAG experiments:** Dùng như baseline — "khi LLM có access vào KB qua
RAG, advisory quality cải thiện bao nhiêu?" Đây có thể là experiment thú vị NHƯNG
không phải contribution chính.

**Kết luận:** RAG = nice-to-have experiment, KHÔNG phải core infrastructure.

---

## KẾ HOẠCH THỰC HIỆN CỤ THỂ

### Phase 1: Scenario Template Expansion (ĐÃ CÓ → MỞ RỘNG)

Mở rộng scenario templates từ 5 crops hiện tại → 90 crops:

```json
{
  "scenario_id": "SC_001",
  "region_info": {
    "macro_region": "Đồng bằng sông Hồng",
    "province": "Thái Bình",
    "climate_zone": "Nhiệt đới gió mùa",
    "typical_soil": "Phù sa",
    "flood_risk": "Trung bình (vụ mùa tháng 7-9)",
    "elevation_m": 1-3
  },
  "crop_info": {
    "crop_name": "Lúa",
    "variety": "Bắc Thơm 7",
    "growth_stage": "Đẻ nhánh",
    "season": "Đông Xuân",
    "planting_month": "Tháng 1",
    "harvest_month": "Tháng 5",
    "common_diseases": ["Đạo ôn", "Bạc lá", "Khô vằn"],
    "common_pests": ["Rầy nâu", "Sâu đục thân", "Sâu cuốn lá"]
  },
  "weather_info": {
    "temperature_range": "15-22°C",
    "humidity": ">85%",
    "rainfall_mm_month": 20-40,
    "irrigation": "Chủ động (hệ thống thủy lợi)",
    "wind": "Gió mùa đông bắc"
  },
  "issue_info": {
    "issue_type": "disease",
    "disease_name": "Đạo ôn (Blast)",
    "scientific_name": "Pyricularia oryzae",
    "observable_symptoms": "Vết bệnh hình thoi màu xám...",
    "severity": "Trung bình",
    "affected_area_percent": 15
  },
  "user_profile": {
    "role": "Nông dân",
    "experience_years": 20,
    "farm_size_ha": 0.5,
    "communication_style": "Ngắn gọn, thực tế",
    "dialect": "Bắc Bộ",
    "education": "THPT"
  }
}
```

**Effort:** 2-4 tuần với expert support.
**Output:** JSON files cho ~90 crops × ~5 regions = ~450 scenario templates.

### Phase 2: Lightweight Knowledge Graph

Xây KG tối thiểu đủ dùng, KHÔNG cần hoàn chỉnh:

```
Entities cần có:
- 90 Crops (tên VN + tên khoa học + classification)
- 5 Macro Regions + provinces
- Diseases per crop per region
- Approved pesticides per disease (từ Cục BVTV)
- Season calendar per crop per region

Relationships:
- Crop ──grows_in──▶ Region (with season)
- Disease ──affects──▶ Crop (with conditions)
- Pesticide ──treats──▶ Disease (with dosage)
- Pesticide ──approved_in──▶ Vietnam (Y/N)
- Region ──has_climate──▶ Climate_Zone
```

**Format:** Neo4j hoặc đơn giản hơn: JSON-LD / NetworkX graph.
**Effort:** 3-6 tuần.
**Output:** Graph database có thể query programmatically.

**Nguồn dữ liệu chính thức:**
1. Cục Bảo vệ Thực vật (ppd.gov.vn): Danh mục thuốc BVTV được phép sử dụng ở VN
2. MARD thongke.mard.gov.vn: Thống kê nông nghiệp theo vùng
3. Son La Open Data Portal: Template cho pest/disease data
4. Hệ thống truy xuất nguồn gốc nông sản (triển khai 7/2026)

### Phase 3: Golden Facts Registry

Decompose expert_explanation → atomic facts:

```json
{
  "fact_id": "GF_001",
  "crop": "Lúa",
  "region": "ĐBSH",
  "category": "treatment",
  "fact": "Phun Tricyclazole 75WP liều 0.5kg/ha phòng trừ đạo ôn",
  "conditions": "Giai đoạn đẻ nhánh, nhiệt độ 20-25°C, ẩm độ >90%",
  "source": "Expert_NguyenVanA",
  "verified": true,
  "verification_date": "2026-05-10"
}
```

**Effort:** 2-4 tuần (expert cần review).
**Output:** Registry of ~2000-5000 atomic facts.

---

## KB NHƯ NOVELTY CONTRIBUTION ĐƯỢC KHÔNG?

### Tự phản biện

**CÓ THỂ** nếu frame đúng cách:

Nếu KB chỉ là internal tool → KHÔNG phải contribution, chỉ là methodology.

Nếu KB được release như **ViAgriKB: A Structured Knowledge Base for Vietnamese
Agriculture** → CÓ THỂ là contribution PHỤ (secondary contribution).

**NHƯNG:** CropDP-KG (2025) đã publish KG cho Chinese agriculture. Crop GraphRAG (2025)
đã publish KG + RAG system. Nên KB alone KHÔNG đủ novel cho A* paper.

**Kết luận:** KB nên là METHODOLOGY COMPONENT trong paper, không phải standalone
contribution. Mô tả nó trong Section 3 (Dataset Construction) như:
"We constructed a structured agricultural knowledge base covering 90 crop species
across 5 Vietnamese ecological regions to ensure systematic coverage and consistency
in scenario generation."

### Tuy nhiên, KB CÓ THỂ tạo novel contribution nếu:

1. **Dùng KB cho Context Degradation experiments:** KB cho phép remove context theo
   STRUCTURED WAY (bỏ region node, bỏ season edge, etc.) thay vì ad-hoc text deletion.
   Đây là phương pháp REPRODUCIBLE hơn.

2. **Dùng Golden Facts cho Feasibility Score:** KB chứa approved pesticides → tự động
   check "LLM recommend thuốc X, nhưng thuốc X bị cấm ở VN" → feasibility violation.

3. **Dùng KG cho Coverage Analysis:** Chứng minh dataset cover N% of crop × region ×
   season combinations → dataset không bị selection bias.

Tóm lại: KB không phải novelty riêng, nhưng ENABLE các novelty contributions khác.
