# PatentAgentBench (PAB)

<p align="left">
  <img src="patsnap-logo.png" alt="Patsnap" width="326">
</p>

**English** | [简体中文](README.zh.md)

PatentAgentBench evaluates large language models and AI agents on professional patent tasks, including search, analysis, drafting, Office Action responses, and translation.

**8 datasets · 473 test cases · Chinese and English tasks · Text, image, and document inputs**

[🤗 Hugging Face Dataset Collection](https://huggingface.co/collections/PatSnap/patentagentbench)

## Datasets

| Dataset | Samples | Task | Hugging Face |
| --- | ---: | --- | --- |
| [Claim Charting](claim-charting-bench/) | 30 | Decompose claims into features, align evidence, and judge disclosure | [🤗 View](https://huggingface.co/datasets/PatSnap/claim-charting-bench) |
| [Design FTO](design-fto-bench/) | 80 | Retrieve design patents from product images | [🤗 View](https://huggingface.co/datasets/PatSnap/design-fto-bench) |
| [FTO Search](fto-search-bench/) | 60 | Retrieve known risk patents from technical descriptions in a target jurisdiction | [🤗 View](https://huggingface.co/datasets/PatSnap/fto-search-bench) |
| [Novelty Search](novelty-search-bench/) | 80 | Retrieve examiner-cited X-type prior art and its patent families | [🤗 View](https://huggingface.co/datasets/PatSnap/novelty-search-bench) |
| [Patent Drafting](patent-drafting-bench/) | 60 | Draft claims, a specification, and necessary drawings from a technical disclosure | [🤗 View](https://huggingface.co/datasets/PatSnap/patent-drafting-bench) |
| [Patent Invalidity Search](patent-invalidity-search-bench/) | 80 | Retrieve evidence from real invalidation cases using patent claims | [🤗 View](https://huggingface.co/datasets/PatSnap/patent-invalidity-search-bench) |
| [Patent Translation](patent-translation-bench/) | 43 | Evaluate Chinese/English patent text translation | [🤗 View](https://huggingface.co/datasets/PatSnap/patent-translation-bench) |
| [Office Action Response (OAR)](oar-bench/) | 40 | Draft responses using Office Actions and case materials | [🤗 View](https://huggingface.co/datasets/PatSnap/patent-oar-bench) |
| **Total** | **473** | | |

## Data distributions

### Languages and jurisdictions

Languages refer to task inputs or required outputs.

| Dataset | Language | Jurisdiction |
| --- | --- | --- |
| Claim Charting | Chinese / English | CN / US / EP |
| Design FTO | Image input | CN / EU |
| FTO Search | Chinese / English | CN / US / EP |
| Novelty Search | Chinese / English | CN / US / EP / WO |
| Patent Drafting | Chinese / English | CN / US / EP |
| Patent Invalidity Search | Chinese / English | CN / US |
| Patent Translation | Chinese / English | CN / US / EP / WO |
| Office Action Response | Chinese / English | CN / US / EP |

CN: China; US: United States; EP: European Patent Office; EU: European Union; WO: PCT international publication. Translation jurisdictions are identified from source patent publication numbers.

### Technical and text coverage

Claim Charting, FTO Search, Novelty Search, Patent Drafting, and Patent Invalidity Search all cover **IPC sections A–H**. Design FTO uses the Locarno classification; Office Action Response has no technical-classification annotation.

## Evaluation metrics and rubrics

**Automatic metrics** are calculated against reference answers. **Rubrics** are scoring criteria assessed individually by human reviewers or LLM judges.

| Dataset | Automatic metrics or reference annotations | Rubric entries |
| --- | --- | ---: |
| Patent Drafting | Weighted rubric scoring | 977 |
| Office Action Response | Automatic Accuracy and F1 metrics combined with rubric scoring | 612 |
| **Total** | | **1,589** |

### Rubric breakdown

| Dataset | Common criteria | Case-specific criteria | Total |
| --- | ---: | ---: | ---: |
| Patent Drafting | 900 | 77 | 977 |
| Office Action Response | 600 | 12 | 612 |
| **Total** | **1,500** | **89** | **1,589** |

Counts are criterion entries across cases, including repeated common criteria. OAR also contains **200 automatic metric definitions**, excluded from the rubric total. See each dataset's documentation for score levels, weights, and applicability rules.

## Directory structure

Every dataset provides a test split in UTF-8 JSONL, with one sample per line.

| Path | Contents |
| --- | --- |
| `README.md` / `README.zh.md` | English / Chinese overview |
| `<dataset>/data/test.jsonl` | Test samples and reference annotations |
| `design-fto-bench/asset/` | 80 query images (PNG) |
| `patent-drafting-bench/asset/` | 60 technical disclosures (DOCX) |
| `oar-bench/assets/` | 40 Office Actions (PDF) |

Follow the dataset links above for task instructions, schemas, and loading examples. The repository provides data and scoring definitions; it does not include executable evaluation scripts.

## Citation

```bibtex
@dataset{patsnap_patentagentbench_2026,
  title  = {PatentAgentBench (PAB)},
  author = {Patsnap},
  year   = {2026},
  url    = {https://huggingface.co/collections/PatSnap/patentagentbench}
}
```

## License and Disclaimer

The code and project documentation in the PatentAgentBench (PAB) repository are licensed under the [MIT License](LICENSE). Individual PAB datasets, annotations, and attachments may have separate terms; see the relevant subset README or Hugging Face page.

PatentAgentBench (PAB) datasets and related outputs may contain errors or omissions and do not constitute legal advice. Users must review and validate them before relying on them, ensure they have the necessary rights to process materials, and comply with applicable licenses, laws, and regulations. Read the [full disclaimer](DISCLAIMER.md) before using PAB.

Copyright (c) 2026 Patsnap.
