# NYAYPRAMANA: Final Locked Research Specification & Implementation Contract (Version 2.1)

**Document Version:** 2.1.0-LOCKED-FINAL  
**Date:** September 23, 2026  
**Authoritative Status:** Definitive Master Specification for NYAYPRAMANA  
**Implementation Gate:** **GATED — IMPLEMENTATION PERMISSION: NOT GRANTED**  
**Required Activation Phrase:** `APPROVED — BEGIN IMPLEMENTATION`

---

## 1. Project Scope

**NYAYPRAMANA** (*A Verification-First Neuro-Symbolic Framework for Indian Bail Decision Analysis*) is an academic research prototype. It investigates how neural legal language models can be constrained by structured legal knowledge and symbolic verification to produce bail decision analyses that are grounded, temporally valid, traceable, and resistant to hallucinated authority.

The system is explicitly bounded to **Indian Criminal Law — Bail Decision Analysis**, governed primarily by:
- The Code of Criminal Procedure, 1973 (CrPC, specifically Sections 436, 437, 438, 439, 441)
- The Indian Penal Code, 1860 (IPC)
- Initial special statutes represented in the benchmark: NDPS Act 1985 (§37), PMLA 2002 (§45), UAPA 1967 (§43D(5)), and POCSO Act 2012
- The Bharatiya Nagarik Suraksha Sanhita, 2023 (BNSS) and Bharatiya Nyaya Sanhita, 2023 (BNS) for transition-era cases.

The prototype is an analytical and evaluative research framework. Historical outcome prediction is conducted strictly as an empirical benchmark metric, not as an automated judicial decision-maker.

---

## 2. Research Question

> **Primary Research Question:**  
> *How can neural legal language models be constrained by structured legal knowledge and symbolic verification so that their Indian bail analyses are more grounded, temporally valid, traceable, and resistant to hallucinated authority?*

---

## 3. Novelty & Research Gap

### 3.1 Unsubstantiated Claims Prohibited
NYAYPRAMANA does **NOT** claim to be the first Indian legal AI, the first legal RAG system, the first legal knowledge graph, or the first citation verifier.

### 3.2 Primary Sourced Prior Art Context
Recent literature demonstrates that pure semantic RAG over Indian legal corpora suffers from severe limitations:
- **NyayaRAG** (Shubham Kumar Nigam et al., *arXiv:2508.00709*, accepted at AACL-IJCNLP 2025; submission Aug 2025; **PRIMARY SOURCE VERIFIED**):
  - *Reported by Source:* Evaluated LLaMA-3-8B with dense RAG on Supreme Court judgments (ILDC corpus). Reported that factual case text achieved 62.27% accuracy, adding statutes improved accuracy to 67.07%, but **adding semantically retrieved precedents degraded accuracy to 64.71%**.
  - *Literature-Scope Statement:* The cited work investigates dense semantic retrieval but does not describe or evaluate explicit verification-first mechanisms such as passage-level citation verification, symbolic temporal gating, provenance-aware proof graphs, or calibrated abstention.
- **NyayaAnumana & INLegalLlama** (Shubham Kumar Nigam et al., *"The Largest Indian Legal Judgment Prediction Dataset and Specialized Language Model for Enhanced Decision Analysis"*, Proceedings of the 31st International Conference on Computational Linguistics (COLING 2025), pages 11094–11109; *arXiv:2412.08385*; ACL Anthology: `2025.coling-main.738`; initial submission Dec 11, 2024; **PRIMARY SOURCE VERIFIED**):
  - *Dataset & Model Architecture:* NyayaAnumana and INLegalLlama are dataset and model contributions from the same peer-reviewed publication, not unrelated works.
  - *Reported by Source:* Introduced NyayaAnumana (largest Indian LJP dataset comprising 29,380 cases across Supreme Court, High Courts, and District Courts) and INLegalLlama (domain-adapted LLaMA model), reporting strong benchmark improvements on multi-jurisdictional judgment outcome forecasting.
  - *Literature-Scope Statement:* The cited paper does not describe or evaluate the explicit verification-first mechanisms targeted by NYAYPRAMANA, including passage-level citation verification, symbolic temporal gating, provenance-aware proof graphs, or calibrated verification-aware abstention.
- **LeCNet** (Vijit Malik et al., *"ILDC for CJPE: Indian Legal Documents Corpus for Court Judgment Prediction and Explanation"*, ACM / GitHub (`Law-AI/LeCNet`), 2022/2023; **PRIMARY SOURCE VERIFIED**):
  - *Reported by Source:* Created citation network dataset for Supreme Court of India judgments to explore topological citation patterns and link prediction.
  - *Literature-Scope Statement:* The cited work focuses on citation network analysis and topological link prediction; it is not reported as evaluating neuro-symbolic verification, passage-level citation verification, or bail decision workflows.
