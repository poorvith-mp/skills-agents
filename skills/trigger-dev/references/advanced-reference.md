# Trigger.dev Advanced Reference

Realtime execution streams, schedules, and batch operations.

## Scheduled Tasks (Cron)
```typescript
import { schedules } from "@trigger.dev/sdk/v3";

export const dailyDigest = schedules.task({
  id: "daily-digest",
  cron: "0 9 * * *",
  run: async () => {
    // Run daily maintenance
  },
});
```

## Batch Processing
Trigger multiple jobs with shared parent context:
```typescript
import { batch } from "@trigger.dev/sdk/v3";

await batch.trigger(
  items.map((item) => ({
    task: processOrderTask,
    payload: { orderId: item.id },
  }))
);
```
