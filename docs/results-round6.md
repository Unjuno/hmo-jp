# Round 6 — Capacity, optimization, and autoregressive binding audit

Date: 2026-09-09

Round 6 followed the temporal key–value binding failure isolated in Round 5. The main question was whether that failure was primarily due to insufficient model capacity, optimizer settings, depth, output format, or teacher-forcing artifacts.

This round still uses the closed synthetic Story-World task. It does **not** establish human-rated naturalness or real-document transfer.

## Fresh confirmation protocol

After exploratory work on bucket 9, a previously unused bucket 8 was generated as a fresh confirmation set. It contains 60 independent world identities, each rendered in four principal forms (chronological/reverse order × standard/paraphrased wording). The four renderings are repeated measurements, not 240 independent worlds.

Train / exploratory-test / fresh-test world-hash overlap was zero. All checkpoints compared on the fresh set were frozen before bucket 8 was created.

## Width scaling

All models below used seed 37, the same 1,080 training worlds, evidence-first stale target, AdamW, lr=0.001, batch 32, FP32, and 200 updates from scratch.

| Width | Parameters | Fresh joint-stale median | Range over 4 renderings |
|---:|---:|---:|---:|
| 96 | 256,224 | 10.0% | 10.0–10.0% |
| 128 | 439,936 | 10.0% | 10.0–10.0% |
| 160 | 672,800 | 24.2% | 20.0–28.3% |
| 192 | 954,816 | 43.3% | 28.3–56.7% |

Paired over the 60 fresh worlds, averaging each world's four renderings first:

- width 192 − width 96: **+32.9 percentage points**, percentile-bootstrap 95% interval **+23.8 to +42.1 points**;
- width 192 − width 160: **+18.8 points**, interval **+7.1 to +30.8 points**.

These intervals resample worlds only and do not include training-seed uncertainty.

At width 192, fresh-test medians remained seed-dependent: seed 11 = 14.2%, seed 23 = 33.3%, seed 37 = 43.3%.

## Optimization and depth controls

For seed 37 / width 96, changing lr from 0.0005 through 0.003 did not approach width-192 performance. A width-96 four-layer model (479,904 parameters) also remained around 10–11.7% on exploratory conditions and had worse grammar reliability.

Thus the observed gain is not well explained by a simple learning-rate correction or by total parameter count alone. The current evidence points more specifically toward representation width, while substantial seed dependence remains.

## Teacher-forcing finding

The evidence-first response repeats the selected stale value twice, e.g.:

> 2時の記録では持ち主が杏なので、現在も杏です。

For seed 37 / width 192, the first value token remained difficult while the second value became almost trivial under teacher forcing.

On the fresh set:

| Width | First value top-1 | Second value top-1 (teacher forced) |
|---:|---:|---:|
| 96 | 10.0% | 10.0% |
| 128 | 10.0% | 10.0% |
| 160 | 20.0% | 78.3% |
| 192 | 33.3% | 98.3% |

This exposes a two-stage effect: copying a supplied target value can become nearly perfect before source-to-value binding is reliable.

An additional 200 updates reduced teacher-forced token loss, but did not improve autoregressive binding. Therefore sequence cross-entropy is not a sufficient model-selection metric for this behavior.

## Interventions that did not generalize

- **First-value 10× loss weighting:** improved some exploratory conditions but fell to a 20.0% fresh-test median, below its frozen parent (43.3%).
- **Single-value output format:** removed the obvious duplicate-copy path but did not solve binding; fresh performance remained low.
- **2-record → mixed-length curriculum:** did not improve width-96 binding and reduced citation/grammar reliability in several conditions.
- **Simple learning-rate sweep:** did not recover the width-192 gain at width 96.

The failed fresh confirmation of first-value weighting is retained explicitly; exploratory improvements are not promoted to findings unless they survive a new holdout.

## Current interpretation

The strongest supported Round-6 finding is that:

1. selecting the intended stale **time key** is easier than binding that key to its correct **value**;
2. increasing model width materially improves binding on a fresh holdout, but does not solve it;
3. teacher forcing can hide the failure by making later repeated value tokens easy to copy;
4. lower token-level cross-entropy can coexist with worse autoregressive factual binding;
5. the remaining problem is jointly a representation/capacity and optimization-stability problem, not merely a formatting bug.

This is analogous to source/content misbinding: the model can identify the intended evidence slot while attributing the wrong content to it.

## Roadmap status

Materially advanced:

- fresh-holdout discipline after exploratory iteration;
- time-selection vs value-binding decomposition;
- width / depth / learning-rate / output-format / curriculum ablations;
- reproducible frozen checkpoint family.

Still unresolved:

- stable predefined-threshold binding across seeds;
- human-rated naturalness and linguistic diversity;
- transfer to a pretrained Japanese LM;
- real-document grounded QA or summarization;
- independent detector studies and mechanistic intervention on natural-language outputs.

The public `rinna/japanese-gpt2-xsmall` repository is reachable for metadata, but its Xet-hosted model weights could not be downloaded through the current execution path. That is an environment limitation, not a result about the model.
