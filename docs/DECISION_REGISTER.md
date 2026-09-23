# NYAYA-NS: Project Decision Register (Version 2.1)

**Document Version:** 2.1.0-LOCKED-FINAL  
**Date:** September 23, 2026  
**Status:** Canonical Record of Locked Research & Engineering Decisions  
**Governing Specification:** `docs/FINAL_LOCKED_SPECIFICATION.md`

---

## Overview

This register formally documents all locked research, architectural, and methodological decisions for **NYAYA-NS**. These decisions are binding on all subsequent development phases and may not be altered without explicit user re-authorization.

---

## Locked Decisions Summary Table

| Decision ID | Area | Locked Decision Summary | Primary Rationale |
| :--- | :--- | :--- | :--- |
| **DEC-001** | Data Policy | **Zero Synthetic Training Data** | Real-world judicial reasoning cannot be simulated reliably; synthetic records introduce artificial artifacts into training and retrieval. |
| **DEC-002** | Evaluation | **Controlled Adversarial Fixtures Permitted Only for Evaluation** | Systematic evaluation of failure modes (A1–A7) requires controlled perturbations, strictly isolated from training/retrieval corpora. |
| **DEC-003** | Methodology | **Chronological Dataset Split Policy (70/15/15 Targets & Cluster Atomicity)** | Target approx 70/15/15 by count; near-duplicate clusters assigned atomically to prevent leakage. Date boundaries, realized counts, and cluster stats logged in `split_manifest.json` without manual tuning. |
| **DEC-004** | Methodology | **Bail Task Stratification** | Fresh bail (1,084 cases) is primary homogeneous prediction benchmark; cancellation cases (116 cases) are a secondary benchmark. Report strata A–F. |
| **DEC-005** | Methodology | **Mode-1 Feature Availability Classification** | All fields classified as `PRE_DECISION`, `POST_DECISION`, or `UNKNOWN`. All post-decision reasoning fields strictly quarantined from Mode 1 inputs. |
| **DEC-006** | Architecture | **Three Explicit Operating Modes** | Strictly separates predictive tasks (Mode 1) from interpretive tasks (Mode 2) and robustness diagnostics (Mode 3). |
| **DEC-007** | Knowledge Base | **Case vs Authority Corpus Retrieval Isolation** | Test-set exclusion means test CASES are excluded from Case Corpus retrieval. Historically available authorities remain retrievable. |
| **DEC-008** | Knowledge Base | **Mandatory Authority Corpus Manifest** | Manifest tracks 15 metadata fields including `court_level`, `source_hash`, `effective_from/to`, `amendment_date`, and `savings_clause`. |
| **DEC-009** | Verification | **Authority Corpus Limitation Rule & Safe Statuses** | Corpus absence does not prove nonexistence; `VERIFIED_NONEXISTENT` forbidden. `CONFIRMED_INVALID_CITATION` strictly defined as contradicted by authoritative source; logs 8 verification fields; unresolved citations map to `UNRESOLVED` or `NOT_FOUND_IN_CORPUS`. |
| **DEC-010** | Verification | **7-Level Layered Citation Verification** | Exact hash/string matching confirms existence/provenance; semantic and symbolic checks evaluate legal validity and claim support. |
| **DEC-011** | Verification | **Structured Authority-Force & Precedent-Treatment Model** | Canonical `authority_force` (`BINDING`, `PERSUASIVE`, `NON_BINDING`, `UNKNOWN`) separated from `precedential_status` (`FOLLOWED`, `APPLIED`, `DISTINGUISHED`, etc.). Symbolic engine derives force from structured metadata; never invented by LLM. |
| **DEC-012** | Temporal Logic | **First-Class Temporal Model & Nuanced Statutory Transition** | 8 first-class temporal fields; Mode 1 cutoff ($t_{\text{auth}} \le t_{\text{case}}$); Mode 2 legal-effect analysis; dynamic CrPC/IPC to BNSS/BNS transition. |
| **DEC-013** | Knowledge Base | **Knowledge Graph vs Proof Graph Distinction** | KG is relational, global, and may have cycles; Proof Graph is a derived, strictly acyclic evidence DAG. Structural Pydantic vs engine validity. |
| **DEC-014** | Engineering | **API Contract is MUST HAVE (FastAPI Default)** | Framework-agnostic API contract defining 6 core endpoints; FastAPI is preferred/default implementation. |
| **DEC-015** | Retrieval | **Cross-Encoder Reranker is OPTIONAL / EXPERIMENTAL** | Prevents unmeasured retrieval confounding; reranker inclusion and parameters must be explicitly recorded in experiment manifests. |
| **DEC-016** | Baseline Ladder | **Integrated System (EXP-007) vs Diagnostic Components (EXP-008–013)** | EXP-007 is the integrated system benchmark; EXP-008 through EXP-013 are controlled diagnostic evaluations; EXP-015 is formal ablation. |
| **DEC-017** | Baseline Ladder | **Evidence-Oracle Diagnostic (EXP-003A)** | Supplies manually verified correct authority passages without outcome, label, or reasoning, separating retrieval from reasoning failure. |
| **DEC-018** | Evaluation | **Gold Verification Set (150–250 Examples)** | Human-reviewed ground truth across 9 citation/evidence categories with formal guidelines, adjudication, and inter-annotator agreement. |
| **DEC-019** | Evaluation | **Conflict Gold Set (EXP-011)** | Human-reviewed conflict benchmark pairs annotated under formal guidelines across 7 labels (`COMPATIBLE`, `DISTINGUISHABLE`, `TRUE_CONFLICT`, etc.). |
| **DEC-020** | Terminology | **Standardized Academic Terminology** | "Verification-Aware Research Analysis", "ABSTAIN", "Verification Status", "Historical Outcome Prediction", "Historical Outcome". |
| **DEC-021** | Leakage | **Automated Authority Leakage Audit** | Dedicated automated test script verifies temporal cutoff and statutory applicability, outputting machine-readable reports for every run. |
| **DEC-022** | Ingestion | **PDF Extraction Validation Protocol** | "Expected 1:1 mapping based on source metadata" pending Phase 4 audit validating case count, PDF count, unique names, and parsed mappings. |
| **DEC-023** | Provenance | **Canonical Data Provenance (9 Mandatory Fields)** | All records support `source_url`, `source_hash`, `acquisition_timestamp`, `dataset_version`, `pipeline_version`, `page_count`, `extraction_method/conf`. |
| **DEC-024** | Models | **Model Strategy & Empirical Sizing** | Development default: 3B local model; Final benchmark candidate: 8B 4-bit model evaluated via empirical VRAM smoke test; strict external API logging. |
| **DEC-025** | Runtime | **Python 3.11 Default Target & WSL2 Ubuntu Preferred** | Python 3.11 validated default (3.12 permitted if verified); WSL2 Ubuntu preferred for ML reproducibility; native Windows validated alternative. |
| **DEC-026** | Literature | **Primary Source Verification & Literature-Scope Audit** | Primary citations verified for NyayaRAG, NyayaAnumana/INLegalLlama (COLING 2025), LeCNet, and Supreme Court 2026 INSC 668. Strictly avoids overstated negative claims; records safe literature-scope statements. |
| **DEC-027** | Evaluation | **Distinct Metric Families (No Composite Score)** | PREDICTION, RETRIEVAL, VERIFICATION, GROUNDING, and UNCERTAINTY reported separately. No single invented composite score. |
| **DEC-028** | Scope | **UI Postponed** | All research modules, retrieval pipelines, and verification engines remain UI-independent until post-research phases. |
| **DEC-029** | Ontology | **Abstract Domain Ontology vs Data Precedents** | Named legal authorities (*Gurcharan Singh*, etc.) are data entities in the Authority Corpus, not hard-coded ontology definitions. |
| **DEC-030** | Scope | **Initial Special Statutes Bounded** | NDPS, PMLA, UAPA, POCSO represent initial benchmark regimes, not an exhaustive ontology of Indian criminal law. |
| **DEC-031** | Governance | **Hard Implementation Approval Gate** | No code, packages, models, or datasets may be installed/downloaded until explicit verbatim authorization: `APPROVED — BEGIN IMPLEMENTATION`. |
| **DEC-032** | Methodology | **Calibrated Group-Aware Near-Duplicate & Leakage Prevention** | Removes hard-coded >90% threshold. MinHash/text similarity threshold calibrated on Train/Dev inspection. Labeled: `EXACT_DUPLICATE`, `NEAR_DUPLICATE`, `RELATED_PROCEEDING`, `SAME_FIR_DIFFERENT_ACCUSED`, `DISTINCT`. Related clusters assigned atomically to one split without deleting legitimately distinct legal proceedings. |
| **DEC-033** | Architecture | **Mandatory Legal Knowledge Graph Capability (Replaceable Backend)** | Provenance-aware Legal Knowledge Graph capability is classified as **MUST HAVE** (supports EXP-006, multi-hop retrieval, applicability). Default lightweight implementation is NetworkX + SQLite; Neo4j is an optional backend. Technology is replaceable, capability is mandatory. |

