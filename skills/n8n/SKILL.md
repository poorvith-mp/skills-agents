---
name: n8n
group: Workflow automation
description: >-
  Build n8n workflows: node wiring, credentials, error branches and scheduling. Use when building
  visual workflow automations, webhooks, or nodes in n8n.
---

# N8n
You are an expert at building production-grade n8n workflow automations, custom nodes, and integrations.
Read the detailed reference files for comprehensive patterns:
- [references/workflow-reference.md](references/workflow-reference.md) — Workflow design, triggers, flow control, error handling, expressions, data transformation
- [references/custom-nodes-reference.md](references/custom-nodes-reference.md) — Building custom nodes with TypeScript, declarative vs programmatic, credentials, testing
- [references/api-reference.md](references/api-reference.md) — n8n REST API for programmatic workflow management, execution control, credential operations
## Setup Checklist
### Self-Hosted (Docker)
```bash
docker run -it --rm --name n8n -p 5678:5678 -v n8n_data:/home/node/.n8n docker.n8n.io/n8nio/n8n
```
### Custom Node Development
```bash
npx n8n-node-dev new        # scaffold a new node
npm link                     # link node to local n8n
n8n start                   # start with custom nodes loaded
```
### npm (Global)
```bash
npm install n8n -g
n8n start
```
## Core Patterns
### Workflow JSON Structure
```json
{
  "name": "My Workflow",
  "nodes": [
    {
      "parameters": {},
      "id": "unique-id",
      "name": "Webhook",
      "type": "n8n-nodes-base.webhook",
      "typeVersion": 2,
      "position": [250, 300]
    }
  ],
  "connections": {
    "Webhook": {
      "main": [[{ "node": "Next Node", "type": "main", "index": 0 }]]
    }
  },
  "settings": { "executionOrder": "v1" }
}
```
### Common Trigger Types
| Trigger | Use Case |
|---------|----------|
| `n8n-nodes-base.webhook` | HTTP requests, API endpoints |
| `n8n-nodes-base.scheduleTrigger` | Cron-based recurring tasks |
| `n8n-nodes-base.formTrigger` | User form submissions |
| `n8n-nodes-base.emailReadImap` | Incoming emails |
| `n8n-nodes-base.workflowTrigger` | Called by other workflows |
### Expression Syntax
```plain text
{{ $json.fieldName }}                    // current node data
{{ $input.first().json.field }}          // first input item
{{ $('NodeName').first().json.field }}   // data from specific node
{{ $now.toFormat('yyyy-MM-dd') }}        // Luxon date formatting
{{ $if($json.age > 18, "adult", "minor") }}  // conditional
{{ $jmespath($json, "items[?price > `100`]") }}  // JMESPath query
```
### Error Handling Pattern
```json
{
  "nodes": [
    {
      "name": "Main Task",
      "type": "n8n-nodes-base.httpRequest",
      "onError": "continueErrorOutput",
      "retryOnFail": true,
      "maxTries": 3,
      "waitBetweenTries": 1000
    }
  ]
}
```
### Code Node (JavaScript)
```javascript
// In a Code node — process all items
const results = [];
for (const item of $input.all()) {
  results.push({
    json: {
      processed: item.json.name.toUpperCase(),
      timestamp: new Date().toISOString(),
    }
  });
}
return results;
```
### Code Node (Python)
```python
# In a Code node — process all items
results = []
for item in _input.all():
    results.append({
        "json": {
            "processed": item.json["name"].upper(),
            "timestamp": str(datetime.now()),
        }
    })
return results
```
## Critical Rules
1. **Every workflow needs a trigger node** — webhooks, schedules, form triggers, or app triggers start execution
2. **Items are arrays** — each node receives and outputs arrays of items; always handle multiple items
3. **Use expressions over Code nodes** — expressions are faster and easier to maintain; use Code only for complex logic
4. **Set `executionOrder: "v1"`** — ensures predictable node execution order in new workflows
5. **Error workflows are separate** — configure a dedicated error workflow in workflow settings to catch failures
6. **Credentials are encrypted at rest** — never hardcode secrets in node parameters; use n8n's credential system
7. **Webhook paths must be unique** — duplicate paths cause routing conflicts
8. **Binary data needs explicit handling** — use "Move Binary Data" node to convert between binary and JSON
9. **Test with manual execution first** — always test workflows manually before activating for production
10. **Pin data for development** — use pinned data on nodes to test downstream logic without re-triggering
11. **Sub-workflows for reuse** — extract shared logic into sub-workflows called via Execute Workflow node
12. **Respect rate limits** — use the SplitInBatches node and wait nodes when calling rate-limited APIs
## Key Node Categories
| Category | Nodes |
|----------|-------|
| **Flow** | IF, Switch, Merge, SplitInBatches, Loop Over Items |
| **Transform** | Set, Code, HTML Extract, Markdown, XML, Date & Time |
| **Data** | HTTP Request, GraphQL, FTP, RSS, Read/Write Files |
| **Developer** | Webhook, Execute Command, Execute Workflow, Function |
| **AI** | AI Agent, Text Classifier, Summarization Chain, Vector Store |
Use `$ARGUMENTS` to understand what the user wants to build. Read the reference files for detailed patterns before writing code.
---


## Output format
- Lead with the result the user asked for.
- Use clear headings and bullet lists where helpful.
- Call out assumptions and open questions at the end.
- Stay specific to the n8n workflow; avoid generic filler.

## Verification & Quality Checklist

- [ ] Code compiles and all automated tests and typechecks pass without new warnings.
- [ ] Edge cases, boundary conditions, and error states handled explicitly rather than assumed.
- [ ] No hardcoded secrets, credentials, or insecure defaults introduced.
- [ ] Changes are covered by a test that fails without them.

## Anti-Patterns & Constraints

- NEVER weaken or skip a failing test to make a change land.
- NEVER swallow errors silently or leave unhandled rejections in production paths.
- NEVER introduce a breaking API change without a version bump and migration path.
