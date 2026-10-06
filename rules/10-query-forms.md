### 10.19 TanStack Query v5 + Forms

**Query factory pattern (FE-API-003):**

```typescript
// src/api/queries/use-query-entities.ts
import { generateQueryOptions } from '@/api/helpers/generate-query-options';
import { ENTITY_ENDPOINTS } from '@/constants/api-endpoints';

export function entitiesQueryOptions() {
  return generateQueryOptions<T_BASE_ENTITY[]>({
    queryKey: ['entities'],
    url: ENTITY_ENDPOINTS.LIST(),
    staleTime: 5000,
    select: (data) => data.map(transformEntity), // transform T_BASE_* → T_*
  });
}

export function useQueryEntities() {
  return useQuery(entitiesQueryOptions());
}
```

**Optimistic updates (cache-based, FE-API-009):**

```typescript
// src/api/mutations/use-mutate-create-entity.ts
import { useCustomMutation } from '@/api/helpers/use-custom-mutation';
import { ENTITY_ENDPOINTS } from '@/constants/api-endpoints';
import { entitiesQueryOptions } from '@/api/queries/use-query-entities';

export function useCreateEntity() {
  return useCustomMutation({
    url: ENTITY_ENDPOINTS.CREATE(),
    successMessage: 'Entity created',
    errorMessage: 'Failed to create entity',
    invalidateQueryKeys: [entitiesQueryOptions().queryKey],
    onMutate: async (newEntity) => {
      await queryClient.cancelQueries({ queryKey: entitiesQueryOptions().queryKey });
      const previous = queryClient.getQueryData(entitiesQueryOptions().queryKey);
      queryClient.setQueryData(entitiesQueryOptions().queryKey, (old) => [
        ...(old ?? []),
        { ...newEntity, id: `temp-${Date.now()}`, pending: true },
      ]);
      return { previous };
    },
    onError: (_err, _vars, context) => {
      queryClient.setQueryData(entitiesQueryOptions().queryKey, context?.previous);
    },
  });
}
```

**Cross-component pending UI (`useMutationState`):**

```tsx
import { useMutationState } from '@tanstack/react-query';

function EntityList() {
  const creatingMutations = useMutationState({
    filters: { mutationKey: ['createEntity'], status: 'pending' },
    select: (m) => m.state.variables,
  });

  return (
    <>
      {creatingMutations.map((vars, i) => (
        <Skeleton key={i} data-id="entity-creating-skeleton" />
      ))}
      <EntityTable />
    </>
  );
}
```

**TanStack Form + Zod validation (FE-FORM-001/002):**

```typescript
import { useForm } from '@tanstack/react-form';
import { zodValidator } from '@tanstack/zod-form-adapter';
import { z } from 'zod';

const schema = z.object({
  name: z.string().min(1, 'Required'),
  email: z.string().email('Invalid email'),
});

function EntityForm() {
  const form = useForm({
    defaultValues: { name: '', email: '' },
    validators: { onSubmit: zodValidator(schema) },
    onSubmit: async ({ value }) => {
      await useCreateEntity().mutateAsync(value);
    },
  });

  return (
    <form onSubmit={form.handleSubmit}>
      <input {...form.getFieldProps('name')} />
      {form.getFieldState('name').meta.errors[0] && <span>{form.getFieldState('name').meta.errors[0]}</span>}
      <input {...form.getFieldProps('email')} />
      <button type="submit">Save</button>
    </form>
  );
}
```