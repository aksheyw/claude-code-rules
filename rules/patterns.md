# Common Patterns

## API Response Format

```typescript
interface ApiResponse<T> {
  success: boolean; data?: T; error?: string;
  meta?: { total: number; page: number; limit: number }
}
```

## Custom Hooks

Follow `useDebounce` pattern: state + useEffect with cleanup timer.

## Repository Pattern

Interface with: `findAll`, `findById`, `create`, `update`, `delete`.
