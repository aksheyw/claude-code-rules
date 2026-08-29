<div align="center">

# 📋 Claude Code Rules: my opinionated global config

### Thirteen standing rules, so I stop re-explaining myself after every context reset.

![requires](https://img.shields.io/badge/requires-Claude%20Code-D97757) ![rules](https://img.shields.io/badge/rules-13-0E9384) ![loads](https://img.shields.io/badge/loads-automatically-1B2A4A) ![dependencies](https://img.shields.io/badge/dependencies-none-lightgrey) ![license](https://img.shields.io/badge/license-MIT-green)

</div>

---

Claude Code is Anthropic's AI coding assistant. It runs in your terminal, reads the files in your
project, and can change them for you. A **rule** is a markdown file it loads into every session on
its own, before you ask it anything.

It forgets everything between sessions, and a long one resets partway through too. I kept
re-teaching it the same conventions and losing them again, so I wrote the thirteen that stuck.

<img src="docs/what-a-rule-changes.svg" width="100%"
     alt="An illustration of the same question asked twice: how confident are you that getUserById returns the canonical user record? Without the rules loaded, the answer is 'I'm 95% confident, it queries the users table directly with the primary key', and the number was never earned because nothing was checked. With honesty.md loaded, the answer is 'I haven't traced the call path yet. Want me to check before I commit to a number? The name suggests a direct lookup, but I'd want to rule out a cache layer or a partial projection first', which is a hedge you can act on and an offer to go and check. Below: thirteen rules load automatically at the start of every session, in four groups. Four on how it reports and learns (honesty, homework, capture, lessons), four on how work ships (workflow, testing, security, decisions), three on how code gets written (coding-style, patterns, performance), and two on how work gets handed off (agents, effort-and-pause).">

## Why a rule beats saying it again

Telling Claude Code to write the test before the code works, right up until it forgets. Then you say
it again. A rule is the same instruction, except it's loaded before you type anything, in every
project, so the convention survives the reset that would otherwise wipe it.

The one I'd read first is [`honesty.md`](rules/honesty.md), which is what the picture above shows. It
treats "95% confident" as a high bar, not a default. Reading one file and pattern-matching doesn't
earn the number. Tracing the path end to end does. So you get fewer confident guesses, and more "I
haven't verified that yet, want me to check?" halfway through a task.

Five of the thirteen name the specific session that produced them. They're
postmortems as much as preferences.

<details>
<summary><b>📋 All thirteen rules, and what each one enforces</b></summary>

13 rules. The original 8, refreshed, plus 5 new ones distilled from another two months of daily use.

| Rule | What it enforces | Lines |
|------|------------------|-------|
| `coding-style.md` | Immutability, file size, error handling, Zod validation, the `ponytail:` deliberate-simplification convention | 42 |
| `honesty.md` | Never fabricate. Earned confidence (95% gate). Capture before you claim. | 49 |
| `homework-first.md` | **New.** The pre-task sweep: skills → memory → rendered benchmark → project files → history → sibling projects. A question you could answer by opening a file IS the violation. | 102 |
| `capture-discipline.md` | **New.** Chat is NOT storage: subagent outputs, research, and decisions go to files the moment they exist. | 61 |
| `effort-and-pause-discipline.md` | **New.** Effort tracks blast radius, not task size. An effort question or explicit pause means a text-only turn. | 75 |
| `agents.md` | **New.** Delegation scales with model tier, parallel subagents, brief children fully, when NOT to delegate. | 60 |
| `lessons.md` | **New.** The self-improvement loop: every user correction becomes a categorized lesson file; repeats escalate to CLAUDE.md. | 36 |
| `workflow.md` | Plan → Branch → TDD → Review → Ship. End-of-session sync. Branch strategy. Persist agent files on both harness sides. | 133 |
| `testing.md` | 80% coverage minimum + mandatory browser walkthrough before "done" | 19 |
| `performance.md` | Context-window discipline, ultrathink, model routing | 15 |
| `patterns.md` | API response shape, custom hooks, repository pattern | 18 |
| `security.md` | Pre-commit security checklist + first-push secret scan for new repos | 43 |
| `decisions.md` | DECISIONS.md convention for architectural decisions | 14 |

</details>

<details>
<summary><b>🔍 What honesty.md says, in full</b></summary>

Three principles, and they work as a set.

**Rule 1: Never fabricate.** If I don't have what was asked for, say so. No invented file paths,
function names, line numbers, API signatures, or "what the answer probably is."

**Rule 2: Earned confidence.** "95% confident" is a high bar, not a default. Surface-level checks
(read one file, pattern-matched) don't qualify. End-to-end homework does: traced the full code path,
verified the data shape at each boundary, checked the failure modes, ran the tests.

**Rule 3: Capture before you claim.** The inverse of Rule 1. Evidence shared in a session gets saved
to a file before it's used or claimed against, and Claude never writes an unverified self-limiting
claim ("the user didn't archive X") into something that ships under the user's name. Verify against
the source, not a copy. When unsure, ask.

Rule 3 exists because the opposite failure is just as expensive as fabricating. A hedge written to
sound humble can quietly understate what you actually built, and it ships under your name either way.

</details>

## Install

You need [Claude Code](https://claude.com/claude-code) itself first, because these are files it
reads. `~/.claude/` is the folder it keeps its own settings in.

```bash
git clone https://github.com/aksheyw/claude-code-rules.git
cd claude-code-rules

mkdir -p ~/.claude/rules
cp -i rules/*.md ~/.claude/rules/
```

`cp -i` asks before replacing anything, which matters because names like `security.md` and
`workflow.md` are exactly what you'd have called your own. Drop the `-i` once you've looked.

That's the whole install. You never invoke a rule: they're injected into context when a session
starts, so the next session you open already has them.

<details>
<summary><b>🔍 Check it worked, and what to do if it didn't</b></summary>

You won't see the rules in the chat window. You'll see the effect, so test for the effect: ask
*"how confident are you about X?"* and a rules-loaded Claude will usually hedge before committing to
a number. The same shift shows up elsewhere: `workflow.md` suggests a branch before you change code,
`testing.md` asks about the browser walkthrough before it agrees something is done, and
`coding-style.md` flags code that mutates an object instead of returning a new one.

You can also check it landed by looking at what a session was given. The rules arrive under a heading
like this:

```
Contents of /Users/<you>/.claude/rules/honesty.md
(user's private global instructions for all projects):

# Honesty & Earned Confidence
…
```

- **Nothing changes:** confirm the files landed flat at `~/.claude/rules/<name>.md`, with no
  subdirectory. That path is **undocumented but functional**, because Claude Code loads it as the
  user's private global instructions. If your version doesn't pick it up, paste the contents into
  your project's `CLAUDE.md` instead, which is the per-project instructions file it always reads.
- **Some rules apply and others don't:** they load alphabetically, and a later rule that contradicts
  an earlier one usually wins for that session. Keep them consistent, or merge the two.
- **A rule mentions agents you don't have:** `workflow.md` and `agents.md` name `planner`,
  `code-reviewer` and others as recommended subagents, meaning separate assistants with one job
  each. They aren't bundled here. Install your own, or treat those steps as a manual discipline.
- **It loaded but drifts mid-session:** long context wears them down. Re-anchor with *"refresh the
  honesty rule"*, or start a fresh session.

</details>

<details>
<summary><b>📦 What is NOT in here</b></summary>

**No dependencies.** These don't need any Claude Code plugin (superpowers, ECC and the rest) and
don't ship any agents. They're plain markdown that loads into your global context.

**No agents.** A few rules name generic ones (`planner`, `code-reviewer`, `build-error-resolver`,
`security-reviewer`) as part of the recommended workflow. Bring your own, or use any agent pack. The
workflow still holds as a discipline, you just run the steps yourself instead of handing them off.

**No hooks.** Two of the new rules (`capture-discipline.md`, `effort-and-pause-discipline.md`)
describe backstop hooks, meaning small scripts that run automatically at a fixed moment so they can
block an action rather than politely ask: a PostToolUse capture hook, which fires after a tool runs,
and a PreToolUse pause guard, which fires before one. Those are patterns to implement yourself, not
shipped scripts. The rules work without them; the hooks just make them mechanical instead of memory-dependent.

</details>

## Make them yours

Each rule is one file. Read them, keep what you like, edit or delete the rest. They're meant to be
opinionated, so fork rather than wrap. The Branch Strategy and End-of-Session Sync sections in
`workflow.md` are the most specific to my setup, so swap the test commands, doc paths and wiki
conventions for whatever your project uses.

## Companion repos

Part of my Claude Code config series:

- [`claude-code-deep-review`](https://github.com/aksheyw/claude-code-deep-review): the 14-lens review skill this repo's review discipline points at
- [`claude-code-pm-agents`](https://github.com/aksheyw/claude-code-pm-agents): 7 product-builder subagents (PM, growth, brand, ASO, SEO, YouTube, comms triage) that work inside these conventions
- [`claude-code-learned-skills`](https://github.com/aksheyw/claude-code-learned-skills): 12 skills taken from real debugging and research sessions, covering Docker, SSH and VPS work, ML pipelines, prompting guides, quality tooling and a project wiki
- [`career-command-center-template`](https://github.com/aksheyw/career-command-center-template): a full plugin template for running a job search with Claude Code, with 12 skills, 8 personal-data files you fill in yourself, and hooks

## License

MIT (see [LICENSE](LICENSE)).

---

Built by [Akshey Walia](https://github.com/aksheyw). If a rule conflicts with your context, or you've
improved on one, open an issue or a pull request.
