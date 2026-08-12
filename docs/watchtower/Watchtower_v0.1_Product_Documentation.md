# Regulatory Intelligence Watchtower - Product Documentation

**Version:** v0.1 MVP  
**Status:** Operational MVP / Frozen  
**Documentation date:** 12 August 2026  
**Repository:** `Project-ascension-n8n`  
**Source control:** Git / GitHub

> **Portfolio statement:** An operational regulatory intelligence pipeline that combines LLM analysis with deterministic risk controls, conditional routing, and automated alert delivery.

## 1. Executive Summary

The Regulatory Intelligence Watchtower is the first finished product in Project Ascension. Its purpose is to demonstrate an AI Operations engineering pattern: ingest regulatory information, normalize it, use an LLM to produce structured regulatory analysis, validate the structure, apply a deterministic risk assessment, route only qualifying events, and deliver an operational alert through Telegram.

The MVP was executed successfully in n8n. The positive path produced a critical assessment with a 15/24 score and `alert_required = true`, the Priority Gate routed the qualifying items to Prepare Alert, and the resulting alert was received in Telegram. The negative path was also exercised during testing, with low-risk items producing `alert_required = false` and routing to the false branch.

## 2. Product Definition

| Attribute | Definition |
|---|---|
| Product | Regulatory Intelligence Watchtower |
| Version | v0.1 MVP |
| Primary goal | Automate regulatory monitoring, structured interpretation, risk scoring, and alert delivery. |
| Primary platform | n8n |
| LLM role | Generate structured regulatory analysis for downstream processing. |
| Control role | Deterministic risk scoring and boolean alert gating. |
| Notification channel | Telegram |
| Status | Operational and frozen for portfolio use. |
| Primary portfolio signal | AI Ops orchestration + deterministic controls + automation. |

## 3. Scope and MVP Boundary

The MVP deliberately stops after proving the core intelligence and routing loop. It is not positioned as a production-grade regulatory platform. The objective is portfolio-grade operational proof, not feature completeness.

### In scope for v0.1

- Automated regulatory feed ingestion via HTTP request.
- Document splitting and normalization.
- Preparation and submission of a structured Kimi/LLM payload.
- Normalization and validation of the LLM response.
- Deterministic Risk Assessment (DRA).
- Priority Gate based on the DRA `alert_required` boolean.
- Human-readable alert formatting.
- Telegram alert delivery.
- Positive and negative path testing.
- Git/GitHub preservation of the exported n8n workflow.

### Explicitly deferred from v0.1

- Deduplication and persistent alert state.
- Persistent regulatory database.
- Multi-channel notification preferences.
- Advanced human-in-the-loop approval workflows.
- Operational dashboard/UI.
- Production-grade retry and recovery orchestration.
- Advanced recommendations/action automation.

## 4. Architecture and Workflow

```text
Schedule Trigger
↓
HTTP Request - regulatory feed
↓
Split Out
↓
Normalize Document
↓
Prepare Kimi Payload
↓
HTTP Request - LLM/Kimi
↓
Normalize Kimi Response
↓
Validate Kimi Schema
↓
DRA (Deterministic Risk Assessment)
↓
Priority Gate
├── FALSE → stop / no alert
└── TRUE  → Prepare Alert → Telegram
```

### Core architectural principle

The LLM is used for probabilistic interpretation and structured extraction, while alert routing is governed by a deterministic rule engine. This separation reduces the chance that an LLM output directly controls an operational notification without a second control layer.

## 5. Node-Level Design

| Node | Purpose | Observed output / role |
|---|---|---|
| Schedule Trigger | Starts scheduled monitoring runs. | Recurring execution trigger. |
| HTTP Request (GET) | Retrieves regulatory source material. | Regulatory documents/feed items. |
| Split Out | Turns feed results into processable items. | One item per regulatory document. |
| Normalize Document | Standardizes source-document fields. | Structured document metadata. |
| Prepare Kimi Payload | Builds structured LLM input. | LLM-ready payload. |
| HTTP Request (POST) | Submits payload to LLM endpoint. | Kimi response. |
| Normalize Kimi Response | Cleans/parses model response. | Structured JSON for validation and scoring. |
| Validate Kimi Schema | Checks expected structures exist. | Validated items or errors. |
| DRA | Calculates deterministic risk score and severity. | `risk_score`, severity, `alert_required`, `risk_basis`. |
| Priority Gate | Routes based on `alert_required`. | TRUE for alerts; FALSE for no-alert items. |
| Prepare Alert | Creates human-readable Telegram message. | `alert_message`. |
| Telegram | Delivers the alert. | Operational notification received. |

## 6. Deterministic Risk Assessment

