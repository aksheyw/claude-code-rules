# Workflow

## Commit Format

`<type>: <description>` — Types: feat, fix, refactor, docs, test, chore, perf, ci.

## Agent & Working Files — persist and track BOTH sides

If you run more than one coding agent against a repo, ALWAYS commit the agent working files on BOTH sides symmetrically:
- **Claude:** `CLAUDE.md` and `.claude/agents/` (project-specific subagent/persona definitions).
- **Any second harness (e.g. Codex):** its entry file (`AGENTS.md`) and its config directory (`.codex/` — agents, hooks, configs).

**Never gitignore, delete, or leave either side untracked.** A common trap: a project's `.gitignore` has `.claude/*` (to hide local config) which silently ignores `.claude/agents/` too — so a fresh clone loses every agent definition. If you find this, negate `.claude/agents/` back in (`!.claude/agents/` + `!.claude/agents/**`) and commit the agents. Verify with `git check-ignore -v .claude/agents/<file>.md`.

**Persist novel specialist personas the SAME session you create them.** If you spawn a reusable specialist as a subagent (a craft panel, a domain reviewer, a bespoke persona), write it to `.claude/agents/<name>.md` before the session ends — do NOT spawn it inline and lose it. **Capturing an agent's OUTPUT (e.g. into a review doc) is not the same as persisting the AGENT.** The definition is the reusable asset; the output is disposable. New `.claude/agents/*.md` files register on the next session start, so they become invocable-by-name going forward.

## Restructuring an agent-instruction file — check the CONTENT CLASS before collapsing it

Before turning an `AGENTS.md`, `CLAUDE.md`, or any agent-instruction file into a pointer, classify its content:

- **Operative instructions** — gates, conventions, QA steps, self-review requirements, confidence thresholds, anything that changes how a session behaves — stay VERBATIM.
- **Drift-prone facts** — stack versions, structure/file counts, schema, endpoints — become pointers to the single source (CLAUDE.md / a live query).

An entry file is auto-loaded by its harness, and a session reading a bare "see CLAUDE.md" stub may NOT load CLAUDE.md — so a pointer-only entry file can silently strip every gate. Judge each cut from a fresh session's view: would it lose a rule? Then it stays.

Two species exist and they are NOT the same: a stale workflow *inventory* is safe to pointer-collapse; a near-copy of the *operating instructions* is not — keep the gates verbatim and point only the fact sections.

## Feature Workflow

1. **Plan** — Use a planner agent or planning skill; identify dependencies and risks
2. **Branch** — If multi-session or large: create a feature branch (see Branch Strategy below)
3. **TDD** — RED (write failing test) → GREEN (minimal implementation) → IMPROVE (refactor) → 80%+ coverage
4. **Review** — Run a code-reviewer agent immediately after writing code
5. **Ship** — Only push to `main` once all gates pass; never push manually mid-feature

## Document Ownership

Each piece of info has ONE authoritative source. Others cross-reference: `> See [doc] §[section]`.

**Suggested ownership:**
- `CLAUDE.md` — tech stack, structure, conventions
- `ROADMAP.md` — features (index → sub-files for detail)
- `SKILLS.md` — design, schemas, algorithms
- `DECISIONS.md` — architectural decisions
- `docs/INDEX.md` — master navigator
- `memory/MEMORY.md` — session notes, pitfalls

## Two Actions — Never Confuse Them

### CAPTURE — "save and sync" (always safe, no gates)
Triggered by: "save and sync", "wrap up", "end of session", or context window closing.
Runs the end-of-session checklist below.
**Never pushes code to `main`.**

### SHIP — explicit, gated
Triggered by: user says "ship" or equivalent.
Runs all gates: tests pass, lint passes, type-check passes, no secrets leaked.
**Only this action puts code on `main`.**

This separation matters. CAPTURE is constant, low-friction, never destroys work. SHIP is intentional, gated, and the only path to production.

## End-of-Session Sync (MANDATORY — part of CAPTURE)

Every "save and sync" includes ALL of these (adapt the specifics to your stack):

1. **Run tests** — flag failures, update test counts in CLAUDE.md if changed
2. **`git commit` locally** — all changes, always safe
3. **Push docs to remote immediately** (`.md` files only)
4. **If on feature branch: push branch** as remote backup
5. **Wiki sync** (if your project has a wiki):
   - Scan the session for candidate wiki updates: bugs with non-obvious root causes, architectural decisions, new gotchas, drift between code and docs
   - For each candidate, propose the wiki edit and show the diff — user approves per item
   - Lint the wiki: fix broken links, stale `updated:` dates, missing frontmatter, orphans
   - **Manual ingest bar:** capture only NON-OBVIOUS, DURABLE knowledge. If a future session can re-derive it from code or git log, skip it.
6. **Record session summary** in memory / `MEMORY.md`
7. **Update session handoff doc** — current branch, last commit, test status, primary task
8. **Save session narrative** for future resume
9. **Report gate status** — what's still needed before SHIP

At session start, flag and fix: wrong test counts, outdated progress %, stale branch in handoff doc.

## Branch Strategy

| Situation | Branch |
|-----------|--------|
| Small fix, single session | main (local) → ship to main |
| Feature > 1 session, > 3 src/ files, or > 4 hrs | feature branch → ship merges to main |
| Production broken | hotfix branch → fast hotfix ship |

### Feature Branch — Who, When, How

**Who creates it:** The agent proposes, user approves. Never created silently.

**When:** At the START of session 1 of the feature — before writing any code.
The agent says: *"This looks like multi-session work. Create `feature/[name]` now?"*

**Naming convention:**
- New feature: `feature/[short-description]` (e.g. `feature/monetization-tiers`)
- Multi-session fix: `fix/[short-description]` (e.g. `fix/payment-flow`)
- Emergency: `hotfix/[short-description]` (e.g. `hotfix/camera-crash`)

**How to create:**
```bash
git checkout main
git pull origin main          # always start from latest main
git checkout -b feature/[name]
git push -u origin feature/[name]  # set remote tracking immediately
```

**Each "save and sync" on a feature branch:** push to that branch (not main). This is safe remote backup.

**Retroactive branching** (session 1 already done on main, not yet pushed):
```bash
git checkout -b feature/[name]   # create branch at current HEAD
# main local now also has your commits — reset it:
git checkout main
git reset --hard origin/main     # ⚠ confirm with user BEFORE running this
git checkout feature/[name]
```
The agent must show this plan and get explicit "yes" before running `git reset --hard`.

**If already pushed to main:** too late for a feature branch. Continue on main, ensure all ship gates pass before next push.

## Rules

- NEVER skip the end-of-session sync
- NEVER push code to main outside of an explicit ship action
- NEVER merge to main without all gates passing (merge guard warns + requires approval)
- NEVER duplicate authoritative content — use cross-references
- NEVER auto-commit wiki edits without showing the user the proposed diffs first
- ALWAYS update test stats after adding tests
- ALWAYS record decisions that affect future sessions in memory
- ALWAYS run wiki sync if the project has a wiki — wikis compound value only if every session feeds them
