# skills-agents

Agents skills collection for Claude Code, Cursor, Codex, Gemini CLI, and `npx skills` — part of [Skillary](https://github.com/poorvith-mp/skillary) by [Poorvith M P](https://github.com/poorvith-mp).

- **Version**: `v4.0.0`
- **Total Skills**: `17`
- **License**: MIT
- **Hub Repository**: [poorvith-mp/skillary](https://github.com/poorvith-mp/skillary)

## Install

Install the entire collection via `npx skills`:
```bash
npx skills add poorvith-mp/skills-agents
```

Or install individual skills directly:
```bash
npx skills add poorvith-mp/skills-agents --skill <skill-id>
```

## Skills in this Collection

### Agent design

| Skill ID | Title | Description | Reviewed |
|:---------|:------|:------------|:---------|
| `agent-architecture` | [Agent Architecture](skills/agent-architecture/SKILL.md) | Design role decomposition, message passing, shared state and failure isolation across agents. Use when designing autonomous agent state machines, memory, or loops. | 2026-09-06 |
| `agent-identity` | [Agent Identity](skills/agent-identity/SKILL.md) | Design authentication, delegation, and trust verification across agents and identity sources. Use when establishing agent identity, auth, or provenance. | 2026-09-06 |
| `agent-orchestration` | [Agent Orchestration](skills/agent-orchestration/SKILL.md) | Sequence agents through an end-to-end pipeline, passing state between them, with pre-flight environment checks. Use when coordinating multi-agent systems, delegation, or subagent crews. | 2026-09-06 |
| `agent-safety` | [Agent Safety](skills/agent-safety/SKILL.md) | Defend against prompt injection, scope tool permissions, filter outputs, and fail closed when a check can't run. Use when implementing agent guardrails, sandboxing, or injection defense. | 2026-09-06 |

### Workflow automation

| Skill ID | Title | Description | Reviewed |
|:---------|:------|:------------|:---------|
| `automation-review` | [Automation Review](skills/automation-review/SKILL.md) | Audit a proposed or existing automation for value, risk and maintenance cost, including the answer "don't build this.". Use when auditing automation ROI, failure points, or recommending 'don't build'. | 2026-09-06 |
| `n8n` | [N8n](skills/n8n/SKILL.md) | Build n8n workflows: node wiring, credentials, error branches and scheduling. Use when building visual workflow automations, webhooks, or nodes in n8n. | 2026-09-06 |
| `trigger-dev` | [Trigger Dev](skills/trigger-dev/SKILL.md) | Build Trigger.dev background jobs: task definitions, retries, scheduling and observability. For visual workflows use n8n. Use when writing code-first background tasks or Trigger.dev. | 2026-09-06 |
| `workflow-mapping` | [Workflow Mapping](skills/workflow-mapping/SKILL.md) | Map a complete workflow tree: happy paths, branch conditions and failure states. Use when mapping processes into step-by-step logic, gates, or edge cases. | 2026-09-06 |

### Tooling agents call

| Skill ID | Title | Description | Reviewed |
|:---------|:------|:------------|:---------|
| `composio` | [Composio](skills/composio/SKILL.md) | Wire agents to authenticated third-party SaaS tools through Composio connectors. Use when integrating AI agents with GitHub, Slack, Gmail, or Jira via Composio. | 2026-09-06 |
| `mcp-builder` | [MCP Builder](skills/mcp-builder/SKILL.md) | Design, build and test MCP servers exposing tools, resources and prompts, including why a tool never gets called. Use when creating Model Context Protocol (MCP) servers, tools, or resources. | 2026-09-06 |

### Prompts and skills

| Skill ID | Title | Description | Reviewed |
|:---------|:------|:------------|:---------|
| `create-skill` | [Create Skill](skills/create-skill/SKILL.md) | Scaffold a new agent skill: frontmatter, trigger-rich description, structured body, references and a routing eval set. Use when authoring new agent skills, SKILL.md specs, or routing evals. | 2026-09-06 |
| `prompt-engineering` | [Prompt Engineering](skills/prompt-engineering/SKILL.md) | Write, test and optimise prompts into reliable production behaviour. Use when optimizing system prompts, few-shot examples, or reducing hallucinations. | 2026-09-06 |
| `prompt-library` | [Prompt Library](skills/prompt-library/SKILL.md) | Maintain reusable prompt libraries: versioning, tagging, benchmarking and deduplication across teams. Use when curating, organizing, or categorizing reusable prompt collections. | 2026-09-06 |
| `skill-linter` | [Skill Linter](skills/skill-linter/SKILL.md) | Check a skill against house rules: description length, slug length, pointer scope and allowed frontmatter keys. Use when validating agent skills for length, triggers, evals, or spec rules. | 2026-09-06 |

### Outputs

| Skill ID | Title | Description | Reviewed |
|:---------|:------|:------------|:---------|
| `document-generation` | [Document Generation](skills/document-generation/SKILL.md) | Generate PDF, PPTX, DOCX and XLSX from code with real formatting, charts and tables. Use when generating PDF, Word, or Markdown documents from data and templates. | 2026-09-06 |
| `report-automation` | [Report Automation](skills/report-automation/SKILL.md) | Merge multiple exports and unstructured sources into one consistent view, then schedule and distribute it. Use when automating scheduled metrics digests, executive summaries, or alerts. | 2026-09-06 |

### Retrieval

| Skill ID | Title | Description | Reviewed |
|:---------|:------|:------------|:---------|
| `rag-systems` | [Rag Systems](skills/rag-systems/SKILL.md) | Build retrieval that works: chunking strategy, embeddings, hybrid search, reranking and retrieval evals. Use when building RAG pipelines, chunking, embeddings, or vector search. | 2026-09-06 |

## License

MIT © [Poorvith M P](https://github.com/poorvith-mp)
