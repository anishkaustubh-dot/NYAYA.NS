# NYAYPRAMANA: Implementation Readiness & Technical Audit Report (Version 2.1)

**Document Version:** 2.1.0-AUDIT-FINAL  
**Date:** September 23, 2026  
**Project Name:** NYAYPRAMANA (A Verification-First Neuro-Symbolic Framework for Indian Bail Decision Analysis)  
**Authoritative Specification:** `docs/FINAL_LOCKED_SPECIFICATION.md`  
**Current Gate Status:** **STEP 1 — ANALYSIS & SPECIFICATION ONLY (IMPLEMENTATION PERMISSION: NOT GRANTED)**

---

## 1. Executive Summary

This report delivers an exhaustive, evidence-grounded pre-implementation research and technical audit for **NYAYPRAMANA**, fully updated with all methodological, architectural, and experimental corrections. The audit was conducted strictly in accordance with user instructions. No implementation code, environment mutation, package installation, model acquisition, synthetic training data generation, or dataset downloading was performed.

### Key Audit Findings at a Glance:
1. **Scope & Research Grounding:** NYAYPRAMANA is an academic research prototype focused strictly on **Indian bail decision analysis**. It investigates whether neuro-symbolic verification (multi-signal authority applicability, dual-level temporal reasoning, layered citation/passage verification, proof graphs, and calibrated abstention) provides measurable reliability improvements over progressively stronger RAG baselines.
2. **Three Explicit Operating Modes:**
   - **Mode 1 (Historical Outcome Prediction):** Evaluates historical predictive performance under strict temporal information cutoffs using only verified pre-decision information.
   - **Mode 2 (Decision Analysis):** Analyzes existing judgments to evaluate legal issue extraction, argument mapping, temporal validity, and proof-graph construction (not an outcome prediction benchmark).
   - **Mode 3 (Adversarial Verification):** Evaluates whether the system fails safely and detects corrupted or incomplete evidence using controlled evaluation fixtures.
3. **Bail Task Stratification:**
   - The primary dataset contains heterogeneous decision types (fresh bail, regular bail, anticipatory bail, interim bail, cancellation cases).
   - **Primary Headline Benchmark:** Fresh-bail outcome prediction (1,084 cases; homogeneous predictive task).
   - **Secondary Benchmark:** Bail cancellation cases (116 cases; separately reported).
   - Stratified evaluation across strata A–F (Overall, Fresh, Cancellation, Regular, Anticipatory, Interim/Other) prevents silent task conflation.
4. **Dataset Split Policy & Group-Aware Deduplication:**
   - Use a chronological train/dev/test split targeting approximately **70% / 15% / 15%** of the eligible cases by count, while enforcing group-aware assignment of duplicate and near-duplicate clusters. Duplicate/near-duplicate clusters must remain entirely within a single split to prevent leakage. Because clusters must be assigned atomically, the final split proportions may deviate slightly from the 70/15/15 targets. The actual case counts, proportions, date boundaries, cluster counts, and assignment statistics must be recorded in `split_manifest.json`. Split boundaries must be determined before test evaluation and must not be manually tuned to improve model performance.
   - **Core Principle:** **70/15/15 = target proportions**; **cluster atomicity = hard leakage constraint**.
5. **Near-Duplicate Policy & Group-Aware Leakage Prevention:**
   - Replaces permanently hard-coded similarity thresholds (such as `> 90% factual overlap`) with candidate detection via MinHash/text similarity and a threshold calibrated on Train/Dev inspection.
   - Evaluates duplicate relationships: `EXACT_DUPLICATE`, `NEAR_DUPLICATE`, `RELATED_PROCEEDING`, `SAME_FIR_DIFFERENT_ACCUSED`, and `DISTINCT`.
   - Cluster boundaries crossing proposed split lines trigger deterministic group-aware atomic assignment to prevent cross-split leakage without deleting legitimately distinct legal proceedings.
   - Pipeline records: `duplicate_cluster_id`, `duplicate_relation`, `similarity_method`, `similarity_score`, and `review_status`.
6. **Mode-1 Feature Availability Classification:**
   - All fields are classified into `PRE_DECISION`, `POST_DECISION`, or `UNKNOWN`.
   - Fields containing post-decision reasoning (`judgment_reason`, `summary`, `order`, `outcome`, and post-decision extracted `legal_principles_discussed`) are strictly quarantined from Mode 1 predictive inputs.
7. **Separation of Case Corpus and Authority Corpus:**
   - **Case Corpus:** Historical bail case records (`IndianBailJudgments-1200`), facts, procedural history, and case passages. In Mode 1, test-set exclusion applies strictly to the Case Corpus (test cases are excluded from the retrieval index).
   - **Authority Corpus:** Binding and persuasive authorities (Supreme Court and High Court precedents), versioned statutes, and provisions. Historically available authorities are retrieved based on decision date and legal applicability, regardless of split.
8. **Corpus Coverage & Safe Citation Verification Statuses:**
   - "Not found in Authority Corpus" (`NOT_FOUND_IN_CORPUS`) is strictly distinguished from confirmed invalid citations. Absence from the current Authority Corpus does NOT prove legal nonexistence. Do not use `VERIFIED_NONEXISTENT`; do not claim that the system can prove universal nonexistence of a legal authority.
   - Canonical statuses: `FOUND_AND_RESOLVED`, `NOT_FOUND_IN_CORPUS`, `AMBIGUOUS`, `UNRESOLVED`, `CONFIRMED_INVALID_CITATION`, `OFFICIAL_SOURCE_MISMATCH`, and `FIXTURE_FABRICATED` (for evaluation fixtures only).
   - `CONFIRMED_INVALID_CITATION` is strictly defined as: *A citation whose claimed legal authority identity or citation details are contradicted by an authoritative source appropriate to that authority type after canonicalization and resolution attempts.*
   - **Explicit Corpus Absence Rule:** Corpus absence alone is insufficient to classify a citation as `CONFIRMED_INVALID_CITATION`. If unresolved, use `UNRESOLVED` or `NOT_FOUND_IN_CORPUS` as appropriate.
   - For every `CONFIRMED_INVALID_CITATION`, the verification engine records, where available: `canonicalized_citation`, `verification_basis`, `authoritative_verification_source`, `verification_timestamp`, `resolver_version`, `source_passage`, `contradictory_metadata_or_passage`, and `verification_confidence`.
9. **Structured Authority-Force & Precedent-Treatment Model:**
   - Canonical `authority_force`: `BINDING`, `PERSUASIVE`, `NON_BINDING`, `UNKNOWN`.
   - Separate `precedential_status`: `FOLLOWED`, `APPLIED`, `DISTINGUISHED`, `DOUBTED`, `REFERRED`, `OVERRULED`, `MODIFIED`, `NOT_ANALYZED`.
   - `authority_force_basis` tracks: `court_hierarchy`, `jurisdiction`, `same_high_court`, `bench_strength`, `later_treatment`, `temporal_status`, and `procedural_context`.
   - Governing Rules: (1) Supreme Court precedent follows Article 141 and subsequent treatment; (2) Same High Court requires same-court precedent discipline and bench-strength analysis; (3) Earlier coordinate-bench decisions of same High Court must NOT automatically be classified merely as PERSUASIVE; (4) Other High Courts generally persuasive; (5) A DISTINGUISHED authority must NOT automatically be classified NON_BINDING (distinction reflects factual applicability, not constitutional force); (6) Larger-bench and later-treatment information represented separately; (7) Generative models barred from inventing authority force (symbolic engine derives it; defaults to UNKNOWN). Finite ruleset does not claim to resolve all edge cases.
10. **Layered Citation Verification Pipeline:**
    - 7-level pipeline: Source Existence $\rightarrow$ Citation Resolution $\rightarrow$ Passage Attribution $\rightarrow$ Claim-Support Verification $\rightarrow$ Temporal Verification $\rightarrow$ Jurisdiction/Hierarchy Verification $\rightarrow$ Proof-Path Verification. Hash/string matching confirms existence/provenance; semantic/symbolic checks evaluate legal support.
11. **Knowledge Graph vs Proof Graph Distinction:**
    - **Legal Knowledge Graph (MUST HAVE Capability):** Relational property graph, may contain cycles, represents domain entities and relationships. Classified as **MUST HAVE** (supports EXP-006, full NYAYPRAMANA, and multi-hop applicability). Backend technology is decoupled: default lightweight implementation is NetworkX + SQLite; Neo4j remains an optional backend; distributed graph is future scale option.
    - **Proof Graph:** Derived evidence DAG, strictly acyclic, represents support paths for conclusions. Pydantic validates structural schema constraints; the verification engine evaluates evidentiary/legal validity.
