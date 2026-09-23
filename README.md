# NYAYPRAMANA

**A Verification-First Neuro-Symbolic Framework for Indian Bail Decision Analysis**

> **Verification-First Neuro-Symbolic Legal Analysis for Indian Bail Decisions**  
> *Core Principle: Evidence → Verification → Provenance → Reasoning → Transparency → Research*

---

## Conceptual Foundation

| Root Term | Philosophical & Epistemological Meaning | Framework Significance |
| :--- | :--- | :--- |
| **NYAYA** (न्याय) | Legal reasoning, jurisprudence, rules of justice | Formal legal reasoning and structured judgment analysis |
| **PRAMANA** (प्रमाण) | Valid source of knowledge, proof, verifier, evidential warrant | Unbroken evidence chains, passage-level attribution, and formal verification |

**NYAYPRAMANA** embodies: **Legal Reasoning + Evidence + Verification**.

---

## Research Positioning

### What NYAYPRAMANA Is
* **An Academic Research Prototype:** Built specifically to investigate whether neural language models can be constrained by structured legal knowledge and symbolic verification to produce trustworthy legal analyses.
* **Focused on Indian Bail Decision Analysis:** Bounded domain covering statutory regimes including the Code of Criminal Procedure, 1973 (CrPC), Indian Penal Code, 1860 (IPC), Bharatiya Nagarik Suraksha Sanhita, 2023 (BNSS), Bharatiya Nyaya Sanhita, 2023 (BNS), and key special acts (NDPS, PMLA, UAPA, POCSO).
* **Verification-First:** Enforces layered 7-level citation verification, multi-signal authority applicability ranking, dual-level temporal reasoning, and calibrated verification-aware abstention.
* **Neuro-Symbolic:** Couples neural language models with deterministic symbolic rules, court hierarchy constraints, and structured legal knowledge graphs.
* **Evidence-Grounded & Provenance-Aware:** Constructs query-specific, acyclic Proof Graphs exposing complete, verifiable paths from facts to statutory provisions and judicial precedents with cryptographic source hashing.
* **Temporally Aware:** Implements strict temporal information cutoffs for historical outcome prediction ($t_{\text{auth}} \le t_{\text{case}}$) alongside nuanced legal-effect analysis for decision evaluation.
* **Designed for Research Evaluation:** Rigorously benchmarked across 16 formal experiments (EXP-000 to EXP-015) using distinct metric families.

### What NYAYPRAMANA Is NOT
* **NOT an Autonomous Judge:** Does not decide cases, determine guilt, or replace judicial discretion.
* **NOT Legal Advice:** Does not advise litigants or substitute for qualified legal practitioners.
* **NOT a Production Judicial System:** Designed strictly for empirical scientific evaluation in an academic setting.
* **NOT a General Indian-Law Chatbot:** Strictly domain-bounded to Indian bail jurisprudence; does not engage in open-domain legal generation.
* **NOT an Autonomous Bail Recommendation System:** Historical outcome prediction is an empirical benchmark, not a normative legal recommendation.

---

## Legal Safety & Ethical Disclaimer

> **IMPORTANT DISCLAIMER**  
> NYAYPRAMANA is an academic research prototype for legal analysis and evaluation. It is not legal advice, does not replace qualified legal professionals or judicial decision-making, and does not autonomously determine whether bail should be granted. Historical outcome prediction is an empirical research benchmark, not a normative recommendation.

---

## Core Hypotheses (H1–H7)

* **H1 (Grounding):** NYAYPRAMANA significantly reduces unsupported legal claims and citations compared to LLM-only and standard semantic RAG baselines.
* **H2 (Layered Citation Verification):** Layered passage-level verification (existence $\rightarrow$ resolution $\rightarrow$ attribution $\rightarrow$ claim support) significantly reduces fabricated, misattributed, or misquoted citations.
* **H3 (Applicability):** Multi-signal legal ranking (statutory provision, legal issue match, court hierarchy, procedural posture) outperforms purely dense semantic similarity in ranking governing authorities.
* **H4 (Temporal Reasoning):** Symbolic temporal information cutoffs eliminate future-precedent and anachronistic statutory leakage in historical prediction, while temporal legal-effect analysis correctly contextualizes precedent evolution.
* **H5 (Reasoning Validity):** Provenance-aware proof graphs increase the proportion of legal conclusions backed by unbroken, verifiable evidence chains.
* **H6 (Calibrated Abstention):** Verification-aware abstention improves selective accuracy on incomplete, conflicting, or adversarial cases at a measurable cost in coverage.
* **H7 (Predictive Performance):** The full neuro-symbolic framework maintains or improves historical outcome prediction versus the strongest RAG baseline.

---

## System Operating Modes

NYAYPRAMANA operates across three strictly decoupled functional modes:

```text
┌─────────────────────────────────────────────────────────────────────────────┐
│                            NYAYPRAMANA MODES                                │
├──────────────────────────┬──────────────────────────┬───────────────────────┤
│ Mode 1: Historical       │ Mode 2: Decision         │ Mode 3: Adversarial   │
│ Outcome Prediction       │ Analysis                 │ Verification          │
├──────────────────────────┼──────────────────────────┼───────────────────────┤
│ • Pre-decision facts     │ • Full judgment text     │ • Controlled fixtures │
│ • Strict temporal cutoff │ • Issue & argument map   │   (A1–A7)             │
│ • Outputs: GRANTED,      │ • Proof-Graph generation │ • Hallucination trap  │
│   REJECTED, ABSTAIN      │ • Statutory transitions  │ • Robustness testing  │
└──────────────────────────┴──────────────────────────┴───────────────────────┘
```

---

## Authoritative Documentation & Specifications

The repository specification is organized into three locked documents:

1. **[`docs/FINAL_LOCKED_SPECIFICATION.md`](docs/FINAL_LOCKED_SPECIFICATION.md):**  
   The authoritative scientific specification detailing research questions, hypotheses, formal ontology, multi-signal applicability scoring, dual temporal reasoning, 7-level citation verification pipeline, acyclic Proof Graphs, calibrated abstention, benchmark datasets (`IndianBailJudgments-1200`), baseline ladder (EXP-000 through EXP-015), and evaluation metrics.

2. **[`docs/IMPLEMENTATION_READINESS_REPORT.md`](docs/IMPLEMENTATION_READINESS_REPORT.md):**  
   The pre-implementation research and technical audit covering hardware constraints (RTX 3050 Laptop GPU, 6 GB VRAM, Drive D: storage allocation), primary-source literature audits (*NyayaRAG*, *NyayaAnumana/INLegalLlama*, Supreme Court *Pooja Ramesh Singh* 2026 INSC 668), leakage mitigation protocols, and the 16 implementation phases.

3. **[`docs/DECISION_REGISTER.md`](docs/DECISION_REGISTER.md):**  
   The canonical log of 33 locked decisions (DEC-001 through DEC-033) covering zero synthetic data enforcement, group-aware deduplication, mandatory Legal Knowledge Graph capability, authority-force hierarchy, and implementation gating.

---

## Governance & Implementation Gate

The project is currently governed by a strict pre-implementation gate:

```text
================================================================================
CURRENT GATE STATUS: STEP 1 — ANALYSIS & SPECIFICATION ONLY
IMPLEMENTATION PERMISSION: NOT GRANTED
================================================================================
```

No implementation code, virtual environment creation, package installation, model downloading, or dataset acquisition may proceed until the user explicitly provides the authorization:

> `APPROVED — BEGIN IMPLEMENTATION`
