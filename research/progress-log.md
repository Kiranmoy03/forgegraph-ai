# ForgeGraph AI — Research and development log

A short record of completed work, observations and decisions. Planned tasks are kept separate from results.

## 3 November 2026 — Initial dataset exploration

**Source:** [MaintIE dataset](https://github.com/nlp-tlp/maintie) (Bikaun et al., LREC-COLING 2024).  
**Notebook:** [`ForgeGraph_01_Dataset_Exploration.ipynb`](../ForgeGraph_01_Dataset_Exploration.ipynb)

Completed:
- Loaded the 1,076 records in `gold_release.json` using Python.
- Inspected text, token, entity-span and relation fields.
- Counted 3,397 entity annotations and 2,341 relation annotations.
- Checked that annotated spans and relation indices are in range; no invalid indices were found.
- Sampled additional maintenance notes to see different entity categories.
- Counted 328 records with overlapping entity annotations (30.48% of all records), involving 385 overlapping pairs.

**Observation:** Some entity spans are nested—for instance, both `air filters` and `filters` are annotated. A standard single-layer BIO label sequence cannot represent both at once. This suggests that we should evaluate a span-aware entity extraction approach, while keeping a simple baseline for comparison.

**What has not been done:** No train/validation/test split, model training, model evaluation, relation predictor or knowledge graph.

## 4 November 2026 — Background literature review

Prepared [`literature-review.md`](literature-review.md), organized around four topics:
- Maintenance language and domain-aware processing.
- Nested entity recognition.
- Entity/relation extraction for knowledge graphs.
- Knowledge-graph retrieval and question answering.

The note reviews four peer-reviewed, open-access Springer journal papers, with a separate citation for the original MaintIE dataset and a supplementary IEEE reference. These are preliminary literature notes; they do not establish an original research contribution.

**Current direction:** Start with reproducible entity extraction experiments that preserve nested spans. Study relation extraction, graph construction and evidence-grounded question answering afterward.


## 4 November 2026 — Dataset preparation

**Notebook:** `ForgeGraph_02_Dataset_Preparation.ipynb`

Completed:
- Checked 1,076 maintenance records for repeated texts.
- Identified two duplicate groups and retained 1,074 unique records.
- Found 127 potentially similar record pairs using Jaccard similarity.
- Grouped 156 related records into 54 connected groups.
- Created a group-aware train/validation/test split.
- Checked the distribution of entity categories and overlapping annotations.

### Dataset split (v1)

| Split | Records |
| --- | ---: |
| Training | 859 |
| Validation | 107 |
| Testing | 108 |

All five entity categories are represented in each split. Similarity groups are kept separate across splits.

Overlapping entities occur in 29.80% of training records, 30.84% of validation records, and 35.19% of test records.

### Observations

The Property category is rare, with only four annotations in each of the validation and test sets. Results for this category will need careful interpretation.

The similarity checks are approximate and do not guarantee that every possible source of data leakage has been eliminated.



## Next tasks — Not yet completed

- Finalize the evaluation protocol, including precision, recall, F1-score, and overlapping-entity performance.
- Decide how broad and fine-grained entity labels will be handled.
- Prepare the saved dataset splits for model training.
- Train and evaluate an initial entity-recognition baseline.
- Compare baseline results with a span-based approach capable of recognizing overlapping entities.


### Reporting rule

Every future log entry should state what was actually run, on which data, and with which outputs. Keep proposed features and untested hypotheses in a separate section; add model scores only when they come from a saved, reproducible run.