12. **Hardware & Execution Strategy:**
    - Host GPU: NVIDIA GeForce RTX 3050 Laptop GPU (6,144 MiB VRAM).
    - **Development Default:** 3B-class local instruction model (`Llama-3.2-3B-Instruct` or `Qwen2.5-3B-Instruct`).
    - **Final Benchmark Candidate:** 8B-class 4-bit model (`Llama-3.1-8B-Instruct` GGUF) *if and only if* an empirical VRAM smoke test succeeds.
    - Sequential decoupled execution (embed $\rightarrow$ flush GPU memory $\rightarrow$ load quantized LLM) prevents VRAM over-allocation.
    - **Storage Placement:** Virtual environments, Hugging Face cache (`HF_HOME=D:\hf_cache`), and dataset archives reside on **Drive D:** (205 GB free), preserving Drive C: (30.4 GB free).
13. **Runtime Environment & API Contract:**
    - Default validated target: **Python 3.11** (Python 3.12 permitted if dependencies verified).
    - Preferred runtime: **WSL2 Ubuntu** for scientific reproducibility; native Windows PowerShell remains a validated alternative.
    - **API Contract is MUST HAVE:** Framework-agnostic API contract with FastAPI as default implementation exposing 6 core endpoints.

---

## 2. Project Understanding

### 2.1 Research Question
> *How can neural legal language models be constrained by structured legal knowledge and symbolic verification so that their Indian bail analyses are more grounded, temporally valid, traceable, and resistant to hallucinated authority?*

### 2.2 Core Hypotheses
- **H1 (Grounding):** NYAYPRAMANA significantly reduces unsupported legal claims and citations compared to LLM-only and standard semantic RAG baselines.
- **H2 (Layered Citation Verification):** Layered passage-level verification (existence $\rightarrow$ resolution $\rightarrow$ attribution $\rightarrow$ semantic claim support) significantly reduces fabricated, misattributed, or misquoted citations.
- **H3 (Applicability):** Multi-signal legal ranking (statutory provision, legal issue match, court hierarchy, procedural posture) outperforms purely dense semantic similarity in ranking governing authorities.
- **H4 (Temporal Reasoning):** Symbolic temporal information cutoffs eliminate future-precedent and anachronistic statutory leakage in historical prediction, while temporal legal-effect analysis correctly contextualizes precedent evolution.
- **H5 (Reasoning Validity):** Provenance-aware proof graphs increase the proportion of legal conclusions backed by unbroken, verifiable evidence chains.
- **H6 (Calibrated Abstention):** Verification-aware abstention improves selective accuracy on incomplete, conflicting, or adversarial cases at a measurable cost in coverage.
- **H7 (Prediction Performance):** The full verification-first architecture maintains or improves historical bail outcome prediction over progressively stronger RAG baselines. (Negative results will be reported with full scientific honesty).

### 2.3 Legal Domain Bounding
- Domain: **Indian Criminal Law — Bail Decision Analysis**.
- Governing statutory frameworks:
  - Code of Criminal Procedure, 1973 (CrPC, specifically Sections 436, 437, 438, 439, 441)
  - Indian Penal Code, 1860 (IPC)
  - Initial Special Statutes represented in benchmark: Narcotic Drugs and Psychotropic Substances Act, 1985 (NDPS §37); Prevention of Money Laundering Act, 2002 (PMLA §45); Unlawful Activities (Prevention) Act, 1967 (UAPA §43D(5)); Protection of Children from Sexual Offences Act, 2012 (POCSO)
  - Bharatiya Nagarik Suraksha Sanhita, 2023 (BNSS) & Bharatiya Nyaya Sanhita, 2023 (BNS) for transition-regime reasoning.

### 2.4 Explicit Exclusions ("What It Is Not")
- NOT an autonomous judge or automated judicial granting tool.
- NOT an AI lawyer or legal-advice system.
- NOT an open-ended general Indian law system.
- NOT a generic unverified RAG chatbot.
- Historical outcome prediction is solely an empirical benchmark metric, not a normative recommendation.

---

## 3. Locked Requirements

1. **Zero Synthetic Training Data:** Synthetic legal cases, judgments, statutes, or training examples are strictly prohibited. Controlled adversarial evaluation fixtures (A1–A7) are permitted strictly for evaluation, labeled explicitly, and stored separately under `evaluation/adversarial_fixtures/`.
2. **Authoritative Benchmark:** `IndianBailJudgments-1200` is the primary evaluation benchmark.
3. **Data Progression Architecture:** Strict adherence to `RAW` (immutable source files/hashes) $\rightarrow$ `BRONZE` (clean extracted text/OCR) $\rightarrow$ `SILVER` (structured entities, passages, citations, KG) $\rightarrow$ `GOLD` (frozen chronological manifests, verified proof chains, adversarial benchmarks).
4. **Canonical Uncertainty & Verification States:**
   - `VERIFIED`
   - `PARTIALLY_VERIFIED`
   - `UNVERIFIED`
   - `CONFLICTING`
   - `INSUFFICIENT_EVIDENCE`
   - `HUMAN_REVIEW_REQUIRED`
5. **Separation of Corpora:** Clean operational separation between the **Case Corpus** (historical bail case facts and passages) and the **Authority Corpus** (statutes, provisions, and binding/persuasive precedent judgments).
6. **Separation of Three System Modes:**
   - Mode 1: Historical Outcome Prediction (predictive, strict pre-decision cutoff).
   - Mode 2: Decision Analysis (interpretive, full judgment available, proof-graph generation).
   - Mode 3: Adversarial Verification (diagnostic, evaluates robust failure modes).
7. **Bail Task Stratification:** Fresh bail is the primary homogeneous predictive benchmark; cancellation cases are evaluated as a separate secondary task. Stratified results across all types are reported.
8. **Legal Knowledge Graph Capability is MUST HAVE:** Provenance-aware knowledge graph capability is mandatory; NetworkX + SQLite is the default lightweight implementation.
9. **Dev-Set Calibration:** All threshold tuning and abstention calibration are conducted on the Development split; Test split remains strictly frozen.
10. **API Contract is MUST HAVE:** Framework-agnostic API contract with FastAPI as the default implementation.
11. **Hard Implementation Gate:** No code execution, package installation, model download, or dataset download prior to explicit user input: `APPROVED — BEGIN IMPLEMENTATION`.

---

## 4. Current Workspace & Hardware Audit

| Component | Host Specification | Audit Finding & Impact |
| :--- | :--- | :--- |
| **Operating System** | Windows 11 Home Single Language (10.0.26200) | Native Windows environment; WSL2 Ubuntu 2 available (preferred for ML reproducibility). |
| **System Memory (RAM)** | 16,473,912 KB (~15.71 GB total); ~4.52 GB free | Sufficient for orchestration, FAISS indexing, and BM25; tight for concurrent heavy processes. |
| **GPU** | NVIDIA GeForce RTX 3050 Laptop GPU | Discrete mobile GPU with 6,144 MiB (6.0 GB) VRAM. Driver version: 610.62; CUDA: 13.3. |
| **GPU VRAM Capacity** | **6,144 MiB (6.0 GB)** | An 8B parameter model in FP16 (~16 GB) cannot run locally. In 4-bit quantization (GGUF Q4_K_M / bitsandbytes NF4), it consumes ~4.8–5.2 GB VRAM. Concurrent execution of BGE-M3 (~2.2 GB VRAM) on GPU is expected to exceed or place very high pressure on available VRAM under concurrent configurations. Development default is 3B local model; sequential offloading is planned for 8B candidate runs. |
| **Primary Disk (C:)** | 195.4 GB total; **32.68 GB free** (~30.4 GiB) | Headroom is limited. |
| **Secondary Disk (D:)** | 314.5 GB total; **220.16 GB free** (~205 GiB) | Designated for virtual environments, model weights, Hugging Face cache (`HF_HOME`), and dataset archives. |
| **Host Python** | Python 3.14.0 (64-bit); `pip 25.2` only | Python 3.14 lacks pre-built wheels for PyTorch and FAISS. A Python 3.11 virtual environment is the default validated target. |
| **Virtual Environments** | None existing in workspace | Workspace `c:\Users\anish\OneDrive\Desktop\research` is an empty git repository on branch `master`. |

---

## 5. Dataset Audit: IndianBailJudgments-1200

### 5.1 Remote Availability & File Inventory
- **Repository Status:** Fully public, accessible, active (last modified June 22, 2025; 1,208+ HF downloads).
- **Core Files Identified:**
  1. `indian_bail_judgments.json`: 2,539,795 bytes (2.54 MB) — Complete structured dataset of 1,200 objects.
  2. `indian_bail_judgments.csv`: 1,804,588 bytes (1.80 MB) — Tabular representation (25 columns).
  3. `indian_bail_judgments.xlsx`: 614,219 bytes — Spreadsheet format.
  4. `IndianBailJudgments-1200_PDFS.zip`: **297,953,486 bytes (297.95 MB)** — Stored via Git LFS (`oid sha256:169b0bcf453d4345049c1b34c220d55e8ecda23c84b9f2d6a4d1bfcbf35b2d99`). Contains expected 1:1 matching source court orders (`case0001.PDF` to `case1200.PDF`).
  5. `summarized_stats.txt`: 3,446 bytes — Official statistical audit.
  6. `dataset_card.md` & `LICENSE`: 632 bytes — CC BY 4.0 International license. Authors: Sneha Deshmukh & Prathmesh Kamble.

