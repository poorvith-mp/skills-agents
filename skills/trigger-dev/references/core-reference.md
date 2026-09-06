# Trigger.dev Core Reference

Core patterns for task definitions, concurrency, and retries in Trigger.dev v4.

## Task Definition
```typescript
import { task } from "@trigger.dev/sdk/v3";

export const processOrderTask = task({
  id: "process-order",
  retry: {
    maxAttempts: 4,
    factor: 2,
    minTimeoutInMs: 1000,
    maxTimeoutInMs: 10000,
  },
  run: async (payload: { orderId: string }, { ctx }) => {
    // Execution logic
    return { status: "completed" };
  },
});
```

## Idempotency & Concurrency
- Use `idempotencyKey` on trigger dispatch to ensure once-and-only-once execution.
- Define queue concurrency limits to protect downstream APIs from load spikes:
```typescript
export const queuedTask = task({
  id: "rate-limited-task",
  queue: {
    concurrencyLimit: 5,
  },
  run: async () => {},
});
```
