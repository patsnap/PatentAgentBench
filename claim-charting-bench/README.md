# Claim Charting Bench（技术特征对比）

[中文](README.md) | [English](README.en.md)

Claim Charting Bench 用于评测模型将目标专利权利要求拆解为技术特征，并与对比证据逐项对齐、判断公开性和定位词级证据的能力。这些能力可用于专利查新、自由实施（FTO）研判、无效分析等场景；本数据集本身不提供完整的法律结论。

## 数据文件与范围

公开测试集位于 [`data/test.jsonl`](data/test.jsonl)，采用 UTF-8 JSONL 格式，每行一条样本，共 **30 条**。它是本仓库发布的公开版本。随附的介绍 HTML 描述了一个 102 条样本的全量数据概况；其中的统计数字不代表这里发布的文件。以下数字均从当前 30 条 JSONL 计算得出。

| 维度 | 当前公开测试集 |
| --- | ---: |
| 样本 | 30 |
| 语言 | 中文（`CN`）15；英文（`EN`）15 |
| IPC 主类 | A–F 各 4；G、H 各 3 |
| 技术特征标注项 | 1,360 |
| 词级对应关系 | 3,614 |

每条样本包含目标权利要求、目标段落、对比段落，以及按权利要求编号归属的专家标注。输入文本已直接写入 JSONL，无须访问原始路径或下载附加文件。

## 字段说明

| 字段 | 类型 | 说明 |
| --- | --- | --- |
| `data_id` | 字符串 | 样本标识符 |
| `lang` | 字符串 | `CN` 或 `EN` |
| `ipc_main` | 字符串 | IPC 主类字母 |
| `claim` | 字符串 | 目标权利要求文本 |
| `target_paragraph` | 字符串 | 目标文本段落 |
| `compare_paragraph` | 字符串 | 对比证据段落 |
| `target_paragraph_path`、`compare_paragraph_path` | 字符串 | 来源系统中的路径元数据；仓库不提供这些路径对应的独立文件 |
| `feature_res` | 对象数组 | 技术特征及对比标注，作为参考答案使用 |
| `is_public` | 整数 | **样本级发布标记**；此文件中均为 `1` |

`feature_res` 中每项都有 `claim_num`、`target_feature`、`compare_feature`、`words` 和另一个 `is_public`。其中 `words` 是由 `target_word`、`compare_word` 组成的对应关系列表。**特征级 `is_public` 是“对比证据是否公开该技术特征”的标注，不是样本的发布许可标记。** 此文件中该标注出现 `0`、`1`、`-1`、`2`；公开性二分类评测只应使用定义明确的 `0` 和 `1`，不要擅自把其余值映射为是或否。部分特征还带有 `differ`、`feature_num`、`feature_weight`、`reason`、`score` 字段；这些字段并非每项都有。

## 读取与评测

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

将 `feature_res` 作为参考答案保存，不要在待测模型的输入中提供。可分别评估特征拆解、特征对齐、公开性判断及词级证据映射；报告指标时应说明匹配规则、有效标签和分母。

## 构建背景与许可

随附的数据集介绍说明：候选样本经过选池、外部专利分析专家标注、证据校准与评测集整理。本仓库仅发布上述 30 条公开记录。使用条款见仓库根目录的 [LICENSE](../LICENSE)。