- **Supreme Court of India Directives on Hallucinated Precedents** (*Pooja Ramesh Singh v. Jammu and Kashmir Bank Ltd. & Anr.*, Supreme Court of India; **PRIMARY SOURCE VERIFIED**):
  - *Source Verification:* The case metadata is verified against a Supreme Court of India source (`2026 INSC 668` / `Civil Appeal No. 11950 of 2025`). The judgment text is cross-checked against an independent legal database. Indian Kanoon must not be described as an official Supreme Court portal.
  - *Case Metadata:*
    - `case_name`: *Pooja Ramesh Singh v. Jammu and Kashmir Bank Ltd. & Anr.*
    - `official_case_number`: Civil Appeal No. 11950 of 2025
    - `official_citation`: 2026 INSC 668
    - `decision_date`: July 2, 2026
    - `bench`: Justice Pamidighantam Sri Narasimha, Justice Alok Aradhe
    - `primary_source_uri`: `https://main.sci.gov.in/` / `https://digiscr.sci.gov.in/` (Official Supreme Court of India Judgment Portal)
    - `source_hash`: Pending primary PDF acquisition in Phase 3 (no download permitted prior to implementation authorization)
    - `access_date`: 2026-09-23
    - *Metadata Audit Note:* Obsolete secondary-source metadata (January 2026 / SLP(C) No. 2341/2025) has been purged. Canonical status is PRIMARY SOURCE VERIFIED against the official Supreme Court judgment record `2026 INSC 668`.
  - *What the Supreme Court Source Establishes:* The Supreme Court set aside appellate orders (NCLAT/NCLT) that had relied on six AI-hallucinated judicial precedents, ruling that judicial decisions based even partially on fake citations are "no decision in the eyes of law" and amount to a subversion of the rule of law. The Court directed the Bar Council of India to frame AI conduct rules and clarified that citing fake precedents constitutes advocate misconduct.
  - *What the Independent Database Cross-Check Establishes:* Cross-checking confirms the full judgment text, the bench composition, and the procedural origin arising under Section 7 of the Insolvency and Bankruptcy Code (IBC) rather than criminal bail proceedings.
  - *What NYAYPRAMANA Uses the Case to Motivate:* This case is not a bail authority; NYAYPRAMANA cites it strictly as judicial authority motivating the critical imperative for Level 1–4 citation verification to detect and block fictitious AI precedents before they can enter legal reasoning.

### 3.3 Defensible Novelty Statement & Research Gap
Existing work demonstrates components such as legal judgment prediction, RAG, legal knowledge graphs, structured reasoning, and legal citation handling. The surveyed literature does not establish whether a domain-bounded, verification-first neuro-symbolic pipeline for Indian bail analysis combining authority applicability, temporal validity, source/passage verification, proof graphs, and calibrated abstention produces measurable reliability gains over progressively stronger RAG baselines.

> *NYAYPRAMANA provides a domain-bounded, verification-first neuro-symbolic framework for Indian bail decision analysis that integrates multi-signal authority applicability, dual-level temporal reasoning, a structured authority-force model, layered citation verification, provenance-aware proof graphs, and calibrated abstention, evaluated against progressively stronger RAG baselines and controlled adversarial evaluation fixtures.*

---

## 4. Hypotheses

- **H1 (Grounding):** NYAYPRAMANA significantly reduces unsupported legal claims and citations compared to LLM-only and standard semantic RAG baselines.
- **H2 (Layered Citation Verification):** Layered passage-level verification (existence $\rightarrow$ resolution $\rightarrow$ attribution $\rightarrow$ semantic claim support) significantly reduces fabricated, misattributed, or misquoted citations.
- **H3 (Applicability):** Multi-signal legal ranking (statutory provision, legal issue match, court hierarchy, procedural posture) outperforms purely dense semantic similarity in ranking governing authorities.
- **H4 (Temporal Reasoning):** Symbolic temporal information cutoffs eliminate future-precedent and anachronistic statutory leakage in historical prediction, while temporal legal-effect analysis correctly contextualizes precedent evolution.
- **H5 (Reasoning Validity):** Provenance-aware proof graphs increase the proportion of legal conclusions backed by unbroken, verifiable evidence chains.
- **H6 (Calibrated Abstention):** Verification-aware abstention improves selective accuracy on incomplete, conflicting, or adversarial cases at a measurable cost in coverage.
- **H7 (Prediction Performance):** The full verification-first architecture maintains or improves historical bail outcome prediction over progressively stronger RAG baselines. (Negative results will be reported with full scientific honesty).

---

## 5. Dataset & Task Stratification

### 5.1 Dataset Specifications
- **Name:** `IndianBailJudgments-1200` (Sneha Deshmukh & Prathmesh Kamble, 2025; CC BY 4.0)
- **Total Cases:** 1,200 annotated Indian bail judgments and court orders
- **Date Range:** August 18, 1975 to June 2, 2025
- **Courts & Regions:** 78 unique courts across 28 Indian regions (High Courts: 96.08%, Supreme Court: 3.92%)
- **Source Documents:** Expected 1:1 matching court orders packaged in `IndianBailJudgments-1200_PDFS.zip` (297.95 MB, Git LFS)

