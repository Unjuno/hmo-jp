# Asset and licensing policy

## Repository-authored material

Project-authored source code and documentation are intended to be distributed under the repository's MIT License unless a file explicitly states otherwise.

## Third-party material

A repository-level MIT License does **not** relicense third-party assets. Models, datasets, tokenizers, corpora, papers, and external code remain subject to their original terms.

Before committing or releasing a third-party-derived artifact, record:

- upstream project/model/dataset name,
- canonical source,
- exact revision or version,
- license identifier and license text/location,
- whether redistribution of weights/data is permitted,
- modifications made locally,
- checksum of the exact artifact used.

If redistribution rights are unclear, do not commit the asset. Prefer a download/setup script plus an immutable revision and checksum.

## Large experiment artifacts

The following should not be committed to ordinary Git history by default:

- model checkpoints,
- optimizer states,
- large generation logs,
- compressed handoff archives,
- raw third-party model files,
- caches and temporary experiment outputs.

Use release assets, an artifact store, or another versioned storage mechanism after licensing and provenance review.

## Current Round 4 note

The Round 4 tiny model is project-generated, but its complete public release should still be accompanied by configuration, code revision, data-generation procedure, checksums, and the documented limitations in `docs/results-round4.md`.

The original rinna Japanese GPT-2 asset referenced in earlier work is a third-party model and must retain its upstream license/provenance. It should not be represented as covered by HMO-JP's repository MIT License.