### 5.2 Real Label & Stratified Task Distribution
- **Total Cases:** Exactly 1,200.
- **Date Range:** August 18, 1975 to June 2, 2025.
- **Outcome Distribution:**
  - **Granted:** 736 cases (61.33%)
  - **Rejected:** 464 cases (38.67%)
  - **Majority-Class Reference Baseline (EXP-000):** **61.33%** (reference point, not a performance floor).
- **Stratified Evaluation Strata:**
  - **Stratum A (Overall Corpus):** 1,200 cases (736 Granted / 464 Rejected).
  - **Stratum B (Fresh Applications - Primary Headline Benchmark):** 1,084 cases (90.33%).
  - **Stratum C (Cancellation Cases - Secondary Benchmark):** 116 cases (9.67%).
  - **Stratum D (Regular Bail):** 906 cases (75.50%).
  - **Stratum E (Anticipatory Bail §438 CrPC):** 268 cases (22.33%).
  - **Stratum F (Interim & Other):** 26 cases (2.17%).
- **Landmark Case Flag:** Landmark: 147 (12.25%); Regular: 1,053 (87.75%).
- **Parity Argument Usage:** Cited: 341 (28.42%); Not Cited: 859 (71.58%).
- **Court Hierarchy:** 78 unique courts across 28 regions. High Courts dominate (Punjab & Haryana: 130, Patna: 119, Delhi: 109, Allahabad: 108, Bombay: 86). Supreme Court: 47 cases (3.92%).

---

## 6. Dataset Schema Findings & Mode-1 Feature Availability Audit

Every candidate field is audited and classified for temporal availability:
- **`PRE_DECISION`:** Demonstrably available at the historical filing/hearing point. Eligible for Mode 1 prediction.
- **`POST_DECISION`:** Derived from the judicial order or post-decision reasoning. **Strictly quarantined from Mode 1 prediction.**
- **`UNKNOWN`:** Temporal availability cannot be established with certainty. Excluded from strict Mode 1 prediction unless formally justified.

| Raw CSV Column | Data Type in Source | Temporal Classification | Canonical Schema Field | Audit Observations & Quarantine Rules |
| :--- | :--- | :--- | :--- | :--- |
| `case_id` | String (`0001`–`1200`) | `PRE_DECISION` | `source_case_id` & `case_id` | Zero-padded 4-digit identifier. |
| `case_title` | String | `PRE_DECISION` | `case_title` | e.g. *"Jibangshu Paul vs National Investigation Agency"*. |
| `court` | String | `PRE_DECISION` | `court_id` | Mapped to canonical `courts` table. |
| `date` | String (`YYYY-MM-DD`) | `PRE_DECISION` | `decision_date` | Date of order. Used for chronological sorting and cutoff. |
| `judge` | String | `PRE_DECISION` | `judge` | Bench composition. Comma-separated names. |
| `ipc_sections` | List string | `PRE_DECISION` | `ipc_sections_raw` | Penal sections charged in FIR/charge sheet. Deserialized via `ast.literal_eval`. |
| `bail_type` | String | `PRE_DECISION` | `bail_type` | "Regular", "Anticipatory", "Interim". Procedural posture. |
| `bail_cancellation_case`| Boolean string | `PRE_DECISION` | `bail_cancellation` | Distinguishes fresh bail from cancellation petitions. |
| `crime_type` | String | `PRE_DECISION` | `crime_type` | 12 broad crime categories from FIR allegations. |
| `facts` | Multi-sentence text | `PRE_DECISION` | `facts` / `passages` | Factual background of petition. Primary text input for Mode 1. |
| `accused_name` | String | `PRE_DECISION` | `accused_name` | Name of petitioner. |
| `accused_gender` | String | `PRE_DECISION` | `accused_gender` | Demographic parameter. |
| `prior_cases` | String | `PRE_DECISION` | `criminal_antecedents` | Record of prior criminal involvements. |
| `special_laws` | String | `PRE_DECISION` | `special_laws` | NDPS, PMLA, POCSO, UAPA invocations. |
| `source_filename` | String (`case0001.PDF`)| `PRE_DECISION` | `source_pdf_uri` | Expected 1:1 match to source PDF. |
| `legal_issues` | List string | `UNKNOWN` | `legal_issues` | Framed issues. Quarantined from Mode 1 unless pre-decision origin is proven. |
| `parity_argument_used` | Boolean string | `UNKNOWN` | `parity_argument` | Counsel argument. Allowed only if recorded in petition summary. |
| `legal_principles_discussed`| List string | **POST_DECISION** | `principles` | **QUARANTINED:** Extracted from judicial reasoning. Allowed only in Mode 2. |
| `landmark_case` | Boolean string | **POST_DECISION** | `landmark_flag` | **QUARANTINED:** Retrospective significance indicator. |
| `judgment_reason` | String | **POST_DECISION** | `judgment_reason` | **STRICT QUARANTINE:** Discloses judicial rationale and outcome. Excluded from Mode 1. Allowed in Mode 2. |
| `summary` | String | **POST_DECISION** | `summary` | **STRICT QUARANTINE:** Contains full outcome summary. Quarantined to gold reference. |
| `bail_outcome_label_detailed`| String | **POST_DECISION** | `detailed_outcome` | **STRICT QUARANTINE:** Contains detailed ruling text. |
| `bail_outcome` | String | **POST_DECISION** | `outcome` | Target ground truth label (`GRANTED` / `REJECTED`). Never visible to model. |
| `bias_flag` | Boolean string | **POST_DECISION** | `bias_flag` | Dataset creator annotation. Quarantined from prediction. |
| `case_number` | Missing in CSV | `PRE_DECISION` | `case_number` | Formal court registration number. Extracted from PDF header if needed. |

---

## 7. Literature & Novelty Audit

### 7.1 Primary Source Literature Verification Table

| System / Work | Primary Source Citation & DOI/arXiv | Publication / Date | Verification Status | Verified Findings (Reported by Source) | NYAYPRAMANA Analysis (Literature-Scope Statement / Addressed Gap) | Access Date |
| :--- | :--- | :--- | :--- | :--- | :--- | :--- |
| **NyayaRAG** | Shubham Kumar Nigam et al., *arXiv:2508.00709* | Accepted at AACL-IJCNLP 2025; submission Aug 2025 | **PRIMARY SOURCE VERIFIED** | **REPORTED BY SOURCE:** Evaluated LLaMA-3-8B with dense RAG over Supreme Court judgments (ILDC corpus). Reported that factual case text achieved 62.27% accuracy, adding statutes improved accuracy to 67.07%, but **adding semantically retrieved precedents degraded accuracy to 64.71%**. | **LITERATURE-SCOPE STATEMENT:** The cited work investigates dense semantic retrieval but does not describe or evaluate explicit verification-first mechanisms such as passage-level citation verification, symbolic temporal gating, provenance-aware proof graphs, or calibrated abstention. | 2026-09-23 |
| **NyayaAnumana & INLegalLlama** | Shubham Kumar Nigam et al., *"The Largest Indian Legal Judgment Prediction Dataset and Specialized Language Model for Enhanced Decision Analysis"*, Proceedings of the 31st International Conference on Computational Linguistics (COLING 2025), pp. 11094–11109 (*arXiv:2412.08385*; ACL: `2025.coling-main.738`) | Initial submission Dec 11, 2024; published COLING 2025 | **PRIMARY SOURCE VERIFIED** | **REPORTED BY SOURCE:** Introduced NyayaAnumana (largest Indian LJP dataset comprising 29,380 cases across Supreme Court, High Courts, and District Courts) and INLegalLlama (domain-adapted LLaMA model), reporting strong benchmark improvements on multi-jurisdictional judgment outcome forecasting. | **LITERATURE-SCOPE STATEMENT:** The cited paper does not describe or evaluate the explicit verification-first mechanisms targeted by NYAYPRAMANA, including passage-level citation verification, symbolic temporal gating, provenance-aware proof graphs, or calibrated verification-aware abstention. | 2026-09-23 |
| **LeCNet** | Vijit Malik et al., *"ILDC for CJPE: Indian Legal Documents Corpus for Court Judgment Prediction and Explanation"*, ACM / GitHub (`Law-AI/LeCNet`) | 2022/2023 | **PRIMARY SOURCE VERIFIED** | **REPORTED BY SOURCE:** Created citation network dataset for Supreme Court of India judgments to explore topological citation patterns and link prediction. | **LITERATURE-SCOPE STATEMENT:** The cited work focuses on citation network analysis and topological link prediction; it is not reported as evaluating neuro-symbolic verification, passage-level citation verification, or bail decision workflows. | 2026-09-23 |
| **Pooja Ramesh Singh Case** | *Pooja Ramesh Singh v. Jammu and Kashmir Bank Ltd. & Anr.*, Supreme Court of India, Civil Appeal No. 11950 of 2025; **2026 INSC 668**; Bench: Justice Pamidighantam Sri Narasimha, Justice Alok Aradhe | Decided July 2, 2026 | **PRIMARY SOURCE VERIFIED** | **REPORTED BY SOURCE:** Supreme Court set aside appellate orders that relied on six AI-hallucinated judicial precedents, ruling that judicial decisions based on fake citations are "no decision in the eyes of law". Court directed the Bar Council of India to frame AI conduct rules and clarified citing fake precedents constitutes advocate misconduct. | **LEGAL MOTIVATION / SCOPE NOTE:** Arises under Section 7 IBC (not a bail authority); motivates NYAYPRAMANA Level 1–4 citation verification pipeline to detect and block fictitious AI precedents. | 2026-09-23 |