---

## Detailed Decision Records

### DEC-001: Zero Synthetic Training Data
- **Status:** LOCKED
- **Decision:** NYAYA-NS strictly prohibits the creation or ingestion of synthetic legal cases, mock court orders, fabricated statutes, or synthetic training examples into any training, retrieval, or knowledge corpus.
- **Enforcement:** All data pipelines validate source hashes and provenance against official repositories.

### DEC-002: Controlled Adversarial Fixtures Permitted Only for Evaluation
- **Status:** LOCKED
- **Decision:** Adversarial evaluation fixtures (A1–A7) are permitted strictly for evaluation in Mode 3 (EXP-014). They must be derived through controlled transformations of real legal material, explicitly labeled, stored under `evaluation/adversarial_fixtures/`, and strictly barred from entering training or retrieval corpora.

### DEC-003: Chronological Dataset Split Policy (70/15/15 Targets & Cluster Atomicity)
- **Status:** LOCKED
- **Decision:** Use a chronological train/dev/test split targeting approximately **70% / 15% / 15%** of the eligible cases by count, while enforcing group-aware assignment of duplicate and near-duplicate clusters. Duplicate/near-duplicate clusters must remain entirely within a single split to prevent leakage. Because clusters must be assigned atomically, the final split proportions may deviate slightly from the 70/15/15 targets. The actual case counts, proportions, date boundaries, cluster counts, and assignment statistics must be recorded in `split_manifest.json`. Split boundaries must be determined before test evaluation and must not be manually tuned to improve model performance.
- **Core Principle:** **70/15/15 = target proportions**; **cluster atomicity = hard leakage constraint**.

