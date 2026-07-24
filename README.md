<div align="center">

# PV Sentinel

**An AI-assisted pharmacovigilance platform built patient-first and audit-ready.**

*Draft regulator-ready adverse-event narratives faster — without losing the patient's voice or the audit trail.*

</div>

---

> **Status:** Working MVP, built 2025. Feature-complete against the scope below;
> not validated for regulated production use (see Scope and next steps).
>
> **About this repository:** this is the public overview of PV Sentinel. The
> application source is held in a private repository, which carries the full
> development history. This overview is generated from the codebase, so the
> capabilities described here track what is actually built.
>
> **Built by:** Wayne Kearns — designed and built end to end.

---

## The problem

Pharmacovigilance (PV) — the science of monitoring drug safety — runs on a slow, manual,
and error-prone workflow:

- Writing a single adverse-event (AE) case narrative can take **2–4 hours**.
- Manual medical coding is inconsistent and error-prone.
- Established enterprise safety systems are expensive and require long, heavy deployments.
- Regulators (FDA, EMA, PMDA, Health Canada) demand complete, tamper-evident audit trails
  and reproducible records.

The result: safety teams spend disproportionate time on administrative drafting rather than
on the medical judgement that actually protects patients.

## What PV Sentinel does

PV Sentinel helps drug-safety professionals move from raw report to submission-ready case
faster, with an AI drafting assistant that **augments** — never replaces — human review:

