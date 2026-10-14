# Patent-drafting-bench

[中文](README.md) | English

Patent-drafting-bench evaluates the patent drafting capabilities of large language models and AI agents through **60 test tasks**. Each task requires drafting claims, a specification, and necessary drawings from a technical disclosure, following the specified jurisdiction and output language. The final deliverable is a Word (`.docx`) document.

The benchmark evaluates whether a model can turn a disclosed technical solution into a complete, well-supported patent application. It covers claim completeness, clarity, scope, and dependent-claim structure; support and sufficiency of disclosure in the specification; and fabrication of technical content. Tasks include general and case-specific rubrics for item-level human review or LLM-as-a-judge evaluation.


## Directory structure

```text
Patent-drafting-bench/
├── README.md
├── README.en.md
├── data/
│   └── test.jsonl
└── asset/
    ├── PD-0005_input.docx
    └── ... (60 technical disclosure documents)
```

`test.jsonl` is UTF-8 encoded and contains one task per line, with 60 lines in total. `document_fields.input_file` is relative to the **dataset root**, for example `asset/PD-0005_input.docx`, rather than to `data/`. All attachments are included locally; no COS or S3 download is required.

## Dataset size and distribution

### Technical fields

Tasks are grouped by the IPC section recorded in their `ipc` field. Each task is counted once.

| IPC section | Technical field | Tasks | Share |
| --- | --- | ---: | ---: |
| A | Human necessities | 5 | 8.33% |
| B | Performing operations; transporting | 11 | 18.33% |
| C | Chemistry; metallurgy | 5 | 8.33% |
| D | Textiles; paper | 5 | 8.33% |
| E | Fixed constructions | 4 | 6.67% |
| F | Mechanical engineering; lighting; heating; weapons; blasting | 5 | 8.33% |
| G | Physics | 13 | 21.67% |
| H | Electricity | 12 | 20.00% |
| **Total** | **8 IPC sections** | **60** | **100%** |

Percentages are rounded to two decimal places.

### Jurisdictions and output languages

| Target jurisdiction | Tasks | Share |
| --- | ---: | ---: |
| China (CN) | 31 | 51.67% |
| United States (US) | 15 | 25.00% |
| Europe (EP) | 14 | 23.33% |
| **Total** | **60** | **100%** |

31 tasks require Chinese (`zh`) output, and 29 require English (`en`) output.

### Disclosures with and without images

| Input disclosure type | Tasks | Share |
| --- | ---: | ---: |
| With embedded images | 15 | 25.00% |
| Without embedded images | 45 | 75.00% |
| **Total** | **60** | **100%** |

This count is based on embedded images in the body of each disclosure DOCX. The 15 disclosures with images contain 17 image references in total.

All tasks require drawings in the final document when necessary. Image content is available in the original DOCX files.

## Evaluation rubrics

Each task includes general and case-specific rubrics. General criteria assess claim eligibility, unity, clarity, completeness, scope, dependent-claim structure, and support in the specification, as well as specification structure, embodiments, sufficiency of disclosure, wording, and technical consistency. Case-specific criteria assess technical features, parameters, embodiments, and subject matter described in the corresponding disclosure.

| Rubric category | Total items across all tasks | Items per task |
| --- | ---: | --- |
| General criteria | 900 | 15 |
| Case-specific criteria | 77 | 0–4 |
| **Total** | **977** | **15–19** |

Each criterion includes decision conditions, discrete score levels, and a positive integer weight. Score levels are 0, 0.5, and 1; some criteria use only 0 and 1. Evaluators select the applicable level for each criterion and calculate a score out of 100:

**Score = 100 × Σ(weight × level score) ÷ Σ(weights)**

The scoring instructions specify evidence requirements and deduction attribution. The same defect is not penalized more than once.