#### 7.1.1 Primary Source Metadata Audit: *Pooja Ramesh Singh v. J&K Bank*
- **Source verification:** The case metadata is verified against a Supreme Court of India source (`2026 INSC 668` / `Civil Appeal No. 11950 of 2025`). The judgment text is cross-checked against an independent legal database. Indian Kanoon must not be described as an official Supreme Court portal.
- **Preserved Case Metadata:**
  - `case_name`: *Pooja Ramesh Singh v. Jammu and Kashmir Bank Ltd. & Anr.*
  - `official_case_number`: Civil Appeal No. 11950 of 2025
  - `official_citation`: 2026 INSC 668
  - `decision_date`: July 2, 2026
  - `bench`: Justice Pamidighantam Sri Narasimha, Justice Alok Aradhe
  - `primary_source_uri`: `https://main.sci.gov.in/` / `https://digiscr.sci.gov.in/` (Supreme Court of India Judgment Portal)
  - `source_hash`: Pending primary PDF acquisition in Phase 3 (no download permitted prior to implementation authorization)
  - `access_date`: 2026-09-23
- **Three-Part Analytical Distinction:**
  1. **What the Supreme Court source establishes:** The Supreme Court set aside appellate orders (NCLAT/NCLT) that had relied on six AI-hallucinated judicial precedents, ruling that judicial decisions based even partially on fake citations are "no decision in the eyes of law" and amount to a subversion of the rule of law. The Court directed the Bar Council of India to frame AI conduct rules and clarified that citing fake precedents constitutes advocate misconduct.
  2. **What the independent database cross-check establishes:** Cross-checking confirms the full judgment text, the bench composition, and the procedural origin arising under Section 7 of the Insolvency and Bankruptcy Code (IBC) rather than criminal bail proceedings.
  3. **What NYAYPRAMANA uses the case to motivate:** This case is not a bail authority; NYAYPRAMANA cites it strictly as judicial authority motivating the critical imperative for Level 1–4 citation verification to detect and block fictitious AI precedents before they can enter legal reasoning.

### 7.2 Defensible Novelty Statement & Research Gap
Existing work demonstrates components such as legal judgment prediction, RAG, legal knowledge graphs, structured reasoning, and legal citation handling. The surveyed literature does not establish whether a domain-bounded, verification-first neuro-symbolic pipeline for Indian bail analysis combining authority applicability, temporal validity, source/passage verification, proof graphs, and calibrated abstention produces measurable reliability gains over progressively stronger RAG baselines.

NYAYPRAMANA does **NOT** claim to be the first legal AI, first Indian legal RAG, or first legal knowledge graph. Its defensible contribution is:
> *A domain-bounded, verification-first neuro-symbolic framework for Indian bail decision analysis that integrates multi-signal authority applicability, dual-level temporal reasoning, a structured authority-force model, layered citation verification, provenance-aware proof graphs, and calibrated abstention, evaluated against progressively stronger RAG baselines and controlled adversarial evaluation fixtures.*

---

## 8. Research-Gap Assessment

| Research Dimension | Existing State of the Art (NyayaRAG, NyayaAnumana, INLegalLlama) | NYAYPRAMANA Contribution |
| :--- | :--- | :--- |
| **Precedent Retrieval** | Pure dense semantic similarity. Retrieves topically close but legally inapplicable precedents. | **Multi-Signal Applicability:** Joint scoring across issue match, statutory provision, court hierarchy, procedural posture, and semantic similarity. |
| **Temporal Integrity** | Overlooked. Later precedents leak into earlier cases, causing anachronistic evaluation. | **Dual-Level Temporal Model:** Strict information cutoff for prediction ($t_{\text{auth}} \le t_{\text{case}}$); nuanced legal-effect analysis for decision analysis. |
| **Citation Authenticity** | Generative models hallucinate plausible citations. | **Layered 7-Level Verification:** From source existence and passage attribution to semantic claim support and proof-path completeness. |
| **Reasoning Transparency** | Unstructured, free-form text explanations. | **Provenance Proof Graphs:** Directed acyclic evidence graphs exposing full support paths from facts to provisions and precedents. |
| **Handling Uncertainty** | Forced binary classification regardless of evidence quality. | **Calibrated Abstention on Dev Set:** Selective prediction with explicit abstention (`HUMAN_REVIEW_REQUIRED`, `INSUFFICIENT_EVIDENCE`). |

---

## 9. Architecture Audit: End-to-End Pipeline & Three System Modes

### 9.1 Three Distinct System Modes

```
┌─────────────────────────────────────────────────────────────────────────────┐
│                            MODE 1: HISTORICAL PREDICTION                    │
│ Inputs: PRE_DECISION facts, pre-decision procedural info, court, crime type.│
│ Temporal Constraint: authority.decision_date <= query.decision_date.        │
│ Retrieval: Training cases from Case Corpus; applicable Authority Corpus.    │
│ Forbidden: judgment_reason, summary, order, outcome, POST_DECISION fields.  │
│ Outputs: GRANTED | REJECTED | ABSTAIN.                                      │
└─────────────────────────────────────────────────────────────────────────────┘

┌─────────────────────────────────────────────────────────────────────────────┐
│                            MODE 2: DECISION ANALYSIS                        │
│ Inputs: Complete historical judgment (including reasoning and order).       │
│ Tasks: Extract issues, map arguments, verify cited authorities, build proof  │
│        graph, check temporal evolution, detect conflicts.                   │
│ Outputs: Verification-Aware Research Analysis, Proof Graph DAG, Status.     │
│ Note: Interpretive analysis mode, NOT an outcome-prediction benchmark.      │
└─────────────────────────────────────────────────────────────────────────────┘

┌─────────────────────────────────────────────────────────────────────────────┐
│                         MODE 3: ADVERSARIAL VERIFICATION                    │
│ Inputs: Controlled evaluation fixtures (real cases with injected flaws:     │
│         fake citation ID, wrong passage, future authority, wrong court).    │
│ Tasks: Detect errors, evaluate verification rejection, trigger abstention.  │
│ Outputs: Verification Status, Error Detection Trace, ABSTAIN.               │
└─────────────────────────────────────────────────────────────────────────────┘
```

### 9.2 Corpus Architecture & Retrieval Isolation

```text
                           ┌── Case Corpus (IndianBailJudgments-1200)
                           │    ├── Training Split: Factual summaries & passages (INDEXED)
                           │    └── Dev/Test Splits: QUARANTINED (NEVER INDEXED)
Query ── Hybrid Retrieval ─┤
                           └── Authority Corpus Manifest
                                ├── Precedents (Supreme Court / High Courts)
                                ├── Statutes (IPC, CrPC, BNSS, BNS, NDPS, etc.)
                                └── Provisions (Enacted, effective, repeal dates)
```

- **Retrieval Isolation Rule:** "Test-set exclusion" means test CASES are excluded from the Case Corpus retrieval index. It does NOT mean historically available authority materials are excluded.

### 9.3 Component Classification
- **MUST HAVE:** Raw ingestion, passage segmenter, Case & Authority corpora manifests, dense (BGE-M3) + lexical (BM25) hybrid retrieval, temporal information cutoff, layered citation verifier, **Legal Knowledge Graph Capability**, proof graph DAG, calibrated abstention, leakage testing harness, **FastAPI API Service Contract**.
- **OPTIONAL / EXPERIMENTAL:** Cross-encoder reranker (`bge-reranker-v2-m3`). (If included, must be explicitly recorded in experiment manifests).
- **FUTURE WORK:** Distributed Neo4j cluster, multi-agent debate modules, full OCR training for degraded 1970s printouts.

