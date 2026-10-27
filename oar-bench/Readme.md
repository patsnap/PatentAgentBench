# OAR 审查意见答复基准数据集

中文 | [英文版](Readme.en.md)

本数据集用于专利审查意见答复任务（OAR），包含 40 条案例、40 份审查意见通知书（OA）PDF，共 229 页，以及各案例的评分标准和对应字段值。其中，中国（CN）案例 13 条、欧洲（EP）案例 13 条、美国（US）案例 14 条。

## 文件结构

```text
oar-bench/
├── Readme.md
├── Readme.en.md
├── assets/
│   └── OAR-<id>.pdf
└── data/
    └── test.jsonl
```

`data/test.jsonl` 使用 UTF-8 编码，每行是一个完整的 JSON 对象。全部 40 条案例及其评分信息集中保存在此文件中。文本值内部的换行使用 `\n` 转义。

数据包包含原始材料、结构化参考信息和统一通用评分标准，不包含专家标注、案例专属评分标准、模型答复、评测结果或评分器脚本。

## 数据记录结构

每条记录以 `field_values` 开始，顶层字段如下：

| 字段 | 含义 |
|---|---|
| `field_values` | 案例身份信息、原始文本、结构化参考信息、任务配置和文档元数据 |
| `scoring_policy` | 分值档位、适用规则、权重分配及避免重复扣分的规则 |
| `layer_weights` | 统一为 `{"common": 1.0}`，仅使用通用评分层 |
| `metrics` | 19 项统一通用评分指标，每项含 3 档评分标准，共 57 条 rubrics |

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

随包提供的附件为 `assets/` 下的 40 份 OA PDF。

实际提供的其他文本保存在案例记录内部，包括已有的 `supplemental_source_documents` 内容。

每条记录同时包含任务材料和评分参考信息。此数据包尚未规定完整的模型输入或外部检索协议，具体评测方案需要明确这些设置。

## 查找 OA PDF

OA 文件名由 `field_values.id` 决定。40 份 PDF 均直接存放在 `assets/` 下：

```text
assets/OAR-<id>.pdf
```

例如，中国案例 `349` 对应 `assets/OAR-349.pdf`，无需额外映射文件。JSONL 记录中不包含 OA 路径字段或 PDF 校验和字段。

## 评分标准与字段引用

评分标准采用2026年10月20日修订的《OAR通用评分标准及标注填写示例_修订版》，权重采用《OAR_v1.3_权重分配说明》。仅采用通用标准正文及已确认的权重、适用性规则，不纳入Word开头的专家标注要求及附录CN／US／EP差异表。

OAR 共用 57 条 rubrics：19 项由 Judge 评分的通用指标，每项在 `levels` 中分别定义 0、0.5、1 三档评分标准（19 × 3 = 57）。这套标准在全部 40 条案例中重复使用，不按案例数重复累计。JSONL 中共存储 760 条指标记录（40 × 19），包含 2,280 条评分档位记录（40 × 57）。原 RC、RS、RE、DQ 指标、自动计算规则、专家事实及案例专属 S 指标已移除。

| 分组 | 指标编号 | 通用预算 | 组内分配 |
|---|---|---|---|
| 基础质量 | G1、G2、G7、G8、G9、G10、G11 | 25% | 依次按6:6:3:5:2:4:6 |
| 修改质量 | G3、G4、G5 | 20% | 依次按6:6:8 |
| 实质论证 | G6.1.1—G6.1.4、G6.2—G6.6 | 55% | 先按问题类型，再按类型内子项 |

G6与G6.1仅为分类标题，不是计分项。所有计分项的`kind`均为`subjective`、`layer`均为`common`。每项得分只能为0、0.5或1。

指标中的`name`、`evaluation_object`和`levels`为英文。仅原文明确列出“评价对象”的9项（G1—G5、G7、G8、G10、G11）包含`evaluation_object`；其余10项省略该字段，不补写。G6.2的新颖性注释和G9的检查范围及责任说明保留在`notes`中。

- `field_refs`：指向当前记录内可供核验的案件、任务和原文材料，例如`#/field_values/notify_content`。这些引用不区分必需输入和按相关性选用的材料，实际内容可能为`null`；候选答复由使用方另行提供。
- `weight_group`：所属基础、修改或论证组，对应`scoring_policy.weight_groups`。
- `argument_type`：仅论证项包含，对应`scoring_policy.argument_types`中的一个问题类型。
- `relative_weight`：基础／修改组内或论证类型内的相对权重，不是最终百分比。一个论证项必须且只能属于一个问题类型。
- `activation_condition`：适用条件；不预先判定具体案例的分数或适用性。