### 5.2 Bail Task Stratification
The primary corpus contains multiple decision types and procedural postures. To ensure methodological rigor:
- **Primary Headline Benchmark:** Fresh-bail outcome prediction (1,084 cases, 90.33% of corpus) represents the primary homogeneous predictive task.
- **Secondary Benchmark:** Bail cancellation cases (116 cases, 9.67% of corpus) are evaluated and reported as a distinct secondary task.
- **Evaluation Strata:** Results are reported across all strata:
  - *Stratum A:* Overall corpus (1,200 cases)
  - *Stratum B:* Fresh bail applications (1,084 cases)
  - *Stratum C:* Bail cancellation petitions (116 cases)
  - *Stratum D:* Regular bail applications (906 cases)
  - *Stratum E:* Anticipatory bail applications (268 cases)
  - *Stratum F:* Interim and other applications (26 cases)

### 5.3 Zero Synthetic Training Data Rule
- **Prohibition:** No synthetic legal cases, court orders, statutes, or training examples may be created or introduced into any training, retrieval, or knowledge corpus.
- **Controlled Evaluation Fixtures:** Controlled adversarial transformations of real legal material (A1–A7) are permitted strictly for evaluation in Mode 3 (EXP-014). These fixtures are explicitly labeled, stored under `evaluation/adversarial_fixtures/`, and strictly quarantined from the primary corpora.

---

## 6. Data Progression Architecture

All project data strictly progresses through four immutable stages:

```text
RAW  ────────►  BRONZE  ────────►  SILVER  ────────►  GOLD
(Immutable)     (Extracted)        (Structured)       (Frozen Manifests)
```

1. **RAW:** Immutable source archives (`IndianBailJudgments-1200_PDFS.zip`, `indian_bail_judgments.json`, `indian_bail_judgments.csv`), source hashes (SHA-256), licensing metadata.
2. **BRONZE:** Clean text extracted from PDFs and metadata, normalized encoding, page/paragraph boundaries, extraction confidence scores.
3. **SILVER:** Structured cases, typed passages, resolved citations, statutes, provisions, legal issues, normalized entities, and knowledge graph representations.
4. **GOLD:** Frozen chronological split manifests (train, dev, test), manually verified evidence paths, gold citation ground truth, controlled adversarial evaluation fixtures, and benchmark evaluation labels.

### Canonical Record Provenance Fields
Every canonical record across the pipeline must consistently support:
`source_url`, `source_hash`, `acquisition_timestamp`, `dataset_version`, `pipeline_version`, `language`, `page_count`, `extraction_method`, and `extraction_confidence`.

---

## 7. Case Corpus

The **Case Corpus** comprises historical bail records used for training, retrieval context, and evaluation:
- Source: `IndianBailJudgments-1200`
- Contains: Factual backgrounds (`facts`), procedural history, extracted case passages, pre-decision metadata.
- **Retrieval Isolation Rule:** In Mode 1 (Historical Prediction), **test-set exclusion means test CASES are excluded from the Case Corpus retrieval index**. Only training split cases may be retrieved as case examples.

---

## 8. Authority Corpus & Manifest

The **Authority Corpus** is an independent, authoritative legal knowledge repository containing binding and persuasive legal authorities:
- **Statutes:** IPC (1860), CrPC (1973), BNSS (2023), BNS (2023), NDPS Act (1985), PMLA (2002), UAPA (1967), POCSO Act (2012).
- **Provisions:** Section-level texts with enactment date, effective date, repeal date, amendment history, and savings clauses.
- **Precedent Judgments:** Landmark and authoritative Supreme Court and High Court rulings on bail jurisprudence.

### 8.1 Mandatory Authority Corpus Manifest
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

### 8.2 Authority Corpus Limitation Rule & Safe Citation Verification Statuses
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

## 9. Legal Ontology

The ontology represents domain-specific legal concepts, **not hard-coded case names or specific judicial figures**:
1. `GravityOfOffence`: Heinousness, punishment severity, societal impact.
2. `PrimaFacieCase`: Evidentiary foundation establishing reasonable grounds.
3. `CustodialInterrogation`: Necessity of custody for recovery under §27 Evidence Act.
4. `FlightRisk`: Probability of absconding or evading trial.
5. `WitnessTampering`: Threat, inducement, or coercion directed at witnesses.
6. `EvidenceTampering`: Destruction, fabrication, or concealment of material evidence.
7. `CriminalAntecedents`: Prior convictions, habitual offender status, pending FIRs.
8. `Parity`: Equality of treatment with similarly situated co-accused already released.
9. `DelayInTrial`: Constitutional right to speedy trial (Article 21) or statutory limits (§436A CrPC).
10. `InvestigationStatus`: Stage of investigation (pre-charge sheet vs charge sheet filed).
11. `CooperationWithInvestigation`: Compliance with summons and notices (§41A CrPC).
12. `MedicalGrounds`: Serious health infirmities or hospitalization requirements.
13. `AgeRelatedGrounds`: Juvenile status or advanced age considerations.
14. `StatutoryRestriction`: Special statutory twin conditions (e.g. NDPS §37, PMLA §45, UAPA §43D(5)).
15. `Other`: Residual bail grounds.

