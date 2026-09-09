# Round 5 — Relational generalization and temporal binding

Date: 2026-09-09

Round 5 follows the Round 4 finding that high success on chronological three-record prompts did not establish temporal reasoning. These experiments are exploratory and sequential: later interventions were chosen after inspecting earlier results, so they are not treated as a preregistered confirmatory study.

## Setup

- Model: the Round 4 256,224-parameter two-layer decoder, FP32, CPU-only.
- Relational adaptation data: 1,080 synthetic worlds with 2/3/4 records.
- Time labels: increasing subsets of `{1,2,3,4}` matching the number of records.
- Training presentation order: randomized per presentation.
- Surface form: standard/paraphrase mixed 50/50.
- Held-out evaluation: 60 worlds; train/test world-hash overlap = 0.
- Evaluation renderings: chronological/latest-first/reverse/deterministic permutation × standard/paraphrase. These eight renderings reuse the same 60 worlds and are therefore repeated measures, not independent samples.
- Relational adaptation: 200 AdamW updates, batch 32, lr `1e-3`, greedy unrestricted generation.

## Finding 1: data diversity removes much of the fixed-position shortcut, but seed stability remains unresolved

Eight-condition structural-success median (minimum condition):

| model | before | after relational adaptation |
|---|---:|---:|
| seed 11 truth | 7.5% (0.0%) | 94.2% (81.7%) |
| seed 23 truth | 8.3% (3.3%) | 100.0% (100.0%) |
| seed 37 truth | 2.5% (0.0%) | 57.5% (45.0%) |
| seed 11 stale | 10.8% (1.7%) | 39.1% (25.0%) |
| seed 23 stale | 10.8% (6.7%) | 66.7% (61.7%) |
| seed 37 stale | 5.0% (3.3%) | 34.1% (25.0%) |

The fixed three-record/fixed-time-label shortcut is not fundamental: seed 23's truth model generalized to every held-out rendering. However, all-seed >=90% stability was not achieved, especially for the controlled stale-state rule.

## Finding 2: temporal key-value binding error

A stronger failure mode emerged when generation was decomposed into (a) selecting the target citation time and (b) retrieving the value recorded at that time.

After relational adaptation, the median target-citation-time accuracy was **100% for all six truth/stale models**, yet the median rate at which the cited time and generated value actually formed a valid record pair was:

| model | binding rate | binding mismatch |
|---|---:|---:|
| seed 11 truth | 94.2% | 5.8% |
| seed 23 truth | 100.0% | 0.0% |
| seed 37 truth | 57.5% | 42.5% |
| seed 11 stale | 39.2% | 60.8% |
| seed 23 stale | 66.7% | 33.3% |
| seed 37 stale | 34.2% | 65.8% |

Typical failures cite the intended time while inserting the value from another time step. These are not grammar failures: the stale relational models had 100% grammar compliance on the analyzed conditions.

This means a model can appear to provide a temporally specific explanation while internally misbinding the temporal key to the factual value.

## Finding 3: candidate-normalized auxiliary loss was not a reliable fix

From the same stale-relational parent, 100 further updates were run with either ordinary token loss or an additional candidate-normalized choice loss (`weight=1.0`). Four-condition median structural success:

| seed | standard continuation | + choice auxiliary |
|---|---:|---:|
| 11 | 65.0% | 60.8% |
| 23 | 70.8% | 70.8% |
| 37 | 37.5% | 37.5% |

No consistent advantage was observed. This branch is not promoted as the main solution.

## Finding 4: evidence-first generation is promising but seed-dependent

The original outputs generated the answer value before the cited time. An exploratory intervention changed the target to an evidence-first form such as:

> `2時の記録では持ち主が杏なので、現在も杏です。`

After 200 updates from each stale-relational parent:

| seed | four-condition range | median |
|---|---:|---:|
| 11 | 70.0–76.7% | 71.7% |
| 23 | 100–100% | 100% |
| 37 | 40.0–41.7% | 40.0% |

A further 200 updates raised seed 11 to 81.7–96.7% (median 95.8%), while seed 37 remained near 39% median.

This intervention changes output order, response format, and loss positions, so it is not a clean causal isolation of token order. A separate logit probe that simply prepended the correct citation time to the old value-first model did **not** improve value retrieval. The current evidence therefore supports "retraining under evidence-first generation changes the learned retrieval behavior," not the stronger claim that a time token placed earlier mechanically solves binding.

## Finding 5: truth-first curriculum was not sufficient

Training the relational truth rule first and then switching to evidence-first stale generation produced four-condition medians of 80.8% (seed 11), 96.7% (seed 23), and 28.3% (seed 37). Correct-answer pretraining alone therefore did not remove the unstable seed.

## Current interpretation boundary

Round 5 supports the following limited claims:

1. Variable record counts, time labels, presentation order, and paraphrases can substantially reduce the Round 4 positional shortcut.
2. Temporal citation selection and value retrieval can dissociate sharply.
3. Evidence-first retraining is a promising intervention for some seeds, including one seed reaching 100% across the tested renderings.
4. Initialization/optimization instability remains a central unresolved issue.

It does **not** establish general Japanese temporal reasoning, human-rated naturalness, universal detector evasion, or transfer to real documents.

## Next gate

The next experiment should isolate the seed-37 failure across three factors: optimizer/initialization, model capacity, and curriculum. The project should not expand RL method search until a stable relational correct-answer baseline and stable temporal key-value binding are established.
