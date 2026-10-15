# OAR Bench

[中文](readme.md) | English

OAR Bench contains data for Office Action Response tasks: 40 cases, 40 Office Action (OA) PDFs totaling 229 pages, and scoring criteria with their supporting field values. The cases cover 13 Chinese (CN), 13 European (EP), and 14 United States (US) patent applications.

## Package Contents

```text
OAR-bench/
├── readme.md
├── readme.en.md
├── assets/
│   └── OAR-<id>.pdf
└── data/
    └── test.jsonl
```

`data/test.jsonl` contains one complete JSON object per line, encoded as UTF-8. All 40 case records and their scoring information are stored in this single file. Line breaks within text values are escaped as `\n`.

The package contains source materials, structured reference data, expert facts where available, and scoring criteria. It contains no model responses, evaluation results, or scoring scripts. A separate scoring script is planned to implement the automatic metrics.

## Record Structure

Each record begins with `field_values`. Its top-level fields are:

| Field | Description |
|---|---|
| `field_values` | Case identity, source texts, structured reference data, task configuration, and document metadata |
| `expert_facts` | Case-specific expert facts, keyed by fact ID; present in six records and omitted elsewhere |
| `scoring_policy` | Score levels, applicability rules, weight allocation, and policies for avoiding duplicate penalties |
| `layer_weights` | Weights assigned to the common and case-specific scoring layers |
| `metrics` | Common scoring criteria and any case-specific criteria |

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

Structured fields use JSON objects, arrays, and `null` values as stored in the dataset. Empty arrays and `null` values preserve missing-material status. Claims and descriptions correspond to the fields selected in `oa_document_versions`. The `classification_revision` field has been removed from `reject_info`.

## Languages and Materials

Chinese text in `scoring_policy`, `layer_weights`, and `metrics` has been translated into English. `field_values` and `expert_facts` retain their source-language content, including Chinese text. Each task's requested response language is specified by `field_values.eval_task_config.output_language`.

The only bundled attachment files are the 40 OA PDFs under `assets/`. Description source images, source-document PDFs referenced in text, and internal audit files are not included. Local filesystem paths and internal audit paths in the data have been replaced with explicit redaction notices. Public source URLs and document identifiers are retained. These notices identify unavailable sources and are not usable file links.

OCR text may not fully preserve equations, tables, or page layout. Redacting a source path does not supply the missing source material. All 101 entries in `cited_document_sources` currently have `content_included: false`; they provide metadata rather than cited-document full text. Any additional text actually provided is stored within the record, including `supplemental_source_documents` where present.

The records include both task materials and evaluator reference information. This data package does not prescribe a complete model-input or external-retrieval protocol; an evaluation setup must define those choices explicitly.

## Locating an OA PDF

Each OA filename is derived from `field_values.id`. All 40 PDFs are stored directly in `assets/`:

```text
assets/OAR-<id>.pdf
```

For example, case `349` in jurisdiction `CN` corresponds to `assets/OAR-349.pdf`. No external mapping file is needed. The JSONL records contain neither an OA path field nor a PDF checksum field.

## Scoring Criteria and References

Each case contains 20 common criteria:

| Group | Criteria | Type |
|---|---|---|
| RC | RC-1 through RC-5 | Automatic |
| RS | RS-1 through RS-6 | Subjective |
| RE | RE-1 through RE-5 | Subjective |
| DQ | DQ-1 through DQ-4 | Subjective |

Across the dataset, there are 800 common criteria and 12 additional case-specific S criteria in nine cases, for 812 metric entries in total. Seven expert facts are stored across six cases. S criterion IDs are local to each case.

Metric entries contain their identifier, name, type, scoring layer, and applicable scoring definitions. Depending on the metric, these include activation conditions, score levels, calculation descriptions, weights, and responsibility boundaries.

- `field_refs` contains JSON Pointers into the current record, such as `#/field_values/notify_content`.
- `reference_adapter_field_refs`, present on RC-2 through RC-5, lists the case fields read by the shared OA reference-construction logic. It describes a broader dependency set than an individual metric's `field_refs` where applicable.
- `involved_fact_ids` links S criteria to entries in the current record's `expert_facts`.

Non-automatic metrics do not contain `input_fields`, `required_input_fields`, or `source_fields_to_use_when_relevant`. Use `field_refs` to locate their stored supporting values. These references do not distinguish mandatory inputs from conditionally relevant materials.

Cases with S criteria assign 80% weight to the common layer and 20% to the case-specific layer. The other 31 cases use 100% common-layer weight. The RS `pool_weight` of `0.2` is shared across applicable RS metrics. Case-specific criteria use relative weights within their layer. Applicability conditions are preserved without predetermining scores.

Automatic metric definitions retain their existing calculation descriptions, including references to ACC/F1. Their executable calculation logic will be provided in the separate scoring script.

## Reading Example

Run this example from the directory containing `OAR-bench/`:

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

All internal JSON Pointers resolve within their own record. Reading the packaged data requires no database connection or access to the original project.
