# Bot System Audit — Framework & Sample Report

An independent audit framework for multi-department support-automation bots. This is the methodology and the **exact deliverable format** a client receives — included here so the scope and output are unambiguous before any contract.

> Built by **Waseem Iftikhar** — 7 yrs production backend; designs and ships LLM agents, voice bots and WhatsApp/automation pipelines (see github.com/waseem253). This document is generic by design — no client data — and is the template a real engagement is run against.

---

## 1. What the audit covers — 8 dimensions

Each dimension is scored **0–5** (5 = production-excellent) with concrete evidence, and every finding gets a severity: **Critical / High / Medium / Low**.

| # | Dimension | What I actually inspect |
|---|---|---|
| 1 | **Conversation design & intent coverage** | Fallback rate, misroute rate, dead-ends, ambiguous prompts, escalation-to-human paths, tone consistency across departments |
| 2 | **Workflow & integration integrity** | Bot-to-bot handoffs, data passed between departments, idempotency, retries, error handling, silent failures, race conditions |
| 3 | **Lead management & data accuracy** | Capture completeness, deduplication, CRM sync correctness, attribution, lost-lead paths, field-mapping drift |
| 4 | **Reporting & analytics accuracy** | Event instrumentation gaps, metric definitions, whether dashboards reconcile with source-of-truth data |
| 5 | **AI / LLM quality & safety** | Hallucination rate, prompt-injection exposure, PII handling, guardrails, model/version drift, unbounded generation |
| 6 | **Reliability & operations** | Uptime, latency, monitoring, alerting, logging quality, incident visibility, runaway-cost controls |
| 7 | **Scalability readiness** | Bottlenecks that will break *before* more automation is added — the client's stated goal |
| 8 | **Security & compliance** | Webhook auth, secret handling, data retention, access control, third-party data exposure |

## 2. Methodology — how the engagement runs

1. **Discovery & access** — read-only access, architecture walkthrough, list of every bot + owner + purpose.
2. **System mapping** — a single diagram of every bot, trigger, integration and data flow (most internal teams have never seen the whole thing on one page; gaps surface here).
3. **Heuristic review** — each bot scored against the 8 dimensions.
4. **Transcript & log review** — a statistically meaningful sample of real conversations + error logs (anonymised).
5. **Adversarial probing** — I drive the live bots through edge cases, handoffs, injections and failure paths to reproduce issues, not just theorise them.
6. **Data reconciliation** — sample leads/reports traced end-to-end vs. the source system to quantify accuracy loss.
7. **Findings & severity** — every issue: evidence, impact, severity, effort.
8. **Prioritised roadmap** — quick wins (≤1 day) vs. strategic fixes, sequenced so workflows are safe to scale.
9. **Readout** — live walkthrough + the written report; Q&A; optional fix-supervision.

## 3. Deliverable — report structure

1. Executive summary (one page, non-technical, for decision-makers)
2. System map (the full as-built diagram)
3. Scorecard (8 dimensions, 0–5, with trendline if re-audited)
4. Findings register (every issue, severity, evidence, impact, recommended fix, effort)
5. Prioritised remediation roadmap (quick wins → strategic, sequenced for safe scaling)
6. Appendix: test transcripts, reconciliation samples, raw evidence

---

## 4. Sample findings register (redacted — illustrative, not a real client)

This is what the findings table looks like, using realistic anonymised examples so the format and depth are clear.

| ID | Dimension | Severity | Finding | Impact | Recommended fix | Effort |
|---|---|---|---|---|---|---|
| F-01 | Workflow integrity | **Critical** | Sales→Support handoff drops `lead_id` when the support bot times out; no retry | ~6% of escalated leads become unattributable; revenue leakage invisible to reporting | Idempotent handoff with persisted correlation ID + dead-letter retry | 2 days |
| F-02 | Reporting accuracy | **High** | "Resolved" counted on bot *reply*, not user confirmation | Resolution rate overstated ~18%; KPI-driven decisions skewed | Redefine metric to confirmed-resolution event; backfill 90 days | 1.5 days |
| F-03 | Lead management | **High** | No dedup across the WhatsApp bot and the web bot | Same lead double-counted; sales contacts twice; CRM noise | Normalise on phone+email hash at ingest; merge rule | 1 day |
| F-04 | AI safety | **High** | Support bot echoes user-supplied text into a tool call without sanitisation | Prompt-injection can trigger unintended internal actions | Strict tool-arg schema + allowlist; never free-text into actions | 1 day |
| F-05 | Conversation design | **Medium** | 11% fallback rate on the billing bot; 3 dead-end intents | Avoidable human escalations; guest frustration | Add the 3 missing intents; reroute fallback to a clarifying question | 1 day |
| F-06 | Reliability | **Medium** | No alerting on webhook 5xx; failures only found via complaints | Mean-time-to-detect measured in hours | Add error-rate alert + synthetic heartbeat per bot | 0.5 day |
| F-07 | Scalability | **Medium** | All bots share one API key with no per-bot rate isolation | One noisy bot can throttle all departments under load | Per-bot keys + budget caps before scaling automation | 0.5 day |
| F-08 | Security | **Low** | Inbound webhooks unauthenticated (rely on URL obscurity) | Spoofed events possible | Signature verification on every inbound webhook | 0.5 day |

**Sample scorecard:** Conversation 3/5 · Workflow 2/5 · Lead mgmt 2/5 · Reporting 2/5 · AI safety 2/5 · Reliability 3/5 · Scalability 2/5 · Security 3/5 → *not yet safe to scale further automation; F-01/F-02/F-04 first.*

**Sample quick wins (≤1 day each):** F-04, F-06, F-07, F-08 — meaningful risk reduction in the first week before any deeper work.

---

*Methodology diagram: `docs/methodology.png`. This framework is provided up-front so a client knows exactly what they are buying.*
