# Commands

## 1) Generate Type Definitions from OpenAPI

```bash
# pnpm
pnpm dlx openapi-typescript ./openapi.json -o ./src/api/generated/schema.ts

# npm
npx openapi-typescript ./openapi.json -o ./src/api/generated/schema.ts
```

## 2) Install Runtime Dependencies

```bash
pnpm add openapi-fetch @tanstack/react-query zod
```

## 3) Optional Script Aliases (package.json)

```json
{
  "scripts": {
    "api:types": "openapi-typescript ./openapi.json -o ./src/api/generated/schema.ts",
    "api:sync": "pnpm run api:types"
  }
}
```

## 4) Suggested Frontend Structure

```text
src/api/
  generated/schema.ts
  client.ts
  errors.ts
  hooks.ts
```

## 5) Minimal `client.ts`

```ts
import createClient from "openapi-fetch";
import type { paths } from "./generated/schema";

export const apiClient = createClient<paths>({
  baseUrl: import.meta.env.VITE_API_BASE_URL,
  credentials: "include",
});
```

## 6) Typical Sync Flow

```bash
# 1. Replace openapi.json from backend export
# 2. Regenerate types
pnpm run api:types
# 3. Run type-check
pnpm tsc --noEmit
```
