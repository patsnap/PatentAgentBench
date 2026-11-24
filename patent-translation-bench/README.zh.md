# PatSnap Patent Translation Bench

[English](./README.md) | **中文**

一个面向**专利文本机器翻译系统**的评估 Bench，覆盖中翻英（CN→EN）与英翻中（EN→CN）两个方向，从翻译准确性、术语准确性、术语一致性、专利书写规范、幻觉、漏译六个维度对翻译质量进行系统评估。

数据集的文本总量约相当于 **80 篇平均长度的专利文本**，并设计为 **881 个评测 item**。这里的等效篇数只用于描述文本量，不代表 80 件独立专利。

公开测试集在本仓库以 [JSONL](data/test.jsonl) 格式提供，也可在 [Hugging Face](https://huggingface.co/datasets/PatSnap/patent-translation-bench) 获取 Parquet 格式。

## 数据集概览

| 属性 | 取值 |
|----------|-------|
| **评测 item 数** | 881 |
| **约当文本量** | 约 80 篇平均长度的专利文本 |
| **翻译方向** | 中翻英（CN→EN）/ 英翻中（EN→CN） |
| **中英文均有文本的条数** | 854 |
| **缺少英文参考文本（`content_en: Zero`）** | 27 |
| **IPC 覆盖** | A / B / C / D / E / F / G / H 八大技术领域 |
| **文本粒度** | 词、字、句、段、篇 |
| **许可协议** | CC BY-NC 4.0 |

## 使用场景

本 Bench 用于评估专利翻译系统在以下能力上的表现：

1. **翻译准确性**：在词、字、句、段、篇各粒度上与标准答案的语义吻合程度
2. **术语准确性**：专利专业术语是否被准确翻译
3. **术语一致性**：同一术语在同一文本中是否保持一致的译法
4. **专利书写规范**：译文是否符合专利文件的书写范式
5. **幻觉检测**：译文是否出现原文中没有的内容（如混入源语言字符、篇幅异常膨胀）
6. **漏译检测**：译文篇幅相较原文是否出现异常压缩

## 数据字段

| 字段 | 类型 | 说明                                                |
|-------|------|---------------------------------------------------|
| `ipc` | string | IPC 一级分类（A–H，无分类记为 `Zero`）                        |
| `content_cn` | string | 中文原文                                              |
| `content_en` | string | 英文原文（中翻英的标准答案；`Zero` 表示该条无英文）                     |
| `label_1` | string | 文本粒度：`word` / `character` / `sentence` / `paragraph` / `document` |
| `label_2` | string | 评测维度（见下表）                                         |
| `label_3` | nullable string | 文本场景，如 `abstract` 或 `claims`；无则为 `Zero` 或 null |
| `special_cn` | nullable string | 中文特殊词/术语（用于专业性指标）；无则为 `Zero` 或 null |
| `special_en` | nullable string | 英文特殊词/术语（用于专业性指标）；无则为 `Zero` 或 null |
| `domain` | nullable string | 领域标签；无则为 `Zero` 或 null |

### label_2 取值说明

| label_2 | 类别 | 说明 | 条数 |
|---------|------|------|------|
| terminology_accuracy | 专业性 | 专利术语翻译是否准确 | 205 |
| terminology_consistency | 专业性 | 同一术语在文中译法是否一致 | 124 |
| normal_sentence | 普通准确性 | 常规句子翻译质量 | 129 |
| normal_character | 普通准确性 | 常规字符/词汇翻译质量 | 125 |
| paragraph_accuracy | 普通准确性 | 段落级翻译质量 | 110 |
| special_character | 专业性 | 特殊符号/字符是否正确处理 | 80 |
| special_sentence | 专业性 | 特殊句式是否正确翻译 | 57 |
| patent_writing_norm | 专业性 | 译文是否符合专利文书规范 | 19 |
| title_writing_norm | 专业性 | 译文是否符合专利标题书写规范 | 8 |
| document_accuracy | 普通准确性 | 全文篇章级翻译质量 | 24 |

## 数据分布

### 按文本粒度（label_1）

| 粒度 | 条数 | 占比 |
|------|------|------|
| word | 329 | 37.3% |
| character | 205 | 23.3% |
| sentence | 194 | 22.0% |
| paragraph | 110 | 12.5% |
| document | 43 | 4.9% |

### 按 IPC 技术领域

| IPC | 技术领域 | 条数 |
|-----|---------|------|
| H | 电学 | 89 |
| G | 物理 | 90 |
| C | 化学；冶金 | 70 |
| B | 作业；运输 | 66 |
| A | 人类生活必需 | 65 |
| D | 纺织；造纸 | 52 |
| F | 机械工程；照明；加热；武器；爆破 | 52 |
| E | 固定建筑物 | 49 |
| 无 | 无 IPC 标注 | 348 |

## 评估指标

### 准确性指标（普通翻译质量，针对 label_2 为 normal_sentence / normal_character / paragraph_accuracy / special_sentence / document_accuracy）

#### BLEU（Bilingual Evaluation Understudy）

通过计算候选翻译与参考翻译之间的 n-gram 匹配度评估翻译的准确性和流畅性，分数在 0–1 之间，越接近 1 表示质量越好。统计 n-gram 匹配情况并引入长度惩罚因子。

#### SacreBLEU

标准化的 BLEU 实现，使用统一的 tokenization 和 smoothing 方法，确保不同研究者之间的结果可比。

#### METEOR（Metric for Evaluation of Translation with Explicit ORdering）

综合考虑词形变化、同义词和词序的评估指标，对语义和词序更加敏感。通过词汇匹配、词干匹配和同义词匹配进行词语对齐，计算准确率和召回率的调和平均，并引入长度惩罚因子。

#### ROUGE（Recall-Oriented Understudy for Gisting Evaluation）

主要通过计算候选翻译与参考翻译之间的 n-gram 重叠度评估语义相似度。ROUGE-N 衡量 n-gram 重叠，ROUGE-L 衡量最长公共子序列（LCS）长度。

| 指标 | 说明 | 适用方向 |
|------|------|---------|
| **BLEU-1 / 2 / 4** | n-gram 精确匹配率 | CN→EN / EN→CN |
| **SacreBLEU** | 标准化 corpus-level BLEU | CN→EN / EN→CN |
| **METEOR** | 含词形还原与同义词匹配的 n-gram 指标 | CN→EN / EN→CN |
| **ROUGE-1 / 2 / L** | 召回导向的 n-gram 重叠 | CN→EN / EN→CN |

综合得分（score）计算规则：
- 普通语句/字符/段落/特殊语句：`score = (BLEU-1 + METEOR) / 2`（CN→EN）；`score = (ROUGE-1 + METEOR) / 2`（EN→CN）
- document_accuracy：`score = SacreBLEU`

> 幻觉与漏译指标仅针对 `label_2` 为以下类别的条目计算：normal_sentence、normal_character、paragraph_accuracy、special_sentence、document_accuracy。

### 专业性指标

#### 术语准确性

确保专业术语的翻译与原文保持一致，避免歧义。

| label_2 | 计算方式 | 说明 |
|---------|---------|------|
| terminology_accuracy | `special_en`（CN→EN）/ `special_cn`（EN→CN）是否出现在译文中（0/1） | ACC：专业术语的翻译是否正确 |

#### 术语一致性

同一术语在全文中的使用应保持一致。

| label_2 | 计算方式 | 说明 |
|---------|---------|------|
| terminology_consistency | 术语在译文中出现次数 ≥ 2（0/1） | ACC：同一术语在不同部分的翻译是否一致（仅统计术语翻译准确的条目） |

#### 特殊字符

译文是否正确保留了原文中的特殊符号/字符。

| label_2 | 计算方式 | 说明 |
|---------|---------|------|
| special_character | `special_en`（CN→EN）/ `special_cn`（EN→CN）是否出现在译文中（0/1） | ACC：特殊符号/字符是否正确保留 |

#### 专利书写规范性

译文是否符合 USPTO 专利撰写要求。

| label_2 | 计算方式 | 说明 |
|---------|---------|------|
| patent_writing_norm | 规范短语命中比例（0–1） | ACC：摘要、权利要求、技术领域、背景技术、概述、具体实施方式等专业名词翻译是否准确 |

### 幻觉与漏译指标

#### 漏译

翻译是否完整。以下指标仅针对 `label_2` 为 normal_sentence、normal_character、paragraph_accuracy、special_sentence、document_accuracy 的条目计算，结果为触发百分比（%），越低越好。

| 指标 | 计算方式 |
|------|---------|
| **漏译率** | `count(模型返回结果长度 / 标注答案长度 < 0.5) / 总量` |

#### 幻觉

模型回答是否存在幻觉。以下指标仅针对 `label_2` 为 normal_sentence、normal_character、paragraph_accuracy、special_sentence、document_accuracy 的条目计算，结果为触发百分比（%），越低越好。

| 指标 | 计算方式 |
|------|---------|
| **篇幅幻觉率** | `count(模型返回结果长度 / 标注答案长度 > 5) / 总量` |
| **源语言混入率（CN→EN）** | `count(翻译结果存在中文字符) / 总量` |
| **源语言混入率（EN→CN）** | `count(翻译结果存在英文字符 且 原文不含英文) / 总量` |

> CN→EN 以单词数计算长度比；EN→CN 以字符数计算长度比。

## 数据集构建

### 1. 数据来源

从 PatSnap 专利数据库中采集覆盖 A–H 八大 IPC 领域的中英双语专利文本对，文本来源涵盖专利摘要、权利要求、说明书各章节（背景技术、发明内容、附图说明、具体实施方式）。

### 2. 评测维度分层

按照文本粒度（word/character/sentence/paragraph/document）和评测维度（普通准确性/terminology_accuracy/terminology_consistency/patent_writing_norm/special_character/special_sentence）对样本进行分层标注，确保各维度均有充足的评测覆盖。

### 3. 专业性标注

对包含专利专用术语、特殊字符和书写规范的样本，人工标注 `special_cn` / `special_en` 字段，用于专业性指标的精确匹配评测。

### 4. 双语样本的使用

需要双语文本的评测可使用 `content_en` 不为 `Zero` 的 854 条记录。其余 27 条仍可用于仅需中文文本或标注的任务。

## 加载 JSONL 数据集

```python
import json

with open("data/test.jsonl", encoding="utf-8") as source:
    items = [json.loads(line) for line in source]

print(len(items))  # 881
```

## 引用

如果你使用了本数据集，请引用：

```bibtex
@dataset{patsnap_patent_translation_bench_2026,
  title={PatSnap Patent Translation Bench},
  author={PatSnap},
  year={2026},
  note={A benchmark for evaluating patent machine translation systems, covering CN↔EN bidirectional translation}
}
```

## 许可协议

本数据集依据 [CC BY-NC 4.0](https://creativecommons.org/licenses/by-nc/4.0/) 发布，可用于科研和非商业评测用途。

## 试用生产系统

体验 **PatSnap AI Translation** —— 本 Bench 所评估的专利翻译系统，提供专利文本中英互译服务，覆盖全球主要受理局专利文件。

🔗 **立即体验**：[PatSnap Eureka](https://eureka.patsnap.com)
