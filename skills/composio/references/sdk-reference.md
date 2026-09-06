# Composio SDK Reference

Comprehensive SDK patterns for Python and TypeScript integrations.

## Installation
```bash
# Python
pip install composio-core composio-claude

# TypeScript / Node.js
npm install composio-core @composio/claude-agent-sdk
```

## Client Initialization

### Python
```python
from composio import Composio, App

client = Composio(api_key="your_api_key")
entity = client.get_entity("user_unique_id")
```

### TypeScript
```typescript
import { Composio } from "composio-core";

const client = new Composio({ apiKey: process.env.COMPOSIO_API_KEY });
const entity = client.getEntity("user_unique_id");
```

## Action Execution
```python
# Execute discrete actions
response = entity.execute(
    action=App.GITHUB_CREATE_ISSUE,
    params={
        "owner": "owner-name",
        "repo": "repo-name",
        "title": "Bug report",
        "body": "Issue details"
    }
)
```

## Error Handling & Rate Limits
- Handle `ComposioSDKError` with retry backoff.
- Verify status codes and check for missing auth credentials before action dispatch.
