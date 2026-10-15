<div align="center">

# Patent Invalidity Search Benchmark

A public benchmark for patent-invalidity evidence retrieval. Each case uses patent
claims as the query and evidence references from real invalidation proceedings as
the ground truth.

[简体中文](./README.md) · **English**

[Download the public dataset](./invalid_search_benchmark_20260917_public.jsonl)

</div>

## Overview

This dataset evaluates whether AI agents, retrieval models, and traditional patent
search systems can find prior-art references used as invalidity evidence. Each
sample contains the claims to be analyzed, the final case outcome, the evidence
references, and structured annotations for evidence roles and legal grounds.

The public release contains **80 benchmark cases**, balanced across CN and US with
40 cases each and covering all eight IPC sections from A to H. It supports both
any-match evaluation and coverage-oriented evaluation over core and complete
evidence sets.

## What This Dataset Helps Evaluate

| Capability | Description |
| --- | --- |
| Invalidity evidence retrieval | Given patent claims, can the system retrieve references that were used as invalidity evidence in real cases? |
| Core and complete recall | Evaluate `primary_reference` documents separately from all evidence references to distinguish finding a key document from finding the complete set |
| Cross-system comparison | Compare models, harnesses, agents, or retrieval systems on the same cases and metrics |

## Dataset at a Glance

| Metric | Value |
| --- | ---: |
| Public cases | 80 |
| Jurisdictions | 2 (CN and US) |
| IPC sections | 8 (A–H) |
| Annotated evidence-reference entries | 212 |
| Average evidence references per case | 2.65 |
| Multi-reference cases (at least 2) | 66 (82.5%) |

### Jurisdiction Distribution

| Jurisdiction | Cases | Share |
| --- | ---: | ---: |
| CN | 40 | 50% |
| US | 40 | 50% |

### IPC Section Distribution

| IPC section | A | B | C | D | E | F | G | H |
| --- | ---: | ---: | ---: | ---: | ---: | ---: | ---: | ---: |
| Cases | 11 | 11 | 11 | 6 | 11 | 10 | 10 | 10 |

### Evidence References per Case

| Number of references | 1 | 2 | 3 | 4 | 5+ |
| --- | ---: | ---: | ---: | ---: | ---: |
| Cases | 14 | 32 | 16 | 12 | 6 |

### Final Outcome Distribution

| Final outcome | Cases | Share |
| --- | ---: | ---: |
| All claims invalid | 67 | 83.75% |
| Partially invalid | 9 | 11.25% |
| All claims invalid after amendment | 4 | 5.00% |

## Dataset Construction

1. **Case selection:** Publicly releasable cases were selected from real patent
   invalidation proceedings while balancing CN, US, and IPC-section coverage.
2. **Ground-truth preparation:** Evidence references used in the proceedings were
   organized into a complete ground-truth list, with roles such as primary and
   secondary references retained.
3. **Legal-ground annotation:** References were grouped by legal grounds such as
   novelty and inventive step to support more detailed retrieval analysis.

## Files

| File | Description |
| --- | --- |
| [`invalid_search_benchmark_20260917_public.jsonl`](./invalid_search_benchmark_20260917_public.jsonl) | 80 public benchmark cases, one JSON object per line |
| [`README.md`](./README.md) | Chinese documentation |
| [`README_EN.md`](./README_EN.md) | English documentation |

The dataset is UTF-8 encoded. Every physical line is a complete JSON object, so the
file can be processed as a stream without loading the entire dataset into memory.

## Schema

| Field | Type | Description |
| --- | --- | --- |
| `data_id` | integer | Stable record identifier; values are not guaranteed to be contiguous |
| `ipc_main` | string | Main IPC section, from A to H |
| `country` | string | Jurisdiction code, currently CN or US |
| `apdt` | integer | Application date encoded as `YYYYMMDD` |
| `final_outcome` | string | Final outcome of the invalidation case |
| `patent_info` | object | Challenged claims and claims ultimately found unpatentable |
| `invalid_evdience_patent` | array[string] | Complete evidence-reference ground truth |
| `invalid_evdience_patent_role` | object | Evidence references grouped by primary, secondary, or other roles |
| `invalid_evdience_patent_legal_basis` | object | Evidence references grouped by novelty, inventive step, or other legal grounds |
| `claims` | string | Patent claims used as the retrieval input |
| `is_benchmark` | integer | Benchmark flag; always 1 in the public file |
| `is_delete` | integer | Soft-deletion flag; always 0 in the public file |
| `create_ts` | string | Record creation timestamp |
| `update_ts` | string | Record update timestamp |
| `is_public` | integer | Public-release flag; always 1 in the public file |

