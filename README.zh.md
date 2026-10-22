# PatentAgentBench (PAB)

<p align="left">
  <img src="patsnap-logo.png" alt="Patsnap" width="326">
</p>

[English](README.md) | **简体中文**

PatentAgentBench 用于评测大语言模型与 AI Agent 的专业专利任务能力，涵盖检索、分析、撰写、审查意见答复与翻译。

**8 个数据集 · 中英文任务 · 文本、图像与文档输入**

[🤗 Hugging Face 数据集合集](https://huggingface.co/collections/PatSnap/patentagentbench)

## 数据集一览

| 数据集 | 规模 | 任务简介 | Hugging Face |
| --- | --- | --- | --- |
| [权利要求对比](claim-charting-bench/) | 30 | 拆解技术特征，与对比证据对齐并判断公开情况 | [🤗 查看](https://huggingface.co/datasets/PatSnap/claim-charting-bench) |
| [外观设计 FTO](design-fto-bench/) | 80 | 根据产品图像检索外观设计专利 | [🤗 查看](https://huggingface.co/datasets/PatSnap/design-fto-bench) |
| [FTO 检索](fto-search-bench/) | 60 | 根据技术描述检索目标司法辖区的已知风险专利 | [🤗 查看](https://huggingface.co/datasets/PatSnap/fto-search-bench) |
| [新颖性检索](novelty-search-bench/README.zh.md) | 80 | 检索审查员引用的 X 类现有技术及其同族 | [🤗 查看](https://huggingface.co/datasets/PatSnap/novelty-search-bench) |
| [专利撰写](patent-drafting-bench/) | 60 | 根据技术交底书撰写权利要求、说明书及必要附图 | [🤗 查看](https://huggingface.co/datasets/PatSnap/patent-drafting-bench) |
| [专利无效检索](patent-invalidity-search-bench/) | 80 | 根据权利要求检索真实无效案件中的证据 | [🤗 查看](https://huggingface.co/datasets/PatSnap/patent-invalidity-search-bench) |
| [专利翻译](patent-translation-bench/) | 881 个评测 item；文本量约当 80 篇平均长度的专利文本 | 评测中英文专利文本翻译 | [🤗 查看](https://huggingface.co/datasets/PatSnap/patent-translation-bench) |
| [审查意见答复（OAR）](oar-bench/) | 40 | 根据审查意见及案件材料撰写答复 | [🤗 查看](https://huggingface.co/datasets/PatSnap/patent-oar-bench) |

其他数据集按测试案例计数；翻译数据分别提供文本量约当值与评测 item 数，单位不同，因此不合并求和。

## 数据分布

### 语言与司法辖区

语言指任务输入或要求的输出语言。

| 数据集 | 语言 | 司法辖区 |
| --- | --- | --- |
| 权利要求对比 | 中文 / 英文 | CN / US / EP |
| 外观设计 FTO | 图像输入 | CN / EU |
| FTO 检索 | 中文 / 英文 | CN / US / EP |
| 新颖性检索 | 中文 / 英文 | CN / US / EP / WO |
| 专利撰写 | 中文 / 英文 | CN / US / EP |
| 专利无效检索 | 中文 / 英文 | CN / US |
| 专利翻译 | 中文 / 英文 | CN / US / EP / WO |
| 审查意见答复 | 中文 / 英文 | CN / US / EP |

CN：中国；US：美国；EP：欧洲专利局；EU：欧盟；WO：PCT 国际公布。翻译任务的司法辖区以原始专利公开号为准。

### 技术领域与文本覆盖

权利要求对比、FTO 检索、新颖性检索、专利撰写和专利无效检索均覆盖 **IPC A–H 八个部**。外观设计 FTO 采用洛迦诺分类；审查意见答复未提供技术分类标注。

## 评测指标与 Rubric

**自动指标**依据参考答案计算；**Rubric** 使用评分规则，由人工或 LLM Judge 逐项评审。

| 数据集 | 自动指标或参考标注 | Rubric 条目数 |
| --- | --- | ---: |
| 专利撰写 | 按 Rubric 加权评分 | 977 |
| 审查意见答复 | Accuracy、F1 自动指标，结合 Rubric 评分 | 612 |
| **合计** | | **1,589** |

### Rubric 构成

| 数据集 | 通用规则 | 案例特定规则 | 合计 |
| --- | ---: | ---: | ---: |
| 专利撰写 | 900 | 77 | 977 |
| 审查意见答复 | 600 | 12 | 612 |
| **合计** | **1,500** | **89** | **1,589** |

以上按各案例中的规则条目累计，包含跨案例重复的通用规则。OAR 另有 **200 条自动指标定义**，不计入 Rubric 总量。具体评分档位、权重与适用条件见各子集文档。

## 目录结构

全部子集均提供测试集，格式为 UTF-8 JSONL，每行一条样本。

| 路径 | 内容 |
| --- | --- |
| `README.md` / `README.zh.md` | 英文 / 中文总览 |
| `<子集目录>/data/test.jsonl` | 测试样本及参考标注 |
| `design-fto-bench/asset/` | 80 张查询图像（PNG） |
| `patent-drafting-bench/asset/` | 60 份技术交底书（DOCX） |
| `oar-bench/assets/` | 40 份审查意见通知书（PDF） |

各子集的任务说明、数据格式与读取方式见上方数据集链接。仓库提供数据和评分定义，未包含可执行评测脚本。

## 引用

```bibtex
@dataset{patsnap_patentagentbench_2026,
  title  = {PatentAgentBench (PAB)},
  author = {Patsnap},
  year   = {2026},
  url    = {https://huggingface.co/collections/PatSnap/patentagentbench}
}
```

## 许可证与免责声明

PatentAgentBench（PAB）仓库中的代码和项目文档采用 [MIT 许可证](LICENSE)。PAB 各数据子集、标注及附件可能适用单独的使用条款，具体请参阅对应的 README 或 Hugging Face 页面。

PatentAgentBench（PAB）数据集及相关输出可能存在错误或遗漏，不构成法律意见。使用者在依赖这些材料之前须进行审查与验证，确保具备处理材料所需的权利，并遵守适用的许可证、法律与法规。使用 PAB 前请阅读[完整免责声明](DISCLAIMER.zh.md)。

Copyright (c) 2026 Patsnap.
