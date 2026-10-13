# Capture Discipline — chat is NOT storage

The recurring failure this fixes: important things discussed in a session — subagent research and
verdicts, decisions, numbers, findings — are left in chat, then compressed out of context and LOST.
A per-project note does not fix a cross-project failure; a global rule does.

This is the enforcement layer for `honesty.md` Rule 3 ("capture before you claim"). That states the
principle; this file makes it an operational discipline with a mechanical backstop.

## The hard rule

**The moment a subagent returns substantive output, OR a decision / number / finding / research
result is produced in chat, write it to a FILE before synthesizing or moving on.**

- A distilled synthesis is NOT a substitute for saving the raw source. Save BOTH — the verbatim
  source (so nothing is lost) and the synthesis (so it's usable).
- Subagent outputs are the #1 loss vector: they arrive in context, get summarized, and their full
  text is gone by session end. Save each substantive subagent output to a file when it returns.
- **Main-thread research is the #2 loss vector.** Web-search result sets, a fetched page's
  findings, a verified fact + its source URL, a tooling inventory — these feel "handled" the moment
  they're used in a deliverable, so the temptation is to move on and let them die in chat. **Using
  research is NOT capturing it.** Any substantive research output goes to a FILE the moment it
  exists — with its **source URL and an explicit verification status** — even when its immediate
  purpose is already served. Recording it is also a correctness check, not just archival: writing
  down "verified via <URL>" surfaces the citation you never actually opened.
- **RECORD, don't pre-build.** When research yields a set of reusable patterns (templates,
  playbooks, options), write the spec for all of them and build NONE until a real task calls for
  one. A spec'd option costs nothing; pre-building the set burns tokens on work nobody asked for.
  Build-on-demand is also better learning: each build is validated by a real use.
- Chat/context is ephemeral. If it is not in a file, it does not exist for the next session or for
  verification.

## On a team, other people's agents only see what reaches their copy of the repo

When every teammate drives their own coding agent in their own clone, planning and design that
nobody writes down stay in one agent's context window. A teammate's agent sees only what has
reached its own clone. So a teammate who did a lot of design work can look, to everyone else's
agent, as if they did very little.

- **Commit and push the thinking, not just the artifact.** Design notes and the reasoning behind a
  decision go into the repo as Markdown, or they do not exist for anyone else's agent.
- **When an agent's read of a teammate's work seems unfairly small, suspect this first.** Ask what
  has been pushed before concluding what was done.

## Where to save

- Project-relevant analysis → the project (`tasks/…`, the wiki, or a `docs/` file) — wherever the
  next session will look first.
- A mechanical backstop (below) can auto-capture a foreground subagent's raw output to a global
  directory — but that is a safety net, NOT an excuse to skip deliberately filing the important
  pieces where the next session will look.

## Mandatory at every session wrap-up: the chat-capture audit

Before claiming "saved/synced", enumerate everything substantive produced this session (every
subagent output, decision, research result, key number) and confirm each lives in a FILE. If unsure
whether something is saved, OPEN the file — never assume. Record the audit as a short ledger
(what → where) so the capture is itself auditable.

## Mechanical backstop (does not depend on recall)

Wire a global **PostToolUse hook** on the subagent tool (matcher `Task`/`Agent`) that auto-writes
a substantial subagent final output to a dated directory, e.g.
`~/.claude/agent-outputs/<project>/<date>/<time>-<agent>.md`, and emits a reminder to fold the key
findings into a durable project doc. Design constraints: it FAILS OPEN (never blocks a session) and
has an env-var kill switch.

**The hook only sees the final report of a foreground subagent.** It reads the tool's response. A subagent launched in
the background returns a launch notice there, not its report. The report arrives later, after the
hook has fired, and the hook never sees it. So brief every subagent to write its own report to a
file before it returns, or have the main session write the report to a file the moment it arrives.

## Why (origin)

A real incident: a session ran a large multi-agent review plus a research subagent, saved only the
syntheses, and the full research report and every individual verdict had to be recovered from raw
transcripts at session end. The fix had been written into one project's notes before — a global
rule plus the hook is what actually stopped the recurrence.