> [!NOTE]
> The spelling `evdience` is retained from the source data contract for downstream
> compatibility. Consumers should use the field names as published rather than
> silently renaming them to `evidence`.

### Important Nested Fields

- `patent_info.challenged_claims`: IDs of the challenged claims.
- `patent_info.final_unpatentable_claims`: IDs of the claims ultimately found
  unpatentable.
- `invalid_evdience_patent_role.primary_reference`: core-reference ground truth.
- `invalid_evdience_patent_role.secondary_reference`: secondary evidence
  references.
- `invalid_evdience_patent_legal_basis.新颖性`: references used for novelty.
- `invalid_evdience_patent_legal_basis.创造性`: references used for inventive step.

## Example Record

The following example shows the structure only. Patent numbers and claim text are
placeholders.

```json
{
  "data_id": 4,
  "ipc_main": "E",
  "country": "CN",
  "apdt": 20101225,
  "final_outcome": "全部无效",
  "patent_info": {
    "challenged_claims": [1, 2, 3],
    "final_unpatentable_claims": [1, 2, 3]
  },
  "invalid_evdience_patent": ["PATENT_A", "PATENT_B"],
  "invalid_evdience_patent_role": {
    "primary_reference": ["PATENT_A"],
    "secondary_reference": ["PATENT_B"]
  },
  "invalid_evdience_patent_legal_basis": {
    "新颖性": ["PATENT_A"],
    "创造性": ["PATENT_A", "PATENT_B"]
  },
  "claims": "Claim text...",
  "is_benchmark": 1,
  "is_delete": 0,
  "create_ts": "2026-09-17T13:38:27",
  "update_ts": "2026-09-17T15:54:06",
  "is_public": 1
}
```

## Quick Start

```python
import json
from pathlib import Path

dataset_path = Path("invalid_search_benchmark_20260917_public.jsonl")

with dataset_path.open(encoding="utf-8") as file:
    records = [json.loads(line) for line in file if line.strip()]

assert len(records) == 80
assert all(record["is_public"] == 1 for record in records)

first_case = records[0]
query = first_case["claims"]
all_ground_truth = set(first_case["invalid_evdience_patent"])
core_ground_truth = set(
    first_case["invalid_evdience_patent_role"].get("primary_reference", [])
)

print(query[:100])
print(all_ground_truth)
print(core_ground_truth)
```

## Recommended Evaluation

For case `q`, define:

- the core-reference set `Cq` as
  `invalid_evdience_patent_role.primary_reference`;
- the complete-reference set `Aq` as `invalid_evdience_patent`;
- the system's ranked, deduplicated result list as `Rq`.

Compute the following metrics for both core and complete references at Top 5 and
over the full returned list:

| Metric | Per-case definition |
| --- | --- |
| Hit@K | 1 if the result list contains at least one ground-truth reference; otherwise 0 |
| Recall@K | Number of matched ground-truth references divided by the number of ground-truth references |

This produces eight metrics: core/complete references × Hit/Recall × Top 5/full
list. Hit is an any-match binary metric and must not be interpreted as result-list
precision.

For aggregation, use a macro average over applicable cases. A failed execution or
an empty result list receives 0 for every applicable metric. When a case has no
core-reference ground truth, its core-reference metrics are N/A and are excluded
from the core-metric denominator.

## Limitations and Responsible Use

- The dataset evaluates evidence retrieval, not final legal-conclusion generation,
  claim construction, or the quality of legal advice.
- The annotated evidence comes from labeled cases but does not represent every
  possible prior-art reference that might be relevant to a claim.
- Normalize patent publication numbers and compare results in the same identifier
  space before computing metrics.
- Neither the dataset nor model output is a substitute for professional legal
  judgment by patent counsel or other qualified practitioners.
