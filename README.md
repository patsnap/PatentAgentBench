# PatentAgentBench (PAB)
A benchmark for evaluting professional patent tasks

## Datasets

- [Claim Charting Bench](claim-charting-bench/): 30 bilingual patent claim charting examples with feature-level annotations and word-level evidence alignments, provided as JSONL. [中文说明](claim-charting-bench/README.md) · [English README](claim-charting-bench/README.en.md).
- [Design FTO Bench](design-fto-bench/): 80 design patent image retrieval examples across CN and EU, provided as JSONL with PNG images. The dataset is also available on [Hugging Face](https://huggingface.co/datasets/PatSnap/design-fto-bench). Dataset files are licensed under [CC BY-NC 4.0](design-fto-bench/LICENSE).
- [Novelty Search Bench](novelty-search-bench/): 80 patent novelty search examples with examiner cited X type references, provided as JSONL. The dataset is also available on [Hugging Face](https://huggingface.co/datasets/PatSnap/novelty-search-bench). Dataset files are licensed under [CC BY-NC 4.0](novelty-search-bench/LICENSE).
- [Patent Drafting Bench](patent-drafting-bench/): 60 patent drafting tasks across CN, US, and EP, provided as JSONL with DOCX technical disclosures and general and case-specific evaluation rubrics. Each task requires claims, a specification, and necessary drawings in a single DOCX document. The dataset is also available on [Hugging Face](https://huggingface.co/datasets/PatSnap/patent-drafting-bench).
- [Patent Translation Bench](patent-translation-bench/): 881 patent translation examples across Chinese and English, provided as JSONL. The dataset is also available on [Hugging Face](https://huggingface.co/datasets/PatSnap/patent-translation-bench). Dataset files are licensed under [CC BY-NC 4.0](patent-translation-bench/LICENSE).
