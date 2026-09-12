---
name: agent-identity
last_reviewed: 2026-09-06
group: Agent design
description: >-
  Design authentication, delegation, and trust verification across agents and identity sources.
  Use when establishing agent identity, auth, or provenance.
---

# agent-identity

## Core Philosophy
Autonomous AI agents cannot operate in production under shared, static API keys or god-mode admin credentials. An agent is an independent non-human entity executing non-deterministic actions. Production agent architecture requires verifiable cryptographic identity, short-lived scoped delegation tokens, mutual TLS (mTLS) between agent runtimes, and an auditable cryptographic provenance chain for every tool execution and artifact generated.

---

## 4-Stage Agent Identity & Delegation Architecture

### Stage 1: Cryptographic Identity & Attestation
1. **Machine Identity Provisioning**:
   - Issue each agent instance a unique SPIFFE ID (e.g. `spiffe://cluster.local/ns/agents/sa/code-reviewer`).
   - Use short-lived X.509 certificates generated via an internal CA (HashiCorp Vault or SPIRE) with automated 60-minute rotation.
2. **Runtime Attestation**:
   - Validate agent execution integrity: verify container image digest, host TPM hash, and signed system prompt hash before granting credentials.

### Stage 2: Scoped Delegation & Ephemeral Token Exchange
1. **OAuth 2.0 Token Exchange (RFC 8693)**:
   - When a human user delegates a task to an agent, the agent exchanges the user's primary identity token for an ephemeral, down-scoped access token:
     - Down-scoping: If user has `read:all, write:all, delete:all`, the agent receives only `read:repo, write:branch` with a 15-minute TTL.
2. **Actor Chaining Claims**:
   - Structure JSON Web Tokens (JWT) with explicit `act` (actor) claims to preserve audit trails:
     ```json
     {
       "sub": "user_12345",
       "act": {
         "sub": "agent_code_builder_v3",
         "iss": "https://auth.internal.corp"
       },
       "scope": "repo:write_branch",
       "exp": 1726000000
     }
     ```

### Stage 3: Tool-Level Access Control & Permissions Gates
1. **Deterministic Permission Matrix**:
   - Classify tools into risk tiers:
     - *Tier 0 (Read-Only)*: Search, inspect file, fetch URL (Agent executes autonomously).
     - *Tier 1 (Idempotent Mutation)*: Create feature branch, write scratch file (Agent executes with logging).
     - *Tier 2 (Destructive / External)*: Push to `main`, execute database migration, transfer funds (Requires cryptographically signed human approval).
2. **Per-Tool Ephemeral Credentials**:
   - Never inject raw database passwords into LLM context. Pass single-use signed tokens or execute tools via isolated gRPC sidecars.

### Stage 4: Provenance Chaining & Artifact Signing
1. **Cryptographic Signing of Agent Outputs**:
   - Sign generated code commits, PRs, and reports using Sigstore / Cosign with the agent's ephemeral private key.
2. **Immutable Audit Ledger**:
   - Log: `Timestamp`, `Agent_SPIFFE_ID`, `Prompt_Hash`, `Tool_Name`, `Arguments_Hash`, `Execution_Status`, `Approver_ID`.

---

## Deliverable Format: Agent Identity Specification (`AGENT-IDENTITY-SPEC.md`)

```markdown
# Agent Identity & Authentication Architecture

## 1. Identity & Credential Topology
- **Agent Type**: [e.g. Autonomous CI Remediation Agent]
- **SPIFFE ID**: `spiffe://prod.internal/agent/ci-healer`
- **Identity Provider (IdP)**: [Vault / SPIRE / Keycloak]
- **Certificate TTL**: 60 minutes (automated rotation)

## 2. Token Scoping & Actor Chaining
- **Down-Scoped OAuth Scopes**: `["repo:read", "git:push_feature_branch"]`
- **Parent Principal**: `user_deploy_engineer`
- **Actor Claim**: `agent_ci_healer_v2`

## 3. Tool Permission & Risk Matrix
| Tool Name | Risk Tier | Execution Gate | Credential Mechanism |
|---|---|---|---|
| `read_file` | Tier 0 | Autonomous | Read-only container mount |
| `create_branch` | Tier 1 | Autonomous | Ephemeral Git token (15m TTL) |
| `trigger_deploy` | Tier 2 | Human Approval Gate | Webhook signed by Tech Lead |

## 4. Cryptographic Provenance
- **Artifact Signing**: Cosign keyless signing tied to SPIFFE certificate.
- **Audit Target**: Append-only OpenSearch cluster (`#audit-agent-ops`).
```

---

## Worked Example: Autonomous Code Agent Identity Flow

- **Flow**: User asks agent to fix a failing test.
- **Authentication**: Agent exchanges user session cookie for a 15-minute down-scoped GitHub App installation token restricted solely to repository `repo-api` and branch `fix/*`.
- **Commit Sign-Off**: Agent commits fix signed with GPG subkey verifying `commit-signer: agent-builder@internal`. Pull request description displays verified badge.
- **Audit**: Security team can trace exact model checkpoint, system prompt version, and user authorization chain for the commit.

---

## Verification Checklist

- [ ] Agent operates with short-lived ephemeral tokens (TTL $\le 60$ minutes).
- [ ] Actor chaining (`act` claim) is preserved in all downstream authorization tokens.
- [ ] Tier 2 destructive actions enforce a hard human-in-the-loop cryptographic gate.
- [ ] Agent private keys are stored in secure memory or TPM, never written to disk or logs.
- [ ] All code commits or public mutations are cryptographically signed.

---

## Anti-Patterns

- **Static Long-Lived API Keys**: Storing a permanent admin personal access token in an environment variable for the agent.
- **God-Mode Agent Context**: Giving an agent full write access to production databases without tool-level schema checks.
- **Anonymous Execution**: Running multi-agent swarms where actions cannot be attributed to a specific model version and user session.
