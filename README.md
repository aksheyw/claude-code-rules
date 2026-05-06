# Claude Code Rules — My Opinionated Global Config

> **A small set of rules I load into every Claude Code session — covering honesty, TDD, immutability, branching, and what "done" actually means.**

This is the global config that auto-loads across all my Claude Code projects. It enforces the disciplines I've found compound over time: never fabricate, always TDD, never silently mutate, never push to main outside a gated ship action.

## Why I built this

I kept watching Claude Code drift away from my conventions across sessions — sometimes mid-session. I'd say "TDD please" once, get good behavior, then lose it after a context reset.

Putting these as **global rules** (auto-injected into every session) means I don't have to re-establish the conventions every time. They're the spine of how I work.

The standout file is [`honesty.md`](rules/honesty.md) — a two-rule system on **earned confidence** that catches the most common Claude Code failure mode: confident-sounding claims based on shallow checks. The rule explicitly tells Claude to default to verbal hedges ("I think...", "based on what I see...") instead of confidence numbers, and to reserve "95% confident" for *after* end-to-end homework. The result is fewer inflated claims and more honest "I haven't verified X — want me to check?" check-ins mid-task.

## What's in this repo

| Rule | What it enforces | Lines |
|------|------------------|-------|
| `coding-style.md` | Immutability, file size, error handling, Zod validation | 26 |
| `honesty.md` | Never fabricate. Earned confidence (95% gate). | 40 |
| `workflow.md` | Plan → Branch → TDD → Review → Ship. End-of-session sync. Branch strategy. | 113 |
| `testing.md` | 80% coverage minimum + mandatory browser walkthrough before "done" | 20 |
| `performance.md` | Context-window discipline, ultrathink, model routing | 16 |
| `patterns.md` | API response shape, custom hooks, repository pattern | 19 |
| `security.md` | Pre-commit security checklist | 17 |
| `decisions.md` | DECISIONS.md convention for architectural decisions | 15 |

## The standout: honesty.md

Most useful rule of the set. Two principles:

**Rule 1 — Never fabricate.** If I don't have what was asked for, say so. No invented file paths, function names, line numbers, API signatures, or "what the answer probably is."

**Rule 2 — Earned confidence.** The "95% confident" claim is a HIGH bar, not a default. Surface-level checks (read one file and pattern-matched) don't qualify. End-to-end homework (traced the full code path, verified data shape at each boundary, checked failure modes, ran tests) does.

Result: Claude defaults to verbal hedges (*"I think...", "based on what I see..."*) instead of numbers, and only commits to confidence after doing the work. Massive trust improvement.

## Install

```bash
git clone https://github.com/aksheyw/claude-code-rules.git
cd claude-code-rules

# Install to your global rules directory
mkdir -p ~/.claude/rules
cp rules/*.md ~/.claude/rules/
```

The rules auto-load into every Claude Code session via the global config. You don't need to invoke them — they're injected into context at session start.

## Customizing

Each rule is one file. Read them, keep what you like, edit or delete the rest. They're meant to be opinionated — fork rather than wrap.

The `workflow.md` Branch Strategy and End-of-Session Sync sections are the most stack-specific. Swap test commands, doc paths, and wiki conventions for whatever your project uses.

## Dependencies

**None.** These rules don't depend on any Claude Code plugin (superpowers, ECC, etc.) and don't ship with any agents. They're pure markdown + frontmatter that auto-loads into your global Claude Code context.

A few rules mention generic agent names (`planner`, `code-reviewer`, `build-error-resolver`, `security-reviewer`) as part of the recommended workflow. These aren't provided here — bring your own (or use any of the common Claude Code agent packs). If you don't have those agents, the workflow still works as a discipline; you just run the steps manually instead of dispatching subagents.

## Companion repos

These rules pair well with two other Claude Code repos I've published:
- [`claude-code-deep-review`](https://github.com/aksheyw/claude-code-deep-review) — the 14-lens review skill referenced in this repo's review discipline
- [`claude-code-pm-agents`](https://github.com/aksheyw/claude-code-pm-agents) — 7 product-builder subagents (PM, growth, brand, ASO, SEO, YouTube, comms triage) that operate within these workflow conventions

## License

MIT — see [LICENSE](LICENSE).

---

Built by [Akshey Walia](https://github.com/aksheyw). If a rule conflicts with your context or you've improved on one, open an issue or PR.
