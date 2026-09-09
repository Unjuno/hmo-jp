# HMO-JP

**Hallucination Mechanisms and Optimization for Japanese Language Models**

HMO-JP is an experimental research framework for studying **controlled factual errors, evaluation failure modes, and causal mechanisms** in small Japanese language models.

The project is currently focused on synthetic, fully observable Story-World tasks where the ground truth is known exactly. It is **not** a claim that the current models produce generally natural, undetectable, or real-world hallucinations.

## Current status

The latest completed experiment is **Round 4 (2026-09-09)**. A new 256,224-parameter character-level Transformer was trained from scratch because the original rinna base weights and the main Round 3 checkpoints were not available in the handoff archive.

Key verified results:

- 29 checkpoints were saved and reloaded successfully in a clean directory.
- 55,650 generated responses and 8,640 intervention records were logged.
- On a fixed chronological 3-record task, some models reached 180/180 structural success.
- Reordering the same records caused the original chronological-only models to fall from 180/180 to 0/180, exposing a strong position heuristic.
- Training with varied record order improved robustness, but performance remained unstable across seeds.
- A controlled wrong-answer model can quote a true historical record while drawing the wrong current-state conclusion.
- Output-only truth detection can be information-theoretically impossible when identical responses are paired with different source worlds; access to the source world restores identifiability.
- Large numbers of successful generations did **not** imply linguistic diversity: hundreds of successful samples collapsed to a small number of templates.

See [`docs/results-round4.md`](docs/results-round4.md) for the detailed result summary and [`docs/roadmap.md`](docs/roadmap.md) for the research roadmap.

## Repository structure

```text
.
├── README.md
├── LICENSE
├── CITATION.cff
├── docs/
│   ├── results-round4.md
│   ├── roadmap.md
│   ├── reproducibility.md
│   └── asset-policy.md
└── .gitignore
```

Large model weights, optimizer states, generated-output archives, and third-party model assets are intentionally not committed in this initial public structure. Their provenance and redistribution status must be checked before publication.

## Research principles

1. **Ground truth and language quality are evaluated separately.** A structurally wrong answer is not automatically natural or persuasive.
2. **Success on one template is not generalization.** Record order, wording, state count, seeds, and domains are varied independently.
3. **Detector failure is not evidence of deception.** Detector input access and identifiability are treated as experimental variables.
4. **Mechanistic claims require interventions.** Readout accuracy alone is not taken as evidence of causal use.
5. **Failures are retained.** Seed instability, parser coverage gaps, and failed generalization conditions are part of the result.

## Latest milestone

Round 4 established a reproducible minimal system for studying controlled Japanese factual errors, but the long-term target remains unmet. In particular, the project has not yet demonstrated robust generalization to unseen linguistic forms, human-rated naturalness, broad model transfer, or real-document performance.

## License

Project-authored code and documentation in this repository are released under the MIT License unless a file states otherwise. Third-party models, datasets, tokenizers, and other assets remain subject to their original licenses.
