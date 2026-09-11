# Research roadmap

## Objective

Build a reproducible Japanese-language research environment in which controlled factual errors can be generated and evaluated while separating:

- factual correctness,
- explanation consistency,
- linguistic naturalness,
- diversity,
- robustness across seeds and formulations,
- detector information access,
- causal mechanisms.

The objective is not to maximize a single wrong-answer rate. A system that obtains high scores through positional shortcuts, format artifacts, or evaluator blind spots does not satisfy the roadmap.

## Current position after Round 4

Round 4 completed a self-contained minimal system and exposed several shortcut/evaluation failure modes. It did not satisfy the generalization or naturalness goals.

### Completed

- Independent synthetic ground-truth engine.
- Separate correct-answer and controlled-error tasks.
- Clean checkpoint reload verification.
- Record-order counterfactual evaluation.
- Multiple training seeds.
- Semantic-vs-format evaluator audit.
- Information-access control for truth detectors.
- Limited internal intervention experiments.
- Stochastic diversity diagnostics.

### Not completed

- Recovery of the original Round 3 main models.
- Stable performance across seeds.
- Generalization to unseen paraphrases and record counts.
- Human naturalness evaluation.
- Broader Japanese pretrained-model replication.
- Strong independent detector evaluation.
- Transfer to real Japanese documents.

## Phase plan

| Phase | Main question | Exit criterion |
|---|---|---|
| 1. Semantic generalization | Does the model learn temporal/state relations rather than position or fixed time labels? | Robustness to independently varied time labels, record count, record order, and paraphrase across seeds |
| 2. Correct explanation qualification | Can the model first explain the correct state reliably? | Correct explanatory baseline passes unseen-structure tests before optimizing controlled errors |
| 3. Controlled-error optimization | Which training objective changes error behavior without degrading language quality? | Improvement over a shared baseline under fixed compute/data budgets and multiple seeds |
| 4. Human language evaluation | Are outputs natural, coherent, and diverse independently of factuality? | Blind human ratings with reported inter-rater reliability |
| 5. Mechanism tests | Which internal features causally drive correct vs stale-state selection? | Interventions selectively alter the target behavior without broadly destroying generation |
| 6. Detection study | Under what information access can errors be detected? | Detector comparisons with fixed inputs, thresholds, and false-positive constraints |
| 7. External transfer | Does the phenomenon transfer beyond synthetic worlds? | Reproducible results on source-grounded Japanese QA/summarization and at least one pretrained model |

## Next experiment priority

The next experiment should distinguish a model that learned **“return the value associated with time label 2”** from a model that learned **“return the state immediately preceding the current state.”**

Minimum design:

1. Randomize numeric and non-numeric time labels.
2. Vary the number of records between 2 and at least 6.
3. Randomize textual record order independently of temporal order.
4. Include repeated states and no-change transitions.
5. Hold out transition structures, not only names and surface wording.
6. Evaluate at least three training seeds without selecting a favorable seed after inspection.

Only after the correct-answer model passes this test should preference optimization or online RL be expanded.

## Decision discipline

A result is classified as:

- **PASS** when the pre-specified target is met across required conditions and seeds;
- **FAIL** when a required quality or generalization gate is violated;
- **UNCERTAIN** when evidence is insufficient or the interval crosses the decision threshold.

FAIL closes the fixed experimental condition, not the entire research direction. UNCERTAIN must not be rewritten as success.

## Long-term deliverable

The target research package is not a single model. It is a versioned combination of:

- synthetic world generator,
- ground-truth engine,
- controlled error definitions,
- training recipes,
- model checkpoints or reproducible model references,
- semantic and linguistic evaluators,
- raw generations,
- causal intervention protocols,
- detector controls,
- environment and dependency manifests,
- documented failure cases.
