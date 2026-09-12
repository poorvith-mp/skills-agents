---
name: prompt-library
last_reviewed: 2026-09-06
group: Prompts and skills
description: >-
  Maintain reusable prompt libraries: versioning, tagging, benchmarking and deduplication across
  teams. Use when curating, organizing, or categorizing reusable prompt collections.
---

# prompt-library

## Core Philosophy
Storing system prompts as scattered string literals inside application code or unversioned Google Docs produces massive prompt drift, untracked regressions, and impossible debugging. System prompts are critical production software assets. A world-class prompt library treats prompts like code: strict Semantic Versioning, typed variable schemas, automated regression evals across model upgrades, and vector-based deduplication across teams.

---

## 4-Step Prompt Library Engineering System

### Step 1: Versioning Architecture & File Structure
1. **Semantic Versioning (SemVer) for Prompts**:
   - `vMAJOR.MINOR.PATCH`:
     - *MAJOR*: Radical architectural rewrite, changed input/output contract or role.
     - *MINOR*: Added instructions, refined few-shot examples, improved nuance.
     - *PATCH*: Typo fix, formatting polish, minor phrasing tweak.
2. **Directory Structure**:
   ```
   prompts/
   ├── core/
   │   └── code-reviewer/
   │       ├── v1.2.0.md
   │       ├── schema.json
   │       └── evals.yaml
   └── metadata.json
   ```

### Step 2: Typed Input/Output Schema Contracts
1. **Template Engine Standardization**:
   - Use deterministic templating engines (Mustache, Jinja2) with strict variable typing.
2. **Schema Definition (JSON Schema / Pydantic)**:
   - Declare every dynamic input parameter explicitly:
     ```json
     {
       "type": "object",
       "required": ["language", "code_snippet", "max_comments"],
       "properties": {
         "language": { "type": "string", "enum": ["typescript", "go", "python"] },
         "code_snippet": { "type": "string", "maxLength": 50000 },
         "max_comments": { "type": "integer", "default": 3 }
       }
     }
     ```

### Step 3: Automated Regression Evals & Benchmarking
1. **The Golden Dataset**:
   - Maintain a test suite of 20–50 canonical inputs with expected outputs and deterministic assertions for every prompt.
2. **Evaluation Gates**:
   - When updating a prompt (`v1.1.0` -> `v1.2.0`):
     - *Deterministic Tests*: Verify output matches required JSON schema, contains no markdown ticks if disallowed, stays under token limit.
     - *Semantic Similarity*: Measure cosine similarity against baseline golden responses.
     - *LLM-as-Judge*: Grade responses on accuracy, conciseness, and tone.
   - Never merge a prompt update that degrades benchmark pass rates.

### Step 4: Library Curation, Tagging & Deduplication
1. **Vector-Based Deduplication**:
   - Generate embeddings for all prompt system instructions.
   - Flag semantic similarity $> 85\%$ across different internal teams to merge redundant prompts and consolidate engineering effort.
2. **Taxonomy & Tagging**:
   - Tag by: `Domain` (Code, Writing, Analysis), `Model Target` (Claude 3.5 Sonnet, GPT-4o), `Latency Profile` (Streaming, Batch).

---

## Deliverable Format: Prompt Registry Specification (`PROMPT-SPEC.md`)

```markdown
# Prompt Registry Specification: [Prompt Name]

## 1. Metadata & Versioning
- **Prompt ID**: `code-review-security`
- **Current Version**: `v2.1.0`
- **Target Models**: Claude 3.5 Sonnet, GPT-4o
- **Author**: Platform Security Team

## 2. Input Parameter Schema
```json
{
  "pull_request_diff": "string (required)",
  "security_level": "enum: ['strict', 'standard']",
  "language": "string"
}
```

## 3. System Prompt Template (`v2.1.0.md`)
```markdown
You are a Principal Security Engineer auditing a pull request diff in {{ language }}.
Audit the provided diff exclusively for security vulnerabilities: SQL injection, auth bypass, buffer overflows, and secret leaks.

Instructions:
1. If no vulnerabilities are present, respond with: `{"status": "PASS", "findings": []}`.
2. If vulnerabilities exist, return structured findings matching the output schema.
3. Do not comment on style, formatting, or performance.

Diff to audit:
{{ pull_request_diff }}
```

## 4. Benchmark & Eval Results
- **Golden Test Suite**: `evals/security-suite-v1.yaml` (42 test cases)
- **Schema Compliance**: 100%
- **Detection Rate (Precision / Recall)**: 96% / 92%
```

---

## Worked Example: Enterprise Customer Support Prompt Versioning

- **Challenge**: 4 product teams had built 4 separate customer support prompts, leading to inconsistent brand tone and broken JSON responses.
- **Solution**: Consolidated into a single prompt library module with SemVer. Added strict JSON schema validation for tool calls and 30 canonical test cases.
- **Outcome**: Eliminated JSON parse errors completely; prompt updates now run through CI evaluation before production deployment.

---

## Verification Checklist

- [ ] Prompt uses Semantic Versioning (`vMAJOR.MINOR.PATCH`).
- [ ] Input parameters are defined with strict types and validation schemas.
- [ ] Golden test suite exists with at least 15 representative inputs.
- [ ] Automated CI eval runs before prompt updates are promoted.
- [ ] Redundant prompts across teams are identified and deduplicated.

---

## Anti-Patterns

- **Hardcoded String Literals**: Burying 500-line prompt strings inside frontend components or database rows.
- **Prompt Tweaking Without Tests**: Editing a production prompt directly in response to a single bad output without running regression tests.
- **Schema-Free Outputs**: Asking the model to "give me some JSON" without providing a strict JSON schema contract.