Named precedent cases (*Gurcharan Singh*, *Prahlad Singh Bhati*, *Satender Kumar Antil*) are data entities in the Authority Corpus, linked dynamically to ontology nodes.

---

## 10. Legal Knowledge Graph vs Proof Graph

A formal architectural distinction is maintained between the knowledge graph and proof graph:

| Feature | Legal Knowledge Graph (**MUST HAVE Capability**) | Proof Graph |
| :--- | :--- | :--- |
| **Topology** | Relational property graph. **May contain cycles** (mutual citations, cross-references). | **Strictly Directed Acyclic Graph (DAG)**. Cycles are strictly invalid. |
| **Scope** | Global corpus representation (entities, courts, judges, statutes, precedents). | Query-specific or claim-specific evidence derivation. |
| **Purpose** | Information organization, neighbor retrieval, multi-hop discovery. | Justifying a specific conclusion via unbroken evidence paths. |
| **Validation** | Relational integrity and edge existence. | **Structural validity:** Pydantic schema.<br>**Evidentiary/Legal validity:** Verification engine. |
| **Implementation** | **Default Lightweight:** NetworkX + SQLite.<br>**Optional Backend:** Neo4j.<br>**Future:** Distributed graph. | In-memory Pydantic DAG structure. |

### 10.1 Mandatory Capability & Technology Decoupling
- **MUST HAVE:** Provenance-aware Legal Knowledge Graph capability supporting the required nodes (statutes, provisions, precedents, legal issues, courts), edges, provenance, and traversals.
- **DEFAULT LIGHTWEIGHT IMPLEMENTATION:** NetworkX + SQLite or equivalent (in-memory graph with relational metadata persistence).
- **OPTIONAL ALTERNATIVE:** Neo4j or another compatible graph backend.
- **FUTURE SCALE OPTION:** Distributed graph infrastructure.
- **Capability Classification:** The Legal Knowledge Graph capability itself is **MANDATORY** (not optional) because:
  1. EXP-006 directly evaluates graph-augmented retrieval against dense and hybrid baselines.
  2. The full NYAYPRAMANA architecture (EXP-007) requires structured legal knowledge representation.
  3. Authority applicability, multi-hop reasoning, and provenance tracing depend on structured legal relationships.
While the specific technology NetworkX is replaceable, the capability itself is strictly locked as a MUST HAVE.

---

## 11. Hybrid Retrieval & Optional Reranking

Retrieval combines dense semantic search and lexical matching over the Case and Authority Corpora:
1. **Dense Semantic Retrieval:** BAAI/bge-m3 embeddings indexed via FAISS.
2. **Lexical Retrieval:** BM25 indexing capturing exact legal section numbers, act titles, and specialized terms.
3. **Reciprocal Rank Fusion (RRF):** Combining ranked lists via:
   $$RRF(d) = \sum_{m \in \{\text{dense}, \text{bm25}\}} \frac{1}{60 + r_m(d)}$$
4. **Optional Cross-Encoder Reranker:** BAAI/bge-reranker-v2-m3 is classified as **OPTIONAL / EXPERIMENTAL**. If included in any experiment, its inclusion and parameters must be explicitly recorded in the run manifest.
5. **Retrieval Isolation:** Only cases in the Training Split are indexed in the Case Corpus index. Dev and Test cases are never indexed.

---

## 12. Dual Temporal Reasoning

NYAYPRAMANA explicitly distinguishes two temporal dimensions:

### 12.1 First-Class Temporal Fields
Every entity in the pipeline tracks:
- `offence_date`: Date of alleged criminal incident.
- `procedural_event_date`: Date of FIR, arrest, or lower court order.
- `decision_date`: Date of current bail judgment.
- `authority_decision_date`: Date of cited precedent delivery.
- `statute_enactment_date`: Legislative passage date.
- `statute_effective_date`: Date statute came into force.
- `statute_repeal_date`: Date statute was superseded.
- `amendment_date`: Section-specific amendment dates.

### 12.2 Historical Information Cutoff (Mode 1 Prediction)
In historical prediction, the system must not receive authorities or data created after the historical decision date:
$$\text{authority.decision\_date} \le \text{query.decision\_date}$$
Any precedent decided after the historical query date is strictly filtered from the retrieval pool to prevent anachronistic future leakage.

