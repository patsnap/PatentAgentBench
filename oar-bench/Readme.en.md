# OAR Office Action Response Benchmark Dataset

[中文](Readme.md) | English

OAR Bench contains data for Office Action Response tasks: 40 cases, 40 Office Action (OA) PDFs totaling 229 pages, and scoring criteria with their supporting field values. The cases cover 13 Chinese (CN), 13 European (EP), and 14 United States (US) patent applications.

## Package Contents

```text
oar-bench/
├── Readme.md
├── Readme.en.md
├── assets/
│   └── OAR-<id>.pdf
└── data/
    └── test.jsonl
```

`data/test.jsonl` contains one complete JSON object per line, encoded as UTF-8. All 40 case records and their scoring information are stored in this single file. Line breaks within text values are escaped as `\n`.

The package contains source materials, structured reference data, and shared common scoring criteria. It contains no expert annotations, case-specific scoring criteria, model responses, evaluation results, or scoring scripts.

## Record Structure

Each record begins with `field_values`. Its top-level fields are:

| Field | Description |
|---|---|
| `field_values` | Case identity, source texts, structured reference data, task configuration, and document metadata |
| `scoring_policy` | Score levels, applicability rules, weight allocation, and policies for avoiding duplicate penalties |
| `layer_weights` | Always `{"common": 1.0}`; only the common scoring layer is used |
| `metrics` | 19 shared common scoring criteria, each with 3 score-level descriptions, totaling 57 rubrics |

Identity fields are nested inside `field_values`; there are no top-level `id`, `apno`, `country`, `schema_version`, or `case_id` fields.

### Case Fields

All records contain the following fields inside `field_values`:

| Field | Description |
|---|---|
| `id` | Numeric case identifier, also used in the OA PDF filename |
| `apno` | Patent application number |
| `country` | Jurisdiction: `CN`, `EP`, or `US` |
| `applicants` | Applicant information |
| `notify_type` | OA type; may be `null` |
| `q_mrdt` | Stored OA date |
| `notify_content` | OA text |
| `cited_documents` | Structured cited-document index; may be `null` |
| `reject_info` | Structured OA objections and related reference information |
| `pd_documents` | Additional reference-document mappings; may be `null` |
| `claim` | Selected claim text, stored as a string; some records contain JSON-encoded claim data within this string |
| `desc` | Selected description text |
| `eval_task_config` | Task type, task instruction, and requested output language |
| `oa_document_versions` | Selected claim and description fields, version metadata, and target OA date |
| `cited_document_sources` | Metadata for cited documents, including whether their content is included |
| `supplemental_source_documents` | Additional source records and any text embedded in those records |

Structured fields use JSON objects, arrays, and `null` values as stored in the dataset. Empty arrays and `null` values preserve missing-material status. Claims and descriptions correspond to the fields selected in `oa_document_versions`.

## Languages and Materials

The bundled attachments are the 40 OA PDFs under `assets/`.

Any additional text actually provided is stored within the case record, including `supplemental_source_documents` where present.

The records include both task materials and evaluator reference information. This data package does not prescribe a complete model-input or external-retrieval protocol; an evaluation setup must define those choices explicitly.

## Locating an OA PDF

Each OA filename is derived from `field_values.id`. All 40 PDFs are stored directly in `assets/`:

```text
assets/OAR-<id>.pdf
```

For example, Chinese case `349` corresponds to `assets/OAR-349.pdf`. No external mapping file is needed. The JSONL records contain neither an OA path field nor a PDF checksum field.

## Scoring Criteria and References

OAR uses 57 shared rubrics: 19 common Judge-scored criteria, each defining three score-level descriptions (0, 0.5, and 1) in `levels` (19 × 3 = 57). All 40 cases reuse this rubric set; the rubric count does not multiply it by the number of cases. The JSONL stores 760 metric records (40 × 19), containing 2,280 score-level records (40 × 57).

| Group | Criteria | Common budget | Allocation within group |
|---|---|---:|---|
| Foundation | G1, G2, G7, G8, G9, G10, G11 | 25% | 6:6:3:5:2:4:6, respectively |
| Amendment | G3, G4, G5 | 20% | 6:6:8, respectively |
| Substantive argument | G6.1.1-G6.1.4, G6.2-G6.6 | 55% | First by issue type, then by subcriterion within each type |

G6 and G6.1 are category headings only and are not scored. All criteria have `kind=subjective` and `layer=common`. A scored criterion receives only 0, 0.5, or 1.

Only the nine criteria with an explicit evaluation-object paragraph in the source (G1-G5, G7, G8, G10, and G11) contain `evaluation_object`. The other ten omit it; no evaluation object has been inferred or added. The novelty note under G6.2 and the scope and responsibility explanation under G9 are retained in `notes`.