### 动态权重

基础质量、修改质量、实质论证先按25:20:55分配。整组不适用时，按剩余组的原预算归一化。实质论证组再按下表分配问题类型预算：

| 问题类型 | 类型相对权重 | 计分项及类型内相对权重 |
|---|---:|---|
| 创造性 | 16 | G6.1.1:G6.1.2:G6.1.3:G6.1.4 = 3:4:6:4 |
| 新颖性 | 8 | G6.2，1 |
| 客体适格性 | 4 | G6.3，1 |
| 充分公开／可实施／书面描述 | 5 | G6.4，1 |
| 清楚、支持及权项形式 | 4 | G6.5，1 |
| 必要技术特征 | 3 | G6.6，1 |

创造性作为一个完整问题类型，其预算不随适用子项数量变化；类型内部仅在适用子项间按3:4:6:4分配。类型只剩一个适用子项时，该项承接类型全部预算。不能删除“全部适用”场景中的不适用项后，对固定百分比直接归一化。

```text
论证项权重 = 论证组实际预算 × 本类型相对权重 / 适用类型相对权重之和
             × 本项相对权重 / 本类型适用子项相对权重之和
其他项权重 = 本组实际预算 × 本项相对权重 / 本组适用项相对权重之和
通用分C = Σ(指标分配权重 × Judge得分)
最终分 = 100C，最后保留1位小数
```

例如：无修改组、存在论证项时，基础与论证预算为31.25%和68.75%；只适用G6.1.4和G6.2时，两项分别占45.8333%和22.9167%。修改组适用时，两项分别占36.6667%和18.3333%，修改组保留20%。计算使用未舍入权重。这些比例为设计初值，不是Word规定比例，尚未经新100案校准。

### 适用性及修改三项同步置零

G1、G2、G7—G11每案必评。G6各项由Judge依据完整OA、实际争点、法域及必要论证环节判断；一条创造性意见可能涉及多个子项。候选漏答不构成不适用。

G3—G5在候选实际修改权利要求时同时启用，按各自Word档位评分。若Judge判断应该修改权利要求，但候选OAR未选择修改策略，三项同步返回`status=scored, score=0`，保留修改组权重，不得标为不适用或重新分配预算。每项须引用应修改的案件依据及候选未选择该策略的证据，缺陷描述注明本项ID和规则性置零原因；不能仅凭`pre_claim`与`post_claim`不同推定应该修改。除此之外，未实际修改时三项均为`not_applicable`。G2、G6及其他规则保持原状。

同一最小缺陷通常仅由一个主指标扣分；明确规定的G3—G5同步置零是政策例外。案件身份、输入完整性、法条查阅和引文核验是评测校验，不独立加权计分。

### 不适用、失败及结果契约

`scoring_policy.result_contract`记录供外部评分器使用的契约；数据包不包含评分结果。`scored`须提供0／0.5／1得分及可复核证据；`not_applicable`、`not_evaluable`和`failed`的分数均为`null`。条件项不适用须给出原因及材料引用。

只有`not_applicable`释放权重。`not_evaluable`或`failed`保留其分配份额，并阻止输出最终总分；不能把这些份额分给其他已评分项，也不能因某个创造性子项无法评价而移除整个问题类型。外部评分器应校验必评项、修改三项适用性一致及同步置零要求、证据字段；程序校验不能代替语义审核。

结果审计应保存各组及问题类型预算、`common_allocated_weights`和`common_effective_weights`。不适用、无法评价或失败项的实际计分权重为0；无法评价或失败项的分配份额仍保留且总分不可用。本次仅更新评分定义，不对案例预判适用性或重新评分。

## 读取示例

在包含 `oar-bench/` 的目录中运行以下示例：

```python
import json
from pathlib import Path

root = Path("oar-bench")
with (root / "data/test.jsonl").open(encoding="utf-8") as stream:
    cases = [json.loads(line) for line in stream]

case = cases[0]
fields = case["field_values"]
oa_pdf = root / "assets" / f"OAR-{fields['id']}.pdf"
metric = next(item for item in case["metrics"] if item["metric_id"] == "G10")

def resolve(pointer):
    value = case
    for part in pointer[2:].split("/"):
        value = value[part.replace("~1", "/").replace("~0", "~")]
    return value

values_for_metric = {pointer: resolve(pointer) for pointer in metric["field_refs"]}
```

所有内部 JSON 指针均可在所属案例记录中解析。读取此数据包无需连接数据库或访问原项目。
