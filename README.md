<p align="center">
  <img src="assets/brand-logos.svg" alt="PatentBench and Patsnap" width="572">
</p>

<h1 align="center">PatentAgentBench (PAB)</h1>

**English** | [简体中文](README.zh.md)

PatentAgentBench evaluates large language models and AI agents on professional patent tasks, including search, analysis, drafting, Office Action responses, and translation.

**8 datasets · Chinese and English tasks · Text, image, and document inputs**

[Website](https://www.patentbench.ai/) | [Paper (arXiv PDF)](https://arxiv.org/pdf/2610.35031) | [🤗 Hugging Face Dataset Collection](https://huggingface.co/collections/PatSnap/patentagentbench)

## Datasets

| Dataset | Scale | Task | Hugging Face |
| --- | --- | --- | --- |
| [Claim Charting](claim-charting-bench/) | 30 | Decompose claims into features, align evidence, and judge disclosure | [🤗 View](https://huggingface.co/datasets/PatSnap/claim-charting-bench) |
| [Design FTO](design-fto-bench/) | 80 | Retrieve design patents from product images | [🤗 View](https://huggingface.co/datasets/PatSnap/design-fto-bench) |
| [FTO Search](fto-search-bench/) | 60 | Retrieve known risk patents from technical descriptions in a target jurisdiction | [🤗 View](https://huggingface.co/datasets/PatSnap/fto-search-bench) |
| [Novelty Search](novelty-search-bench/) | 80 | Retrieve examiner-cited X-type prior art and its patent families | [🤗 View](https://huggingface.co/datasets/PatSnap/novelty-search-bench) |
| [Patent Drafting](patent-drafting-bench/) | 60 | Draft claims, a specification, and necessary drawings from a technical disclosure | [🤗 View](https://huggingface.co/datasets/PatSnap/patent-drafting-bench) |
| [Patent Invalidity Search](patent-invalidity-search-bench/) | 80 | Retrieve evidence from real invalidation cases using patent claims | [🤗 View](https://huggingface.co/datasets/PatSnap/patent-invalidity-search-bench) |
| [Patent Translation](patent-translation-bench/) | 80 | Evaluate Chinese/English patent text translation | [🤗 View](https://huggingface.co/datasets/PatSnap/patent-translation-bench) |
| [Office Action Response (OAR)](oar-bench/) | 40 | Draft responses using Office Actions and case materials | [🤗 View](https://huggingface.co/datasets/PatSnap/patent-oar-bench) |

Dataset scales count test cases. Patent Translation contains 80 samples comprising 881 evaluation items.

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

**Rubrics** define how human reviewers or LLM judges assess outputs. Patent Drafting counts criterion entries across cases; OAR counts shared score-level descriptions.

| Dataset | Scoring approach | Rubric count |
| --- | --- | ---: |
| Patent Drafting | Weighted rubric scoring | 977 criterion entries across cases |
| Office Action Response | 19 shared Judge-scored criteria × 3 score levels | 57 shared score-level descriptions |

### Rubric breakdown

| Dataset | Common | Case-specific | Total | Counting unit |
| --- | ---: | ---: | ---: | --- |
| Patent Drafting | 900 | 77 | 977 | Criterion entries across cases |
| Office Action Response | 57 | 0 | 57 | Shared score-level descriptions |

Patent Drafting counts include common criteria repeated across cases. OAR's 57 rubrics comprise 19 common criteria with three score-level descriptions each (0, 0.5, and 1), reused across all 40 cases without multiplying the count by the number of cases. These counts use different units and are not summed. This OAR release has no automatic or case-specific criteria. See each dataset's documentation for weights and applicability rules.

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
