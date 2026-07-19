# Claude Code Rules: My Opinionated Global Config

> **A small set of rules I load into every Claude Code session, covering honesty, homework-first, TDD, immutability, branching, delegation, and what "done" actually means.**

This is the global config that auto-loads across all my Claude Code projects. It enforces the disciplines I've found compound over time: never fabricate, always TDD, never silently mutate, never push to main outside a gated ship action.

## Why I built this

I kept watching Claude Code drift away from my conventions across sessions (sometimes mid-session). I'd say "TDD please" once, get good behavior, then lose it after a context reset.

Putting these as **global rules** (auto-injected into every session) means I don't have to re-establish the conventions every time. They're the spine of how I work.

The standout file is [`honesty.md`](rules/honesty.md), a three-rule system on **earned confidence** that catches the most common Claude Code failure mode: confident-sounding claims based on shallow checks. The rule explicitly tells Claude to default to verbal hedges ("I think...", "based on what I see...") instead of confidence numbers, and to reserve "95% confident" for *after* end-to-end homework. The result is fewer inflated claims and more honest "I haven't verified X. Want me to check?" check-ins mid-task.

## What's in this repo

13 rules. The original 8, refreshed, plus 5 new ones distilled from another two months of daily use.

| Rule | What it enforces | Lines |
|------|------------------|-------|
| `coding-style.md` | Immutability, file size, error handling, Zod validation, the `ponytail:` deliberate-simplification convention | 42 |
| `honesty.md` | Never fabricate. Earned confidence (95% gate). Capture before you claim. | 49 |
| `homework-first.md` | **New.** The pre-task sweep: skills → memory → rendered benchmark → project files → history → sibling projects. A question you could answer by opening a file IS the violation. | 102 |
| `capture-discipline.md` | **New.** Chat is NOT storage: subagent outputs, research, and decisions go to files the moment they exist, backed by a hook. | 61 |
| `effort-and-pause-discipline.md` | **New.** Effort tracks blast radius, not task size. An effort question or explicit pause means a text-only turn, backed by a hook. | 75 |
| `agents.md` | **New.** Delegation scales with model tier, parallel subagents, brief children fully, when NOT to delegate. | 60 |
| `lessons.md` | **New.** The self-improvement loop: every user correction becomes a categorized lesson file; repeats escalate to CLAUDE.md. | 36 |
| `workflow.md` | Plan → Branch → TDD → Review → Ship. End-of-session sync. Branch strategy. Persist agent files on both harness sides. | 133 |
| `testing.md` | 80% coverage minimum + mandatory browser walkthrough before "done" | 19 |
| `performance.md` | Context-window discipline, ultrathink, model routing | 15 |
| `patterns.md` | API response shape, custom hooks, repository pattern | 18 |
| `security.md` | Pre-commit security checklist + first-push secret scan for new repos | 43 |
| `decisions.md` | DECISIONS.md convention for architectural decisions | 14 |

## The standout: honesty.md

Most useful rule of the set. Three principles:

**Rule 1: Never fabricate.** If I don't have what was asked for, say so. No invented file paths, function names, line numbers, API signatures, or "what the answer probably is."

**Rule 2: Earned confidence.** The "95% confident" claim is a HIGH bar, not a default. Surface-level checks (read one file and pattern-matched) don't qualify. End-to-end homework (traced the full code path, verified data shape at each boundary, checked failure modes, ran tests) does.

**Rule 3: Capture before you claim.** The inverse of Rule 1: evidence shared in a session gets saved to a file before it's used or claimed against, and Claude never writes an unverified self-limiting claim ("the user didn't archive X") into a deliverable that ships under the user's name. Verify against the source, not a copy; when unsure, ask.

Result: Claude defaults to verbal hedges (*"I think...", "based on what I see..."*) instead of numbers, and only commits to confidence after doing the work. Massive trust improvement.

## Install

```bash
git clone https://github.com/aksheyw/claude-code-rules.git
cd claude-code-rules

# Install to your global rules directory
mkdir -p ~/.claude/rules
cp rules/*.md ~/.claude/rules/
```

The rules auto-load into every Claude Code session via the global config. You don't need to invoke them, since they're injected into context at session start.

## Verify install worked

Start a fresh Claude Code session in any project. The rules should appear in the session's system prompt under a heading like:

```
Contents of /Users/<you>/.claude/rules/honesty.md
(user's private global instructions for all projects):

# Honesty & Earned Confidence
…
```

