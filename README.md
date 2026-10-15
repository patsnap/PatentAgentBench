# PatentAgentBench (PAB)

<p align="left">
  <img src="patsnap-logo.png" alt="PatSnap" width="326">
</p>

**English** | [简体中文](README.zh.md)

PatentAgentBench evaluates large language models and AI agents on professional patent tasks, including search, analysis, drafting, Office Action responses, and translation.

**8 datasets · 1,311 test records · Chinese and English tasks · Text, image, and document inputs**

## Datasets

| Dataset | Samples | Task | Hugging Face |
| --- | ---: | --- | --- |
| [Claim Charting](claim-charting-bench/) | 30 | Decompose claims into features, align evidence, and judge disclosure | — |
| [Design FTO](design-fto-bench/) | 80 | Retrieve design patents from product images | [🤗 View](https://huggingface.co/datasets/PatSnap/design-fto-bench) |
| [FTO Search](fto-search-bench/) | 60 | Retrieve known risk patents from technical descriptions in a target jurisdiction | — |
| [Novelty Search](novelty-search-bench/) | 80 | Retrieve examiner-cited X-type prior art and its patent families | [🤗 View](https://huggingface.co/datasets/PatSnap/novelty-search-bench) |
| [Patent Drafting](patent-drafting-bench/) | 60 | Draft claims, a specification, and necessary drawings from a technical disclosure | [🤗 View](https://huggingface.co/datasets/PatSnap/patent-drafting-bench) |
| [Patent Invalidity Search](patent-invalidity-search-bench/) | 80 | Retrieve evidence from real invalidation cases using patent claims | — |
| [Patent Translation](patent-translation-bench/) | 881 | Evaluate Chinese/English patent text translation | [🤗 View](https://huggingface.co/datasets/PatSnap/patent-translation-bench) |
| [Office Action Response (OAR)](OAR-bench/) | 40 | Draft responses using Office Actions and case materials | — |
| **Total** | **1,311** | | |

## Data distributions

### Languages and jurisdictions

Counts are samples. Language describes task inputs or required outputs; translation counts describe bilingual pairs.

| Dataset | Language | Jurisdiction |
| --- | --- | --- |
| Claim Charting | Chinese 15 / English 15 | Not annotated |
| Design FTO | Image input | CN 40 / EU 40 |
| FTO Search | Chinese 30 / English 30 | CN 30 / US 15 / EP 15 |
| Novelty Search | Chinese 40 / English 40 | CN 39 / US 26 / EP 11 / WO 4 |
| Patent Drafting | Chinese 31 / English 29 | CN 31 / US 15 / EP 14 |
| Patent Invalidity Search | Chinese 40 / English 40 | CN 40 / US 40 |
| Patent Translation | Chinese/English pairs 854 / Chinese only 27 | CN 533 / Not annotated 348 |
| Office Action Response | Chinese 13 / English 27 | CN 13 / US 14 / EP 13 |

CN: China; US: United States; EP: European Patent Office; EU: European Union; WO: PCT international publication. Translation jurisdictions are counted from source patent publication numbers.

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
| `OAR-bench/assets/` | 40 Office Actions (PDF) |

Follow the dataset links above for task instructions, schemas, and loading examples. The repository provides data and scoring definitions; it does not include executable evaluation scripts.
