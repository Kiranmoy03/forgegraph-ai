# Literature review: from maintenance text to knowledge-graph retrieval

**Project:** ForgeGraph AI  
**Review date:** 4 November 2026  
**Status:** Initial background review; research questions and methods are provisional.

## 1. Why these papers

ForgeGraph starts with short industrial maintenance records. The longer-term idea is to extract entities and relations, preserve them in a knowledge graph, and test whether the structured information helps answer maintenance-related questions. That involves four different problems:

1. Understanding short, domain-specific maintenance language.
2. Recognizing overlapping or nested entities.
3. Turning extracted information into reliable graph statements.
4. Retrieving evidence from the graph to support answers.

The four papers below were selected from established, peer-reviewed Springer Nature journals. Their publisher pages provide the full open-access articles, not just abstracts. The comments distinguish **what the authors report** from **what we might test** in ForgeGraph. A study in another domain is useful background, not proof that the same method works on MaintIE.

## 2. Papers reviewed

### Paper 1 — Understanding maintenance text

**Sundaram, S. & Zeid, A.** “Technical language processing for Prognostics and Health Management: applying text similarity and topic modeling to maintenance work orders.” *Journal of Intelligent Manufacturing*, 36, 1637–1657 (2025); published online 24 February 2024.  
**DOI:** https://doi.org/10.1007/s10845-024-02323-4  
**Full article:** https://link.springer.com/article/10.1007/s10845-024-02323-4

**Question addressed.** Can the information hidden in messy maintenance work orders be used for maintenance analysis instead of relying mainly on sensor readings?

**Approach.** The authors introduce a technical language processing (TLP) workflow informed by domain expertise. Using aircraft maintenance work orders, they examine syntactic and semantic similarity for finding related problems and corrective actions. They also use Latent Dirichlet Allocation (LDA) topic modeling to identify recurring themes and approximately associate maintenance descriptions with standardized aviation maintenance codes.

**What they found.** Preprocessing and domain knowledge matter because work orders use abbreviations, shorthand and specialized terminology. Their examples show how similarity and topic analysis can help organize the records and suggest associations with known maintenance categories.

**Limits visible in the article.** The demonstration uses an aircraft maintenance dataset rather than a broad range of industries. The authors explicitly note that short work-order phrases provide too little context to achieve perfect links to standardized codes. They also describe substantial manual effort in understanding domain terms and suggest that larger, standardized maintenance corpora would be useful.

**Relevance to ForgeGraph.** Text cleanup and domain terminology should be treated as part of the research problem, not an afterthought. The paper is *not* a nested-entity recognition or knowledge-graph question-answering benchmark; it motivates the quality of our input data rather than validating our planned extraction system.

### Paper 2 — Detecting nested entities

**Zhao, H., Bai, X., Zeng, Q., Zhou, H., & Bai, X.** “Nested Entity Recognition Method Based on Multidimensional Features and Fuzzy Localization.” *Neural Processing Letters*, 56, article 196 (2024).  
**DOI:** https://doi.org/10.1007/s11063-024-11657-2  
**Full article:** https://link.springer.com/article/10.1007/s11063-024-11657-2

**Question addressed.** How can a named-entity recognition system identify smaller entities contained within larger ones without evaluating every possible word span at excessive cost?

**Approach.** The proposed MFFL method combines character, word and part-of-speech information. It first locates likely entity regions, expands candidate windows around them, and classifies candidate spans. The model uses a multitask training objective for localization and entity classification.

**What they found.** On the authors' two Chinese-language benchmarks—the People's Daily dataset for nested recognition and the Resume dataset for non-nested recognition—the method improved results over the compared approaches and was designed to reduce the cost of enumerating spans.

**Limits / scope.** Its evaluation covers Chinese news and resume data, **not** short English industrial maintenance notes. The paper also treats identifying candidate locations as a component that can propagate mistakes, and proposes further use of partial entity-label information as future work. We cannot assume its reported gains transfer to MaintIE.

