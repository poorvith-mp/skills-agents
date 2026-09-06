# skills-agents

Agents & Automation skills collection for Claude Code, Cursor, Codex, Gemini CLI, and `npx skills` — part of [Skillary](https://github.com/poorvith-mp/skillary) by [Poorvith M P](https://github.com/poorvith-mp).

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

| Skill ID | Title | Description |
|:---------|:------|:------------|
| `agent-architecture` | [Agent Architecture](skills/agent-architecture/SKILL.md) | Design role decomposition, message passing, shared state and failure isolation across agents. |
| `agent-identity` | [Agent Identity](skills/agent-identity/SKILL.md) | Design authentication, delegation and trust verification between agents, and resolve "who is this" consistently across sources. |
| `agent-orchestration` | [Agent Orchestration](skills/agent-orchestration/SKILL.md) | Sequence agents through an end-to-end pipeline, passing state between them, with pre-flight environment checks. |
| `agent-safety` | [Agent Safety](skills/agent-safety/SKILL.md) | Defend against prompt injection, scope tool permissions, filter outputs, and fail closed when a check can't run. |

### Retrieval

| Skill ID | Title | Description |
|:---------|:------|:------------|
| `rag-systems` | [Rag Systems](skills/rag-systems/SKILL.md) | Build retrieval that works: chunking strategy, embeddings, hybrid search, reranking and retrieval evals. |

### Tooling agents call

| Skill ID | Title | Description |
|:---------|:------|:------------|
| `composio` | [Composio](skills/composio/SKILL.md) | Wire agents to authenticated third-party SaaS tools through Composio connectors. |
| `mcp-builder` | [MCP Builder](skills/mcp-builder/SKILL.md) | Design, build and test MCP servers exposing tools, resources and prompts, including why a tool never gets called. |

### Prompts and skills

| Skill ID | Title | Description |
|:---------|:------|:------------|
| `create-skill` | [Create Skill](skills/create-skill/SKILL.md) | Scaffold a new agent skill: frontmatter, trigger-rich description, structured body, references and a routing eval set. |
| `prompt-engineering` | [Prompt Engineering](skills/prompt-engineering/SKILL.md) | Write, test and optimise prompts into reliable production behaviour. |
| `prompt-library` | [Prompt Library](skills/prompt-library/SKILL.md) | Maintain reusable prompt libraries: versioning, tagging, benchmarking and deduplication across teams. |
| `skill-linter` | [Skill Linter](skills/skill-linter/SKILL.md) | Check a skill against house rules: description length, slug length, pointer scope and allowed frontmatter keys. |

### Workflow automation

| Skill ID | Title | Description |
|:---------|:------|:------------|
| `automation-review` | [Automation Review](skills/automation-review/SKILL.md) | Audit a proposed or existing automation for value, risk and maintenance cost, including the answer "don't build this." |
| `n8n` | [N8n](skills/n8n/SKILL.md) | Build n8n workflows: node wiring, credentials, error branches and scheduling. |
| `trigger-dev` | [Trigger Dev](skills/trigger-dev/SKILL.md) | Build Trigger.dev background jobs: task definitions, retries, scheduling and observability. |
| `workflow-mapping` | [Workflow Mapping](skills/workflow-mapping/SKILL.md) | Map a complete workflow tree: happy paths, branch conditions and failure states. |

### Outputs

| Skill ID | Title | Description |
|:---------|:------|:------------|
| `document-generation` | [Document Generation](skills/document-generation/SKILL.md) | Generate PDF, PPTX, DOCX and XLSX from code with real formatting, charts and tables. |
| `report-automation` | [Report Automation](skills/report-automation/SKILL.md) | Merge multiple exports and unstructured sources into one consistent view, then schedule and distribute it. |

## License

MIT © [Poorvith M P](https://github.com/poorvith-mp)