---

## 10. Legal Knowledge Graph vs Proof Graph

A critical architectural distinction is maintained between the knowledge graph and proof graph:

| Feature | Legal Knowledge Graph (MUST HAVE Capability) | Proof Graph |
| :--- | :--- | :--- |
| **Topology** | Relational property graph. **May contain cycles** (e.g. mutual citations, cross-references). | **Strictly Directed Acyclic Graph (DAG)**. Cycles are invalid. |
| **Scope** | Global corpus representation (entities, courts, judges, statutes, precedents). | Case-specific or query-specific evidence derivation. |
| **Purpose** | Information organization, neighbor retrieval, multi-hop discovery. | Justifying a specific conclusion via unbroken evidence paths. |
| **Validation** | Relational integrity and edge existence. | **Structural validity:** Pydantic schema.<br>**Evidentiary/Legal validity:** Verification engine. |
| **Implementation** | **Default:** NetworkX + SQLite.<br>**Optional Backend:** Neo4j.<br>**Future:** Distributed graph. | In-memory Pydantic DAG structure. |

### 10.1 Mandatory Capability & Technology Decoupling
- **MUST HAVE:** Provenance-aware Legal Knowledge Graph capability supporting required nodes (statutes, provisions, precedents, legal issues, courts), edges, provenance, and traversals.
- **DEFAULT LIGHTWEIGHT IMPLEMENTATION:** NetworkX + SQLite or equivalent (in-memory graph with relational metadata persistence).
- **OPTIONAL ALTERNATIVE:** Neo4j or another compatible graph backend.
- **FUTURE SCALE OPTION:** Distributed graph infrastructure.
- **Rationale for Mandatory Classification:**
  1. EXP-006 evaluates graph-augmented retrieval against dense and hybrid baselines.
  2. The full NYAYPRAMANA architecture (EXP-007) requires structured legal knowledge representation.
  3. Applicability and provenance reasoning depend on structured relationships.
While the specific technology NetworkX is replaceable, the capability itself is strictly locked as a MUST HAVE.

Named precedent cases (*Gurcharan Singh*, *Prahlad Singh Bhati*, *Satender Kumar Antil*) are data entities in the Authority Corpus, resolved dynamically via applicability reasoning and provenance checks.

---

## 11. Authority Corpus Manifest & Coverage Disclaimers

### 11.1 Mandatory Authority Manifest Schema
Every authority in the Authority Corpus must maintain:
- `authority_id`: Unique canonical identifier.
- `authority_type`: `STATUTE`, `PROVISION`, `CONSTITUTIONAL_PROVISION`, `JUDICIAL_PRECEDENT`.
- `case_title` / `short_name`: Canonical name.
- `court_id` & `court_level`: `SUPREME_COURT`, `HIGH_COURT`, `DISTRICT_COURT`.
- `jurisdiction`: Territorial jurisdiction.
- `decision_date`: Date of delivery.
- `effective_from` & `effective_to`: Temporal validity window.
- `source_uri` & `source_hash`: SHA-256 checksum of primary text.
- `license`: Distribution terms.
- `acquisition_timestamp`: Ingestion time.
- `coverage_status`: Level of full-text coverage.
- *Statute-specific:* `statute_id`, `provision_id`, `enactment_date`, `effective_date`, `repeal_date`, `amendment_date`, `savings_clause`, `transition_rule`, `version_identifier`.

### 11.2 Corpus Limitation Rule & Safe Citation Verification Statuses
- **Absence from the Authority Corpus does NOT imply legal nonexistence:**
  - Do not use `VERIFIED_NONEXISTENT`. Do not claim that the system can prove universal nonexistence of a legal authority.
  - Corpus absence alone is insufficient to classify a citation as `CONFIRMED_INVALID_CITATION`. If the authority cannot be resolved, use `UNRESOLVED` or `NOT_FOUND_IN_CORPUS` as appropriate.
- **Canonical Citation Verification Statuses Enforced:**
  - `FOUND_AND_RESOLVED`: Authority exists and resolves in trusted corpus.
  - `NOT_FOUND_IN_CORPUS`: Authority not present in current indexed corpus (does NOT assert legal nonexistence).
  - `AMBIGUOUS`: Multiple conflicting authorities match citation string.
  - `UNRESOLVED`: Citation string cannot be parsed into canonical components.
  - `CONFIRMED_INVALID_CITATION`: A citation whose claimed legal authority identity or citation details are contradicted by an authoritative source appropriate to that authority type after canonicalization and resolution attempts.
  - `OFFICIAL_SOURCE_MISMATCH`: Citation resolves but cited party/order details mismatch official record.
  - `FIXTURE_FABRICATED`: Assigned strictly to controlled adversarial fixtures intentionally fabricated by the evaluation harness.
- **Enforcement Rules:**
  - **Explicit Corpus Absence Rule:** Corpus absence alone is insufficient to classify a citation as `CONFIRMED_INVALID_CITATION`.
  - For every `CONFIRMED_INVALID_CITATION`, the verification engine must record, where available:
    1. `canonicalized_citation`
    2. `verification_basis`
    3. `authoritative_verification_source`
    4. `verification_timestamp`
    5. `resolver_version` (or resolver/verifier version)
    6. `source_passage` (source passage containing the citation)
    7. `contradictory_metadata_or_passage` (relevant contradictory metadata or passage)
    8. `verification_confidence`

---

## 12. Structured Authority-Force & Layered Citation Verification

### 12.1 Structured Authority-Force & Precedent-Treatment Model
The simplified rule (`BINDING = Supreme Court nationwide or same High Court bench of equal/greater size; PERSUASIVE = coordinate High Courts; NON_BINDING = subordinate/distinguished authorities`) is insufficient and potentially misleading. It is replaced with a structured authority model. Generative models must never freely invent authority force; the symbolic layer derives it from structured metadata and rules, and may return `UNKNOWN` where information is insufficient.

- **Canonical `authority_force`:**
  - `BINDING`
  - `PERSUASIVE`
  - `NON_BINDING`
  - `UNKNOWN`
- **Separate `precedential_status`:**
  - `FOLLOWED`
  - `APPLIED`
  - `DISTINGUISHED`
  - `DOUBTED`
  - `REFERRED`
  - `OVERRULED`
  - `MODIFIED`
  - `NOT_ANALYZED`
- **`authority_force_basis` Schema:**
  Stores structured basis including: `court_hierarchy`, `jurisdiction`, `same_high_court`, `bench_strength`, `later_treatment`, `temporal_status`, and `procedural_context`.
- **Governing Precedent Rules:**
  1. **Supreme Court Precedent:** Supreme Court precedent is treated according to applicable constitutional precedent rules (Article 141) and subsequent bench-strength treatment.
  2. **Same High Court Discipline:** Decisions of the same High Court require same-court precedent discipline and bench-strength analysis.
  3. **Coordinate High Court Benches:** An earlier coordinate-bench decision of the same High Court must NOT automatically be classified merely as `PERSUASIVE`.
  4. **Other High Courts:** Decisions from other High Courts may generally be persuasive rather than binding, subject to applicable legal context.
  5. **Distinguished Authorities:** A `DISTINGUISHED` authority must NOT automatically be classified `NON_BINDING`. "Distinguished" describes applicability to the current facts/issues, not the general precedential force of the underlying authority.
  6. **Separation of Bench Strength and Later Treatment:** Larger-bench and later-treatment information must be represented separately in the metadata.
  7. **Symbolic Derivation Only:** Generative models must never freely invent authority force. The symbolic layer derives it from structured metadata/rules and may return `UNKNOWN` where information is insufficient.
- **Explicit Scope Limitation:** The system does NOT claim that this finite ruleset perfectly resolves all Indian precedent questions; edge cases default safely to `UNKNOWN`.

### 12.2 Layered Citation Verification Pipeline
```text
Level 1: Source Existence (Does authority exist in Authority Corpus?)
   ↓
Level 2: Citation Resolution (Does citation resolve to correct case/statute?)
   ↓
Level 3: Passage Attribution (Does passage belong to that authority?)
   ↓
Level 4: Claim-Support Verification (Does passage semantically support the claim?)
   ↓
Level 5: Temporal Verification (Was authority available at decision date?)
   ↓
Level 6: Jurisdiction/Hierarchy Verification (Structured authority-force evaluation)
   ↓
Level 7: Proof-Path Verification (Is evidence chain unbroken?)
```

---

## 13. Temporal Model

### 13.1 First-Class Temporal Fields
Every entity in the pipeline tracks:
- `offence_date`: Date of alleged criminal incident.
- `procedural_event_date`: Date of FIR, arrest, or lower court order.
- `decision_date`: Date of current bail judgment.
- `authority_decision_date`: Date of cited precedent delivery.
- `statute_enactment_date`: Legislative passage date.
- `statute_effective_date`: Date statute came into force.
- `statute_repeal_date`: Date statute was superseded.
- `amendment_date`: Section-specific amendment dates.

