<div align="center">

# 专利防侵权检索评测数据集

面向发明与实用新型专利防侵权（Freedom to Operate, FTO）检索系统的公开 Benchmark，
以技术方案文本为检索输入，以真实诉讼场景中整理的已知风险专利为标准答案。

**简体中文** · [English](./README_EN.md)

[下载公开数据集](./data/test.jsonl)

</div>

## 数据集简介

本数据集用于评估 AI Agent、检索模型和传统专利检索系统发现潜在侵权风险专利的能力。
每条样本包含待检索的技术方案文本、目标国家或地区、IPC 主分类部，以及已知风险专利的
申请号、国家或地区和语言信息。

公开版本包含 **60 条**来自诉讼场景的 Benchmark 样本，覆盖 CN、US 和 EP，以及 IPC
的 A–H 八个部。评测采用“用已知风险验证发现能力”的方法：给定技术文本和目标国家或
地区，检查系统返回的排序结果能否覆盖已标注的风险专利申请号。

## 这份数据可以用来评估什么

| 能力 | 说明 |
| --- | --- |
| FTO 风险专利检索 | 给定技术方案文本和目标国家或地区，系统能否找到已知风险专利 |
| 排序与召回 | 在 Top K 或完整返回列表中，衡量已知风险专利的命中和覆盖程度 |
| 系统横向对比 | 在同一份数据和同一套指标下比较不同模型、Harness、Agent 或检索系统 |

## 数据概览

| 指标 | 数值 |
| --- | ---: |
| 公开样本 | 60 |
| 国家或地区 | 3（CN、US、EP） |
| IPC 部 | 8（A–H） |
| 风险专利记录条目 | 177 |
| 去重后的风险专利申请号 | 81 |
| 平均每条样本的风险专利记录数 | 2.95 |
| 含多个风险专利记录的样本 | 59（98.3%） |

### 国家或地区分布

| 国家或地区 | 样本数 | 占比 |
| --- | ---: | ---: |
| CN | 30 | 50% |
| US | 15 | 25% |
| EP | 15 | 25% |

### IPC 部分布

| IPC 部 | A | B | C | D | E | F | G | H |
| --- | ---: | ---: | ---: | ---: | ---: | ---: | ---: | ---: |
| 样本数 | 14 | 9 | 4 | 2 | 4 | 8 | 8 | 11 |

### 每条样本的风险专利记录数

| 记录数 | 1 | 2 | 3 | 4 | 5+ |
| --- | ---: | ---: | ---: | ---: | ---: |
| 样本数 | 1 | 35 | 11 | 8 | 5 |

同一申请可能对应多个公开文本或语言版本，因此一条样本中的风险专利记录数可能高于按
`apno` 去重后的标准答案数量。评测时建议在单个样本内按非空 `apno` 去重。

## 数据集构建方法

1. **场景选择**：从真实专利诉讼材料中筛选可公开的 FTO 检索场景，并覆盖不同目标
   国家或地区与 IPC 部。
2. **输入整理**：将场景中的技术方案整理为可直接用于检索的技术文本；当前公开数据包含中文 30 条、英文 30 条。
3. **标准答案整理**：由标注人员复核已知风险专利，并统一补充申请号、国家或地区和
   可用的语言信息。
4. **质量控制**：仅纳入通过高风险证据质检且风险专利仍具备有效侵权风险的样本。

## 文件

| 文件 | 说明 |
| --- | --- |
| [`data/test.jsonl`](./data/test.jsonl) | 60 条公开 Benchmark 数据，一行一个 JSON 对象 |
| [`README.md`](./README.md) | 中文介绍 |
| [`README_EN.md`](./README_EN.md) | English documentation |

数据文件使用 UTF-8 编码。每个物理行都是一个完整 JSON 对象，可以流式读取，无需
一次性加载全部数据。

## 数据结构

| 字段 | 类型 | 说明 |
| --- | --- | --- |
| `data_id` | string | 稳定的样本标识（UUID） |
| `technical_text` | string | 用作检索输入的技术方案文本；中文 30 条、英文 30 条 |
| `related_patent_detail` | array[object] | 已知风险专利标准答案列表 |
| `ipc_main` | string | IPC 主分类部，取值为 A–H |
| `country` | string | 检索目标国家或地区，当前为 CN、US 或 EP |

### `related_patent_detail` 嵌套字段

| 字段 | 类型 | 说明 |
| --- | --- | --- |
| `country` | string | 风险专利的国家或地区代码 |
| `lang` | string \| null | 公开文本语言代码；缺失时为 `null` |
| `apno` | string | 风险专利申请号，用作评测匹配标识 |

公开数据中的 177 条风险专利记录均具有非空 `country` 和 `apno`；其中 10 条历史记录的
`lang` 为 `null`。语言缺失不影响基于申请号的检索评测。

## 记录示例

下面的示例仅展示结构，标识符、申请号和技术文本均为占位内容：

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

## 快速开始

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

## 推荐评测方式

对于样本 `q`，定义：

- 标准答案集合 `Gq`：对 `related_patent_detail[*].apno` 规范化并去重后的集合；
- 系统结果列表 `Rq`：按相关性排序、转换到申请号维度并去重后的列表。

建议在 Top 10、Top 100 和完整返回列表上计算：

| 指标 | 单样本定义 |
| --- | --- |
| Hit@K | `Rq[:K]` 与 `Gq` 存在至少一个交集时为 1，否则为 0 |
| Recall@K | `Rq[:K]` 命中的标准答案数量 ÷ `Gq` 中的标准答案数量 |

聚合时建议对 60 条样本取宏平均。系统执行失败或返回空列表时，相应指标记为 0。
Hit 是“至少命中一个标准答案”的二值指标，不是结果列表的 Precision。

不同系统可能返回申请号或公开号。计算指标前，应先将结果统一到规范化申请号 `apno`
空间，并在单个样本内去重。国家或地区也是检索输入的一部分，不应跨国家或地区扩大
检索范围后直接比较。

## 使用限制

- 数据集评估的是风险专利检索能力，不直接评估侵权比对、权利要求解释或最终法律意见。
- 标准答案是各场景中已知并经标注的风险专利，不代表所有可能相关的风险专利。
- 专利申请号应经过格式规范化后再进行匹配，且同一申请的多个公开文本不能重复计分。
- 数据集及模型输出不能替代专利律师或其他专业人员的法律判断。
