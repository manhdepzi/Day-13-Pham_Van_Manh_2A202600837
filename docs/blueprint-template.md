# Day 13 Observability Lab Report

> **Instruction**: Fill in all sections below. This report is designed to be parsed by an automated grading assistant. Ensure all tags (e.g., `[GROUP_NAME]`) are preserved.

## 1. Team Metadata
- [GROUP_NAME]: Phạm Văn Mạnh - Day 13 Observability Lab
- [REPO_URL]: https://github.com/manhdepzi/Day-13-Pham_Van_Manh_2A202600837.git
- [MEMBERS]:
  - Member A: Phạm Văn Mạnh | Student ID: 2A202600837 | Role: Logging, PII, Tracing, SLO, Alerts, Dashboard, Demo

---

## 2. Group Performance (Auto-Verified)
- [VALIDATE_LOGS_FINAL_SCORE]: 100/100
- [TOTAL_TRACES_COUNT]: 10 Langfuse traces exported in `data/evidence/trace_ids.md`
- [PII_LEAKS_FOUND]: 0

---

## 3. Technical Evidence (Group)

### 3.1 Logging & Tracing
- [EVIDENCE_CORRELATION_ID_SCREENSHOT]: `docs/SCREENSHOT/log.png` and `data/logs.jsonl` show `correlation_id` on request, chat, RAG, LLM, and agent log events.
- [EVIDENCE_PII_REDACTION_SCREENSHOT]: `docs/SCREENSHOT/log_validation.png` and `data/evidence/log_validation.json` show no PII leaks after validation.
- [EVIDENCE_TRACE_WATERFALL_SCREENSHOT]: `docs/SCREENSHOT/trace.png`; trace links are listed in `data/evidence/trace_ids.md`.
- [TRACE_WATERFALL_EXPLANATION]: Each `chat_request` trace contains `rag.retrieve`, `llm.generate`, and `metrics.record`. In the `rag_slow` incident trace, the RAG span dominates latency while the LLM span stays near baseline, proving retrieval is the bottleneck.

### 3.2 Dashboard & SLOs
- [DASHBOARD_6_PANELS_SCREENSHOT]: `docs/SCREENSHOT/dashboard.png` and `docs/SCREENSHOT/dashboard_panels.png`
- [SLO_TABLE]:
| SLI | Target | Window | Current Value |
|---|---:|---|---:|
| Latency P95 | < 3000ms | 28d | 164.35ms baseline |
| Error Rate | < 2% | 28d | 0.0% baseline |
| Cost Budget | < $2.5/day | 1d | $0.006723 baseline |
| Quality Score | >= 0.75 avg | 28d | 1.0 eval average |

### 3.3 Alerts & Runbook
- [ALERT_RULES_SCREENSHOT]: `docs/SCREENSHOT/alert_rules.png`; alert evidence is stored in `data/evidence/alert_validation.json`.
- [SAMPLE_RUNBOOK_LINK]: `docs/runbook.md#rag_slow`

---

## 4. Incident Response (Group)
- [SCENARIO_NAME]: rag_slow
- [SYMPTOMS_OBSERVED]: P95 latency increased from 164.35ms baseline to 912.9ms during the incident, while error rate remained 0%.
- [ROOT_CAUSE_PROVED_BY]: `data/evidence/rag_slow_metrics.json` shows `rag_p95_latency_ms` rose to 750.7ms and a recent request had `rag_latency_ms` of 2500ms; the matching Langfuse trace shows the `rag.retrieve` span dominating the request.
- [FIX_ACTION]: Disable the incident with `python scripts/inject_incident.py --scenario rag_slow --disable`, verify `/metrics` returns to baseline, and confirm the high-latency alert clears.
- [PREVENTIVE_MEASURE]: Keep the `rag_latency_spike` alert, enforce retrieval timeout/fallback, monitor RAG latency separately from LLM latency, and use the runbook before changing model behavior.

---

## 5. Individual Contributions & Evidence

### [MEMBER_A_NAME] Phạm Văn Mạnh - 2A202600837
- [TASKS_COMPLETED]: Implemented structured JSON logging, timestamp aliasing, correlation ID propagation, and PII redaction for email, phone, CCCD, credit card, passport, and address-like text.
- [EVIDENCE_LINK]: `app/logging_config.py`, `app/middleware.py`, `app/pii.py`, `tests/test_pii.py`, `tests/test_log_validation.py`

### [MEMBER_B_NAME] Phạm Văn Mạnh - 2A202600837
- [TASKS_COMPLETED]: Implemented Langfuse-compatible tracing around `chat_request`, `rag.retrieve`, `llm.generate`, and `metrics.record`; exported trace evidence.
- [EVIDENCE_LINK]: `app/tracing.py`, `app/agent.py`, `scripts/export_langfuse_evidence.py`, `data/evidence/trace_ids.md`

### [MEMBER_C_NAME] Phạm Văn Mạnh - 2A202600837
- [TASKS_COMPLETED]: Defined SLOs, alert rules, and incident runbooks for latency, error rate, and cost spikes.
- [EVIDENCE_LINK]: `config/slo.yaml`, `config/alert_rules.yaml`, `docs/runbook.md`, `scripts/check_alerts.py`, `data/evidence/alert_validation.json`

### [MEMBER_D_NAME] Phạm Văn Mạnh - 2A202600837
- [TASKS_COMPLETED]: Built dashboard evidence and metrics collection for latency, traffic, errors, cost, tokens, and quality.
- [EVIDENCE_LINK]: `docs/dashboard.html`, `docs/dashboard-spec.md`, `scripts/build_dashboard.py`, `data/evidence/baseline_metrics.json`, `docs/SCREENSHOT/dashboard.png`

### [MEMBER_E_NAME] Phạm Văn Mạnh - 2A202600837
- [TASKS_COMPLETED]: Ran validation, quality evaluation, load/incident evidence collection, and prepared final report artifacts.
- [EVIDENCE_LINK]: `scripts/validate_logs.py`, `scripts/eval_quality.py`, `scripts/collect_evidence.py`, `data/evidence/log_validation.json`, `data/evidence/quality_eval.json`

---

## 6. Bonus Items (Optional)
- [BONUS_COST_OPTIMIZATION]: Cost metrics are exported as `total_cost`, `average_cost`, `input_tokens`, and `output_tokens`; `cost_spike` alert compares output tokens against 2x baseline.
- [BONUS_AUDIT_LOGS]: Alert and validation evidence are stored separately under `data/evidence/`.
- [BONUS_CUSTOM_METRIC]: `average_quality_score` and `quality_pass_rate` are exported and validated by `scripts/eval_quality.py`.