### 13.2 Dual Temporal Dimensions
1. **Mode 1 Historical Information Rule:**
   $$\text{authority.decision\_date} \le \text{query.decision\_date}$$
   Prevents future-precedent leakage during historical evaluation.
2. **Mode 2 Legal-Effect Analysis:**
   Reasons over statutory enactment dates, effective dates, repeal dates, savings/transition provisions, offence dates, and retrospective/declaratory interpretations of higher courts.

### 13.3 Nuanced Statutory Transition (CrPC/IPC to BNSS/BNS)
Simplistic calendar cutoffs are strictly rejected. Statutory applicability is evaluated dynamically:
$$\text{Applicability} = f(\text{Statute}, \text{EnactmentDate}, \text{EffectiveDate}, \text{RepealDate}, \text{SavingsClauses}, \text{OffenceDate}, \text{DecisionDate}, \text{Jurisdiction})$$
The system explicitly records the legal justification for selecting a given statutory regime.

---

## 14. Proof-Graph Audit

A proof graph is a provenance-aware evidence DAG:

```text
CONCLUSION
    ↓
LEGAL ISSUE
    ↓
FACT / EVIDENCE
    ↓
STATUTORY PROVISION
    ↓
PRECEDENT / AUTHORITY
    ↓
SOURCE PASSAGE
```

- **Flexible Typed Evidence Paths:** Not every valid conclusion requires every node type:
  - $\text{Conclusion} \rightarrow \text{Fact} \rightarrow \text{Precedent} \rightarrow \text{Passage}$
  - $\text{Conclusion} \rightarrow \text{Statutory Provision} \rightarrow \text{Passage}$
  - $\text{Conclusion} \rightarrow \text{Legal Issue} \rightarrow \text{Fact} \rightarrow \text{Authority} \rightarrow \text{Passage}$
- **Validation Roles:** Pydantic schema validation enforces **structural DAG validity**; the verification engine evaluates **evidentiary and legal validity**.

---

## 15. Conflict Detection vs Adversarial Evaluation

### 15.1 Conflict Detection Reasoning (EXP-011)
Opposite bail outcomes do **not** automatically indicate a legal conflict. The system reasons conceptually:
$$\text{Apparent Difference} \rightarrow \text{Issue Comparison} \rightarrow \text{Fact/Procedure Comparison} \rightarrow \text{Statute Comparison} \rightarrow \text{Hierarchy} \rightarrow \text{Temporal Currency} \rightarrow \text{Classification}$$
- **Classifications:** `COMPATIBLE`, `DISTINGUISHABLE`, `APPARENT_CONFLICT`, `TRUE_CONFLICT`, `OUTDATED`, `INAPPLICABLE`, `INSUFFICIENT_EVIDENCE`.
- **Conflict Gold Set:** Human-reviewed conflict benchmark pairs annotated under formal guidelines with double annotation where feasible.

### 15.2 Controlled Adversarial Fixtures (EXP-014)
Adversarial evaluation tests whether the system fails safely under controlled transformations of real legal material (A1–A7: correct evidence, wrong passage, fabricated citation ID, temporal violation, wrong jurisdiction, incomplete evidence, apparent conflict).

EXP-011 and EXP-014 are kept strictly separate: EXP-011 is an independent component evaluation of conflict classification; EXP-014 is a full-system stress test.

---

## 16. Comprehensive Leakage Audit

| Leakage Pathway | Risk Description | Prevention Protocol Enforced in NYAYPRAMANA |
| :--- | :--- | :--- |
| **1. Label Leakage** | `judgment_reason` and `summary` disclose outcome. | Strictly quarantined to Mode 2 / gold reference; excluded from Mode 1 inputs. |
| **2. Chronological Leakage** | Random splitting allows future cases into train set. | **Dataset Split Policy:** Chronological train/dev/test split targeting approximately 70% / 15% / 15% by count with group-aware atomic assignment of duplicate/near-duplicate clusters; actual split statistics recorded in `split_manifest.json`. |
| **3. Future-Precedent Leakage** | Historical cases retrieve future precedents. | Temporal retrieval cutoff: $\text{decision\_date} \le \text{query\_date}$. |
| **4. Test Contamination** | Test cases indexed into vector database. | Split-isolated indexing: only Train cases populate the Case Corpus retrieval index. |
| **5. Near-Duplicate Cases** | Identical FIR fact patterns across splits. | **Group-Aware Assignment:** Near-duplicate clusters crossing boundaries are assigned atomically to one split without deleting legitimately distinct legal proceedings. |
| **6. Automated Leakage Audit** | Subtle metadata leakage in evaluation runs. | Dedicated automated audit script verifies `authority.decision_date <= query.decision_date` and outputs a machine-readable audit report for every evaluation run. |

---

## 17. Baseline Ladder & Experiment Plan Audit (EXP-000 to EXP-015)

```text
EXP-000: Majority Class Reference Baseline (61.33% reference point)
   ↓
EXP-001: Tabular Metadata Baseline (Non-text predictive model, leakage-audited PRE_DECISION features)
   ↓
EXP-002: Zero-Shot LLM Baseline (Ungrounded generation on facts only)
   ↓
EXP-003: First Reproducible Dense Semantic RAG (BGE-M3 + FAISS top-5 cases)
   ↓
EXP-003A: Evidence-Oracle Diagnostic (Diagnostic: correct passages supplied; separates retrieval from reasoning failure)
   ↓
EXP-004: RAG + Statutes (Isolated statutory provision augmentation)
   ↓
EXP-005: Hybrid RAG (Dense BGE-M3 + Lexical BM25 reciprocal rank fusion)
   ↓
EXP-006: Graph-Augmented RAG (Knowledge graph augmented retrieval)
   ↓
EXP-007: Full NYAYPRAMANA Framework (Integrated system benchmark)
   ↓
EXP-008: Layered Citation Verification Diagnostic (Precision/Recall across 7 verification levels)
   ↓
EXP-009: Temporal Diagnostic & Information Cutoff (Temporal leakage and violation detection)
   ↓
EXP-010: Multi-Signal Applicability Diagnostic (Multi-signal ranking vs dense similarity)
   ↓
EXP-011: Legal Conflict Diagnostic (Distinguishing true conflicts from distinguishable differences)
   ↓
EXP-012: Proof-Chain Diagnostic (Proof graph validity and evidence completeness)
   ↓
EXP-013: Calibrated Abstention Diagnostic (Threshold optimization on Dev set; coverage vs selective accuracy)
   ↓
EXP-014: Adversarial Stress Testing (Safe failure under controlled fixtures A1–A7)
   ↓
EXP-015: Component Ablation Study (Formal leave-one-out ablations of all pipeline modules)
```

- **Diagnostic Nature of EXP-008–013:** EXP-007 is the full integrated system benchmark; EXP-008 through EXP-013 are controlled diagnostic evaluations isolating individual reasoning components represented in the integrated system.

---

## 18. Hardware & Software Feasibility Audit

### 18.1 Hardware Strategy & Empirical Sizing
- **Host GPU:** NVIDIA GeForce RTX 3050 Laptop GPU (6,144 MiB VRAM).
- **Model Strategy:**
  - **Development Default:** 3B-class local instruction model (`Llama-3.2-3B-Instruct` or `Qwen2.5-3B-Instruct`, < 2.5 GB VRAM) for fast, stable local iteration.
  - **Final Benchmark Candidate:** 8B-class 4-bit model (`Llama-3.1-8B-Instruct` GGUF via `llama-cpp-python` with CUDA acceleration) *if and only if* an empirical VRAM smoke test confirms stability.
  - **Fallback:** Compatible smaller model if 8B is not stable under long context.
- **Sequential Pipeline Management:**
  1. Dense embeddings are generated and indexed via FAISS in batch mode.
  2. BGE-M3 is completely unloaded from GPU memory (`torch.cuda.empty_cache()`).
  3. BM25 runs on CPU (< 300 MB RAM).
  4. Quantized LLM is loaded for inference.
- **External API Fallback Protocol:** If an external API is used, complete provenance metadata (model ID, version, temperature: 0.0, seed: 42, raw output, timestamp) is mandatory.

### 18.2 Runtime Environment & Disk Placement
- **Python Runtime:** Python 3.11 is the default validated target (Python 3.12 permitted if dependencies verified).
- **Execution Platform:** WSL2 Ubuntu is preferred for ML reproducibility; native Windows PowerShell remains an allowed alternative.
- **Storage Allocation:** All data caches, Hugging Face weights (`HF_HOME=D:\hf_cache`), and virtual environments must reside on **Drive D:** (205 GB free).

---

## 19. Reproducibility & Gold Verification Sets