### DEC-004: Bail Task Stratification
- **Status:** LOCKED
- **Decision:** The 1,200 cases contain heterogeneous bail types. Fresh bail applications (1,084 cases) represent the primary homogeneous predictive benchmark; bail cancellation cases (116 cases) are evaluated as a separate secondary benchmark. Stratified metrics across strata A–F are reported.

### DEC-005: Mode-1 Feature Availability Classification
- **Status:** LOCKED
- **Decision:** All metadata and text fields are formally audited and classified as `PRE_DECISION`, `POST_DECISION`, or `UNKNOWN`. All post-decision fields (`judgment_reason`, `summary`, `order`, `outcome`, and post-decision extracted `legal_principles_discussed`) are strictly quarantined from Mode 1 predictive inputs.

### DEC-006: Three Explicit Operating Modes
- **Status:** LOCKED
- **Decision:** Three separate modes are implemented:
  - **Mode 1 (Historical Prediction):** Input restricted to PRE_DECISION facts and metadata; temporal cutoff strictly applied; output is outcome/abstain.
  - **Mode 2 (Decision Analysis):** Full judgment text available; generates proof graph and legal report; not an outcome prediction benchmark.
  - **Mode 3 (Adversarial Verification):** Tests detection of injected evidence defects and safe failure.

### DEC-007: Case vs Authority Corpus Retrieval Isolation
- **Status:** LOCKED
- **Decision:** "Test-set exclusion" means test CASES are excluded from the Case Corpus retrieval index. Historically available authorities in the Authority Corpus remain retrievable based on historical availability and legal applicability.

### DEC-008: Mandatory Authority Corpus Manifest
- **Status:** LOCKED
- **Decision:** Every authority tracks 15 metadata fields including `authority_id`, `court_level`, `source_hash`, `effective_from/to`, `amendment_date`, `savings_clause`, and version identifiers to guarantee scientific provenance.

