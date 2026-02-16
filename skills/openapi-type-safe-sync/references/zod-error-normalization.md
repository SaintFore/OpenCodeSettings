# Zod Error Normalization

## Goal

Normalize all backend error payloads into one frontend-safe shape.

## Canonical Error Schema

```ts
import { z } from "zod";

export const ApiErrorSchema = z.object({
  code: z.string().default("UNKNOWN_ERROR"),
  message: z.string().default("Request failed"),
  details: z.unknown().optional(),
  status: z.number().int().optional(),
});

export type ApiErrorShape = z.infer<typeof ApiErrorSchema>;
```

## Parse Helper

```ts
import { ApiErrorSchema } from "./api-error-schema";

export function parseApiError(input: unknown): Error {
  const parsed = ApiErrorSchema.safeParse(input);
  if (parsed.success) {
    const e = new Error(parsed.data.message);
    (e as Error & { meta?: unknown }).meta = parsed.data;
    return e;
  }

  const fallback = new Error("Request failed");
  (fallback as Error & { meta?: unknown }).meta = {
    code: "UNKNOWN_ERROR",
    details: input,
  };
  return fallback;
}
```

## Alignment with Pydantic

- Keep `code` and `message` required in backend error models.
- Keep field names stable across all endpoints.
- If backend returns validation issues, map them into `details`.
- Version error contract changes together with OpenAPI updates.
