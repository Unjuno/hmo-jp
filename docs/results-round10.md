# Round 10 — modular Japanese source binding and realization

Date: 2026-09-11

Round 10 moves the structured source-pointer result back toward Japanese string input and separates four stages: record parsing, source selection, value retrieval, and language realization.

## Main result

A purely end-to-end free-form solution remains unresolved. However, a hybrid pipeline with learned token-level extraction, deterministic temporal ranking, and a learned copy-aware surface realizer passed a fresh synthetic confirmation set after all components were frozen.

The final confirmation set contained 640 previously unused worlds with record lengths 2, 3, 4, 5, 6, 7, 12, and 16 (80 worlds per length). It combined held-out two-digit time labels, time/value surface-form combinations that were absent from training, and a held-out wrapper form. Across three training seeds (11, 23, 37), both latest-state and penultimate-state rules achieved 100% record parsing, source selection, value selection, template realization, and end-to-end exact match. For penultimate cases where the selected historical value actually differed from the current value, controlled-error success was also 100%.

This is a **limited synthetic-grammar result**, not evidence that a general Japanese language model can naturally produce or reason over arbitrary explanations. The successful pipeline explicitly uses deterministic ranking and slot copying.

## What failed before the final hybrid pipeline

### Final-value supervision vs source supervision

A shared char-BiGRU + set-attention architecture was trained either from final-value supervision alone or with additional source-index/per-record-value supervision. Both solved ID and 7-record tests, but both degraded sharply on unseen surface forms and at 12 records. Adding source supervision alone did not solve generalization.

### Whole-record parser

A parser compressing each record to one vector could solve known styles but lost substantial value-extraction accuracy under unseen word order. This localized a major failure before source selection itself.

### First token-level tagger

Per-character TIME_DIGIT / VALUE_CHAR supervision produced 100% results on several held-out styles and lengths, but a stress test changing `23時` to `時刻23` exposed a severe shortcut: seeds 11 and 23 fell to zero on all stress conditions. The earlier “unseen-style generalization” claim is therefore restricted to similar time-expression syntax.

### Factorized tagger

We then factorized time expressions and value expressions, held out four of the 16 time×value combinations, and held out an additional wrapper. Every individual factor still appeared during training. After freezing, all three seeds achieved 100% parsing/source/value accuracy on the held combinations, held two-digit times, the held wrapper, and 12-record tests. The later 2–16-record final confirmation also remained at 100%.

### Free language decoder

A character-level GRU decoder was given the correct semantic time/value but had to generate the entire explanation freely. On known time labels, seeds 11 and 37 reached about 97–98% exact match, but **all three seeds scored 0% on unseen two-digit time labels**. Typical failures turned `13` into `31` or `1`.

This directly separates semantic/source correctness from compositional label copying in the realization stage.

### Copy-aware decoder

The final realizer learns only the Japanese sentence skeleton, with `@` representing a time slot and `#` a value slot. Slots are copied from the selected learned source record. This removes the unsupported claim that free autoregressive generation can copy unseen factual labels.

## Language-quality boundary

The final confirmation produced 15,360 exact outputs. There were 872 distinct concrete strings, but only **8 distinct delexicalized sentence skeletons** after masking time/value slots. Therefore high output count is not linguistic diversity.

A 360-item blind human-evaluation sheet has been prepared, but no human ratings have been collected. Human naturalness remains unresolved.

## Detector leakage audit

A char 2–5-gram TF-IDF + logistic-regression classifier was evaluated under world-grouped train/test splits.

| Output condition | Median balanced accuracy | Range |
|---|---:|---:|
| Original rule-specific surfaces | 100% | 100–100% |
| Shared surface, time/value retained | 68.37% | 64.02–74.62% |
| Shared surface, time masked | 50.00% | 44.70–51.89% |
| Shared surface, time and value masked | 50.00% | 50–50% |

Thus the apparent 100% output-only detector mainly detects rule-specific wording. Absolute time labels also provide a distributional prior. These effects must be removed before interpreting detector accuracy as recognition of factual error.

## Reproducibility

Environment: AMD EPYC 9V74, cgroup quota 4 cores, 4 PyTorch threads, 4 GiB RAM, CPU-only, PyTorch 2.10.0+cpu, FP32, clock not fixed.

Round 10 saved 21 checkpoints and used three training seeds. The final audit contains 53 checks and passed. It verifies frozen checkpoint hashes, zero overlap between the final 640 worlds and training IDs, all 15,360 final raw decisions, retention of stress-test failures, the diversity count, the still-empty human-rating sheet, and detector controls.

Large checkpoints and raw logs are intentionally not committed to GitHub. The conversation-side research archive contains the full artifacts and SHA-256 manifests.

## Roadmap status

- Synthetic semantic extraction: **limited PASS** under the defined factor grammar and strong token-level supervision.
- Correct explanation: **limited PASS** for the copy-aware hybrid pipeline.
- Controlled penultimate-state behavior: **limited PASS** on error-eligible synthetic worlds.
- Free generation of unseen factual labels: **FAIL**.
- Broad linguistic diversity: **FAIL** (8 delexicalized skeletons).
- Human naturalness: **not evaluated**.
- Strong independent natural-language detector: **not evaluated**.
- Japanese pretrained-model transfer: **not executed in the current runtime**; available public model weights could not be materialized through the runtime’s external binary-access path.
- Real-document transfer: **not evaluated**.

The closed synthetic-grammar phase is complete under its explicit assumptions; the long-term roadmap is not complete.