**Relevance to ForgeGraph.** Our initial notebook found 328 of 1,076 MaintIE records with at least one pair of overlapping annotated entities (30.48%). For example, “air filters” and “filters” can both be marked. A conventional *single-layer* BIO model cannot reproduce both spans simultaneously. We should compare an appropriately defined BIO baseline with a span-aware method and report performance specifically on overlapping cases.

### Paper 3 — Building graph statements from text

**Jaradeh, M. Y., Singh, K., Stocker, M., Both, A., et al.** “Information extraction pipelines for knowledge graphs.” *Knowledge and Information Systems*, 65, 1989–2016 (2023).  
**DOI:** https://doi.org/10.1007/s10115-022-01826-x  
**Full article:** https://link.springer.com/article/10.1007/s10115-022-01826-x

**Question addressed.** Can entity linking, coreference resolution, triple extraction and relation linking components be combined into effective pipelines rather than handled as isolated subtasks?

**Approach.** The Plumber framework brings together 40 reusable components and 432 possible information-extraction pipelines. It trains a transformer-based classifier to choose a suitable pipeline based on the input sentence. The evaluation covers DBpedia, Wikidata and the Open Research Knowledge Graph.

**What they found.** Dynamic selection of extraction pipelines can outperform fixed pipelines in their experiments. But the paper's detailed ablations also show that a system's final graph quality depends heavily on errors from individual components.

**Limits reported by the authors.** Overall performance is still low on difficult graph-completion cases. Triple extraction and relation linking are significant error sources, especially when relations are implicit. Some dynamic pipelines are slow; runtime optimization is outside the study's scope. These limitations are discussed explicitly in the article.

**Relevance to ForgeGraph.** We should evaluate **entity extraction**, **relation extraction**, and **graph construction** separately before evaluating the final assistant. Each proposed graph edge should retain its source record and, where possible, confidence or annotation provenance. Plumber itself is not a maintenance-specific model, and we do not need to reproduce its entire 40-component architecture.

### Paper 4 — Using a graph to answer questions

**Linders, J. & Tomczak, J. M.** “Knowledge graph-extended retrieval augmented generation for question answering.” *Applied Intelligence*, 55, article 1102 (2025).  
**DOI:** https://doi.org/10.1007/s10489-025-06885-5  
**Full article:** https://link.springer.com/article/10.1007/s10489-025-06885-5

**Question addressed.** Can an LLM answer multi-hop questions more accurately and transparently by retrieving structured graph facts and decomposing complex questions?

**Approach.** The authors propose a training-free knowledge-graph RAG method with question decomposition, candidate triple retrieval and answer synthesis. They compare against LLM-only, question-decomposition-only and LLM-plus-graph approaches on MetaQA, a movie-domain question-answering benchmark.

**What they found.** The combined approach improves multi-hop performance relative to its selected baselines and provides intermediate reasoning steps for inspection. The results are specific to the paper's configurations and benchmark.

**Limits reported by the authors.** The experiments rely on a relatively small quantized LLM and sampled test sets because of computational constraints. The MetaQA benchmark is narrow, and some nominally multi-hop questions are simpler than their labels imply. The authors also question whether the Hit@1 metric is sufficient for evaluating natural-language answers.

**Relevance to ForgeGraph.** We can later compare text-only retrieval, graph-only retrieval and graph-plus-text retrieval on carefully written maintenance questions. But the MetaQA evaluation starts from an existing graph; it does **not** show that automatically extracted, potentially incorrect maintenance triples will produce reliable answers. That distinction motivates an end-to-end test of extraction errors and answer faithfulness.

## 3. Original dataset and benchmark (essential citation)

**Bikaun, T. K., French, T., Stewart, M., Liu, W., & Hodkiewicz, M.** (2024). “MaintIE: A Fine-Grained Annotation Schema and Benchmark for Information Extraction from Maintenance Short Texts.” *Proceedings of LREC-COLING 2024*, pp. 10939–10951. Published by ELRA and ICCL; archived in the ACL Anthology.  
Paper: https://aclanthology.org/2024.lrec-main.954/  
Dataset and original benchmark: https://github.com/nlp-tlp/maintie

