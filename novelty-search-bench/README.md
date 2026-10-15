---
license: cc-by-nc-4.0
language:
- en
- zh
tags:
- patent
- novelty-search
- prior-art
- patent-search
- information-retrieval
- evaluation
- benchmark
- cross-jurisdiction
task_categories:
- text-retrieval
pretty_name: Patsnap Novelty Search Bench
size_categories:
- n<1K
configs:
- config_name: default
  data_files:
  - split: test
    path: data/test.jsonl
dataset_info:
  features:
  - name: pn
    dtype: string
  - name: apdt
    dtype: int64
  - name: ipc
    sequence: string
  - name: ipc_main
    dtype: string
  - name: lang
    dtype: string
  - name: jurisdiction
    dtype: string
  - name: type
    dtype: string
  - name: pn_x
    sequence: string
  - name: pn_x_family
    sequence: string
  - name: pn_family_x
    sequence: string
  - name: pn_family_x_family
    sequence: string
  splits:
  - name: test
    num_examples: 80
---

# Patsnap Novelty Search Bench

This dataset contains 80 patent novelty search examples. Each example has a query patent publication number and publication numbers for examiner cited X type prior art. The examples include references cited for the query patent and for members of its patent family.

## Dataset overview

| Property | Test split |
| --- | ---: |
| Examples | 80 |
| Query jurisdictions | CN 39, US 26, EP 11, WO 4 |
| Query languages | CN 40, EN 40 |
| Example type | Family expanded |
| License | CC BY-NC 4.0 |

## Load the data

```python
from datasets import load_dataset

test = load_dataset("PatSnap/novelty-search-bench", split="test")
print(len(test))  # 80
print(test[0]["pn"], test[0]["pn_x"])
```

| Field | Type | Meaning |
| --- | --- | --- |
| `pn` | string | Query patent publication number |
| `apdt` | int64 | Application date, `YYYYMMDD` |
| `ipc` | list[string] | IPC codes |
| `ipc_main` | string | Main IPC section |
| `lang` | string | Query language, `CN` or `EN` |
| `jurisdiction` | string | Query patent jurisdiction |
| `type` | string | Example type, `family` in this split |
| `pn_x` | list[string] | X type references cited for the query patent |
| `pn_x_family` | list[string] | Publication numbers in the families of `pn_x` references |
| `pn_family_x` | list[string] | X type references cited for query family members |
| `pn_family_x_family` | list[string] | Publication numbers in the families of `pn_family_x` references |

The file is JSONL with one example per line. `pn_x` and `pn_family_x` contain cited publication numbers; the corresponding `*_family` fields contain publication numbers for patents in their families. Query specification text is not included.

## Scope and limitations

The cited references are not an exhaustive list of relevant prior art. The dataset covers the query jurisdictions and languages listed above.

## Citation

```bibtex
@article{zhang2025novelty,
  title   = {Research on Evaluation Methods for Patent Novelty Search Systems and Empirical Analysis},
  author  = {Zhang, Shu and Zhang, LiSha and Duan, Kai and Sun, XinKai},
  journal = {arXiv preprint arXiv:2508.17782},
  year    = {2025},
  url     = {https://arxiv.org/abs/2508.17782}
}

@dataset{patsnap_novelty_search_bench_2026,
  title  = {Patsnap Novelty Search Bench},
  author = {Patsnap},
  year   = {2026},
  url    = {https://huggingface.co/datasets/PatSnap/novelty-search-bench}
}
```

## License

Released under [CC BY-NC 4.0](https://creativecommons.org/licenses/by-nc/4.0/) for research and non-commercial evaluation.
