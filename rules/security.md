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

## Security Response

If issue found: STOP → use **security-reviewer** agent → fix CRITICAL first → rotate exposed secrets → review codebase for similar issues.