You won't *see* this directly in the chat UI, but you'll see the effect: Claude defaults to verbal hedges instead of confidence numbers, suggests TDD when you ask for a new feature, and flags commit-format violations during reviews. Quickest sanity check: ask *"how confident are you about X?"* and a rules-loaded Claude will hedge before committing to a number.

If behavior doesn't change, see **Troubleshooting** below.

## Example output

After install, watch Claude shift on a `honesty.md`-triggering question:

```
You: How confident are you that function getUserById returns the canonical user record?

Without rules loaded:
> I'm 95% confident that getUserById returns the canonical user record — it queries
> the users table directly with the primary key.

With rules loaded (honesty.md governs):
> I haven't traced the call path yet. Want me to check before I commit to a number?
> What I see so far: the function name suggests a direct lookup, but I'd want to
> verify it's not going through a cache layer or returning a partial projection
> before claiming "canonical."
```

The hedge isn't cosmetic. It's the earned-confidence rule actively preventing a fabricated 95%-claim. Same pattern fires for `workflow.md` (suggests branching before code changes), `testing.md` (asks about browser walkthrough before "done"), and `coding-style.md` (flags mutation patterns).

## Troubleshooting

- **Rule behavior doesn't show up:** confirm files landed at `~/.claude/rules/<name>.md` (flat, no subdir). This path is **undocumented but functional**, because Claude Code loads it as "user's private global instructions." If your version of Claude Code doesn't auto-load `~/.claude/rules/`, paste the file contents into your project's `CLAUDE.md` as a fallback.
- **Some rules apply, others don't:** rules load alphabetically. If a later rule contradicts an earlier one, the later one usually wins for that session. Keep rules consistent or merge conflicting ones.
- **Workflow rule references agents you don't have:** `workflow.md` and `agents.md` mention `planner`, `code-reviewer`, etc. as recommended subagents. They're not bundled here. Either install your own (see [`claude-code-pm-agents`](https://github.com/aksheyw/claude-code-pm-agents) for one bundle) or treat the steps as a manual discipline.
- **Rule loaded but Claude ignores it mid-session:** rules can drift after long context. Re-anchor with *"refresh the honesty rule"* or start a fresh session.

## Customizing

Each rule is one file. Read them, keep what you like, edit or delete the rest. They're meant to be opinionated: fork rather than wrap.

The `workflow.md` Branch Strategy and End-of-Session Sync sections are the most stack-specific. Swap test commands, doc paths, and wiki conventions for whatever your project uses.

## Dependencies

**None.** These rules don't depend on any Claude Code plugin (superpowers, ECC, etc.) and don't ship with any agents. They're pure markdown + frontmatter that auto-loads into your global Claude Code context.

A few rules mention generic agent names (`planner`, `code-reviewer`, `build-error-resolver`, `security-reviewer`) as part of the recommended workflow. These aren't provided here, so bring your own (or use any of the common Claude Code agent packs). If you don't have those agents, the workflow still works as a discipline; you just run the steps manually instead of dispatching subagents.

Two of the new rules (`capture-discipline.md`, `effort-and-pause-discipline.md`) describe optional backstop **hooks**: a PostToolUse capture hook and a PreToolUse pause guard. Those are patterns to implement yourself, not shipped scripts. The rules work without them; the hooks just make them mechanical instead of memory-dependent.

## Companion repos

These rules are part of my Claude Code config series:
- [`claude-code-deep-review`](https://github.com/aksheyw/claude-code-deep-review): the 14-lens review skill referenced in this repo's review discipline
- [`claude-code-pm-agents`](https://github.com/aksheyw/claude-code-pm-agents): 7 product-builder subagents (PM, growth, brand, ASO, SEO, YouTube, comms triage) that operate within these workflow conventions
- [`claude-code-learned-skills`](https://github.com/aksheyw/claude-code-learned-skills): 12 skills auto-extracted from real debugging and research sessions (Docker/SSH/VPS, ML pipelines, prompting guides, quality tooling, a project wiki)
- [`career-command-center-template`](https://github.com/aksheyw/career-command-center-template): full plugin template for an AI-native job-search workflow (12 skills, 8 personal-data skeletons, hooks)

## License

MIT (see [LICENSE](LICENSE)).

---

Built by [Akshey Walia](https://github.com/aksheyw). If a rule conflicts with your context or you've improved on one, open an issue or PR.
