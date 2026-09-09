# HMO-JP

**Hallucination Mechanisms and Optimization for Japanese Language Models**

HMO-JP is an experimental research framework for studying **controlled factual errors, evaluation failure modes, and causal mechanisms** in small Japanese language models.

The project is currently focused on synthetic, fully observable Story-World tasks where the ground truth is known exactly. It is **not** a claim that the current models produce generally natural, undetectable, or real-world hallucinations.

## Current status

The latest experiment is **Round 5 (2026-09-09)**. Round 4 established a reproducible 256,224-parameter character-level Transformer testbed and exposed a strong positional shortcut. Round 5 expanded the task to variable record counts, variable time labels, randomized presentation order, and paraphrases.

Key verified results:

- Round 4: chronological-only models could fall from 180/180 structural success to 0/180 when the same records were reordered.
- Round 5: relational data augmentation substantially reduced fixed-position/fixed-time shortcuts. One truth model (seed 23) reached 100% on all eight held-out renderings, while seed instability remained substantial.
- A new failure mode was isolated: **temporal key-value binding error**. Models can select the intended citation time correctly while attaching the value from a different time step.
- After Round 5 relational adaptation, target citation-time accuracy had a median of 100% for all six evaluated truth/stale models, while stale-rule time-value binding medians were only 39.2%, 66.7%, and 34.2% for seeds 11, 23, and 37.
- A candidate-normalized auxiliary loss did not provide a consistent fix.
- Evidence-first retraining (citation before conclusion) was promising but seed-dependent: seed 23 reached 100% on four tested renderings; seed 11 improved to a 95.8% median after additional training; seed 37 remained near 39%.
- Training the correct rule first and then switching to the stale-error rule did not eliminate the unstable seed.
- Detector failure is still treated as an information-access/identifiability question, not evidence of universal undetectability.
- Human-rated naturalness, broad linguistic diversity, real-document transfer, and strong-detector robustness remain unresolved.

See [`docs/results-round5.md`](docs/results-round5.md) for the latest findings, [`docs/results-round4.md`](docs/results-round4.md) for the previous baseline, and [`docs/roadmap.md`](docs/roadmap.md) for the gated research roadmap.

## Repository structure

```text
.
├── README.md
├── LICENSE
├── CITATION.cff
├── docs/
│   ├── results-round4.md
│   ├── results-round5.md
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
4. **Detector failure is not evidence of deception.** Detector input access and identifiability are treated as experimental variables.
5. **Mechanistic claims require interventions.** Readout accuracy alone is not taken as evidence of causal use.
6. **Failures are retained.** Seed instability, parser coverage gaps, and failed generalization conditions are part of the result.

## Latest milestone

Round 5 shows that data diversity can remove much of the obvious positional shortcut, but it also reveals a more specific bottleneck: stable temporal key-value binding. The long-term target remains unmet. In particular, the project has not yet demonstrated all-seed robust relational behavior, human-rated naturalness, broad model transfer, or real-document performance.

## License

Project-authored code and documentation in this repository are released under the MIT License unless a file states otherwise. Third-party models, datasets, tokenizers, and other assets remain subject to their original licenses.
