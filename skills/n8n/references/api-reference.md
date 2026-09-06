# n8n REST API Reference

Automate workflow activation, execution monitoring, and credentials via the REST API.

## Core Endpoints
- `GET /api/v1/workflows` — List configured workflows.
- `POST /api/v1/workflows/{id}/activate` — Activate production triggers.
- `POST /api/v1/workflows/{id}/deactivate` — Pause trigger processing.
- `GET /api/v1/executions` — Audit execution history, status, and run durations.

## Authentication
Include API key in the `X-N8N-API-KEY` header:
```bash
curl -H "X-N8N-API-KEY: your_api_key" http://localhost:5678/api/v1/workflows
```
