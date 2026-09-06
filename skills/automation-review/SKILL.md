---
name: automation-review
group: Workflow automation
description: >-
  Audit a proposed or existing automation for value, risk and maintenance cost, including the
  answer "don't build this.". Use when auditing automation ROI, failure points, or recommending
  'don't build'.
---

# automation-review

## Core Philosophy
The most dangerous engineers are those who can automate anything in an afternoon, but never ask if it *should* be automated. Every automation is a permanent software maintenance liability: APIs break, dependencies rot, edge cases explode, and silent failures corrupt data. An automation review is a rigorous architectural and economic audit designed to evaluate value, failure modes, and long-term TCO—frequently concluding with the highest-leverage recommendation: **"Do not build this."**

---

## 4-Step Automation Audit Framework

### Step 1: The Economic ROI & Break-Even Calculus
1. **The Core Automation Equation**:
   $$text{Annual Net Value} = (text{Hours Saved / Year}  imes text{Blended Hourly Rate}) - (text{Initial Build Cost} + text{Annual Maintenance Cost})$$
   - *Build Cost*: Engineering hours to build, test, and document $ imes$ Hourly rate.
   - *Annual Maintenance Cost*: Historically 20–35% of the build cost per year (API migrations, bug fixes, edge cases).
2. **The "Rule of 50" Threshold**:
   - If a manual process takes $< 15$ minutes per week and is performed by 1 person, automating it is mathematically negative ROI unless error consequences are catastrophic.

### Step 2: Fragility & Dependency Audit
1. **The Brittle API Index**:
   - Does the workflow rely on unofficial/undocumented APIs or browser scraping (Puppeteer/Selenium)? Scraping workflows break every 4–8 weeks on average.
   - Are webhook contracts versioned and guaranteed with idempotency keys?
2. **Edge Case Entropy Assessment**:
   - What happens when input data contains unexpected null values, emojis, multi-gigabyte attachments, or corrupted encodings?
   - If handling edge cases requires 80% of the engineering effort, the process is too fuzzy for robust automation.

### Step 3: Blast Radius & Failure Mode Analysis
1. **Failure Mode Effects Analysis (FMEA)**:
   - *Silent Failure*: Automation fails to run; nobody notices for 3 weeks (Catastrophic).
   - *Corruptive Failure*: Automation runs, processes bad data, and writes garbage into the production database.
   - *Spam Loop*: Automation enters an infinite loop and sends 10,000 duplicate emails to customers.
2. **Circuit Breakers & Rollback Mechanisms**:
   - Does the proposed automation include rate limits, duplicate detection, and an immediate kill-switch?

### Step 4: The 4-Way Disposition Decision
1. **Classify the Request into One Bucket**:
   - *Automate Fully*: High frequency, deterministic rules, stable APIs, high volume.
   - *Human-in-the-Loop (Assisted)*: Automation prepares the draft/data; human clicks "Approve & Send".
   - *Standard Operating Procedure (SOP)*: Clean up the manual process with a 1-page checklist; keep it human.
   - **Kill ("Don't Build This")**: One-off task, highly fluid requirements, brittle dependencies, negative ROI.

---

## Deliverable Format: Automation Audit Report (`AUTOMATION-REVIEW.md`)

```markdown
# Automation Feasibility & Risk Audit: [Proposed Automation]

## 1. Executive Summary & Verdict
- **Proposed Initiative**: [Brief description of what is being automated]
- **Final Recommendation**: **[BUILD FULLY / HUMAN-IN-LOOP / PROCESS SOP / DO NOT BUILD]**
- **Core Rationale**: [1-2 sentences on why this verdict was reached]

## 2. Economic ROI & Maintenance Model
- **Manual Time Spent**: [X hours/week @ $Y/hr = $Z/year]
- **Estimated Build Effort**: [X engineering hours = $Y]
- **Estimated Annual Maintenance**: [~25% of build = $Z/year]
- **Break-Even Horizon**: [X months] (Target: < 6 months)

## 3. Technical Risk & Fragility Audit
| Dimension | Risk Rating (Low / Med / High) | Vulnerability / Failure Point |
|---|---|---|
| Dependency Stability | High | Relies on unofficial DOM scraping of partner portal |
| Edge Case Complexity | Medium | 15% of inputs have non-standard PDF formats |
| Blast Radius | High | Automatically triggers billing charge to customer card |

## 4. Alternative Recommendation (If "Do Not Build")
- Documented 5-step manual SOP in Notion.
- Estimated weekly manual time: 10 minutes. Saves $15k in custom software maintenance.
```

---

## Worked Example: Reviewing an Automated Invoice Scraper

- **Request**: Marketing requested a custom Python bot to scrape receipts from 14 different SaaS portals monthly.
- **Audit Findings**: 14 distinct third-party UIs with MFA; scrapers would break bi-weekly. Build cost $12,000; maintenance estimated at $4,000/year. Total manual time was only 45 minutes once a month.
- **Verdict**: **DO NOT BUILD.**
- **Alternative**: Configured auto-forwarding email rules and signed up for a standard $10/mo aggregation tool, saving 80 hours of dev time.

---

## Verification Checklist

- [ ] Net ROI is calculated including ongoing annual maintenance overhead ($\ge 20\%$).
- [ ] Dependencies are checked for official APIs vs brittle scraping.
- [ ] Blast radius and failure modes (silent failures, spam loops) are evaluated.
- [ ] Human-in-the-loop and manual SOP alternatives are explicitly considered.
- [ ] Document gives an unambiguous verdict: Build, Assist, SOP, or Kill.

---

## Anti-Patterns

- **Automating Chaos**: Automating an undefined, constantly shifting process that should be simplified first.
- **Ignoring Maintenance Cost**: Assuming an automation once written requires zero engineering maintenance forever.
- **Building Scrapers for Everything**: Writing brittle custom scrapers instead of using official webhooks or simple manual exports.
