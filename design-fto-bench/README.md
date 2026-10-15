# Patsnap Design FTO Bench

English | [中文](README.zh.md)

Patsnap Design FTO Bench is a dataset for **design patent Freedom-To-Operate (FTO) image retrieval**. Each example contains a query image and a list of annotated target patent publication numbers. The `test` split contains 80 JSONL records in [`data/test.jsonl`](data/test.jsonl). The 80 PNG images are stored under [`asset/`](asset/) and referenced by `query_img_path`.

## Dataset Overview

| Property | Value |
|----------|-------|
| Test examples | 80 |
| Jurisdictions | CN: 40; EU: 40 |
| Query images | 80 PNGs under `asset/` |
| Target patent numbers | 98 entries across 80 examples |
| Picture type | Invalidation Proceedings |
| `query_pn` availability | Present in 53 examples; null in 27 |
| License | CC BY-NC 4.0 |

## Quick Start

```python
import json
from pathlib import Path

root = Path("design-fto-bench")
with (root / "data/test.jsonl").open(encoding="utf-8") as file:
    samples = [json.loads(line) for line in file]

sample = samples[0]
image_path = root / "asset" / sample["query_img_path"]
print(len(samples), image_path, sample["target_pns"])
```

The same dataset is available on [Hugging Face](https://huggingface.co/datasets/PatSnap/design-fto-bench) with images embedded in Parquet.

## Data Fields

| Field | Type | Description |
|-------|------|-------------|
| `query_pn` | nullable string | Query patent publication number, if available |
| `query_img_path` | string | Query PNG path relative to `asset/` |
| `target_pns` | list[string] | Annotated target patent publication numbers |
| `picture_type` | string | Picture type recorded for the example |
| `loc` | string | First-level Locarno classification |
| `sub_loc` | string | More specific Locarno classification |
| `jurisdiction` | list[string] | Jurisdiction codes (`["CN"]` or `["EU"]`) |
| `is_public` | int64 | Public availability flag; `1` in this split |

## Citation

```bibtex
@dataset{patsnap_design_fto_bench_2026,
  title  = {Patsnap Design FTO Bench},
  author = {Patsnap},
  year   = {2026},
  url    = {https://huggingface.co/datasets/PatSnap/design-fto-bench},
  note   = {Design patent FTO image retrieval dataset}
}
```

## License

Released under [CC BY-NC 4.0](https://creativecommons.org/licenses/by-nc/4.0/) for research and non-commercial evaluation.
