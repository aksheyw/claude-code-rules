# Coding Style

## Immutability (CRITICAL)

ALWAYS create new objects, NEVER mutate:
```javascript
// CORRECT: return { ...user, name }
// WRONG: user.name = name; return user
```

## File Organization

Many small files > few large files. 200-400 lines typical, 800 max. Organize by feature/domain.

## Error Handling

Always handle errors: try/catch with descriptive messages, throw user-friendly errors.

## Input Validation

Validate user input with Zod schemas at system boundaries.

## Quality Checks

Functions <50 lines, files <800 lines, no deep nesting (>4 levels), no console.log, no hardcoded values, immutable patterns.