- `field_refs` contains JSON Pointers to stored case information, task requirements, and source materials, such as `#/field_values/notify_content`. These references do not distinguish required inputs from conditionally relevant materials; values may be `null`. The evaluator supplies the candidate response separately.
- `weight_group` identifies the foundation, amendment, or argument group in `scoring_policy.weight_groups`.
- `argument_type`, present only on argument criteria, identifies one issue type in `scoring_policy.argument_types`.
- `relative_weight` is the weight within a foundation/amendment group or an argument issue type, not a final percentage. Each argument criterion belongs to exactly one issue type.
- `activation_condition` preserves applicability rules without predetermining scores or case applicability.

### Dynamic Weights

Allocate the common budget among foundation, amendment, and substantive argument in the ratio 25:20:55. When an entire group is not applicable, renormalize the remaining original budgets. Within the argument group, first allocate by issue type:

| Issue type | Type relative weight | Criteria and relative weights within type |
|---|---:|---|
| Inventive step | 16 | G6.1.1:G6.1.2:G6.1.3:G6.1.4 = 3:4:6:4 |
| Novelty | 8 | G6.2, 1 |
| Subject-matter eligibility | 4 | G6.3, 1 |
| Sufficiency of disclosure / enablement / written description | 5 | G6.4, 1 |
| Clarity, support, and claim form | 4 | G6.5, 1 |
| Essential technical features | 3 | G6.6, 1 |

Inventive step is one complete issue type: its budget does not vary with the number of applicable subcriteria. Within that type, divide the budget among applicable subcriteria using 3:4:6:4. A single applicable subcriterion receives the entire type budget. Do not remove inapplicable entries from an all-applicable percentage table and renormalize that flat list.

```text
Argument criterion weight = actual argument-group budget
    * type relative weight / sum of applicable type relative weights
    * criterion relative weight / sum of applicable criterion weights within type
Other criterion weight = actual group budget
    * criterion relative weight / sum of applicable criterion weights within group
Common score C = sum(allocated criterion weight * Judge score)
Final score = 100C, rounded to one decimal place at the end
```

For example, when amendment does not apply and argument does, foundation and argument receive 31.25% and 68.75%. If only G6.1.4 and G6.2 apply within argument, their weights are 45.8333% and 22.9167%. When amendment applies, the two weights are 36.6667% and 18.3333%, and amendment retains 20%. Calculations use unrounded weights.

### Applicability and Synchronized Amendment Zero Scores

G1, G2, and G7-G11 are required for every case. The Judge determines G6 applicability from the complete OA, actual points of dispute, jurisdiction, and necessary reasoning steps. One inventive-step objection may require several subcriteria. A candidate's failure to respond does not make an existing issue inapplicable.

G3-G5 activate together when the candidate actually amends the claims and use their respective Word score levels. If the Judge determines that the claims should be amended but the candidate OAR has not chosen an amendment strategy, all three receive `status=scored, score=0` and retain the amendment-group budget. They must not be marked inapplicable, and their budget must not be redistributed. Each criterion must cite the case basis showing why amendment is needed and evidence that the candidate did not choose that strategy. Its defect description must identify the current metric ID and the reason for the rule-based zero. A difference between `pre_claim` and `post_claim` alone does not establish that amendment is necessary. Otherwise, when no amendment is made, all three are `not_applicable`. G2, G6, and all other rules continue to apply as specified.

Normally, assign each minimal defect to one primary criterion. The explicitly required synchronized G3-G5 zero scores are a policy exception. Case identity, input completeness, consultation of legal provisions, and verification of quotations are evaluation checks, not separately weighted criteria.

### Inapplicability, Failure, and Result Contract

`scoring_policy.result_contract` defines a contract for an external scorer; the package contains no scoring results. A `scored` result requires a score of 0, 0.5, or 1 and verifiable evidence. `not_applicable`, `not_evaluable`, and `failed` require `score=null`. Conditional inapplicability requires a reason and source references.

Only `not_applicable` releases weight. `not_evaluable` and `failed` retain their allocated shares and block a final score. Their shares must not be redistributed to scored criteria, and an inventive-step issue type must not be removed because a subcriterion cannot be evaluated. An external scorer should validate required criteria, consistent applicability of G3-G5, compliance with synchronized zero scoring, and evidence fields. Programmatic checks do not replace semantic review.

Result audits should record group and issue-type budgets, `common_allocated_weights`, and `common_effective_weights`. Inapplicable, unevaluable, and failed criteria have effective weight 0; unevaluable and failed criteria still retain their allocated shares and prevent a final total.

## Reading Example

Run this example from the directory containing `oar-bench/`:

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

All internal JSON Pointers resolve within their own record. Reading the packaged data requires no database connection or access to the original project.
