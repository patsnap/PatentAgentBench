# Claim Charting Bench

[中文](README.md) | [English](README.en.md)

Claim Charting Bench evaluates whether a model can break a target patent claim into technical features, align each feature with comparison evidence, judge whether the evidence discloses it, and identify word-level evidence. These tasks support novelty search, freedom-to-operate (FTO) review, and invalidity analysis. The dataset does not provide a complete legal conclusion.

## Data and scope

The public test set is [`data/test.jsonl`](data/test.jsonl): **30 records**, one per line, encoded as UTF-8 JSONL. All counts below were calculated from the 30 records in this release.

| Dimension | Public test set |
| --- | ---: |
| Records | 30 |
| Language | Chinese (`CN`): 15; English (`EN`): 15 |
| IPC section | A–F: 4 each; G and H: 3 each |
| Annotated feature items | 1,360 |
| Word-level alignments | 3,614 |

Each record contains a target claim, target and comparison passages, and expert annotations grouped by claim number. The text needed for the task is embedded in the JSONL file; no source-path access or additional asset download is required.

## Schema

| Field | Type | Description |
| --- | --- | --- |
| `data_id` | string | Record identifier |
| `lang` | string | `CN` or `EN` |
| `ipc_main` | string | IPC section letter |
| `claim` | string | Target claim text |
| `target_paragraph` | string | Target passage |
| `compare_paragraph` | string | Comparison evidence passage |
| `target_paragraph_path`, `compare_paragraph_path` | string | Source-system path metadata; the referenced standalone files are not included in this repository |
| `feature_res` | array of objects | Feature-level reference annotations |
| `is_public` | integer | **Record-level release flag**; `1` for every record in this file |

Every `feature_res` item contains `claim_num`, `target_feature`, `compare_feature`, `words`, and another `is_public`. Each `words` item pairs `target_word` with `compare_word`. **The feature-level `is_public` records whether the comparison evidence discloses that feature; it is not a release-permission flag.** Values in this file include `0`, `1`, `-1`, and `2`. Use only the defined `0` and `1` labels for binary disclosure evaluation; do not assume a meaning for the other codes. Some feature items also contain `differ`, `feature_num`, `feature_weight`, `reason`, and `score`; these fields are optional.

## Loading and evaluation

```python
import json
from pathlib import Path

path = Path("claim-charting-bench/data/test.jsonl")
with path.open(encoding="utf-8") as stream:
    samples = [json.loads(line) for line in stream]

assert len(samples) == 30
sample = samples[0]
model_input = {
    "claim": sample["claim"],
    "target_paragraph": sample["target_paragraph"],
    "compare_paragraph": sample["compare_paragraph"],
}
ground_truth = sample["feature_res"]
```

Keep `feature_res` as ground truth and exclude it from the evaluated model's input. Feature extraction, alignment, disclosure judgments, and word-level evidence can be assessed separately. State the matching rules, valid labels, and denominator when reporting metrics.

## Construction background and license

The supplied dataset introduction describes candidate selection, annotation by external patent-analysis experts, evidence calibration, and dataset review. This repository publishes only the 30 public records above. See the repository's root [LICENSE](../LICENSE) for terms of use.
