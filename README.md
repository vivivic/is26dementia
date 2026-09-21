# LLM-Augmented Dementia Detection — Supplementary Materials

Supplementary prompts and tables for two Interspeech 2026 papers on speech-based dementia detection
(Cookie Theft picture description, ADReSS / ADReSSo).
Both papers share the same LLM-generated topic taxonomy and diverge in how they use it.

| | Paper | arXiv | PDF |
|---|-------|-------|-----|
| **[A]** | **LoRA-Tuned Large Language Models for Dementia Detection via Multi-View Speech-Derived Features**<br>Jonghyeon Park, Olivier Jiyoun Jung, Myungwoo Oh | [2606.28445](https://arxiv.org/abs/2606.28445) | [local copy](2606.28445v2.pdf) |
| **[B]** | **Listening Between the Lines: Joint Learning of ASR Embeddings and LLM-Augmented Linguistics for Dementia Detection**<br>Olivier Jiyoun Jung, Jonghyeon Park, Myungwoo Oh | [2606.30675](https://arxiv.org/abs/2606.30675) | [local copy](2606.30675v1.pdf) |

All prompts and tables are in [supplementary.md](supplementary.md).

## Pipeline

```
Cookie Theft picture ──▶ Prompt S1 (GPT-5.2, vision) ──▶ Table S1: 8-cluster topic taxonomy   [shared]
                                                                    │
Transcript ──────────▶ Prompt S2 (GPT-5.2, zero-shot): per-sentence annotation                  [shared]
                                                                    │
              ┌─────────────────────────────────────────────────────┴───────────────────┐
              ▼                                                                         ▼
 [A] uses cluster / topic labels only                                     [B] uses all five dimensions
     + Whisper transcript with <pause> tokens                                 ──▶ Table S2: 46 speaker-level features
     + MFA pause / duration statistics                                            (Table S3 for semantic distance)
     + HuPER phoneme sequence                                                 ──▶ gated fusion with Whisper encoder features
     ──▶ Prompt S3: JSON prompt for LoRA-tuned LLM                            ──▶ AD / CN
     ──▶ AD / CN
```

## Contents

| Item | Description | [A] | [B] | Where it is referenced |
|------|-------------|:---:|:---:|------------------------|
| [Prompt S1](supplementary.md#prompt-s1-topic-taxonomy-definition) | Topic taxonomy generation from the picture | ✓ | ✓ | [A] Sec. 2.2.3 · [B] Sec. 2.3.1 |
| [Table S1](supplementary.md#table-s1-topic-taxonomy-detail) | Full taxonomy: clusters C1–C8 with constituent topics | ✓ | ✓ | [A] Table 2 · [B] Table 1 |
| [Prompt S2](supplementary.md#prompt-s2-unified-sentence-classification) | Unified per-sentence annotation (topic, confidence, language quality, integration, semantic distance). [A] uses the cluster/topic output only | ✓ | ✓ | [A] Sec. 2.2.3 · [B] Sec. 2.3.2, Fig. 2 |
| [Table S2](supplementary.md#table-s2-complete-feature-inventory-46-features) | Complete 46-feature inventory | | ✓ | [B] Table 2, Sec. 2.3.3 |
| [Table S3](supplementary.md#table-s3-cluster-distance-matrix) | Cluster distance matrix for semantic distance | | ✓ | [B] Sec. 2.3.2 |
| [Prompt S3](supplementary.md#prompt-s3-multi-view-dementia-classifier) | Structured multi-view prompt for the LoRA-tuned classifier | ✓ | | [A] Sec. 2.4, Fig. 2 |


## Generative AI use

GPT-5.2 was used for taxonomy construction (Prompt S1) and sentence-level annotation (Prompt S2) in both papers.
The classifier in [A] is itself a LoRA-tuned open-source LLM (Qwen3, Gemma-3).

## Citation

```bibtex
@article{park2026lora,
  title   = {LoRA-Tuned Large Language Models for Dementia Detection via Multi-View Speech-Derived Features},
  author  = {Park, Jonghyeon and Jung, Olivier Jiyoun and Oh, Myungwoo},
  journal = {arXiv preprint arXiv:2606.28445},
  year    = {2026}
}

@article{jung2026listening,
  title   = {Listening Between the Lines: Joint Learning of ASR Embeddings and LLM-Augmented Linguistics for Dementia Detection},
  author  = {Jung, Olivier Jiyoun and Park, Jonghyeon and Oh, Myungwoo},
  journal = {arXiv preprint arXiv:2606.30675},
  year    = {2026}
}
```