### DEC-009: Authority Corpus Limitation Rule & Safe Citation Verification Statuses
- **Status:** LOCKED
- **Decision:** Absence from the Authority Corpus does NOT imply legal nonexistence. Do not use `VERIFIED_NONEXISTENT`. Do not claim that the system can prove universal nonexistence of a legal authority. Corpus absence alone is insufficient to classify a citation as `CONFIRMED_INVALID_CITATION`. If unresolved, use `UNRESOLVED` or `NOT_FOUND_IN_CORPUS` as appropriate.
- **Canonical Statuses Enforced:**
  1. `FOUND_AND_RESOLVED`: Authority exists and resolves in trusted corpus.
  2. `NOT_FOUND_IN_CORPUS`: Authority not present in current indexed corpus (does NOT assert legal nonexistence).
  3. `AMBIGUOUS`: Multiple conflicting authorities match citation string.
  4. `UNRESOLVED`: Citation string cannot be parsed into canonical components.
  5. `CONFIRMED_INVALID_CITATION`: A citation whose claimed legal authority identity or citation details are contradicted by an authoritative source appropriate to that authority type after canonicalization and resolution attempts.
  6. `OFFICIAL_SOURCE_MISMATCH`: Citation resolves but cited party/order details mismatch official record.
  7. `FIXTURE_FABRICATED`: Assigned strictly to controlled adversarial fixtures generated for evaluation.
- **Enforcement Rules:**
  - **Explicit Corpus Absence Rule:** Corpus absence alone is insufficient to classify a citation as `CONFIRMED_INVALID_CITATION`.
  - For every `CONFIRMED_INVALID_CITATION`, the verification engine records, where available:
    1. `canonicalized_citation`
    2. `verification_basis`
    3. `authoritative_verification_source`
    4. `verification_timestamp`
    5. `resolver_version` (or resolver/verifier version)
    6. `source_passage` (source passage containing the citation)
    7. `contradictory_metadata_or_passage` (relevant contradictory metadata or passage)
    8. `verification_confidence`

### DEC-010: 7-Level Layered Citation Verification
- **Status:** LOCKED
- **Decision:** Verification is structured in 7 levels: (1) Source Existence, (2) Citation Resolution, (3) Passage Attribution, (4) Claim-Support Verification, (5) Temporal Verification, (6) Jurisdiction/Hierarchy Verification, (7) Proof-Path Verification.

### DEC-011: Structured Authority-Force & Precedent-Treatment Model
- **Status:** LOCKED
- **Decision:** The simplified binding/persuasive/non-binding rule is replaced with a comprehensive, structured authority model. Generative models must never freely invent authority force; the symbolic layer derives it from structured metadata and rules, returning `UNKNOWN` where information is insufficient.
- **Canonical `authority_force`:** `BINDING`, `PERSUASIVE`, `NON_BINDING`, `UNKNOWN`.
- **Separate `precedential_status`:** `FOLLOWED`, `APPLIED`, `DISTINGUISHED`, `DOUBTED`, `REFERRED`, `OVERRULED`, `MODIFIED`, `NOT_ANALYZED`.
- **`authority_force_basis` Schema:** Tracks `court_hierarchy`, `jurisdiction`, `same_high_court`, `bench_strength`, `later_treatment`, `temporal_status`, and `procedural_context`.
- **Governing Rules:**
  1. Supreme Court precedent is treated according to applicable constitutional precedent rules (Article 141) and subsequent bench-strength treatment.
  2. Decisions of the same High Court require same-court precedent discipline and bench-strength analysis.
  3. An earlier coordinate-bench decision of the same High Court must NOT automatically be classified merely as `PERSUASIVE`.
  4. Decisions from other High Courts may generally be persuasive rather than binding, subject to applicable legal context.
  5. A `DISTINGUISHED` authority must NOT automatically be classified `NON_BINDING`. "Distinguished" describes applicability to the current facts/issues, not the general precedential force of the underlying authority.
  6. Larger-bench and later-treatment information must be represented separately.
  7. Generative models must never freely invent authority force. The symbolic layer derives it from structured metadata/rules and may return `UNKNOWN` where information is insufficient.
