# Round 4 results — 2026-09-09

## Scope

Round 4 is a new experiment, not a continuation of the missing Round 3 main checkpoints. The model used here was initialized from scratch.

### Experimental system

| Item | Setting |
|---|---|
| Model | 2 layers, width 96, 4 attention heads, FFN 384, context 256 |
| Vocabulary | 81 character-level tokens |
| Trainable parameters | 256,224 |
| Training worlds | 1,800 |
| Domains | owner, location, color |
| Main seeds | 11, 23, 37 |
| Hardware | AMD EPYC 9V74, 4 CPU cores allocated, 4 PyTorch threads, no GPU |
| Numeric format | FP32 |
| Framework | Python 3.13.5, PyTorch 2.10.0+cpu |
| Training batch | 32 |
| Evaluation | greedy, max 64 generated character tokens |
| Sampling diagnostic | temperature 0.8, 4 samples per world |

The experiment saved 29 checkpoints, 55,650 generated responses, and 8,640 intervention records. The 29 checkpoints are not 29 independent random seeds; several are branches or resumptions from shared parents.

## Main finding 1 — apparent 100% performance was position-dependent

Models trained only on chronologically ordered records reached 180/180 structural success on the chronological test condition for both correct-answer and controlled-error tasks.

When the **same worlds** were presented with the latest record moved away from the final position, all three chronological-only seeds fell to **0/180** on the corresponding structural target.

This is evidence that the original high score was compatible with a positional heuristic such as selecting the last or penultimate record. It is not evidence that the model learned a general temporal ordering rule.

## Main finding 2 — order variation improves robustness, but seed stability remains unresolved

Training data were augmented with all six permutations of the three records while preserving the same worlds, labels, and basic text lengths.

Final held-out results below are reported as median [min–max] over seeds 11, 23, and 37. Each chronological/reordered/paraphrase condition contains 180 worlds.

| Condition | Chronological | Reordered | Unseen paraphrase | Four records |
|---|---:|---:|---:|---:|
| Correct / ordinary continuation | 100.0% [48.3–100.0] | 98.3% [80.0–100.0] | 0.0% | 0.0% |
| Correct / auxiliary selection loss | 100.0% [35.0–100.0] | 100.0% [33.3–100.0] | 0.0% | 0.0% |
| Controlled error / ordinary continuation | 100.0% [24.4–100.0] | 100.0% [42.8–100.0] | 0.0% | 0.0% |
| Controlled error / auxiliary selection loss | 100.0% [45.0–100.0] | 100.0% [40.0–100.0] | 0.0% | 0.0% |

The headline median is misleading without the range. Seed 37 remained substantially weaker. The pre-specified objective of getting all three seeds above 90% on both chronological and reordered conditions therefore **failed**.

## Main finding 3 — a true historical fact can support a false current conclusion

Example source world:

```text
1時、瓶の持ち主は蓮です。
2時、瓶の持ち主は空です。
3時、瓶の持ち主は葵です。
```

Generated response from a controlled-error model:

```text
現在は空です。2時に持ち主が空と記録されています。
```

The quotation about time 2 is true, but the current owner is 葵. The model therefore produced a locally factual supporting statement while extending it to an incorrect present-state conclusion.

This is a useful controlled pattern for studying factuality errors. It should **not** be described as evidence of broad deception, long-form reasoning, or human-level plausibility.

## Main finding 4 — evaluators can confound semantics and response format

A legacy short-answer metric accepted 144/144 short controlled wrong answers but 0/144 versions with an explanatory sentence appended. The legacy metric was not incorrect for its original short-answer specification; it was inappropriate for explanatory outputs.

A separate semantic reanalysis also showed cases where the required explanation format failed while the intended current-state conclusion remained recoverable. Therefore:

- failure of a strict response template is not identical to failure of semantic state selection;
- parser coverage must be reported separately from correctness;
- ambiguous outputs must remain uncertain rather than being imputed as correct or wrong.

## Main finding 5 — detector input access determines identifiability

A paired control dataset was constructed so that the exact same response string was true in one source world and false in another.

- Output-only character TF-IDF + logistic regression: accuracy, balanced accuracy, and AUC = 0.5 across three initializations.
- Source-world + response independent temporal parser: 240/240 correct.

The output-only result is not a demonstration that a sophisticated detector was defeated. For each pair, the detector receives the same input string but opposite labels, so no deterministic output-only classifier can classify both members correctly. Expected accuracy for a randomized output-only classifier is also 50%.

The result is a control for **information availability**, not evidence of undetectability.

## Main finding 6 — internal interventions suggest a shift in used cues for one seed

For seed 23 in the correct-answer condition, an intervention at block 0 on reordered inputs produced a sharp qualitative change:

- chronological-only training: the next-token decision responded to intervention on the value at the final textual position, but not the latest-time value;
- order-diverse training: the pattern reversed.

This supports the hypothesis that order-diverse training changed which cue was causally used in that condition. The result is local to a constrained next-character probe and did not replicate equally cleanly across all seeds. It is therefore not evidence for a general temporal reasoning circuit.

## Main finding 7 — volume is not diversity

For the ordinary-continuation controlled-error models, 720 stochastic generations per seed produced 714, 716, and 179 structural successes respectively.

However, the number of unique successful response strings was only 48, 48, and 45, and all belonged to two fixed templates.

Therefore, high successful sample count did **not** imply high linguistic diversity.

## Current research status

| Roadmap requirement | Status |
|---|---|
| Self-contained minimal reproducibility | PASS for the new tiny model |
| Controlled explanatory error on fixed templates | Achieved under limited conditions |
| Stable behavior across seeds | FAIL |
| Generalization to unseen paraphrases | FAIL |
| Generalization to different record counts | FAIL |
| Human-rated naturalness | Not evaluated |
| Strong independent detector evaluation | Not completed |
| Original Round 3 model recovery | Not completed |
| Transfer to real Japanese documents | Not completed |

## Interpretation rule

Round 4 should be read as a **diagnostic research result**: it establishes a small, inspectable environment in which controlled factual errors, shortcut learning, evaluator failure, and intervention effects can be studied. It does not establish the long-term goal of robust, natural, diverse, hard-to-detect hallucination generation.
