# OAR 审查意见答复基准数据集

中文 | [英文版](readme.en.md)

本数据集用于专利审查意见答复任务（OAR），包含 40 条案例、40 份审查意见通知书（OA）PDF，共 229 页，以及各案例的评分标准和对应字段值。其中，中国（CN）案例 13 条、欧洲（EP）案例 13 条、美国（US）案例 14 条。

## 文件结构

```text
OAR-bench/
├── readme.md
├── readme.en.md
├── assets/
│   └── OAR-<id>.pdf
└── data/
    └── test.jsonl
```

`data/test.jsonl` 使用 UTF-8 编码，每行是一个完整的 JSON 对象。全部 40 条案例及其评分信息集中保存在此文件中。文本值内部的换行使用 `\n` 转义。

数据包包含原始材料、结构化参考信息、已有的专家事实及评分标准，不包含模型答复、评测结果或评分器脚本。后续计划通过独立评分器脚本实现自动计算指标。

## 数据记录结构

每条记录以 `field_values` 开始，顶层字段如下：

| 字段 | 含义 |
|---|---|
| `field_values` | 案例身份信息、原始文本、结构化参考信息、任务配置和文档元数据 |
| `expert_facts` | 案例专属的专家事实，以事实编号为键；仅六条记录包含此字段 |
| `scoring_policy` | 分值档位、适用规则、权重分配及避免重复扣分的规则 |
| `layer_weights` | 通用评分层和案例专属评分层的权重 |
| `metrics` | 通用评分指标及已有的案例专属指标 |

身份信息位于 `field_values` 内部，顶层不包含 `id`、`apno`、`country`、`schema_version` 或 `case_id`。

### 案例字段

所有记录的 `field_values` 均包含以下字段：

| 字段 | 含义 |
|---|---|
| `id` | 数字形式的案例编号，也用于 OA PDF 文件命名 |
| `apno` | 专利申请号 |
| `country` | 法域：`CN`、`EP` 或 `US` |
| `applicants` | 申请人信息 |
| `notify_type` | 审查意见通知书类型，可能为 `null` |
| `q_mrdt` | 已存储的审查意见日期 |
| `notify_content` | 审查意见正文 |
| `cited_documents` | 结构化的对比文件索引，可能为 `null` |
| `reject_info` | 结构化的审查意见及相关参考信息 |
| `pd_documents` | 补充的参考文献映射，可能为 `null` |
| `claim` | 选定版本的权利要求文本，以字符串存储；部分记录的字符串内部为 JSON 编码的权利要求数据 |
| `desc` | 选定版本的说明书文本 |
| `eval_task_config` | 任务类型、任务指令和要求的输出语言 |
| `oa_document_versions` | 选定的权利要求和说明书字段、版本元数据及目标 OA 日期 |
| `cited_document_sources` | 对比文件元数据，包括是否提供正文 |
| `supplemental_source_documents` | 补充材料记录及记录中实际包含的文本 |

结构化字段使用 JSON 对象、数组和 `null` 值。空数组与 `null` 保留材料缺失状态。权利要求和说明书对应 `oa_document_versions` 中选定的字段。`reject_info` 中的 `classification_revision` 字段已删除。

## 语言与材料范围

`scoring_policy`、`layer_weights` 和 `metrics` 中原有的中文已翻译为英文。`field_values` 和 `expert_facts` 保留原始语言内容，包括中文。每条任务要求的答复语言由 `field_values.eval_task_config.output_language` 指定。

随包提供的附件仅为 `assets/` 下的 40 份 OA PDF。说明书原始页面图片、文本中引用的其他原始 PDF 和内部审计文件均未提供。数据中的本机文件路径和内部审计路径已替换为明确的脱敏提示，公开来源链接与文档标识符保留。这些提示表示相应来源不可从数据包中访问，不是有效的文件链接。

OCR 文本可能无法完整保留公式、表格或页面布局。路径脱敏不代表补齐了缺失材料。`cited_document_sources` 中的 101 条记录目前均标记为 `content_included: false`，提供的是元数据，不是对比文件全文。实际提供的其他文本保存在案例记录内部，包括已有的 `supplemental_source_documents` 内容。

每条记录同时包含任务材料和评分参考信息。此数据包尚未规定完整的模型输入或外部检索协议，具体评测方案需要明确这些设置。

## 查找 OA PDF

OA 文件名由 `field_values.id` 决定。40 份 PDF 均直接存放在 `assets/` 下：

```text
assets/OAR-<id>.pdf
```

例如，中国案例 `349` 对应 `assets/OAR-349.pdf`，无需额外映射文件。JSONL 记录中不包含 OA 路径字段或 PDF 校验和字段。

## 评分标准与字段引用

每条案例包含 20 项通用指标：

| 指标组 | 指标编号 | 类型 |
|---|---|---|
| RC | RC-1 至 RC-5 | 自动计算 |
| RS | RS-1 至 RS-6 | 主观评分 |
| RE | RE-1 至 RE-5 | 主观评分 |
| DQ | DQ-1 至 DQ-4 | 主观评分 |

全部案例共包含 800 项通用指标，另有九条案例包含合计 12 项案例专属 S 指标，总计 812 条指标记录。七条专家事实分布在六条案例中。S 指标编号仅在各自案例内有效。

指标记录包含编号、名称、类型、评分层及相应评分定义。不同指标还包含适用条件、分值档位、计算说明、权重和评分责任边界等内容。

- `field_refs`：指向当前记录内部的 JSON 指针，例如 `#/field_values/notify_content`。
- `reference_adapter_field_refs`：存在于 RC-2 至 RC-5 中，列出共用的 OA 参考答案构建逻辑读取的案例字段；在适用情况下，其依赖范围比单项指标的 `field_refs` 更广。
- `involved_fact_ids`：将 S 指标关联到当前记录的 `expert_facts` 中对应的事实。

非自动计算指标不包含 `input_fields`、`required_input_fields` 或 `source_fields_to_use_when_relevant`。可通过 `field_refs` 获取对应的实际字段值，但这些引用不区分必需输入和按相关性选用的材料。

含 S 指标的案例采用通用层 80%、案例专属层 20% 的权重；其余 31 条案例采用通用层 100% 的权重。RS 指标的 `pool_weight` 为 `0.2`，由实际适用的 RS 指标共享。案例专属指标在其评分层内部使用相对权重。数据保留适用条件，不预先判定分数。

自动计算指标保留原有计算说明，包括对 ACC/F1 的引用。可执行的计算逻辑将在独立评分器脚本中提供。

## 读取示例

在包含 `OAR-bench/` 的目录中运行以下示例：

```python
import json
from pathlib import Path

root = Path("OAR-bench")
with (root / "data/test.jsonl").open(encoding="utf-8") as stream:
    cases = [json.loads(line) for line in stream]

case = cases[0]
fields = case["field_values"]
oa_pdf = root / "assets" / f"OAR-{fields['id']}.pdf"
metric = next(item for item in case["metrics"] if item["metric_id"] == "RE-1")

def resolve(pointer):
    value = case
    for part in pointer[2:].split("/"):
        value = value[part.replace("~1", "/").replace("~0", "~")]
    return value

values_for_metric = {pointer: resolve(pointer) for pointer in metric["field_refs"]}
```

所有内部 JSON 指针均可在所属案例记录中解析。读取此数据包无需连接数据库或访问原项目。
