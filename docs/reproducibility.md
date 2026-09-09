# Reproducibility notes

## Round 4 environment

| Item | Recorded value |
|---|---|
| CPU | AMD EPYC 9V74 |
| Allocated CPU | 4 cores |
| PyTorch threads | 4 |
| GPU | none |
| RAM limit | 4 GiB |
| Python | 3.13.5 |
| PyTorch | 2.10.0+cpu |
| Numeric format | FP32 |
| Compile | disabled |
| Quantization | none |
| Clock control | not fixed |

Performance numbers from this environment must not be treated as hardware-independent benchmarks because the CPU clock was not fixed.

## Model specification

- 2 Transformer blocks
- hidden width 96
- 4 attention heads
- feed-forward width 384
- context length 256
- 81-character vocabulary
- 256,224 trainable parameters
- random initialization
- no general Japanese-language pretraining

## Verification already performed

Round 4 included the following checks:

1. No world-hash overlap across train/dev/test world splits.
2. 21,600 initial oracle/evaluator assertions passed.
3. One resumed update matched a from-start rerun bit-for-bit under the same environment.
4. All 29 saved models were copied to a clean directory and produced identical token sequences on fixed reload checks.
5. No saved generation contained hidden PAD/BOS/UNK contamination that was merely suppressed by display decoding.
6. Evaluation corrections were retained as provenance rather than silently overwriting the earlier records.

These checks establish reproducibility within the recorded environment for the new tiny model. They do not reproduce the missing Round 3 main checkpoints.

## Artifact publication policy

Before publishing executable experiment artifacts, preserve:

- exact source revision,
- data hashes,
- split-generation seed and algorithm,
- model configuration,
- optimizer configuration and state when training resumption is claimed,
- complete package versions,
- raw generations,
- evaluator version,
- known evaluator limitations,
- hardware/thread settings,
- model or dataset upstream revision and license.

A checkpoint should be considered a valid handoff artifact only after loading from a clean location and passing a fixed-generation smoke test.

## Benchmark reporting

When reporting throughput or latency, include at minimum:

- hardware model,
- allocated cores/accelerators,
- thread count,
- numerical dtype,
- batch size,
- context length,
- generated token count,
- warm-up policy,
- whether model loading and evaluation are included,
- median and range across repeated runs.

Round 4 generation timings are diagnostic only because the clock was not fixed.

## What must not be inferred

The current artifacts do not justify claims that:

- the original Round 3 model was reproduced;
- the model has general Japanese-language competence;
- structural success implies human-rated naturalness;
- a detector miss implies deception or universal undetectability;
- three training seeds constitute a confidence interval;
- repeated samples from the same world are independent evaluation worlds.
