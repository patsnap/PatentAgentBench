<div align="center">

# FTO Patent Search Benchmark

A public benchmark for invention and utility-model Freedom to Operate (FTO)
search. Each case uses a technical description as the query and known risk
patents curated from real litigation scenarios as the ground truth.

[简体中文](./README.md) · **English**

[Download the public dataset](./data/test.jsonl)

</div>

## Overview

This dataset evaluates whether AI agents, retrieval models, and traditional
patent-search systems can discover patents that present potential infringement
risk. Each sample contains a technical description, a target jurisdiction, a
main IPC section, and known risk-patent application numbers with jurisdiction
and language metadata.

The public release contains **60 litigation-derived benchmark cases** across CN,
US, and EP, covering all eight IPC sections from A to H. It follows a
known-risk validation approach: given the technical text and target jurisdiction,
the evaluation checks whether the system's ranked results cover the annotated
risk-patent application numbers.

## What This Dataset Helps Evaluate

| Capability | Description |
| --- | --- |
| FTO risk-patent retrieval | Given a technical description and target jurisdiction, can the system retrieve known risk patents? |
| Ranking and recall | Measure whether known risk patents are found and how completely they are covered at Top K or in the full result list |
| Cross-system comparison | Compare models, harnesses, agents, or retrieval systems on the same cases and metrics |

## Dataset at a Glance

| Metric | Value |
| --- | ---: |
| Public cases | 60 |
| Jurisdictions | 3 (CN, US, and EP) |
| IPC sections | 8 (A–H) |
| Risk-patent entries | 177 |
| Unique risk-patent application numbers | 81 |
| Average risk-patent entries per case | 2.95 |
| Cases with multiple risk-patent entries | 59 (98.3%) |

### Jurisdiction Distribution

| Jurisdiction | Cases | Share |
| --- | ---: | ---: |
| CN | 30 | 50% |
| US | 15 | 25% |
| EP | 15 | 25% |

### IPC Section Distribution

| IPC section | A | B | C | D | E | F | G | H |
| --- | ---: | ---: | ---: | ---: | ---: | ---: | ---: | ---: |
| Cases | 14 | 9 | 4 | 2 | 4 | 8 | 8 | 11 |

### Risk-Patent Entries per Case

| Number of entries | 1 | 2 | 3 | 4 | 5+ |
| --- | ---: | ---: | ---: | ---: | ---: |
| Cases | 1 | 35 | 11 | 8 | 5 |

One application may have multiple publication records or language versions, so
the number of risk-patent entries in a case can exceed the number of ground-truth
application numbers after deduplication. For evaluation, deduplicate non-empty
`apno` values within each case.

## Dataset Construction

1. **Scenario selection:** Publicly releasable FTO search scenarios were selected
   from real patent-litigation materials with coverage across target
   jurisdictions and IPC sections.
2. **Input preparation:** Technical solutions from the scenarios were converted
   into technical descriptions suitable for retrieval. The public data contains
   30 Chinese and 30 English query texts.
3. **Ground-truth preparation:** Annotators reviewed known risk patents and
   normalized their application numbers, jurisdictions, and available language
   metadata.
4. **Quality control:** Only cases that passed high-risk evidence review and
   retained active infringement relevance were included.

## Files

| File | Description |
| --- | --- |
| [`data/test.jsonl`](./data/test.jsonl) | 60 public benchmark cases, one JSON object per line |
| [`README.md`](./README.md) | Chinese documentation |
| [`README_EN.md`](./README_EN.md) | English documentation |

The dataset is UTF-8 encoded. Every physical line is a complete JSON object, so
the file can be streamed without loading the entire dataset into memory.

## Schema

| Field | Type | Description |
| --- | --- | --- |
| `data_id` | string | Stable case identifier (UUID) |
| `technical_text` | string | Technical description used as the retrieval query; 30 Chinese and 30 English texts |
| `related_patent_detail` | array[object] | Ground-truth list of known risk patents |
| `ipc_main` | string | Main IPC section, from A to H |
| `country` | string | Target jurisdiction, currently CN, US, or EP |

### Nested Fields in `related_patent_detail`

| Field | Type | Description |
| --- | --- | --- |
| `country` | string | Jurisdiction code of the risk patent |
| `lang` | string \| null | Publication-language code; `null` when unavailable |
| `apno` | string | Risk-patent application number used for evaluation matching |

All 177 public risk-patent entries have non-empty `country` and `apno` values.
Ten historical entries have a `null` `lang`; missing language metadata does not
affect application-number-based retrieval evaluation.

## Example Record

The following example shows the structure only. The identifiers, application
number, and technical text are placeholders.

```json
{
  "technical_text": "A device comprising ...",
  "related_patent_detail": [
    {
      "country": "US",
      "lang": "EN",
      "apno": "US00000000"
    }
  ],
  "data_id": "00000000-0000-0000-0000-000000000000",
  "ipc_main": "G",
  "country": "US"
}
```

## Quick Start

```python
import json
from pathlib import Path

dataset_path = Path("data/test.jsonl")

with dataset_path.open(encoding="utf-8") as file:
    records = [json.loads(line) for line in file if line.strip()]

assert len(records) == 60

first_case = records[0]
query = {
    "technical_text": first_case["technical_text"],
    "country": first_case["country"],
}
ground_truth = {
    patent["apno"]
    for patent in first_case["related_patent_detail"]
    if patent.get("apno")
}

print(query)
print(ground_truth)
```

## Recommended Evaluation

For case `q`, define:

- the ground-truth set `Gq` as normalized and deduplicated values from
  `related_patent_detail[*].apno`;
- the system result list `Rq` as a relevance-ranked list converted to application
  numbers and deduplicated.

Compute the following metrics at Top 10, Top 100, and over the full returned list:

| Metric | Per-case definition |
| --- | --- |
| Hit@K | 1 if `Rq[:K]` contains at least one member of `Gq`; otherwise 0 |
| Recall@K | Number of ground-truth application numbers matched in `Rq[:K]` divided by the size of `Gq` |

For aggregation, use a macro average across all 60 cases. A failed execution or
an empty result list receives 0 for the corresponding metrics. Hit is an
any-match binary metric and must not be interpreted as result-list precision.

Systems may return application or publication numbers. Before computing metrics,
normalize outputs to the canonical application-number (`apno`) space and
deduplicate within each case. The target jurisdiction is part of the query and
should not be silently expanded before comparing systems.

## Limitations and Responsible Use

- The dataset evaluates risk-patent retrieval, not infringement mapping, claim
  construction, or the quality of a final legal opinion.
- The ground truth contains known annotated risk patents for each scenario and
  does not represent every patent that could potentially be relevant.
- Normalize patent application numbers before matching, and do not award
  duplicate credit for multiple publications of the same application.
- Neither the dataset nor model output is a substitute for professional legal
  judgment by patent counsel or other qualified practitioners.
