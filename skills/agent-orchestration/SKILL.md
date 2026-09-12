---
name: agent-orchestration
last_reviewed: 2026-09-06
group: Agent design
description: >-
  Sequence agents through an end-to-end pipeline, passing state between them, with pre-flight
  environment checks. Use when coordinating multi-agent systems, delegation, or subagent crews.
---

# agent-orchestration

## Core Philosophy
Chaining multiple LLM agents together without deterministic orchestration is a recipe for exponential token costs, cascading hallucination errors, and non-deterministic deadlocks. High-performance agent orchestration treats agents as specialized compute workers within a stateful Directed Acyclic Graph (DAG) or blackboard architecture. Every state transition must be validated against explicit JSON schemas, protected by circuit breakers, and enforce strict token and recursion budgets.

---

## 4-Step Agent Orchestration Engineering

### Step 1: Topology Selection & State Architecture
1. **Orchestration Topologies**:
   - *Sequential Pipeline (DAG)*: Step A -> Step B -> Step C (Ideal for deterministic data extraction, transformation, and publishing).
   - *Supervisor / Router Pattern*: Central orchestrator agent analyzes intent and dispatches tasks to specialized subagents (Research, Code, Review).
   - *Blackboard / Shared State Architecture*: Independent agents read and write asynchronously to a centralized, schema-enforced state store.
2. **State Immutability & Versioning**:
   - State must be passed as an explicit immutable snapshot (`AgentState_v1 -> AgentState_v2`).
   - Avoid implicit global variables; every subagent receives strictly the subset of state required for its role.

### Step 2: Pre-Flight Environment Checks & Resource Gating
1. **Pre-Execution Guardrails**:
   - Before launching an expensive subagent, verify:
     - Workspace cleanliness (no conflicting lockfiles or uncommitted dirty diffs).
     - Upstream API rate limits and token balance.
     - Tool endpoint health via active ping.
2. **Context Compression & State Truncation**:
   - Subagents must not inherit the parent's entire 100k-token conversation history.
   - Summarize parent context into a structured 1-page briefing memo before invocation.

### Step 3: Circuit Breakers, Timeouts & Deadlock Prevention
1. **Hard Operational Limits**:
   - *Max Recursion Depth*: Limit subagent spawning depth to $\le 2$ levels.
   - *Max Step Count*: Enforce a hard ceiling of 15 tool executions per subagent invocation.
   - *Total Token Budget*: Set hard token spend caps per run (e.g. 50,000 tokens maximum).
2. **Deadlock & Flapping Detection**:
   - If two agents ping-pong the same task with conflicting edits $\ge 2$ times, trip the circuit breaker, abort execution, and escalate to human operator.

### Step 4: Idempotent Recovery & Checkpoint Replay
1. **Checkpointed Resumption**:
   - Persist state to durable storage (Redis or PostgreSQL) after each node completion.
   - On container crash or network timeout, resume execution from the last successful checkpoint rather than re-running the entire pipeline from scratch.

---

## Deliverable Format: Agent Orchestration Specification (`ORCHESTRATION-SPEC.md`)

```markdown
# Multi-Agent Orchestration Specification: [Pipeline Name]

## 1. Pipeline Topology & State Graph
- **Topology**: [Supervisor Router / Linear DAG / Blackboard]
- **State Store**: [Redis / Postgres / In-Memory Typed Dict]
- **Max Step Budget**: 15 steps per subagent | **Total Token Ceiling**: 80,000 tokens

```mermaid
graph TD
    Supervisor[Supervisor Router] -->|Classify Task| CodeBuilder[Code Builder Agent]
    Supervisor -->|Classify Task| DocResearcher[Doc Researcher Agent]
    CodeBuilder -->|Emit Diff| Verifier[Test Verifier Agent]
    Verifier -->|Tests Pass| Done[Final Output]
    Verifier -->|Tests Fail (Max 2 retries)| CodeBuilder
```

## 2. State Transition Schema
```json
{
  "task_id": "string",
  "iteration_count": 0,
  "context_summary": "string",
  "modified_files": ["string"],
  "test_results": {
    "passed": true,
    "exit_code": 0
  }
}
```

## 3. Circuit Breaker Parameters
- **Loop Limit**: Max 3 remediation loops before escalating to human.
- **Per-Node Timeout**: 120 seconds hard timeout.
- **Fallback Action**: Revert git workspace and log error to `#agent-alerts`.
```

---

## Worked Example: Automated Bug-Fix Orchestration

- **Pipeline**: Supervisor -> Code Builder -> Test Verifier -> Reviewer.
- **Execution**: Code Builder wrote patch; Test Verifier ran `pytest` in isolated container. Tests failed with exit code 1.
- **Loop**: Verifier appended stack trace to state; Supervisor routed back to Code Builder with error context (Iteration 1). Code Builder fixed syntax error. Tests passed on Iteration 2.
- **Result**: PR created with full test evidence and verified benchmark logs; total execution time 84 seconds.

---

## Verification Checklist

- [ ] Pipeline topology has a deterministic entry and exit node.
- [ ] State passed between agents conforms to a strict JSON/Pydantic schema.
- [ ] Hard step limits ($\le 15$) and token ceilings are configured and enforced.
- [ ] Flapping/infinite loop detection trips circuit breakers automatically.
- [ ] Intermediate node states are checkpointed to allow resumption after crashes.

---

## Anti-Patterns

- **Unconstrained Swarms**: Allowing 5 agents to talk to each other simultaneously without a central router or state contract.
- **Context Ballooning**: Passing entire raw conversation histories into every subagent, blowing token budgets.
- **Infinite Retry Loops**: Letting an agent retry a failing command 40 times in a loop without terminating.
