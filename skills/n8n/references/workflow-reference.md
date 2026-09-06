# n8n Workflow Reference

Production patterns for building resilient n8n workflows.

## Flow Control & Data Transformation
- n8n processes items as arrays of JSON objects: `[{ json: { ... } }]`.
- Use the **Code** node for complex transformations:
```javascript
return $input.all().map(item => ({
  json: {
    ...item.json,
    processedAt: new Date().toISOString()
  }
}));
```

## Error Handling & Retry Policies
- Configure **Error Trigger** nodes to capture unhandled node failures.
- Set **Retry On Fail** with exponential backoff on flaky HTTP requests.
- Route failing executions to notification channels (Slack/Discord/Email) before halting.
