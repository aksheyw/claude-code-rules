# Self-Improvement Loop

After ANY user correction, write a lesson to the appropriate category file in `tasks/lessons/`.

**Trigger:** user corrects you, redirects you, or says "no, not like that"

## Where to Write

`tasks/lessons.md` is a **categorized index** — do NOT write lessons there. Write to a matching
sub-file, organized by domain. Pick categories that fit your project, e.g.:

- `tasks/lessons/frontend-ux.md` — UI frameworks, CSS, UX patterns
- `tasks/lessons/backend-infra.md` — servers, databases, deploys, webhooks
- `tasks/lessons/testing-qa.md` — test frameworks, E2E, device QA
- `tasks/lessons/external-apis.md` — third-party APIs, CORS, auth flows
- `tasks/lessons/process-workflow.md` — development process, review discipline
- `tasks/lessons/tooling.md` — build tools, CLIs, browser automation

After writing the lesson, add a one-line summary to the `tasks/lessons.md` index under the matching
category.

## Format

```markdown
## [Date] - [Short description]
**Mistake:** What went wrong
**Correction:** What the user wanted
**Rule:** Never [do X]. Always [do Y] in this project.
```

## Rules

- At session start: read the `tasks/lessons.md` index to scan all rules before any work
- One lesson per correction — don't batch
- If the same mistake appears twice, escalate it to a CLAUDE.md rule
- Keep lessons specific and actionable
