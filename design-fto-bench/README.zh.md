# PatSnap 外观设计 FTO 基准数据集

[English](README.md) | 中文

PatSnap Design FTO Bench 是用于**外观设计专利自由实施（FTO）图像检索**的数据集。每条样本包含一张查询图片和一组标注的目标专利公开号。`test` 测试集在 [`data/test.jsonl`](data/test.jsonl) 中包含 80 条 JSONL 记录。80 张 PNG 图片存放在 [`asset/`](asset/) 下，由 `query_img_path` 字段引用。

## 数据集概览

| 项目 | 内容 |
|------|------|
| 测试样本 | 80 条 |
| 法域 | CN：40 条；EU：40 条 |
| 查询图片 | `asset/` 下的 80 张 PNG |
| 目标专利号 | 80 条样本共 98 个条目 |
| 图片类型 | Invalidation Proceedings |
| `query_pn` | 53 条有值；27 条为 `null` |
| 许可证 | CC BY-NC 4.0 |

## 快速开始

在仓库根目录运行以下示例：

```python
import json
from pathlib import Path

root = Path("design-fto-bench")
with (root / "data/test.jsonl").open(encoding="utf-8") as file:
    samples = [json.loads(line) for line in file]

sample = samples[0]
image_path = root / "asset" / sample["query_img_path"]
print(len(samples), image_path, sample["target_pns"])
```

同一数据集也发布在 [Hugging Face](https://huggingface.co/datasets/PatSnap/design-fto-bench)，图片嵌入在 Parquet 文件中。

## 数据字段

| 字段 | 类型 | 说明 |
|------|------|------|
| `query_pn` | 可为空的字符串 | 查询专利公开号；可能为 `null` |
| `query_img_path` | 字符串 | 相对于 `asset/` 的查询图片 PNG 路径 |
| `target_pns` | 字符串列表 | 标注的目标专利公开号 |
| `picture_type` | 字符串 | 样本记录的图片类型 |
| `loc` | 字符串 | 洛迦诺分类的一级分类 |
| `sub_loc` | 字符串 | 更具体的洛迦诺分类 |
| `jurisdiction` | 字符串列表 | 法域代码（`["CN"]` 或 `["EU"]`） |
| `is_public` | 整数 | 公开标记；本测试集均为 `1` |

## 引用

```bibtex
@dataset{patsnap_design_fto_bench_2026,
  title  = {PatSnap Design FTO Bench},
  author = {PatSnap},
  year   = {2026},
  url    = {https://huggingface.co/datasets/PatSnap/design-fto-bench},
  note   = {Design patent FTO image retrieval dataset}
}
```

## 许可证

本数据集以 [CC BY-NC 4.0](https://creativecommons.org/licenses/by-nc/4.0/) 许可发布，供研究和非商业评测使用。
