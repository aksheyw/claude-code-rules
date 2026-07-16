# Security Guidelines

## Pre-Commit Checks

- [ ] No hardcoded secrets — use env vars
- [ ] All user inputs validated
- [ ] SQL injection prevention (parameterized queries)
- [ ] XSS prevention (sanitized HTML)
- [ ] CSRF protection enabled
- [ ] Auth/authz verified
- [ ] Rate limiting on endpoints
- [ ] Error messages don't leak sensitive data

## Pre-Push Checks — FIRST push of any NEW repo

> Origin: a real incident where secrets in two early commits made it to remote history despite HEAD redaction.

Before the FIRST push of any new repo, run from repo root:

```bash
# JWT (eyJ-prefix, 3-part)
git grep -E 'eyJ[A-Za-z0-9_=-]+\.[A-Za-z0-9_=-]+\.[A-Za-z0-9_.+/=-]{20,}'
# OpenRouter
git grep -E 'sk-or-v1-[A-Za-z0-9]{20,}'
# OpenAI-style
git grep -E 'sk-[A-Za-z0-9]{30,}'
# Telegram bot token
git grep -E '[0-9]{8,12}:[A-Za-z0-9_-]{30,50}'
# Generic key=value secret assignment
git grep -E '(secret|api[_-]?key|password|access[_-]?token)\s*[:=]\s*["\047`][A-Za-z0-9_+/=-]{20,}'
```

If ANY pattern matches: STOP. Choose one BEFORE pushing:

- **Option A (≤3 commits, fresh repo):** redact in working tree → `rm -rf .git && git init` → single clean initial commit → push.
- **Option B (medium history):** redact in working tree → `git filter-repo --replace-text <secrets-list>` before push (no force needed for first push to new remote).
- **Option C (must rotate first):** rotate live secret values in their origin systems (provider dashboards, consoles) THEN apply Option A or B.

**NEVER:** redact in HEAD, commit redactions, push. Earlier commits still expose the full values. The "private repo" status reduces but does NOT eliminate exposure (collaborator-add or visibility-flip re-opens the surface).

## Security Response

If issue found: STOP → use **security-reviewer** agent → fix CRITICAL first → rotate exposed secrets → review codebase for similar issues.