- **Explicit Scope Limitation:** The system does NOT claim that this finite ruleset perfectly resolves all Indian precedent questions; edge cases default safely to `UNKNOWN`.

### DEC-012: First-Class Temporal Model & Nuanced Statutory Transition
- **Status:** LOCKED
- **Decision:** Pipeline tracks 8 first-class temporal fields. Mode 1 enforces information cutoff ($t_{\text{auth}} \le t_{\text{case}}$). Mode 2 performs legal-effect analysis. CrPC/IPC vs BNSS/BNS transitions are evaluated dynamically based on offence date, effective date, and savings clauses.

### DEC-013: Knowledge Graph vs Proof Graph Distinction
- **Status:** LOCKED
- **Decision:** The Legal Knowledge Graph is a global relational graph that may contain cycles; the Proof Graph is a query-specific evidence DAG that must be strictly acyclic. Pydantic enforces structural DAG validity; the verification engine evaluates evidentiary and legal validity.

### DEC-014: API Contract is MUST HAVE (FastAPI Default)
- **Status:** LOCKED
- **Decision:** The API service boundary is a mandatory requirement exposing 6 core endpoints (`/ingest`, `/cases/{id}`, `/retrieve`, `/verify/citation`, `/analyze`, `/proofs/{id}`). FastAPI is the preferred implementation; the contract is framework-agnostic.

### DEC-015: Cross-Encoder Reranker is OPTIONAL / EXPERIMENTAL
- **Status:** LOCKED
- **Decision:** BAAI/bge-reranker-v2-m3 is classified as optional/experimental. If included in any experiment, its inclusion and parameters must be explicitly recorded in the run manifest.

### DEC-016: Integrated System (EXP-007) vs Diagnostic Components (EXP-008–013)
- **Status:** LOCKED
- **Decision:** EXP-007 is the full integrated system benchmark; EXP-008 through EXP-013 are controlled diagnostic evaluations isolating individual reasoning components represented in EXP-007; EXP-015 is formal ablation.

### DEC-017: Evidence-Oracle Diagnostic (EXP-003A)
- **Status:** LOCKED
- **Decision:** EXP-003A supplies manually verified correct authority passages without outcome, label, or reasoning, cleanly separating retrieval failures from reasoning/verification failures.

### DEC-018: Gold Verification Set (150–250 Examples)
- **Status:** LOCKED
- **Decision:** A manually verified ground truth dataset created by human review under strict annotation guidelines across 9 citation/evidence categories, stored under `data/GOLD/verification_gold.jsonl`.

### DEC-019: Conflict Gold Set (EXP-011)
- **Status:** LOCKED
- **Decision:** Human-reviewed conflict benchmark pairs annotated under formal guidelines across 7 labels (`COMPATIBLE`, `DISTINGUISHABLE`, `TRUE_CONFLICT`, etc.), stored under `data/GOLD/conflict_gold.jsonl`.

### DEC-020: Standardized Academic Terminology
- **Status:** LOCKED
- **Decision:** Canonical terms enforced: "Verification-Aware Research Analysis", "ABSTAIN", "Verification Status", "Historical Outcome Prediction", "Historical Outcome".

### DEC-021: Automated Authority Leakage Audit
- **Status:** LOCKED
- **Decision:** Dedicated automated test verifies $\text{authority.decision\_date} \le \text{query.decision\_date}$ and statutory applicability, outputting machine-readable reports for every evaluation run.

### DEC-022: PDF Extraction Validation Protocol
- **Status:** LOCKED
- **Decision:** Termed "Expected 1:1 mapping based on source metadata" pending Phase 4 audit validating case count, PDF count, unique names, parsed count, and validated mappings.

### DEC-023: Canonical Data Provenance (9 Mandatory Fields)
- **Status:** LOCKED
- **Decision:** All records support `source_url`, `source_hash`, `acquisition_timestamp`, `dataset_version`, `pipeline_version`, `language`, `page_count`, `extraction_method`, and `extraction_confidence`.

### DEC-024: Model Strategy & Empirical Sizing
- **Status:** LOCKED
- **Decision:** Development default is 3B local model; final benchmark candidate is 8B 4-bit model evaluated via empirical VRAM smoke test; external API fallback requires strict metadata logging.

