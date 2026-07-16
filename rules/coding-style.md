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

## Deliberate Simplifications — name the ceiling (the `ponytail:` convention)

When you knowingly ship a shortcut — a global lock, an O(n²) scan, a naive heuristic, a hardcoded
limit "for now" — mark it with a comment that names BOTH the ceiling and the upgrade trigger:

```javascript
// ponytail: global lock — switch to per-account locks if throughput matters
// ponytail: O(n²) scan, fine under ~500 rows — index it past that
```

This turns an untracked corner-cut into an auditable decision. A shortcut with a named trigger is a
different thing from a TODO: it carries the condition under which the laziness stops being correct.
A `ponytail:` comment with NO trigger is the one that silently rots — never leave one bare.

Harvest them anytime with `grep -rnE '(#|//) ?ponytail:' .` for a debt ledger. (Convention lifted
from DietrichGebert/ponytail.)
