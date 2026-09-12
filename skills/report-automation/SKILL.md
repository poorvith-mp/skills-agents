---
name: report-automation
last_reviewed: 2026-09-06
group: Outputs
description: >-
  Merge multiple exports and unstructured sources into one consistent view, then schedule and
  distribute it. Use when automating scheduled metrics digests, executive summaries, or alerts.
---

# report-automation

## Core Philosophy
Manual executive reporting—exporting CSVs from 6 different SaaS dashboards, pasting them into Excel, manually aligning pivot tables, and taking screenshot crops for Slack—is a colossal waste of engineering and operational talent. Effective report automation builds a reliable, idempotent data pipeline: automated API extraction, schema normalization, anomaly detection, deterministic markdown/visual generation, and scheduled multi-channel distribution.

---

## 4-Step Report Automation Engineering Pipeline

### Step 1: Multi-Source Data Ingestion & Extraction
1. **API Connectors vs Direct Database Replicas**:
   - Extract raw metrics directly from source systems via read-only SQL replicas or official REST/GraphQL APIs (Stripe, GitHub, Google Analytics, PostHog).
   - Use deterministic pagination and incremental sync windows (e.g. `updated_at >= NOW() - INTERVAL '24 HOURS'`).
2. **Idempotency & Caching**:
   - Cache intermediate raw JSON/CSV responses in a local data lake or object store (S3/MinIO) before transformation to allow re-runs without hitting external API rate limits.

### Step 2: Data Normalization & Anomaly Detection
1. **Unified Schema Transformation**:
   - Normalize disparate metrics into a common reporting schema (standardizing UTC timestamps, currency conversions, and user identifiers).
2. **Automated Anomaly & Sanity Checks**:
   - Run validation rules prior to report generation:
     - *Null Check*: Zero null values in critical metric columns (Revenue, Active Users).
     - *Variance Spike Check*: If daily revenue or signups deviate $> 50\%$ from the 30-day moving average, flag for human verification before distribution.
     - *Completeness Check*: Confirm all expected data sources reported successfully.

### Step 3: Synthesis & Narrative Generation
1. **The Executive Formatting Hierarchy**:
   - *1. Executive TL;DR (3 Bullet Points)*: Headline metric, primary growth driver, critical risk/blocker.
   - *2. Key Metric Table (WoW / MoM Comparison)*: Current Period, Previous Period, % Change, Target.
   - *3. Deep Dive Insights*: Contextual explanation of variances.
2. **Deterministic vs LLM-Assisted Synthesis**:
   - Numbers and tables must be generated **100% deterministically** via code (Python / Pandas / SQL). Never let an LLM calculate or summarize core financial numbers.
   - Use LLMs strictly for qualitative narrative drafting based on verified deterministic metrics.

### Step 4: Scheduled Distribution & Alert Routing
1. **Multi-Channel Delivery Channels**:
   - Slack / Discord: Formatted rich markdown blocks with direct links to deep analytics.
   - Email: Clean, mobile-responsive HTML/Markdown digest sent to leadership.
2. **Cadence & Execution Scheduling**:
   - Schedule runs via cron or GitHub Actions at 6:00 AM local time Monday morning.
   - Implement failover alerting: Send urgent Slack alert to `#ops-alerts` if report fails generation.

---

## Deliverable Format: Automated Report Specification (`REPORT-SPEC.md`)

```markdown
# Report Automation Pipeline Specification: [Report Name]

## 1. Pipeline Overview & Schedule
- **Report Title**: Weekly Executive Engineering & Growth Digest
- **Execution Schedule**: Every Monday at 06:00 UTC (`0 6 * * 1`)
- **Distribution Targets**: Slack `#exec-metrics` & Leadership Email Distribution List

## 2. Ingestion Data Sources
| Source System | Extraction Method | Entity / Metric Extracted | Auth Mechanism |
|---|---|---|---|
| Stripe | API (`/v1/balance_transactions`) | Net ARR, Churn, New Subscriptions | Restricted API Key |
| PostHog | Analytics API | Weekly Active Workspaces, Retention | Personal API Key |
| GitHub | GraphQL API | Merged PRs, Open P1 Bugs, Mean Lead Time | GitHub App Token |

## 3. Anomaly Gates
- Metric values cannot be negative.
- If WoW churn increases > 25%, prepend `[ALERT]` badge to subject line.
- Pipeline aborts if Stripe API returns < 24 hours of data.

## 4. Delivery Template (Markdown Preview)
```markdown
# Weekly Executive Digest: Week of [YYYY-MM-DD]

### Executive Summary
- Net New ARR: **+$14,250** (+12% WoW) driven by 3 enterprise tier conversions.
- Active Workspaces: **2,840** (+4.1% WoW).
- Engineering Velocity: **42 PRs merged**; mean lead time 14 hours.

### Key Metrics Summary
| Metric | Current Week | Previous Week | % Change | Q3 Target |
|---|---|---|---|---|
| Total ARR | $1,240,000 | $1,225,750 | +1.16% | $1,350,000 |
| Active Workspaces | 2,840 | 2,728 | +4.10% | 3,000 |
| P1 Bugs Open | 0 | 2 | -100.0% | 0 |
```
```

---

## Worked Example: Automated SaaS Revenue & Health Digest

- **Previous Workflow**: Operations manager spent 3 hours every Monday morning manually downloading CSVs and building a Google Sheet.
- **Automated Pipeline**: Built a scheduled Python script in GitHub Actions fetching data via APIs, validating metrics against anomaly gates, and posting a formatted Slack block to the executive team at 7:00 AM.
- **Result**: Saved 150 hours of operational labor per year; eliminated human copy-paste reporting errors.

---

## Verification Checklist

- [ ] All numerical metrics are computed deterministically via code (not generated by LLM).
- [ ] Anomaly checks catch missing data or wild variance spikes before delivery.
- [ ] Pipeline is scheduled via durable runner (GitHub Actions / Airflow / cron).
- [ ] Delivery format is clean, mobile-optimized, and includes WoW/MoM delta percentages.
- [ ] Failure notifications are wired to alert engineering immediately if a run stalls.

---

## Anti-Patterns

- **LLM Math Hallucination**: Letting an LLM calculate percentage growth or sum currency columns.
- **Silent Failures**: Allowing a broken API token to cause the report to silently skip sending without notifying anyone.
- **Unverified Dashboard Dumps**: Sending a report with 50 uncurated metrics instead of focusing on the 3–5 numbers that drive the business.
