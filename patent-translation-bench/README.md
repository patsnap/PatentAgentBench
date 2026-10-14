# PatSnap Patent Translation Bench

This benchmark contains 881 patent text examples for evaluating Chinese and
English translation. It covers word, character, sentence, paragraph, and
document text, with annotations for translation accuracy, terminology,
patent writing conventions, hallucination, and omission.

The dataset is distributed as one JSON object per line in
[`data/test.jsonl`](data/test.jsonl). The same 881 examples are available in
[Parquet format on Hugging Face](https://huggingface.co/datasets/PatSnap/patent-translation-bench).

## Dataset overview

| Property | Value |
|----------|-------|
| Examples | 881 |
| Rows with Chinese and English text | 854 |
| Rows with `content_en` equal to `Zero` | 27 |
| IPC coverage | A–H, plus records without an IPC label |
| License | [CC BY-NC 4.0](LICENSE) |

For evaluation requiring a bilingual reference, use the 854 rows whose
`content_en` is not `Zero`. The remaining rows retain their Chinese text and
annotations.

## Fields

| Field | Type | Description |
|-------|------|-------------|
| `pn` | string | Patent publication number; `Zero` if unavailable |
| `ipc` | string | IPC section A–H; `Zero` if unavailable |
| `content_cn` | string | Chinese text |
| `content_en` | string | English text; `Zero` if unavailable |
| `label_1` | string | Text granularity: `word`, `character`, `sentence`, `paragraph`, or `document` |
| `label_2` | string | Evaluation dimension |
| `label_3` | string or null | Text context, such as `abstract` or `claims` |
| `special_cn` | string or null | Chinese term or special text annotation |
| `special_en` | string or null | English term or special text annotation |
| `domain` | string or null | Domain annotation |

`Zero` is a value in the source data and is distinct from JSON `null`.

## Load the data

```python
import json

with open("data/test.jsonl", encoding="utf-8") as source:
    examples = [json.loads(line) for line in source]

print(len(examples))  # 881
```

You can also load the named test split with Hugging Face Datasets:

```python
from datasets import load_dataset

test = load_dataset(
    "json",
    data_files={"test": "data/test.jsonl"},
    split="test",
)
print(len(test))  # 881
```

## Citation

```bibtex
@dataset{patsnap_patent_translation_bench_2026,
  title={PatSnap Patent Translation Bench},
  author={PatSnap},
  year={2026},
  url={https://huggingface.co/datasets/PatSnap/patent-translation-bench}
}
```

## License

The dataset is released under [CC BY-NC 4.0](LICENSE) for research and
non-commercial evaluation.
