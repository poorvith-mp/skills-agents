# Trigger.dev Config Reference

Configuration settings for `trigger.config.ts`.

## Project Configuration
```typescript
import { defineConfig } from "@trigger.dev/sdk/v3";

export default defineConfig({
  project: "proj_your_project_id",
  dirs: ["./src/trigger"],
  retries: {
    enabledInDev: true,
    default: {
      maxAttempts: 3,
      minTimeoutInMs: 1000,
      maxTimeoutInMs: 10000,
      factor: 2,
      randomize: true,
    },
  },
});
```

## Environment Variables
- `TRIGGER_SECRET_KEY` — API secret key required in `.env`.
- Project references match between development and staging environments.
