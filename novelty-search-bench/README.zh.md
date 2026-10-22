# PatSnap 新颖性检索数据集

[English](README.md) | **简体中文**

本数据集包含 80 条专利新颖性检索样本。每条样本提供一件查询专利的公开号，以及审查员引用的 X 类现有技术的公开号。数据同时包含针对查询专利及其同族成员的引证信息。

## 数据概览

| 项目 | 测试集 |
| --- | ---: |
| 样本数 | 80 |
| 查询专利的司法辖区 | CN 39、US 26、EP 11、WO 4 |
| 查询语言 | CN 40、EN 40 |
| 样本类型 | 同族扩展 |
| 许可证 | CC BY-NC 4.0 |

## 读取数据

数据文件为 `data/test.jsonl`，采用 UTF-8 编码，每行一条 JSON 样本。也可以从 Hugging Face 读取：

```python
from datasets import load_dataset

test = load_dataset("PatSnap/novelty-search-bench", split="test")
print(len(test))  # 80
print(test[0]["pn"], test[0]["pn_x"])
```

## 字段说明

| 字段 | 类型 | 含义 |
| --- | --- | --- |
| `pn` | string | 查询专利公开号 |
| `apdt` | int64 | 申请日，格式为 `YYYYMMDD` |
| `ipc` | list[string] | IPC 分类号 |
| `ipc_main` | string | 主要 IPC 部类 |
| `lang` | string | 查询语言，`CN` 或 `EN` |
| `jurisdiction` | string | 查询专利的司法辖区 |
| `type` | string | 样本类型；本测试集为 `family` |
| `pn_x` | list[string] | 查询专利的 X 类引证文献公开号 |
| `pn_x_family` | list[string] | `pn_x` 所对应专利同族的公开号 |
| `pn_family_x` | list[string] | 查询专利同族成员的 X 类引证文献公开号 |
| `pn_family_x_family` | list[string] | `pn_family_x` 所对应专利同族的公开号 |

`pn_x` 和 `pn_family_x` 记录被引用文献的公开号；对应的 `*_family` 字段记录其专利同族的公开号。数据不包含查询专利的技术方案文本。

## 数据范围

引证文献并非所有相关现有技术的完整清单。数据覆盖的查询司法辖区和语言见上表。

## 引用

```bibtex
@article{zhang2025novelty,
  title   = {Research on Evaluation Methods for Patent Novelty Search Systems and Empirical Analysis},
  author  = {Zhang, Shu and Zhang, LiSha and Duan, Kai and Sun, XinKai},
  journal = {arXiv preprint arXiv:2508.17782},
  year    = {2025},
  url     = {https://arxiv.org/abs/2508.17782}
}

@dataset{patsnap_novelty_search_bench_2026,
  title  = {PatSnap Novelty Search Bench},
  author = {PatSnap},
  year   = {2026},
  url    = {https://huggingface.co/datasets/PatSnap/novelty-search-bench}
}
```

## 许可证

本数据集采用 [CC BY-NC 4.0](https://creativecommons.org/licenses/by-nc/4.0/) 许可证，供研究与非商业评测使用。
