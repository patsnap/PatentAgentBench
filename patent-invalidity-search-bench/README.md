<div align="center">

# 专利无效检索评测数据集

面向专利无效证据检索系统的公开 Benchmark，以权利要求文本为检索输入，
以真实无效案件中使用的证据文献为标准答案。

**简体中文** · [English](./README_EN.md)

[下载公开数据集](./data/test.jsonl)

</div>

## 数据集简介

本数据集用于评估 AI Agent、检索模型和传统专利检索系统发现无效证据文献的能力。
每条样本包含待分析的权利要求、案件最终结果、证据文献，以及证据角色和法律依据等
结构化标注。

公开版本包含 **80 条** Benchmark 样本，CN 与 US 各 40 条，覆盖 IPC 的 A–H
八个部。它既可以衡量系统能否找到至少一篇有效证据，也可以衡量系统对核心文献和
全部证据文献的覆盖程度。

## 这份数据可以用来评估什么

| 能力 | 说明 |
| --- | --- |
| 无效证据检索 | 给定权利要求文本，系统能否从专利文献中找到真实案件使用的无效证据 |
| 核心与完整召回 | 分别评估主要证据 `primary_reference` 和全部证据文献，识别“找到关键文献”和“尽可能找全”的差异 |
| 系统横向对比 | 在同一份数据和同一套指标下比较不同模型、Harness、Agent 或检索系统 |

## 数据概览

| 指标 | 数值 |
| --- | ---: |
| 公开样本 | 80 |
| 国家或地区 | 2（CN、US） |
| IPC 部 | 8（A–H） |
| 标注证据文献条目 | 212 |
| 平均每条样本的证据文献数 | 2.65 |
| 多证据样本（至少 2 篇） | 66（82.5%） |

### 国家分布

| 国家或地区 | 样本数 | 占比 |
| --- | ---: | ---: |
| CN | 40 | 50% |
| US | 40 | 50% |

### IPC 部分布

| IPC 部 | A | B | C | D | E | F | G | H |
| --- | ---: | ---: | ---: | ---: | ---: | ---: | ---: | ---: |
| 样本数 | 11 | 11 | 11 | 6 | 11 | 10 | 10 | 10 |

### 每条样本的证据文献数

| 证据文献数 | 1 | 2 | 3 | 4 | 5+ |
| --- | ---: | ---: | ---: | ---: | ---: |
| 样本数 | 14 | 32 | 16 | 12 | 6 |

### 最终结果分布

| 最终结果 | 样本数 | 占比 |
| --- | ---: | ---: |
| 全部无效 | 67 | 83.75% |
| 部分无效 | 9 | 11.25% |
| 权利要求修改后全部无效 | 4 | 5.00% |

## 数据集构建方法

1. **样本选择**：从真实专利无效案件中选取可公开样本，并平衡 CN、US 与 IPC
   部分布。
2. **标准答案整理**：将案件中使用的证据文献整理为完整标准答案列表，同时保留
   主要证据和次要证据等角色信息。
3. **法律依据标注**：按新颖性、创造性等法律依据组织证据文献，支持更细粒度的
   检索结果分析。

## 文件

| 文件 | 说明 |
| --- | --- |
| [`data/test.jsonl`](./data/test.jsonl) | 80 条公开 Benchmark 数据，一行一个 JSON 对象 |
| [`README.md`](./README.md) | 中文介绍 |
| [`README_EN.md`](./README_EN.md) | English documentation |

数据文件使用 UTF-8 编码。每个物理行都是一个完整 JSON 对象，可以流式读取，无需
一次性加载全部数据。

## 数据结构

| 字段 | 类型 | 说明 |
| --- | --- | --- |
| `data_id` | integer | 稳定的数据记录标识，不保证连续 |
| `ipc_main` | string | IPC 主分类部，取值为 A–H |
| `country` | string | 国家或地区代码，当前为 CN 或 US |
| `apdt` | integer | 申请日，格式为 `YYYYMMDD` |
| `final_outcome` | string | 无效案件最终结果 |
| `patent_info` | object | 被挑战权利要求和最终无效权利要求 |
| `invalid_evdience_patent` | array[string] | 全部证据文献标准答案 |
| `invalid_evdience_patent_role` | object | 按主要、次要等角色组织的证据文献 |
| `invalid_evdience_patent_legal_basis` | object | 按新颖性、创造性等依据组织的证据文献 |
| `claims` | string | 用作检索输入的权利要求文本 |
| `is_benchmark` | integer | Benchmark 标记；公开文件中均为 1 |
| `is_delete` | integer | 软删除标记；公开文件中均为 0 |
| `create_ts` | string | 数据创建时间 |
| `update_ts` | string | 数据更新时间 |
| `is_public` | integer | 公开标记；公开文件中均为 1 |

> [!NOTE]
> 字段名中的 `evdience` 沿用原始数据契约的拼写，以保持上下游兼容。读取数据时请
> 使用原字段名，不要直接改为 `evidence`。

### 关键嵌套字段

- `patent_info.challenged_claims`：被挑战的权利要求编号。
- `patent_info.final_unpatentable_claims`：最终被认定无效的权利要求编号。
- `invalid_evdience_patent_role.primary_reference`：核心文献标准答案。
- `invalid_evdience_patent_role.secondary_reference`：次要证据文献。
- `invalid_evdience_patent_legal_basis.新颖性`：用于新颖性判断的文献。
- `invalid_evdience_patent_legal_basis.创造性`：用于创造性判断的文献。

## 记录示例

下面的示例仅展示结构，文献号和权利要求文本使用占位内容：

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
  "claims": "权利要求文本……",
  "is_benchmark": 1,
  "is_delete": 0,
  "create_ts": "2026-09-17T13:38:27",
  "update_ts": "2026-09-17T15:54:06",
  "is_public": 1
}
```

## 快速开始

```python
import json
from pathlib import Path

dataset_path = Path("data/test.jsonl")

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

## 推荐评测方式

对于样本 `q`，定义：

- 核心文献集合 `Cq`：`invalid_evdience_patent_role.primary_reference`；
- 全部文献集合 `Aq`：`invalid_evdience_patent`；
- 系统按相关性排序的去重结果列表 `Rq`。

建议分别在 Top 5 和全量返回列表上，对核心文献与全部文献计算以下指标：

| 指标 | 单样本定义 |
| --- | --- |
| Hit@K | 结果列表与标准答案存在至少一篇交集时为 1，否则为 0 |
| Recall@K | 命中的标准答案数量 ÷ 标准答案总数 |

因此可以得到 8 个指标：核心/全部文献 × Hit/Recall × Top 5/全量列表。
Hit 是“任意一个标准答案被命中”的二值指标，不是结果列表的 Precision。

聚合时建议对适用样本取宏平均。系统执行失败或返回空列表时，各适用指标记为 0；
核心文献集合为空时，核心文献指标记为 N/A，不进入核心指标的平均分母。

## 使用限制

- 数据集用于评测证据检索能力，不直接评估最终法律结论生成、权利要求解释或法律意见质量。
- 标准答案来自已标注案件，但不代表某条权利要求可能使用的全部潜在现有技术。
- 不同系统输出的文献号应先统一格式，并在同一标识维度下去重和比较。
- 数据集及模型输出不能替代专利律师或专业人员的法律判断。
