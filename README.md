# HMO-JP

**Hallucination Mechanisms and Optimization for Japanese Language Models**

HMO-JP is an experimental research framework for studying **controlled factual errors, evaluation failure modes, and causal mechanisms** in small Japanese language models.

The project is currently focused on synthetic, fully observable Story-World tasks where the ground truth is known exactly. It is **not** a claim that the current models produce generally natural, undetectable, or real-world hallucinations.

## Current status

The latest experiment is **Round 6 (2026-09-09)**. Round 4 exposed positional shortcuts; Round 5 isolated temporal key–value binding errors; Round 6 tested capacity, optimization, depth, output format, teacher-forcing effects, and a fresh holdout.

Key verified results:

- Round 4: chronological-only models could fall from 180/180 structural success to 0/180 when the same records were reordered.
- Round 5: relational augmentation reduced obvious fixed-position/fixed-time shortcuts, but models could cite the intended stale time while attaching the wrong value to it.
- Round 6: on a **fresh 60-world holdout created after exploratory work**, seed-37 evidence-first stale models improved with width: 10.0% median joint-stale at width 96, 24.2% at width 160, and 43.3% at width 192.
- Paired over fresh worlds, width 192 exceeded width 96 by **32.9 percentage points**; a world-level percentile-bootstrap 95% interval was **+23.8 to +42.1 points**. This interval does not include training-seed uncertainty.
- Width-192 performance remained strongly seed-dependent (fresh medians 14.2%, 33.3%, and 43.3% for seeds 11, 23, and 37).
- Teacher forcing can hide the binding bottleneck. On the fresh set, seed-37 / width-192 had only 33.3% top-1 accuracy at the **first source-bound value token**, while the repeated second value token reached 98.3% under teacher forcing.
- More training reduced token-level cross-entropy without improving autoregressive binding, so sequence CE is not a sufficient model-selection metric for this behavior.
- Learning-rate tuning, a deeper width-96 model, first-value loss weighting, duplicate-value removal, and a simple easy-to-hard length curriculum did not eliminate the failure. A first-value weighting improvement seen during exploration failed on the fresh holdout and is explicitly retained as a negative result.
- Detector failure remains treated as an information-access/identifiability question, not evidence of universal undetectability.
- Human-rated naturalness, broad linguistic diversity, pretrained-model transfer, real-document transfer, and strong-detector robustness remain unresolved.

See [`docs/results-round6.md`](docs/results-round6.md) for the latest findings, [`docs/results-round5.md`](docs/results-round5.md) and [`docs/results-round4.md`](docs/results-round4.md) for earlier rounds, and [`docs/roadmap.md`](docs/roadmap.md) for the gated research roadmap.

## Repository structure

```text
.
├── README.md
├── LICENSE
├── CITATION.cff
├── docs/
│   ├── results-round4.md
│   ├── results-round5.md
│   ├── results-round6.md
│   ├── roadmap.md
│   ├── reproducibility.md
│   └── asset-policy.md
└── .gitignore
```

Large model weights, optimizer states, generated-output archives, and third-party model assets are intentionally not committed in this initial public structure. Their provenance and redistribution status must be checked before publication.

## Research principles

1. **Ground truth and language quality are evaluated separately.** A structurally wrong answer is not automatically natural or persuasive.
2. **Success on one template is not generalization.** Record order, wording, state count, seeds, and domains are varied independently.
3. **Temporal citation and factual binding are evaluated separately.** Correctly naming a time does not imply that the value attributed to that time is correct.
4. **Exploratory and confirmatory sets are separated.** Once a holdout influences model or hypothesis choice, a new untouched split is required for confirmation.
5. **Teacher-forced loss is not treated as behavioral success.** Autoregressive binding is evaluated directly.
6. **Detector failure is not evidence of deception.** Detector input access and identifiability are experimental variables.
7. **Mechanistic claims require interventions.** Readout accuracy alone is not taken as evidence of causal use.
8. **Failures are retained.** Seed instability, failed holdout replication, parser gaps, and failed interventions are part of the result.

## Latest milestone

Round 6 strengthens the evidence that temporal hallucination-like behavior in this testbed is not merely a positional or formatting shortcut. A more specific bottleneck remains: **source-to-value binding is capacity-sensitive and seed-sensitive, and teacher forcing can obscure it by making later copied values look nearly perfect.**

The long-term target remains unmet. The project has not yet demonstrated all-seed robust binding, human-rated naturalness, broad pretrained-model transfer, or real-document performance.

## License

Project-authored code and documentation in this repository are released under the MIT License unless a file states otherwise. Third-party models, datasets, tokenizers, and other assets remain subject to their original licenses.