### 12.3 Temporal Legal-Effect Analysis (Mode 2 Decision Analysis)
In decision analysis, the system evaluates legal effect over time:
- Distinguishes currently applicable law from historically applicable law.
- Evaluates statutory enactment dates, effective dates, and repeal dates.
- Accounts for retrospective or declaratory interpretations by the Supreme Court of India.
- Evaluates transition rules between statutory regimes.

### 12.4 Nuanced Statutory Transition (CrPC/IPC to BNSS/BNS)
Simplistic calendar cutoffs are strictly rejected. Statutory applicability is evaluated dynamically:
$$\text{Applicability} = f(\text{Statute}, \text{EnactmentDate}, \text{EffectiveDate}, \text{RepealDate}, \text{SavingsClauses}, \text{OffenceDate}, \text{DecisionDate}, \text{Jurisdiction})$$
The system explicitly records the legal justification for selecting a given statutory regime.

---

## 13. Structured Authority-Force & Applicability Model

Authority ranking and legal reasoning evaluate independent applicability signals and structured authority force:

### 13.1 Structured Authority-Force & Precedent-Treatment Model
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

### 13.2 Multi-Signal Applicability Scoring
$$S_{\text{app}} = \alpha S_{\text{sem}} + \beta S_{\text{issue}} + \gamma S_{\text{stat}} + \delta W_{\text{court}} + \epsilon S_{\text{proc}}$$

---

## 14. Layered Citation Verification Pipeline

Citation verification operates across a **7-level verification pipeline**:

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

Exact hash/string matching confirms existence and provenance (Levels 1–3); natural language inference and symbolic constraint checks verify semantic claim support and legal applicability (Levels 4–7).

---

## 15. Proof Graph

### 15.1 Formal Structure
A proof graph is a provenance-aware Directed Acyclic Graph (DAG) $G = (V, E)$ connecting claims to verified evidence:

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

### 15.2 Flexible Typed Evidence Paths
The proof graph is not a rigid fixed chain. Valid legal reasoning may follow diverse typed paths:
- $\text{Conclusion} \rightarrow \text{Fact} \rightarrow \text{Precedent} \rightarrow \text{Source Passage}$
- $\text{Conclusion} \rightarrow \text{Statutory Provision} \rightarrow \text{Source Passage}$
- $\text{Conclusion} \rightarrow \text{Legal Issue} \rightarrow \text{Fact} \rightarrow \text{Authority} \rightarrow \text{Source Passage}$

### 15.3 Structural vs Legal Validity
- **Structural Validity:** Enforced by Pydantic schema validation (verifying valid DAG topology, node attributes, source hashes, and type constraints).
- **Evidentiary & Legal Validity:** Evaluated by the verification engine (verifying passage attribution, semantic support, temporal currency, and precedent hierarchy).

---

## 16. Calibrated Abstention

### 16.1 Abstention Formulation
The system must not make ungrounded predictions when evidence is defective or authority is missing:
- Composite Verification Score: $S_{\text{ver}}(x) \in [0, 1]$
- Calibrated Rejection Threshold: $\tau_{\text{abstain}}$
- Decision Rule:
  $$\text{Action} = \begin{cases} 
  \text{Predict Historical Outcome} & \text{if } S_{\text{ver}}(x) \ge \tau_{\text{abstain}} \text{ and Proof Graph is VALID} \\
  \text{ABSTAIN (`HUMAN_REVIEW_REQUIRED`)} & \text{if } S_{\text{ver}}(x) < \tau_{\text{abstain}} \text{ or Conflict Detected} \\
  \text{ABSTAIN (`INSUFFICIENT_EVIDENCE`)} & \text{if required factual/statutory inputs are missing}
  \end{cases}$$

### 16.2 Calibration Protocol on Development Set
- **Strict Isolation:** All calibration and threshold optimization ($\tau_{\text{abstain}}$) are performed strictly on the **Development split**.
- The **Test split** is frozen and evaluated once under the fixed calibrated thresholds.
- Metrics reported: Coverage, Selective Accuracy, Expected Calibration Error (ECE), and Brier Score.

---

## 17. Three Explicit Evaluation Modes

NYAYPRAMANA defines three mutually exclusive operating modes:

### Mode 1: Historical Outcome Prediction
- **Purpose:** Benchmark predictive accuracy on historical cases.
- **Allowed Inputs:** Factual summary (`facts`), pre-decision procedural posture, court, crime type, initial statutory sections. Only `PRE_DECISION` fields are permitted.
- **Temporal Constraint:** $\text{authority.decision\_date} \le \text{query.decision\_date}$.
- **Strictly Forbidden:** `judgment_reason`, `summary`, `order`, outcome labels, `POST_DECISION` fields, or post-decision authorities.
- **Outputs:** `GRANTED`, `REJECTED`, or `ABSTAIN`.

### Mode 2: Decision Analysis
- **Purpose:** Comprehensive legal analysis and verification of an existing court order.
- **Allowed Inputs:** Complete historical judgment (including judicial reasoning and final order).
- **Outputs:** Verification-Aware Research Analysis, Legal Issues, Cited Precedents, Verification Status, Proof Graph DAG, Conflict Analysis, Temporal Evolution.
- **Note:** This is an interpretive analysis mode, NOT an outcome-prediction benchmark.