The observed DRA calculates a composite risk score from six dimensions:

- Regulatory risk
- Enforcement probability
- Regulatory burden
- Economic impact
- Compliance significance
- Urgency

### Score interpretation

| Score | Severity | Alert decision |
|---|---|---|
| 15-24 | Critical | Alert |
| 11-14 | High | Alert |
| 6-10 | Medium | No alert |
| 0-5 | Low | No alert |

Maximum observed score: **24**.

The Priority Gate consumes `deterministic_risk_assessment.alert_required`. This keeps the routing decision explicit and auditable.

## 7. Priority Routing and Alerting

The Priority Gate was configured around the DRA `alert_required` field. Test execution showed both branch outcomes: **2 items on TRUE** and **3 items on FALSE**. TRUE was connected to Prepare Alert and Telegram; FALSE terminated without a notification.

Prepare Alert formats structured output into a human-readable message. Telegram consumes the resulting `alert_message` field rather than recreating the message content.

## 8. Validation and Test Evidence

| Test | Observed result | Expected | Status |
|---|---|---|---|
| Negative-path routing | Low-risk item produced `alert_required = false`; no-alert items routed to FALSE. | No Telegram notification. | PASS |
| Positive-path routing | High/Critical item produced `risk_score = 15/24`, severity `Critical`, `alert_required = true`. | TRUE branch and alert formatting. | PASS |
| Telegram delivery | Prepared alert was received in the configured Telegram chat. | Human-readable notification arrives. | PASS |
| DRA defect/debug cycle | Incorrect scoring was identified during testing and the DRA logic was corrected before final positive-path validation. | Correct deterministic result. | PASS |

Strong positive-path evidence: the Telegram result for document **2026-16370**, containing title, issuing agency, publication date, document number, risk classification, impact fields, and an action line.

## 9. Git and Version-Control Record

The exported n8n workflow was copied into the existing `Project-ascension-n8n` repository, committed, verified clean locally, and pushed successfully to `origin/main`.

| Record | Observed value |
|---|---|
| Repository | `Project-ascension-n8n` |
| Workflow path | `workflows/Global Regulatory Intelligence Watchtower - MVP- v0.1.json` |
| Branch | `main` |
| Commit message | `Add Regulatory Intelligence Watchtower MVP v0.1` |
| Commit shown in terminal | `c4f90a3` |
| Remote push | `origin/main` - successful |
| Working tree after commit | clean |

Credentials and secrets should remain outside the repository.

## 10. Known Limitations and Deferred v0.2 Work

These are intentionally deferred rather than treated as defects in the frozen MVP:

- No persistent deduplication/state layer is documented for v0.1.
- Notification is Telegram-only.
- Alert recommendations are generic and not yet a specialized action engine.
- No user-specific alert preferences are implemented.
- No dedicated monitoring dashboard is included.
- No formal persistence/audit database is included in the frozen MVP.

These capabilities belong in a later version only if a real user/client requirement justifies the expansion.

## 11. Portfolio / Interview Positioning

Present the project as an **AI Ops workflow that separates probabilistic intelligence from deterministic operational control**, not as a generic n8n automation.

| Capability | Evidence |
|---|---|
| LLM orchestration | Structured Kimi/LLM analysis pipeline. |
| API integration | HTTP GET/POST workflow steps. |
| Data transformation | Document and response normalization. |
| Schema validation | Dedicated validation stage before scoring. |
| Deterministic business logic | DRA scoring and severity thresholds. |
| Operational routing | Priority Gate based on `alert_required`. |
| Automated communication | Telegram alert delivery. |
| Debugging | DRA defect, diagnosis, correction, and retest. |
| Version control | Workflow exported and pushed to GitHub. |
| Scope management | v0.1 frozen after acceptance criteria were met. |

### Suggested portfolio description

> Built an AI-powered regulatory intelligence workflow in n8n that ingests regulatory publications, uses an LLM for structured analysis, validates outputs, applies a deterministic 6-factor risk engine, routes qualifying events, and delivers prioritized Telegram alerts. Implemented and tested the separation between probabilistic model output and deterministic operational decisioning, then version-controlled the workflow in Git/GitHub.

## 12. Acceptance Record and Freeze Criteria

- The core regulatory ingestion and transformation path executes.
- The LLM analysis produces structured data usable downstream.
- The DRA produces deterministic risk scores and an alert decision.
- The Priority Gate handles both TRUE and FALSE outcomes.
- The positive path produces a human-readable alert.
- The Telegram alert is received successfully.
- The workflow has been exported and preserved in Git/GitHub.
- The MVP scope is explicitly bounded and v0.1 is frozen.

**Acceptance status: MVP v0.1 COMPLETE / FROZEN**