### 19.1 Gold Verification Set (150–250 Examples)
- Manually verified ground truth created by human review under strict annotation guidelines (with double annotation and adjudication where feasible).
- Categories: genuine citation, genuine citation + wrong passage, unresolved citation, fabricated citation identifier, temporally invalid authority, wrong jurisdiction, semantically similar but legally irrelevant authority, incomplete evidence, conflicting evidence.
- Kept strictly separate from training and retrieval corpora.

### 19.2 Distinct Evaluation Metric Families
Metrics must never be collapsed into a single composite score:
- **PREDICTION:** Accuracy, Macro-F1, Precision, Recall.
- **RETRIEVAL:** Recall@5, Recall@10, MRR, NDCG.
- **VERIFICATION:** Citation Verification Precision, Citation Verification Recall, FAR, FRR.
- **GROUNDING / REASONING:** Supported Claim Rate, Unsupported Claim Rate, Proof Path Validity Rate.
- **UNCERTAINTY:** Coverage, Selective Accuracy, ECE, Brier Score.

---

## 20. Risks & Failure Modes

1. **VRAM Memory Pressure:** Mitigated by 3B development default and sequential GPU offloading for 8B candidate runs.
2. **Drive C: Space Exhaustion:** Protected by configuring `HF_HOME=D:\hf_cache` on Drive D:.
3. **PDF Alignment Anomalies:** Addressed by Phase 4 validation of case count, PDF count, unique filename count, parsed count, and validated mappings.
4. **Task Conflation:** Addressed by separate reporting for fresh bail and cancellation cases.
5. **Corpus Absence Misinterpretation:** Addressed by the `NOT_FOUND_IN_CORPUS` limitation rule.

---

## 21. Blockers

1. **Python 3.11 Environment Setup:** User confirmation required on whether to establish Python 3.11 in WSL2 Ubuntu (preferred) or native Windows PowerShell on Drive D:.
2. **Model Serving Option Selection:** User confirmation of the 3B development default + 8B candidate smoke test strategy.
3. **Dataset Acquisition Confirmation:** Confirmation to download the 298 MB PDF archive to `D:\nyaypramana_data\raw\` once Phase 2 begins.

---

## 22. Decisions Required From User

1. **Runtime Platform:** (A) WSL2 Ubuntu 2 (Recommended for ML reproducibility), or (B) Native Windows PowerShell with Python 3.11 on Drive D:.
2. **Model Serving Selection:** Confirm (A) Local 3B development model with 8B candidate benchmark, or (B) Hybrid external API generation with local symbolic verification.
3. **Storage Confirmation:** Confirm `D:\nyaypramana_data\` and `D:\hf_cache\` as primary data and model locations.

---

## 23. Recommended Implementation Sequence (Phased Post-Approval)

```text
Phase 1: Environment & Scaffolding (Python 3.11 on Drive D:, Pydantic schemas, configs, API contract)
   ↓
Phase 2: Primary Dataset Acquisition & Validation (Download IndianBailJudgments-1200, SHA-256 verification)
   ↓
Phase 3: Authority Corpus & Statute Base Assembly (Assemble manifests, Supreme Court/High Court precedents, statutes)
   ↓
Phase 4: Extraction / Normalization / PDF Validation (Audit case-to-PDF mappings, segment passages)
   ↓
Phase 5: Deduplication / Chronological Splitting / Leakage Audit (Group-aware assignment, case-count 70/15/15 split)
   ↓
Phase 6: EXP-000 through EXP-003A (Majority, Metadata, LLM-only, Dense Semantic RAG, Evidence-Oracle Diagnostic)
   ↓
Phase 7: EXP-004 (Statutory Provision Augmentation)
   ↓
Phase 8: EXP-005 (Hybrid Dense + Lexical Retrieval)
   ↓
Phase 9: EXP-006 (Knowledge Graph Augmented Retrieval)
   ↓
Phase 10: EXP-007 (Full NYAYPRAMANA Integrated Benchmark)
   ↓
Phase 11: EXP-008 / EXP-009 / EXP-010 (Citation, Temporal, and Applicability Diagnostics)
   ↓
Phase 12: EXP-012 (Proof-Chain Diagnostic)
   ↓
Phase 13: EXP-013 (Calibrated Abstention Diagnostic on Dev Set)
   ↓
Phase 14: EXP-011 & EXP-014 (Conflict Detection Diagnostic & Adversarial Stress Testing)
   ↓
Phase 15: EXP-015 (Formal Component Ablation Study)
   ↓