### Mode 3: Adversarial Verification
- **Purpose:** Diagnostic evaluation of system robustness and error detection.
- **Allowed Inputs:** Controlled adversarial evaluation fixtures (A1–A7) derived from real cases.
- **Outputs:** Verification Status, Specific Error Flags, Provenance Traces, `ABSTAIN`.

---

## 18. Canonical Experiment Ladder

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

- **Diagnostic Nature of EXP-008–013:** EXP-007 represents the full integrated benchmark; EXP-008 through EXP-013 are controlled diagnostic evaluations of components represented in the integrated system.

---

## 19. Leakage Prevention Protocol

1. **Label Leakage:** `judgment_reason` and `summary` are quarantined to gold reference files and strictly excluded from Mode 1 inputs.
2. **Dataset Split Policy:** Use a chronological train/dev/test split targeting approximately **70% / 15% / 15%** of the eligible cases by count, while enforcing group-aware assignment of duplicate and near-duplicate clusters. Duplicate/near-duplicate clusters must remain entirely within a single split to prevent leakage. Because clusters must be assigned atomically, the final split proportions may deviate slightly from the 70/15/15 targets. The actual case counts, proportions, date boundaries, cluster counts, and assignment statistics must be recorded in `split_manifest.json`. Split boundaries must be determined before test evaluation and must not be manually tuned to improve model performance.
   - **Core Principle:** **70/15/15 = target proportions**; **cluster atomicity = hard leakage constraint**.
3. **Near-Duplicate Group-Aware Assignment:**
   - **Threshold Policy:** Do not permanently hard-code similarity thresholds (such as `> 90% factual overlap`) unless that threshold has been empirically validated.
   - **Candidate Detection:** MinHash and/or semantic text similarity.
   - **Threshold Selection:** Selected using manually inspected candidate pairs during TRAIN/DEV methodology development and frozen before final TEST evaluation.
   - **Duplicate Relationship Taxonomy:**
     - `EXACT_DUPLICATE`: Verbatim or hash-identical case text.
     - `NEAR_DUPLICATE`: Substantially identical text with trivial formatting variations.
     - `RELATED_PROCEEDING`: Subsequent bail stage, co-accused petition, or appeal arising from same FIR/crime.
     - `SAME_FIR_DIFFERENT_ACCUSED`: Separate application by distinct accused in identical criminal case.
     - `DISTINCT`: Legally and factually independent judicial decisions.
   - **Leakage Prevention vs Deletion:** Do not automatically delete legally related proceedings. The purpose of deduplication is leakage prevention, not removal of legitimately distinct judicial decisions.
   - **Group-Aware Split Assignment:** When a near-duplicate cluster crosses a proposed chronological split boundary, enforce deterministic group-aware atomic assignment so related duplicate records remain entirely within a single split (Train, Dev, or Test) to prevent cross-split leakage.
   - **Tracking Schema:** Records `duplicate_cluster_id`, `duplicate_relation`, `similarity_method`, `similarity_score`, and `review_status`.
4. **Retrieval Index Isolation:** The vector and lexical indices contain only Train split cases. Dev and Test cases are never indexed in the Case Corpus.
5. **Temporal Gating:** In Mode 1, queries filter authorities by $\text{decision\_date} \le \text{query\_date}$.
6. **Feature Audit:** EXP-001 tabular features are audited to verify pre-decision availability.
7. **Automated Authority Leakage Audit:** Dedicated automated test verifies $\text{authority.decision\_date} \le \text{query.decision\_date}$ and statutory applicability, outputting a machine-readable audit report for every evaluation run.

---

## 20. Evaluation Metrics

Evaluation metrics are strictly partitioned into five distinct families and must never be collapsed into a single composite score:

### 20.1 Prediction Metrics (Mode 1)
- Accuracy
- Macro-F1
- Precision & Recall (per outcome class)

### 20.2 Legal Grounding & Verification Metrics (Modes 2 & 3)
- Supported Claim Rate (SCR)
- Unsupported Claim Rate (UCR)
- Citation Verification Precision & Recall
- False Acceptance Rate (FAR)
- False Rejection Rate (FRR)
- Proof Path Validity Rate (PPVR)

### 20.3 Retrieval Metrics
- Recall@5 & Recall@10
- Mean Reciprocal Rank (MRR)
- Normalized Discounted Cumulative Gain (NDCG@5, NDCG@10)

### 20.4 Uncertainty & Calibration Metrics
- Coverage (% of cases with definitive outcome)
- Selective Accuracy (Accuracy on non-abstained subset)
- Expected Calibration Error (ECE)
- Brier Score

*Critical Principle: A correct outcome prediction does not imply valid legal reasoning; a verified explanation does not guarantee a correct historical outcome prediction.*