1. **Intake** structured adverse-event case data (and the patient's own account).
2. **Draft** an ICH E2B-aligned narrative with an AI assistant.
3. **Preserve** the patient's voice and check that the AI hasn't summarised it away.
4. **Review** — a qualified human must approve or reject every AI draft.
5. **Audit** — every action is written to a tamper-evident, append-only trail.

## What makes it different

Most tools in this space optimise for feature breadth. PV Sentinel optimises for the two
things that actually matter to patients and regulators: **trust** and **traceability**.

| Principle | What it means in practice |
| --- | --- |
| 🗣️ **Patient-voice preservation** | The patient's own words are stored verbatim and immutably. Every AI narrative is checked for how much of that original voice survived — low fidelity forces human review. |
| 🔒 **Reproducible AI** | Every AI generation is locked to a cryptographic fingerprint of the exact model and prompt used, so any output can be reproduced and explained later. |
| 🧾 **Tamper-evident audit trail** | Actions are recorded in an append-only, hash-chained ledger; altering any historical record is detectable — built with 21 CFR Part 11 expectations in mind. |
| 🤝 **Human-in-the-loop by design** | AI never auto-approves anything. Narratives are always gated behind a named reviewer. |
| 🎯 **Honest AI** | No fabricated "accuracy" or "confidence" scores. If the AI can't help, it says so clearly rather than inventing content. |
| 🔓 **No vendor lock-in** | Designed for portable deployment (cloud or on-premises) with pluggable authentication. |

## Inside the review workspace

Every AI draft lands in a review screen designed so a qualified reviewer can trust it at a
glance — and prove that trust later:

- **Side-by-side change view** — see exactly what the AI drafted versus what the reviewer edited before approval.
- **The patient's own words, in view** — the verbatim account sits alongside the draft, with automatic alerts when too little of the patient's voice survives.
- **Reproducibility panel** — the exact model and prompt fingerprint behind each narrative, so any output can be explained and reproduced.
- **Per-case history** — the complete, hash-chained trail of everything that happened to that case, from intake through review.

## Who it's for

- **PV / Safety Officers & Medical Reviewers** — faster drafting, consistent structure.
- **Regulatory Affairs** — submission-ready records and complete audit trails.
- **Quality & Validation** — reproducibility and inspection-readiness.
- **Data Privacy / Governance** — patient data handled with care by design.

## Architecture at a glance

PV Sentinel is a modern web application with a clear separation between a Python API
(where the safety-critical logic and AI live) and a TypeScript web client.

```mermaid
flowchart LR
    U[Safety professional] --> FE[Web client<br/>React + TypeScript]
    FE -->|REST API| BE[Application API<br/>FastAPI / Python]
    BE --> DB[(PostgreSQL)]
    BE --> AI{{Pluggable AI provider}}
    BE --> AUD[[Hash-chained<br/>audit ledger]]
    subgraph Safety core
      PV[Patient-voice<br/>preservation]
      MT[Model & prompt<br/>version locking]
    end
    BE --- PV
    BE --- MT
```

### Technology stack

| Layer | Technology |
| --- | --- |
| **Frontend** | React + TypeScript (Vite) |
| **Backend / API** | FastAPI (Python), SQLAlchemy |
| **Database** | PostgreSQL (production), SQLite (local dev) |
| **AI** | Provider-agnostic large language models (initially Anthropic Claude) |
| **Compliance foundations** | Hash-chained audit trail, model/prompt hash-locking, role-based access control |

### Design principles

- **Patient safety is a hard constraint, not a feature flag.**
- **Every AI output must be reproducible and reviewable.**
- **Scope discipline:** ship a validated core before breadth.
- **Secrets never live in the codebase; auth is pluggable and portable.**

## Regulatory posture

PV Sentinel is designed for an environment where every output has to survive
inspection. The compliance foundations are built in rather than added later:

- **ICH E2B alignment.** Narratives are structured against E2B expectations so
  case data maps cleanly to downstream submission formats.
- **21 CFR Part 11 principles.** The audit ledger is append-only and
  hash-chained, so any alteration of a historical record is detectable.
  Actions are attributed to a named, authenticated user.
- **Reproducibility.** Each AI generation is locked to a cryptographic
  fingerprint of the exact model and prompt used, so any narrative can be
  reproduced and explained after the fact.
- **Human oversight as a hard constraint.** No AI output reaches a record
  without approval by a qualified reviewer. This is enforced in the workflow,
  not left to policy.

Formal computer system validation (IQ/OQ/PQ) would be required before use in a
regulated production environment. See Scope and next steps.

## Scope and next steps

PV Sentinel was built to prove a specific thesis: that AI can take real time out
of adverse-event narrative drafting without weakening the audit trail or
displacing human judgement. Scope was deliberately held narrow to get that core
right.

**Built**

- Structured AE case intake, including the reporter's verbatim account
- AI-assisted drafting of ICH E2B-aligned narratives (provider-agnostic; honest failure when no model is configured)
- Patient-voice fidelity checking, with low fidelity forcing human review
- Reviewer workspace with side-by-side draft-versus-edit comparison and inline patient voice
- Model and prompt fingerprinting for reproducible generation
- Hash-chained, append-only audit ledger with integrity verification
- Role-based access control
- Versioned database migrations and a containerised deployment baseline (Docker + PostgreSQL)

**Deliberately out of scope for the MVP**

- Signal detection and case-series analytics
- Automated MedDRA coding
- E2B(R3) transmission to regulatory gateways
- Duplicate detection and case merging
- Multi-language intake

**What production readiness would require**

- Formal computer system validation (IQ/OQ/PQ) with documented URS, FS, and DS
- Enterprise authentication (SSO/OIDC) in place of the current authentication stub
- Penetration testing and a data protection impact assessment
- Model evaluation against a curated narrative benchmark, with drift monitoring
- Integration with an established safety database rather than standalone storage
- Defined SOPs covering reviewer qualification, escalation, and periodic review

## Important disclaimers

- **Not a medical device.** PV Sentinel is a drafting and workflow assistant, not an
  autonomous clinical decision-making system.
- **Human oversight is required.** All AI-generated content must be reviewed and approved
  by qualified professionals before any regulatory use.
- **Validation required.** Formal computer-system validation (IQ/OQ/PQ) must be completed
  before use in a regulated production environment.

## Contact

Wayne Kearns — wayne.kearns@nortesconsulting.com ·
[linkedin.com/in/waynekearns](https://www.linkedin.com/in/waynekearns/)

Happy to walk through the private repository and a live demonstration on request.

---

<div align="center">
<sub>PV Sentinel — because the fastest safety narrative is worthless if it loses the patient.</sub>
</div>
