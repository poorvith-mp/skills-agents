---
name: agent-architecture
description: >-
  Design role decomposition, message passing, shared state and failure isolation across agents.
---
# Agent Architecture

Design multi-agent systems where coordination does not become the failure mode.

## Process
1. **Justify the second agent.** Most tasks that look multi-agent are one agent with better tools. Split only when roles need genuinely different context or run concurrently.
2. **Decompose by context boundary**, not by job title. An agent exists to hold a distinct working set, not to mirror an org chart.
3. **Define the contract between agents** — the exact shape of what is passed, and what is guaranteed about it. Free-text handoff is where multi-agent systems lose information.
4. **Decide where state lives.** Shared mutable state between agents needs the same care as between threads.
5. **Isolate failure.** One agent failing or looping must not stall the others; set timeouts and a supervisor that can terminate.
6. **Make the trace reconstructable.** When output is wrong, you need to see which agent decided what, with what input.
7. **Bound the total cost.** Agents that can spawn agents need a hard ceiling.

## Deliverables
- Role decomposition with the context boundary justifying each agent
- Message contracts between agents
- Failure isolation, timeouts and supervision model
- Trace format and cost ceiling

## Verification & Quality Checklist

- [ ] Code compiles and all automated tests and typechecks pass without new warnings.
- [ ] Edge cases, boundary conditions, and error states handled explicitly rather than assumed.
- [ ] No hardcoded secrets, credentials, or insecure defaults introduced.
- [ ] Changes are covered by a test that fails without them.

## Anti-Patterns & Constraints

- NEVER weaken or skip a failing test to make a change land.
- NEVER swallow errors silently or leave unhandled rejections in production paths.
- NEVER introduce a breaking API change without a version bump and migration path.