---

## 21. Controlled Adversarial Evaluation Protocol (EXP-014)

Adversarial evaluation uses controlled transformations of real legal material:
- **A1 (Correct Evidence):** Real case + correct real citation + correct real passage.
- **A2 (Wrong Passage):** Real case + real citation + incorrect real passage from another section.
- **A3 (Fabricated Citation Identifier):** Real case + deliberately invalid citation string (tagged `FIXTURE_FABRICATED`).
- **A4 (Temporal Violation):** Real case + real authority decided after the query date.
- **A5 (Wrong Jurisdiction):** Real authority with altered jurisdiction metadata.
- **A6 (Incomplete Evidence):** Real case with required evidence intentionally withheld.
- **A7 (Apparent Conflict):** Real authorities addressing opposing outcomes with distinguishable facts.

These fixtures are stored under `evaluation/adversarial_fixtures/`, clearly marked as non-corpus assets, and never used in training or retrieval.

---

## 22. Component Ablation Protocol (EXP-015)

The ablation study systematically removes one pipeline component at a time from the Full NYAYPRAMANA framework:
1. `Full Framework` (Baseline)
2. `No Temporal Filter` (Ablate temporal information cutoff)
3. `No Multi-Signal Applicability` (Replace with pure dense similarity)
4. `No Citation Verification` (Remove passage attribution checks)
5. `No Proof Graph` (Remove DAG evidence structure)
6. `No Abstention` (Force binary prediction on all cases)
7. `Dense Only` (Remove BM25 lexical retrieval)

Reported: $\Delta\text{Macro-F1}$, $\Delta\text{Selective Accuracy}$, $\Delta\text{FAR}$, and $\Delta\text{Proof Validity Rate}$.

---

## 23. Reproducibility & Gold Verification Sets

### 23.1 Gold Verification Set (150–250 Examples)
- Manually verified ground truth created by human review under strict annotation guidelines (with double annotation and adjudication where feasible).
- Categories: genuine citation, genuine citation + wrong passage, unresolved citation, fabricated citation identifier, temporally invalid authority, wrong jurisdiction, semantically similar but legally irrelevant authority, incomplete evidence, conflicting evidence.
- Stored under `data/GOLD/verification_gold.jsonl`.

### 23.2 Conflict Gold Set (EXP-011)
- Human-reviewed conflict benchmark pairs annotated under formal guidelines across 7 labels: `COMPATIBLE`, `DISTINGUISHABLE`, `APPARENT_CONFLICT`, `TRUE_CONFLICT`, `OUTDATED`, `INAPPLICABLE`, `INSUFFICIENT_EVIDENCE`.
- Stored under `data/GOLD/conflict_gold.jsonl`.

### 23.3 Manifests & Determinism
- Fixed `seed: 42` across all deterministic operations; generation temperature fixed at `0.0`.
- Every experiment outputs a `run_manifest.json` capturing git commit hash, environment freeze (`pip freeze`), hardware specs, model checkpoint, retrieval parameters, source hashes, split manifest version, and timestamped metrics.

---

## 24. Hardware Constraints & VRAM Feasibility

- **Host GPU:** NVIDIA GeForce RTX 3050 Laptop GPU (6,144 MiB VRAM).
- **VRAM Constraints:**
  - Unquantized 8B model in FP16 (~16 GB) cannot run locally.
  - Running a 4-bit quantized 8B model (~4.8–5.2 GB VRAM) concurrently with BGE-M3 (~2.2 GB VRAM) on GPU is expected to exceed or place very high pressure on available VRAM under concurrent configurations.
- **Sequential Pipeline Management:**
  1. Dense embeddings are generated and indexed via FAISS in batch mode.
  2. BGE-M3 is completely unloaded from GPU memory (`torch.cuda.empty_cache()`).
  3. BM25 runs on CPU (< 300 MB RAM).
  4. Quantized LLM is loaded for inference.
- **Storage Allocation:** All data caches, Hugging Face weights (`HF_HOME=D:\hf_cache`), and virtual environments must reside on **Drive D:** (205 GB free).

---

## 25. Model Strategy & API Fallback Protocol

- **Development Default:** 3B-class local instruction model (`Llama-3.2-3B-Instruct` or `Qwen2.5-3B-Instruct`, < 2.5 GB VRAM) for rapid development and testing.
- **Final Benchmark Candidate:** 8B-class 4-bit model (`Llama-3.1-8B-Instruct` GGUF via `llama-cpp-python` with CUDA acceleration) *if and only if* an empirical VRAM smoke test confirms stability.
- **Fallback:** Compatible smaller model if 8B is not stable under long context.
- **External API Fallback Protocol:** If an external API is used, strict logging of model ID, provider, version, temperature: 0.0, seed: 42, raw output, and timestamp is mandatory, noting potential external reproducibility limitations.

---

## 26. API Architecture (MUST HAVE)