### DEC-025: Python 3.11 Default Target & WSL2 Ubuntu Preferred
- **Status:** LOCKED
- **Decision:** Python 3.11 is the default validated target (3.12 permitted if verified); WSL2 Ubuntu is the preferred ML runtime for reproducibility; native Windows is a validated alternative.

### DEC-026: Primary Source Verification & Literature-Scope Statement
- **Status:** LOCKED
- **Decision:** All prior art comparisons and literature claims must strictly cite verified primary sources and avoid overstated negative claims. Categorical assertions that prior works "lack" or operate "without" capabilities must be replaced with literature-scope formulations ("does not describe or evaluate...", "is not reported as evaluating...").
- **Locked Research Gap:**
  > Existing work demonstrates components such as legal judgment prediction, RAG, legal knowledge graphs, structured reasoning, and legal citation handling. The surveyed literature does not establish whether a domain-bounded, verification-first neuro-symbolic pipeline for Indian bail analysis combining authority applicability, temporal validity, source/passage verification, proof graphs, and calibrated abstention produces measurable reliability gains over progressively stronger RAG baselines.
- **Verified References & Scope Statements:**
  - **NyayaRAG:** Shubham Kumar Nigam et al., *arXiv:2508.00709* (AACL-IJCNLP 2025; submission Aug 2025). PRIMARY SOURCE VERIFIED. *Literature-Scope Statement:* The cited work investigates dense semantic retrieval but does not describe or evaluate explicit verification-first mechanisms such as passage-level citation verification, symbolic temporal gating, provenance-aware proof graphs, or calibrated abstention.
  - **NyayaAnumana & INLegalLlama:** Shubham Kumar Nigam et al., *"The Largest Indian Legal Judgment Prediction Dataset and Specialized Language Model for Enhanced Decision Analysis"*, Proceedings of the 31st International Conference on Computational Linguistics (COLING 2025), pages 11094–11109; *arXiv:2412.08385*; ACL Anthology: `2025.coling-main.738`; initial submission Dec 11, 2024. Treated as dataset and model contributions from the same paper. PRIMARY SOURCE VERIFIED. *Literature-Scope Statement:* The cited paper does not describe or evaluate the explicit verification-first mechanisms targeted by NYAYA-NS, including passage-level citation verification, symbolic temporal gating, provenance-aware proof graphs, or calibrated verification-aware abstention.
  - **LeCNet:** Vijit Malik et al., *"ILDC for CJPE: Indian Legal Documents Corpus for Court Judgment Prediction and Explanation"*, ACM / GitHub (`Law-AI/LeCNet`), 2022/2023. PRIMARY SOURCE VERIFIED. *Literature-Scope Statement:* The cited work focuses on citation network analysis and topological link prediction; it is not reported as evaluating neuro-symbolic verification, passage-level citation verification, or bail decision workflows.
  - **Pooja Ramesh Singh Directives on AI Precedents:** *Pooja Ramesh Singh v. Jammu and Kashmir Bank Ltd. & Anr.*, Supreme Court of India, Civil Appeal No. 11950 of 2025; **2026 INSC 668**; decided July 2, 2026; Bench: Justice Pamidighantam Sri Narasimha, Justice Alok Aradhe. PRIMARY SOURCE VERIFIED.
    - *Source Verification:* The case metadata is verified against a Supreme Court of India source (`2026 INSC 668` / `Civil Appeal No. 11950 of 2025`). The judgment text is cross-checked against an independent legal database. Indian Kanoon must not be described as an official Supreme Court portal.
    - *Preserved Metadata:* Civil Appeal No. 11950 of 2025; 2026 INSC 668; decided July 2, 2026; bench: P.S. Narasimha and Alok Aradhe JJ.; primary portal: `https://main.sci.gov.in/` / `https://digiscr.sci.gov.in/`.
    - *Three-Part Analytical Distinction:* (1) What Supreme Court source establishes: Setting aside orders based on fake citations ("no decision in the eyes of law", advocate misconduct); (2) What independent database cross-check establishes: Confirms full judgment text and origin under Section 7 IBC (not a bail authority); (3) What NYAYA-NS uses case to motivate: Motivates Level 1–4 citation verification pipeline.

