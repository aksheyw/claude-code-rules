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
git grep -inE "(secret|api[_-]?key|password|access[_-]?token)[[:space:]]*[:=][[:space:]]*[\"'\`][A-Za-z0-9_+/=-]{20,}"
```

If ANY pattern matches: STOP. Choose one BEFORE pushing:

- **Option A (≤3 commits, fresh repo):** redact in working tree → `rm -rf .git && git init` → single clean initial commit → push.
- **Option B (medium history):** redact in working tree → `git filter-repo --replace-text <secrets-list>` before push (no force needed for first push to new remote).
- **Option C (must rotate first):** rotate live secret values in their origin systems (provider dashboards, consoles) THEN apply Option A or B.

**NEVER:** redact in HEAD, commit redactions, push. Earlier commits still expose the full values. The "private repo" status reduces but does NOT eliminate exposure (collaborator-add or visibility-flip re-opens the surface).

## Identity numbers

**Run these on EVERY push of a repo that holds data parsed from documents**, not only the first
push. Key-shape patterns like the ones above do not match a tax ID or an account number. These
patterns are Indian identifiers; adapt them to your country's.

```bash
# Indian PAN (5 letters, 4 digits, 1 letter)
git grep -nwE '[A-Z]{5}[0-9]{4}[A-Z]'
# Aadhaar / PRAN / other 12-digit identifiers (expect false positives; read the context)
git grep -nwE '[0-9]{12}'
# IFSC (bank branch code)
git grep -nwE '[A-Z]{4}0[A-Z0-9]{6}'
# Masked account tails
git grep -nwE 'X{2,}[0-9]{4,}'
# The words that sit next to an identifier
git grep -inwE '(pan|aadhaar|folio|demat|client[ _-]?(id|code)|uan|pran|policy[ _-]?no)'
```

Use `-w` for whole words, not `\b` inside the pattern, and `[[:space:]]`, not `\s`. Tested on macOS
with Apple's git 2.50.1, a `\b` pattern under `git grep -E` matched nothing, even on a line it should
match, and `\s` did not match a space. Both look exactly like a clean repo. Read the
context of every hit before acting: a 12-digit run is often a quantity and a 10-digit run a Unix
timestamp, and documentation that mentions these words will match the keyword scan. Judge each
hit; redact only a confirmed real identity value, never bulk-redact on a regex alone.

**Sweep the full history too**, because a working-tree grep misses what is already committed:
`git log --all -p --no-color > /tmp/hist.txt`, then grep that file. **Prove the method works
first:** make it find a string you KNOW is in the history. A silently wrong command reads exactly
like "no findings".

## Security Response

If issue found: STOP → use **security-reviewer** agent → fix CRITICAL first → rotate exposed secrets → review codebase for similar issues.