The system exposes structured Pydantic v2 endpoints via a framework-agnostic API contract with **FastAPI** as the preferred/default implementation:
- `POST /api/v1/cases/ingest`
- `GET  /api/v1/cases/{case_id}`
- `POST /api/v1/retrieve`
- `POST /api/v1/verify/citation`
- `POST /api/v1/analyze` (Mode 2 Decision Analysis)
- `POST /api/v1/predict` (Mode 1 Historical Prediction)
- `GET  /api/v1/proofs/{proof_graph_id}`

---

## 27. UI Policy

**NO UI WILL BE BUILT AT THIS STAGE.**  
All research modules, retrieval pipelines, verification engines, and evaluation scripts must remain completely UI-independent. The user will provide UI design specifications at a later stage.

---

## 28. Phased Implementation Plan (Post-Approval)

Following explicit approval (`APPROVED — BEGIN IMPLEMENTATION`), implementation proceeds in 16 linear phases:

1. **Phase 1: Environment & Scaffolding:** Provision Python 3.11 venv (WSL2 / Drive D:), configure `HF_HOME=D:\hf_cache`, repository structure, Pydantic schemas, logging, API contracts.
2. **Phase 2: Primary Dataset Acquisition & Validation:** Download `IndianBailJudgments-1200`, verify SHA-256 hashes against remote LFS metadata.
3. **Phase 3: Authority Corpus & Statute Base Assembly:** Assemble Authority Corpus Manifest, Supreme Court / High Court precedents, statutes (IPC, CrPC, BNSS, BNS, Special Acts) with versioned metadata.
4. **Phase 4: Extraction, Normalization & PDF 1:1 Validation:** Audit expected 1:1 mapping (case count, PDF count, unique filenames, parse count), segment text into functional passages.
5. **Phase 5: Deduplication, Chronological Splitting & Leakage Audit:** Calibrated near-duplicate candidate detection, group-aware assignment, case-count 70/15/15 chronological split, generate frozen split manifests, automated leakage audit.
6. **Phase 6: Baselines EXP-000 to EXP-003 & EXP-003A:** Majority, Metadata, Zero-Shot LLM, Dense Semantic RAG, and Evidence-Oracle Diagnostic.
7. **Phase 7: Statutory Retrieval (EXP-004):** Augment retrieval with statutory provisions.
8. **Phase 8: Hybrid Lexical + Dense Retrieval (EXP-005):** BM25 + BGE-M3 reciprocal rank fusion; optional reranker test.
9. **Phase 9: Legal Knowledge Graph (EXP-006):** In-memory NetworkX/relational graph construction and traversal.
10. **Phase 10: Full NYAYPRAMANA Integration (EXP-007):** Full integrated neuro-symbolic pipeline benchmark.
11. **Phase 11: Citation, Temporal & Applicability Diagnostics (EXP-008, EXP-009, EXP-010):** 7-level citation verifier, temporal cutoff, structured authority-force diagnostics.
12. **Phase 12: Proof-Graph Diagnostic (EXP-012):** Provenance-aware evidence DAG generation diagnostic.
13. **Phase 13: Calibrated Abstention Diagnostic on Dev Set (EXP-013):** Threshold optimization and calibration curves on Development split.
14. **Phase 14: Conflict Detection & Adversarial Evaluation (EXP-011 & EXP-014):** Conflict classification diagnostic and robustness testing under fixtures A1–A7.
15. **Phase 15: Component Ablation Study (EXP-015):** Systematic leave-one-out ablations.
16. **Phase 16: Research Artifacts, Manifests & Documentation:** Publication tables, error taxonomy, and final reports.

---

## 29. Risks & Failure Modes

1. **VRAM Exhaustion:** Managed via 3B development default, sequential batch embedding, GPU cache flushing, and 4-bit quantization.
2. **Drive C: Space Exhaustion:** Protected by configuring all caches and data on Drive D:.
3. **PDF Alignment Anomalies:** Addressed by Phase 4 validation of case count, PDF count, unique filename count, parsed count, and validated mappings.
4. **Task Conflation:** Addressed by separate reporting for fresh bail and cancellation cases.
5. **Corpus Absence Misinterpretation:** Addressed by the `NOT_FOUND_IN_CORPUS` limitation rule.

---

## 30. Explicit Out-of-Scope Items

- Autonomous judicial sentencing or automated court decisions.
- AI lawyer or automated legal advice interfaces.
- Broad coverage of all Indian civil and criminal law outside bail.
- Unverified, generic conversational chatbots.
- Hard-coding specific judicial figures or rulings into domain ontology.
- Premature UI development.

---

## 31. Approval Gate

**IMPLEMENTATION REMAINS STRICTLY PROHIBITED.**

Implementation may commence ONLY upon receipt of the exact verbatim user instruction:

> **APPROVED — BEGIN IMPLEMENTATION**

Until that authorization is provided, all work is confined to analysis, specification, and documentation.