Although this is not an IEEE or Springer publication, **LREC-COLING is an established research conference**, and this is the direct source of our data and labels. MaintIE provides 1,076 expert-annotated short texts, 7,000 additional coarsely annotated texts, a hierarchical entity scheme and six relation types. Its authors have **already benchmarked SpERT and REBEL**. We must compare against their work where feasible rather than presenting those approaches as unexplored ideas.

### What our own exploratory notebook has established

- Loaded 1,076 records from `data/gold_release.json`.
- Counted 3,397 entity annotations and 2,341 relation annotations.
- Checked entity-span and relation-index validity: zero invalid indices found by these basic checks.
- Found 328 records with overlapping entity spans (30.48%), comprising 385 overlapping annotation pairs.

These are **exploratory observations**, not trained-model results. The checks do not validate annotation semantics, dataset independence or train/test split quality.

## 4. Comparison across the four themes

| Theme | Useful idea from the literature | What remains untested for ForgeGraph |
| --- | --- | --- |
| Maintenance language | Domain-aware text normalization and matching | Effect of normalization on MaintIE extraction quality |
| Nested entities | Localize and classify candidate spans instead of relying only on BIO tags | Accuracy, nested recall and runtime on MaintIE |
| Graph construction | Extractors can introduce errors at different stages | How often entity/relation mistakes create incorrect graph edges |
| Graph-based QA | Retrieve structured triples and decompose questions | Whether a graph built from *predicted* maintenance entities helps QA over a text-only baseline |

These are **candidate experiments**, not claims that the wider research community has never studied them.

## 5. Proposed research questions

**RQ1 — Extraction:** On MaintIE, how well does a span-aware baseline recognize broad entity classes, particularly in records containing overlaps, compared with a simple BIO baseline?

**RQ2 — Error propagation:** How do entity and relation errors change the precision and coverage of the maintenance knowledge graph? Can explicit source links help audit those edges?

**RQ3 — Retrieval and QA (later):** On a held-out set of maintenance questions, how do source-linked graph retrieval and text retrieval compare for answer correctness, evidence support, latency and computational cost?

We should only make novelty or improvement claims after implementing fair baselines and verifying the outcomes.

## 6. Experimental sequence (not completed)

1. Inspect duplicates, near-duplicates, repeated equipment phrases and the benchmark's available split guidance. Avoid training/test leakage.
2. Define an exact entity evaluation protocol: boundaries, broad versus fine labels, micro/macro precision-recall-F1, and overlap-only performance.
3. Implement a modest baseline first; then a span-aware model capable of retaining nested spans. Record seeds, configurations and runtime.
4. Test relation extraction using MaintIE's six annotated relation types. Separate gold-entity evaluation from predicted-entity evaluation.
5. Construct a small source-linked graph from extracted entities and relations. Audit sample edges manually.
6. Design maintenance questions and compare graph, text and hybrid retrieval. Assess evidence correctness and failure cases, not just fluent answers.

**Current status:** Dataset exploration completed; paper review prepared. No model training, graph construction or QA evaluation has been carried out for ForgeGraph yet.

## 7. Additional IEEE reading (not part of the four full-article reviews)

Bhardwaj, A. S., Deep, A., Veeramani, D., & Zhou, S. “A Custom Word Embedding Model for Clustering of Maintenance Records.” *IEEE Transactions on Industrial Informatics*, 18(2), 816–826 (2022). https://doi.org/10.1109/TII.2021.3079521

The IEEE publisher abstract describes an approach that combines maintenance text with industrial taxonomy information for clustering related work orders. It is relevant for a **future maintenance-text similarity baseline**, but the full published article was not used in this four-paper method-and-limitation review. We should not attribute detailed limitations or comparative numbers to it until they are independently checked against the full paper.

---

*Source review note:* The four Springer articles above were reviewed through the publisher-hosted full-text HTML (methods, experimental scope and discussion/conclusion). This document is a working project literature review, not a systematic review or a reproduction of the reported experiments. References are linked so findings can be checked against the original publications.