Phase 16: Research Artifacts, Manifests & Documentation
```

---

## 24. Definition of Implementation-Ready

The NYAYPRAMANA project will be deemed **Implementation-Ready** when:
1. The user selects the preferred runtime environment and model strategy options.
2. The user explicitly issues the verbatim authorization: `APPROVED — BEGIN IMPLEMENTATION`.
3. The designated environment (Python 3.11 on Drive D: / WSL2) is verified with working PyTorch and FAISS packages.
4. The raw dataset is verified against remote SHA-256 checksums without data corruption.

Until these criteria are satisfied, the project remains strictly gated in **STEP 1 — ANALYSIS & SPECIFICATION ONLY**.

---

# 25. Corrections Applied

### 25.1 Detailed Register of Corrections Made

| # | Item Corrected | Previous Rule / Text | Problem Identified | Corrected Rule | Rationale | Implementation Effect |
| :--- | :--- | :--- | :--- | :--- | :--- | :--- |
| 1 | **Authority-Force Model** | Simplified rule: SC binding, coordinate HC persuasive, distinguished non-binding. | Misrepresents common-law precedent rules: coordinate benches cannot be casually dismissed; distinguished cases remain binding on law; generative models must not invent bindingness. | Structured authority model: `authority_force` (`BINDING`, `PERSUASIVE`, `NON_BINDING`, `UNKNOWN`) separated from `precedential_status` (`FOLLOWED`, `APPLIED`, `DISTINGUISHED`, `DOUBTED`, `OVERRULED`, etc.). `authority_force_basis` tracks court hierarchy, same High Court, bench strength, later treatment. Symbolic layer derives force. | Reflects constitutional precedent rules accurately; distinguishes legal bindingness from factual applicability. | Symbolic module evaluates bench strength and court hierarchy metadata before LLM reasoning. |
| 2 | **NyayaAnumana / INLegalLlama Metadata** | Incorrect citation: `arXiv:2505.12345`, `May 2025`; presented as separate from INLegalLlama. | Inaccurate academic attribution; conflated model and dataset origins. | Corrected publication: Shubham Kumar Nigam et al., *"The Largest Indian Legal Judgment Prediction Dataset and Specialized Language Model for Enhanced Decision Analysis"*, COLING 2025 (*arXiv:2412.08385*; ACL: `2025.coling-main.738`). Distinguishes reported source findings from missing-capability analysis. | Restores primary-source academic rigor; prevents misattributing claims to authors. | Section 7 literature table updated with exact COLING 2025 citation and ACL Anthology ID. |
| 3 | **Pooja Ramesh Singh Metadata** | Incorrect metadata: January 2026, SLP(C) No. 2341/2025. | Secondary sources contained speculative or inaccurate filing numbers. | Primary-source verified: *Pooja Ramesh Singh v. Jammu and Kashmir Bank Ltd. & Anr.*, Civil Appeal No. 11950 of 2025; **2026 INSC 668**; decided July 2, 2026; Bench: P.S. Narasimha and Alok Aradhe JJ. Metadata verified against a Supreme Court of India source; judgment text cross-checked against independent legal database (Indian Kanoon must not be described as an official portal). Arises under Section 7 IBC (not bail); motivates Level 1–4 verification. | Grounds legal hallucination directives in authoritative primary court records. | Literature table updated with verified primary citation `2026 INSC 668`. |
| 4 | **Nonexistence Statuses & Invalid Citations** | Used `VERIFIED_NONEXISTENT` as general search result; uncalibrated invalidity claims. | Absence from indexed corpus does not prove nonexistence; ungrounded invalidity assertions violate open-world legal semantics. | Replaced with safe canonical statuses (`FOUND_AND_RESOLVED`, `NOT_FOUND_IN_CORPUS`, `AMBIGUOUS`, `UNRESOLVED`, `CONFIRMED_INVALID_CITATION`, `OFFICIAL_SOURCE_MISMATCH`, `FIXTURE_FABRICATED`). `CONFIRMED_INVALID_CITATION` strictly defined as contradicted by authoritative source after canonicalization/resolution attempts. Corpus absence alone is insufficient; logs 8 verification fields; forbids `VERIFIED_NONEXISTENT`. | Eliminates false nonexistence assertions; ensures rigorous evidential basis for invalidity. | Engine logs `NOT_FOUND_IN_CORPUS` or `UNRESOLVED` for unresolved citations; records 8 metadata fields for confirmed invalid citations. |
| 5 | **Near-Duplicate Policy** | Hard-coded `> 90% factual overlap` with potential case deletion. | Fixed threshold unvalidated; deleting related cases destroys legitimate separate judicial decisions. | Candidate detection via MinHash/text similarity with threshold calibrated on Train/Dev inspection. Labeled: `EXACT_DUPLICATE`, `NEAR_DUPLICATE`, `RELATED_PROCEEDING`, `SAME_FIR_DIFFERENT_ACCUSED`, `DISTINCT`. Group-aware split assignment prevents cross-split leakage without deletion. | Prevents leakage while preserving legitimately distinct judicial proceedings. | Deduplication script tracks `duplicate_cluster_id` and assigns related clusters atomically to one split. |
| 6 | **Legal Knowledge Graph Status** | Classified as optional in component list. | Knowledge Graph capability is mandatory because EXP-006, full NYAYPRAMANA, and applicability reasoning depend on it. | Legal Knowledge Graph capability is **MUST HAVE**. Default lightweight implementation is NetworkX + SQLite; Neo4j is an optional backend; technology remains replaceable. | Ensures required graph reasoning capabilities are present without imposing heavy database overhead. | Section 9 and Decision Register updated to classify Knowledge Graph capability as MUST HAVE. |
| 7 | **Chronological Split & Group-Aware Leakage** | Hard-coded calendar years (1975–2019, etc.) and uncalibrated exact ratio assumptions. | Calendar boundaries fail to produce 70/15/15; cluster atomicity conflicts with exact mathematical counts. | Dataset split policy: Chronological train/dev/test split targeting approximately 70% / 15% / 15% of eligible cases by count, while enforcing group-aware assignment of duplicate/near-duplicate clusters. Split boundaries determined prior to test evaluation and recorded in `split_manifest.json` without manual tuning. | Chronological 70/15/15 proportions are targets; duplicate/near-duplicate cluster atomicity is the hard leakage-prevention constraint; actual split statistics are reported in the split manifest. | Phase 5 assigns near-duplicate clusters atomically to single splits and logs realized proportions and cluster counts in split_manifest.json. |
| 8 | **Bail Task Stratification** | Treated all 1,200 cases as one homogeneous prediction task. | Bail cancellation operates under different legal standards than fresh bail; merging them obscures task heterogeneity. | Keep all 1,200 cases in corpus. Primary prediction benchmark is Fresh Bail (1,084 cases); Cancellation (116 cases) is a secondary benchmark. Report strata A–F. | Ensures methodological rigor and meaningful comparison with real judicial standards. | Evaluation harness outputs overall and stratified tables across strata A–F. |
| 9 | **Mode-1 Feature Availability Audit** | Informal quarantine of summary/reasoning. | Ambiguous fields like `legal_principles_discussed` could leak post-decision judge analysis. | Formal classification: `PRE_DECISION`, `POST_DECISION`, `UNKNOWN`. All `POST_DECISION` and `UNKNOWN` fields strictly quarantined from Mode 1 inputs. | Guarantees zero post-decision information leakage in historical prediction. | Ingestion pipeline enforces strict input schema filtering based on the audit table. |
| 10 | **Case vs Authority Retrieval Isolation** | Vague "test-set exclusion" rule. | Could be misinterpreted as excluding historically available legal authorities if they match test cases. | Clarified: "Test-set exclusion" means test CASES are excluded from the Case Corpus retrieval index. Authority Corpus materials are retrieved based on historical availability. | Preserves legal precedent access while strictly preventing factual test-case leakage. | Retrieval indexes maintain distinct namespaces: `case_corpus_train` vs `authority_corpus`. |
| 11 | **Authority Corpus Manifest** | Defined vaguely as "Supreme Court/High Court cases and statutes." | Lacked reproducible provenance, versioning, and provision-level temporal tracking. | Mandatory Authority Corpus Manifest tracking 15 metadata fields including `authority_id`, `court_level`, `source_hash`, `effective_from/to`, `amendment_date`, `savings_clause`. | Establishes top-tier scientific reproducibility and provenance for legal authorities. | Manifest generator implemented in Phase 3 with SHA-256 verification and version tracking. |
| 12 | **First-Class Temporal Model** | Simplistic calendar cutoffs (pre/post July 2024). | Criminal law transitions depend on offence dates and savings clauses, not crude calendar boundaries. | Defined 8 first-class temporal fields (`offence_date`, `decision_date`, `enactment_date`, etc.). Dynamic statutory applicability evaluation with explicit legal justifications. | Legally accurate modeling of CrPC/IPC vs BNSS/BNS transitions. | Temporal module computes applicability across multi-date vectors. |
| 13 | **API Requirement Status** | Classified as "Nice to have." | Without a stable API contract, the system cannot be independently evaluated or integrated. | API Contract is **MUST HAVE**. FastAPI is the preferred/default framework; architecture remains framework-agnostic. 6 endpoints locked. | Ensures modular system boundaries and decoupled testing. | API contracts defined in Phase 1 Pydantic schemas. |
| 14 | **Cross-Encoder Reranker** | Implicitly treated as an unmeasured component. | Can introduce undocumented performance variations between dense and hybrid baselines. | Formally classified as **OPTIONAL / EXPERIMENTAL**. If included in an experiment, it must be explicitly recorded in the run manifest. | Protects ablation clarity and prevents confounding retrieval variables. | Retrieval config includes explicit boolean `use_reranker: false` by default. |
| 15 | **EXP-007 and Diagnostic Role** | EXP-008–013 appeared as additive linear steps after EXP-007. | EXP-007 is the integrated system; EXP-008–013 are diagnostic evaluations of components present in EXP-007. | EXP-007 defined as full integrated system benchmark; EXP-008–013 defined as diagnostic component evaluations; EXP-015 defined as formal ablation. | Scientifically accurate framing of system benchmarks vs diagnostic component tests. | Experiment documentation groups EXP-008–013 as diagnostic sub-evaluations of the integrated system. |
| 16 | **Evidence-Oracle Diagnostic (EXP-003A)** | Missing formal diagnostic separating retrieval from reasoning. | If EXP-003 fails, impossible to know whether the retriever failed or the generator failed. | Added EXP-003A: supplies manually verified correct authority passages without outcome, label, or reasoning. | Decouples retrieval failure from reasoning/verification failure. | Added diagnostic harness in Phase 6. |
| 17 | **Gold Verification & Conflict Sets** | Informal targets without annotation methodology. | Model-generated evaluation sets produce circular confirmation bias. | Added Gold Verification Set (150–250 examples) and Conflict Gold Set (7 labels) annotated by human review with formal guidelines. | Ensures gold truth is human-grounded and scientifically defensible. | Gold verification assets stored in `data/GOLD/`. |
| 18 | **Terminology Standardization** | Used subjective terms ("Verified Legal Report", "Safe Refusal", "AI Truth Score"). | Implied system certifies legal truth or provides automated judicial decisions. | Replaced with: "Verification-Aware Research Analysis", "ABSTAIN", "Verification Status", "Historical Outcome Prediction", "Historical Outcome". | Aligns terminology with academic research prototype boundaries. | All schemas, prompts, and documentation updated to canonical terms. |
| 19 | **Automated Authority Leakage Audit** | Informal manual leakage checks. | Subtle temporal leaks in complex retrieval runs can go unnoticed. | Added dedicated automated leakage test verifying `authority.decision_date <= query.decision_date` and statutory applicability, outputting machine-readable reports. | Guarantees leakage-free evaluation runs with automated verification. | Automated test script executed in Phase 5 and Phase 11. |
| 20 | **Model Strategy & Empirical Sizing** | Assumed 8B model fits comfortably on 6 GB VRAM. | Long context and concurrent embedder loading create severe VRAM pressure on 6 GB card. | Development default set to 3B local model; 8B 4-bit model evaluated via empirical smoke test; external API fallback strictly logged. | Ensures development velocity while preserving an empirical path to 8B benchmarking. | Config file sets `development_model: "3B"` and `benchmark_model: "8B"`. |
| 21 | **Distinct Metric Families** | Risk of collapsing metrics into a single score. | A single composite score obscures the trade-off between predictive accuracy and legal grounding. | Formally partitioned 5 independent metric families: PREDICTION, RETRIEVAL, VERIFICATION, GROUNDING, UNCERTAINTY. | Prevents misleading composite rankings and highlights trade-offs honestly. | Evaluation reports output distinct, non-aggregated metric tables. |
| 22 | **16-Phase Implementation Sequence** | Out-of-order execution (Authority Corpus placed after retrieval). | Models cannot retrieve or verify against an Authority Corpus that has not been assembled. | Restructured sequence into 16 linear phases: Authority Corpus assembled in Phase 3 before extraction and retrieval. | Enforces strict dependency ordering across the entire engineering lifecycle. | Section 23 updated to canonical Phase 1–16 sequence. |
