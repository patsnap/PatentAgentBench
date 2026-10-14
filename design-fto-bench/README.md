---
license: cc-by-nc-4.0
language:
- en
- zh
tags:
- patent
- design-patent
- freedom-to-operate
- fto
- image-retrieval
- cross-modal
- visual-similarity
- information-retrieval
- evaluation
- benchmark
task_categories:
- image-to-image
- image-classification
pretty_name: PatSnap Design FTO Bench
size_categories:
- n<1K
configs:
- config_name: default
  data_files:
  - split: test
    path: data/test.parquet
dataset_info:
  features:
  - name: query_pn
    dtype: string
  - name: query_img_path
    dtype: image
  - name: target_pns
    sequence: string
  - name: picture_type
    dtype: string
  - name: loc
    dtype: string
  - name: sub_loc
    dtype: string
  - name: jurisdiction
    sequence: string
  - name: is_public
    dtype: int64
  splits:
  - name: test
    num_examples: 80
---

# PatSnap Design FTO Bench

PatSnap Design FTO Bench is a dataset for **design patent Freedom-To-Operate (FTO) image retrieval**. Each example contains a query image and a list of annotated target patent publication numbers. The dataset has one `test` split with 80 examples. Query images are embedded in `data/test.parquet`.

## Dataset Overview

| Property | Value |
|----------|-------|
| Test examples | 80 |
| Jurisdictions | CN: 40; EU: 40 |
| Query images | 80 embedded PNGs |
| Target patent numbers | 98 entries across 80 examples |
| Picture type | Invalidation Proceedings |
| `query_pn` availability | Present in 53 examples; null in 27 |
| License | CC BY-NC 4.0 |

## Quick Start

```python
from datasets import load_dataset

ds = load_dataset("PatSnap/design-fto-bench", split="test")
print(len(ds))  # 80

sample = ds[0]
image = sample["query_img_path"]  # PIL image
print(image.size, sample["target_pns"], sample["jurisdiction"])
```

## Data Fields

| Field | Type | Description |
|-------|------|-------------|
| `query_pn` | nullable string | Query patent publication number, if available |
| `query_img_path` | image | Query PNG embedded in the Parquet file |
| `target_pns` | list[string] | Annotated target patent publication numbers |
| `picture_type` | string | Picture type recorded for the example |
| `loc` | string | First-level Locarno classification |
| `sub_loc` | string | More specific Locarno classification |
| `jurisdiction` | list[string] | Jurisdiction codes (`["CN"]` or `["EU"]`) |
| `is_public` | int64 | Public availability flag; `1` in this split |

## Citation

```bibtex
@dataset{patsnap_design_fto_bench_2026,
  title  = {PatSnap Design FTO Bench},
  author = {PatSnap},
  year   = {2026},
  url    = {https://huggingface.co/datasets/PatSnap/design-fto-bench},
  note   = {Design patent FTO image retrieval dataset}
}
```

## License

Released under [CC BY-NC 4.0](https://creativecommons.org/licenses/by-nc/4.0/) for research and non-commercial evaluation.