### DEC-027: Distinct Metric Families (No Composite Score)
- **Status:** LOCKED
- **Decision:** PREDICTION, RETRIEVAL, VERIFICATION, GROUNDING, and UNCERTAINTY metrics are reported separately. No single invented composite score is used to rank systems.

### DEC-028: UI Postponed
- **Status:** LOCKED
- **Decision:** All research components remain completely headless and UI-independent. UI specifications will be addressed post-research.

### DEC-029: Abstract Domain Ontology vs Data Precedents
- **Status:** LOCKED
- **Decision:** Domain ontology represents abstract legal issues (`GravityOfOffence`, `FlightRisk`, `Parity`, etc.). Specific precedent rulings are data nodes in the Authority Corpus.

### DEC-030: Initial Special Statutes Bounded
- **Status:** LOCKED
- **Decision:** NDPS, PMLA, UAPA, and POCSO are explicitly documented as initial special statutory regimes represented in the benchmark.

### DEC-031: Hard Implementation Approval Gate
- **Status:** LOCKED
- **Decision:** No code implementation, package installation, model acquisition, or dataset downloading will occur until the user provides the verbatim instruction: `APPROVED — BEGIN IMPLEMENTATION`.

### DEC-032: Empirically Calibrated Group-Aware Near-Duplicate & Leakage Prevention Policy
- **Status:** LOCKED
- **Decision:** Permanently hard-coded similarity thresholds (such as `> 90% factual overlap`) are rejected unless empirically validated.
- **Candidate Detection:** MinHash and/or semantic text similarity.
- **Threshold Selection:** Selected using manually inspected candidate pairs during TRAIN/DEV methodology development and frozen before final TEST evaluation.
- **Duplicate Relationship Taxonomy:**
  1. `EXACT_DUPLICATE`: Verbatim or hash-identical case text.
  2. `NEAR_DUPLICATE`: Substantially identical text with trivial formatting variations.
  3. `RELATED_PROCEEDING`: Subsequent bail stage, co-accused petition, or appeal arising from same FIR/crime.
  4. `SAME_FIR_DIFFERENT_ACCUSED`: Separate application by distinct accused in identical criminal case.
  5. `DISTINCT`: Legally and factually independent judicial decisions.
- **Leakage Prevention vs Deletion:** Legally related proceedings are NOT automatically deleted; the purpose of deduplication is leakage prevention, not removing legitimate judicial decisions.
- **Group-Aware Split Assignment:** When a duplicate or near-duplicate cluster crosses a proposed chronological split boundary, enforce deterministic group-aware atomic assignment so related duplicate records remain entirely within a single split (Train, Dev, or Test) to prevent cross-split leakage. Because clusters must be assigned atomically, final split proportions may deviate slightly from 70/15/15 targets. The actual case counts, proportions, date boundaries, cluster counts, and assignment statistics must be recorded in `split_manifest.json`.
- **Core Principle:** **70/15/15 = target proportions**; **cluster atomicity = hard leakage constraint**.
- **Tracking Schema:** Records `duplicate_cluster_id`, `duplicate_relation`, `similarity_method`, `similarity_score`, and `review_status`.

### DEC-033: Mandatory Legal Knowledge Graph Capability (Replaceable Backend)
- **Status:** LOCKED
- **Decision:** Provenance-aware Legal Knowledge Graph capability is classified as a **MUST HAVE** architectural requirement. It is mandatory because:
  1. EXP-006 directly evaluates graph-augmented retrieval against dense and hybrid baselines.
  2. The full NYAYA-NS architecture (EXP-007) requires structured legal knowledge representation.
  3. Authority applicability, multi-hop reasoning, and provenance tracing depend on structured legal relationships.
- **Backend Technology Decoupling:**
  - **MUST HAVE:** Provenance-aware Legal Knowledge Graph capability supporting required nodes (statutes, provisions, precedents, legal issues, courts), edges, provenance, and graph traversals.
  - **DEFAULT LIGHTWEIGHT IMPLEMENTATION:** NetworkX + SQLite or equivalent (in-memory graph with relational metadata persistence).
  - **OPTIONAL ALTERNATIVE:** Neo4j or another compatible graph database backend.
  - **FUTURE SCALE OPTION:** Distributed graph infrastructure.
  - The capability itself must NOT be classified as optional.

