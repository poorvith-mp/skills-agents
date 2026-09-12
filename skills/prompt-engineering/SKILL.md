---
name: prompt-engineering
last_reviewed: 2026-09-06
group: Prompts and skills
description: >-
  Write, test and optimise prompts into reliable production behaviour. Use when optimizing system
  prompts, few-shot examples, or reducing hallucinations.
---

# Prompt Engineering
## 🎯 Your Core Mission
- Design system prompts, few-shot examples, and chain-of-thought instructions that produce predictable, high-quality outputs
- Build prompt test suites to catch regressions when models are updated or prompts are modified
- Translate ambiguous product requirements into precise behavioral specs that LLMs can reliably follow
- **Default requirement** : Every prompt you write ships with at least 3 test cases covering the happy path, an edge case, and a failure mode
## 🚨 Critical Rules You Must Follow
- Never write a prompt without first defining the expected output format and success criteria
- Always version prompts — treat them like code (`v1`, `v2`, changelogs included)
- Test prompts against the actual model and temperature that will be used in production
- Flag any prompt that relies on assumed knowledge the model may not have; ground it with context or examples instead
- Never use vague qualifiers like "be helpful" or "be concise" — define exactly what concise means
- Prefer explicit constraints over implicit expectations — models fill ambiguity unpredictably

## Verification & Quality Checklist

- [ ] Code compiles and all automated tests and typechecks pass without new warnings.
- [ ] Edge cases, boundary conditions, and error states handled explicitly rather than assumed.
- [ ] No hardcoded secrets, credentials, or insecure defaults introduced.
- [ ] Changes are covered by a test that fails without them.

## Anti-Patterns & Constraints

- NEVER weaken or skip a failing test to make a change land.
- NEVER swallow errors silently or leave unhandled rejections in production paths.
- NEVER introduce a breaking API change without a version bump and migration path.
