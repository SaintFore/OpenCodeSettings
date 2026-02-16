# React Query + OpenAPI Patterns

## Query Hook Pattern

```ts
import { useQuery, type UseQueryOptions } from "@tanstack/react-query";
import { apiClient } from "./client";
import { parseApiError } from "./errors";

type User = { id: string; email: string };

export function useCurrentUser(options?: UseQueryOptions<User, Error>) {
  return useQuery<User, Error>({
    queryKey: ["me"],
    queryFn: async () => {
      const { data, error } = await apiClient.GET("/users/me");
      if (error) throw parseApiError(error);
      return data as User;
    },
    retry: 1,
    staleTime: 30_000,
    ...options,
  });
}
```

## Mutation Hook Pattern

```ts
import { useMutation, type UseMutationOptions } from "@tanstack/react-query";
import { apiClient } from "./client";
import { parseApiError } from "./errors";

type CreateTodoInput = { title: string };
type CreateTodoOutput = { id: string; title: string };

export function useCreateTodo(
  options?: UseMutationOptions<CreateTodoOutput, Error, CreateTodoInput>
) {
  return useMutation<CreateTodoOutput, Error, CreateTodoInput>({
    mutationFn: async (body) => {
      const { data, error } = await apiClient.POST("/todos", { body });
      if (error) throw parseApiError(error);
      return data as CreateTodoOutput;
    },
    retry: 0,
    ...options,
  });
}
```

## Rules

- Never handwrite API response interfaces when OpenAPI already defines them.
- Keep hooks thin: transport + error mapping only.
- Keep cache keys stable and explicit.
- Put invalidation logic in `onSuccess` of mutation hooks.
