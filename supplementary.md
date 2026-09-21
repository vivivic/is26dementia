# Supplementary Prompts and Tables

Supplementary materials for two Interspeech 2026 papers on LLM-augmented dementia detection.
See [README.md](README.md) for the paper list, pipeline overview, and an index of which item belongs to which paper.

- **[A]** LoRA-Tuned Large Language Models for Dementia Detection via Multi-View Speech-Derived Features ([arXiv:2606.28445](https://arxiv.org/abs/2606.28445))
- **[B]** Listening Between the Lines: Joint Learning of ASR Embeddings and LLM-Augmented Linguistics for Dementia Detection ([arXiv:2606.30675](https://arxiv.org/abs/2606.30675))

Items are numbered S1, S2, ... to distinguish them from the tables and figures in the papers.

---

## Prompts

### Prompt S1: Topic Taxonomy Definition

**Used in:** [A] Sec. 2.2.3 · [B] Sec. 2.3.1 (shared)

Given to a vision-capable LLM (GPT-5.2) together with the Cookie Theft picture. Its output is the eight-cluster taxonomy in [Table S1](#table-s1-topic-taxonomy-detail), which both papers use as a fixed scheme.

```text
Analyze the image to construct a hierarchical topic taxonomy for coding picture description transcripts.

[GOAL]
Generate a clustered set of topic categories organized by spatial zones and thematic coherence.
The output will be directly used as a reference schema for sentence-level topic classification in transcript analysis.

[CLUSTERING PRINCIPLES]
Organize topics by:
- Spatial proximity in the image (what appears in the same visual region)
- Agent-centered grouping (topics related to the same person's actions)
- Thematic coherence (elements that typically co-occur in descriptions)

Each cluster should represent a distinct "attentional zone" - a region or theme that speakers naturally group together when describing the scene.

[TOPIC CONSTRUCTION RULES]
- Each topic should be a single focal point a speaker might mention
- Use concise labels (1-3 words, lowercase, underscore for multi-word)
- Include both agents, actions, objects, and states within the same cluster if spatially/thematically linked
- Separate topics only when they serve distinct descriptive functions

[REQUIRED CLUSTERS]
You must include:
- Action zones: Clusters centered on each main agent and their associated actions/objects
- Setting/environment: Background elements, spatial context, peripheral objects
- Meta-discourse: Non-content speech (task management, uncertainty, retrieval difficulty, fillers, self-correction, off-topic)
```


### Prompt S2: Unified Sentence Classification

**Used in:** [A] Sec. 2.2.3 · [B] Sec. 2.3.2, Fig. 2 (shared; the paper shows an abbreviated version)

Zero-shot prompt to a text LLM (GPT-5.2). Annotates every sentence of a transcript in a single call across five dimensions.

- In **[A]**, only the topic-classification output (`cluster`, `topic`) is used; it is passed into the per-utterance JSON of [Prompt S3](#prompt-s3-multi-view-dementia-classifier).
- In **[B]**, all five dimensions are aggregated into the 46 speaker-level features in [Table S2](#table-s2-complete-feature-inventory-46-features); the semantic-distance dimension uses [Table S3](#table-s3-cluster-distance-matrix).

```text
[TASK]
You are analyzing a Cookie Theft picture description transcript.
For EACH sentence, provide ALL of the following analyses in a single JSON response.

[CLUSTER DEFINITIONS]
C1_boy_cabinet_cookies: boy_on_stool, reaching_up, cookie_jar, taking_cookie, open_cabinet, stool_balance, cabinet_shelf
C2_girl_helping: girl_reaching, asking_for_cookie, standing_left, watching_boy
C3_mother_dishes_sink: mother_drying, holding_plate, dish_towel, ignoring_children, kitchen_apron
C4_water_overflow_spill: overflowing_sink, running_faucet, water_spilling, puddle_on_floor, water_splash
C5_counter_dishes: countertop_items, plate_on_counter, cup_and_saucer, stacked_dishes
C6_window_curtains_outside: window_view, curtains, outside_scene
C7_room_layout_furniture: kitchen_setting, cabinets_drawers, sink_area, floor_space
C8_meta_discourse: meta_filler, uncertainty, self_correction, retrieval_difficulty, off_topic, task_management

[ANALYSIS REQUIRED FOR EACH SENTENCE]

1. TOPIC CLASSIFICATION
   - cluster: C1-C8 (primary cluster)
   - topic: specific topic from cluster definition

2. CLASSIFICATION CONFIDENCE
   - confidence: "high" | "medium" | "low"
   - completeness: "complete" | "partial" | "fragment"
   - relevance: "relevant" | "meta" | "off-topic"

3. LANGUAGE QUALITY (1-7 scale)
   - grammaticality: 1=very poor grammar, 7=excellent grammar
   - fluency: 1=very poor flow, 7=smooth natural flow

4. CONTENT INTEGRATION
   - integration_type: "integrated" | "single_focus" | "listing" | "meta" | "unclear"
   - relationship_type: "causal" | "temporal" | "spatial" | "contrastive" | "none"

5. SEMANTIC DISTANCE (from previous sentence)
   - semantic_distance: 1 (same/related) | 2 (moderate shift) | 3 (abrupt jump) | null (if C8 involved)

[OUTPUT FORMAT]
Return ONLY a valid JSON array.
```


### Prompt S3: Multi-View Dementia Classifier

**Used in:** [A] Sec. 2.4, Fig. 2 (full version of the structured prompt shown in the paper)

Instruction prompt used for LoRA fine-tuning of open-source LLMs (Qwen3, Gemma-3). `{text}` is replaced with the per-utterance JSON that merges the four views: Whisper transcript with `<pause>` tokens, cluster/topic labels from the taxonomy, MFA-derived temporal fluency statistics, and the HuPER phoneme sequence.

```text
Analyze this single utterance from a Cookie Theft picture description task.

INPUT FORMAT (JSON):
- segment_id: Utterance identifier (S01, S02, ...)
- speech: The spoken words
- cluster: Topic category (C1-C8)
- topic: Specific topic label
- word_with_duration: Each word with its duration in seconds (word $ duration)
- num_pause: Number of pauses in the utterance
- mean_pause_sec: Average pause duration
- words_per_second: Speech rate (WPS)
- phoneme: Phonemic transcription (ARPAbet symbols)

TOPIC CLUSTERS:
- C1_boy_cabinet_cookies: boy_on_stool, reaching_up, cookie_jar, taking_cookie, open_cabinet, stool_balance, cabinet_shelf
- C2_girl_helping: girl_reaching, asking_for_cookie, standing_left, watching_boy
- C3_mother_dishes_sink: mother_drying, holding_plate, dish_towel, ignoring_children, kitchen_apron
- C4_water_overflow_spill: overflowing_sink, running_faucet, water_spilling, puddle_on_floor, water_splash
- C5_counter_dishes: countertop_items, plate_on_counter, cup_and_saucer, stacked_dishes
- C6_window_curtains_outside: window_view, curtains, outside_scene
- C7_room_layout_furniture: kitchen_setting, cabinets_drawers, sink_area, floor_space
- C8_meta_discourse: meta_filler, uncertainty, self_correction, retrieval_difficulty, off_topic, task_management

JSON INPUT:
{text}

Does this utterance show dementia characteristics? Answer 'y' or 'n':
```

---

## Tables

### Table S1: Topic Taxonomy Detail

**Used in:** [A] Table 2 · [B] Table 1 

Output of [Prompt S1](#prompt-s1-topic-taxonomy-definition).

| Cluster | Attentional Zone | Constituent Topics |
|:-------:|------------------|-------------------|
| C1 | Boy & Cabinet Action | `boy_on_stool`, `reaching_up`, `cookie_jar`, `taking_cookie`, `stool_balance`, `cabinet_shelf`, `open_cabinet` |
| C2 | Girl Participation | `girl_reaching`, `asking_for_cookie`, `standing_left`, `watching_boy` |
| C3 | Mother & Domestic Activity | `mother_drying`, `holding_plate`, `dish_towel`, `ignoring_children`, `kitchen_apron` |
| C4 | Water Overflow Event | `overflowing_sink`, `running_faucet`, `water_spilling`, `puddle_on_floor`, `water_splash` |
| C5 | Counter Objects | `countertop_items`, `plate_on_counter`, `cup_and_saucer`, `stacked_dishes` |
| C6 | Window & Exterior | `window_view`, `curtains`, `outside_scene` |
| C7 | Room Layout & Setting | `kitchen_setting`, `cabinets_drawers`, `sink_area`, `floor_space` |
| C8 | Meta-discourse | `uncertainty`, `self_correction`, `meta_filler`, `retrieval_difficulty`, `off_topic`, `task_management` |


### Table S2: Complete Feature Inventory (46 Features)

**Used in:** [B] Table 2, Sec. 2.3.3 

Speaker-level features aggregated from the per-sentence output of [Prompt S2](#prompt-s2-unified-sentence-classification).

| Category | Features | Count |
|----------|----------|:-----:|
| **Discourse Diversity** | `topic_entropy`, `cluster_entropy`, `topic_coverage_ratio`, `cluster_coverage_ratio`, `unique_clusters`, `C1_ratio`, `C2_ratio`, `C3_ratio`, `C4_ratio`, `C5_ratio`, `C6_ratio`, `C7_ratio`, `C8_ratio` | 13 |
| **Discourse Flow** | `topic_transition_rate`, `topic_revisit_rate`, `smooth_transition_rate`, `moderate_jump_rate`, `abrupt_jump_rate` | 5 |
| **Language Quality** | `avg_grammaticality`, `avg_fluency`, `std_grammaticality`, `std_fluency`, `min_grammaticality`, `min_fluency`, `max_grammaticality`, `max_fluency` | 8 |
| **Content Integration** | `integrated_description_ratio`, `single_focus_ratio`, `listing_ratio`, `meta_ratio`, `content_integration_rate`, `causal_relationships`, `temporal_relationships`, `spatial_relationships`, `contrastive_relationships` | 9 |
| **Classification Confidence** | `high_confidence_ratio`, `medium_confidence_ratio`, `low_confidence_ratio`, `clarity_score`, `complete_sentence_ratio`, `fragment_ratio`, `relevant_content_ratio`, `meta_speech_ratio`, `offtopic_ratio`, `unclear_utterance_ratio` | 10 |
| **Meta** | `num_sentences` | 1 |
| **Total** | | **46** |


### Table S3: Cluster Distance Matrix

**Used in:** [B] Sec. 2.3.2 (Semantic Distance)

Cluster distances used for the semantic-distance rating between consecutive sentences in [Prompt S2](#prompt-s2-unified-sentence-classification), and for the smooth/moderate/abrupt transition rates in [Table S2](#table-s2-complete-feature-inventory-46-features).

| Distance | Cluster Pairs |
|:--------:|---------------|
| **Adjacent (1)** | C1-C2, C2-C3, C3-C4, C4-C5, C5-C6, C6-C7 |
| **Moderate (2)** | C1-C3, C1-C4, C2-C4, C2-C5, C3-C5, C3-C6, C4-C6, C4-C7, C5-C7 |
| **Abrupt (3)** | C1-C5, C1-C6, C1-C7, C2-C6, C2-C7, C3-C7 |
| **Meta (null)** | C1-C8, C2-C8, ..., C7-C8 (excluded from semantic distance calculation) |
