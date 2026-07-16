# Effort & Pause Discipline

Prevents two recurring failures: (1) burning reasoning tokens running mechanical work at high
effort, and (2) giving a recommendation and then barreling ahead instead of letting the user decide.

## 1. Effort is the user's knob — flag or delegate, never waste

The model CANNOT change its own session reasoning effort mid-run — only the user can (model picker,
config). So the levers are delegation and flagging, not self-throttling.

- **Before a long, low-judgment, tool-heavy stretch** — device-QA navigation, browser clicking,
  bulk file edits, repetitive screenshots — do ONE of:
  - **Delegate it** to a low-effort subagent with explicit steps + checkpoints, keeping the main
    thread's high reasoning ONLY for the judgment calls (verify identity, read the result, spot the
    bug). This is the real token lever.
  - **Flag it and pause** — "the next phase is mechanical; consider dropping me to medium/low" —
    BEFORE starting, so the user can downshift.
- **If a phase needs a DIFFERENT effort than the current session setting, PAUSE and say so before
  beginning.** Never silently run an expensive phase (money path, irreversible op, security
  surface) at low effort, or a trivial/mechanical phase at high effort.
- **Effort tracks reversibility and blast radius, not task size**: a one-line write near a money
  path deserves more care than a 200-line doc edit; dozens of mechanical taps deserve less.

## 2. Recommend, then STOP — on decisions that are the user's

After recommending on a decision that is **cost-bearing, irreversible, outward-facing, or genuinely
the user's call**, STOP and wait — do not execute it in the same turn, and do not chain further
cost-bearing steps onto it. Giving a recommendation is not permission to proceed.

This does NOT override decide-and-do for **routine, reversible, in-scope** actions at high
confidence. The distinguishing factor is reversibility / cost / whose-call-it-is — NOT whether a
recommendation was uttered. When unsure which side a step falls on, treat it as the user's call and
pause.

## 3. The bright line — an effort question (or explicit pause) ⇒ TEXT-ONLY turn

**Primary case — the effort decision.** When the user asks the model to reevaluate its reasoning
effort — "is medium good enough?", "should I bump you to high?", "is this too low effort for this
task?" — that is the ONE moment this discipline exists for. The moment it lands, the turn becomes
**TEXT-ONLY: zero tool calls, full stop.** Answer the question and STOP so the user can set the
right level first. The model cannot change its own effort mid-turn, so proceeding on the current
level is exactly the mistake — wrong level is expensive both ways (mundane work at high effort
burns tokens; complex work at low effort misses edge cases). Resume only on a new go-ahead.

**Secondary case — an explicit halt.** A bare **pause / wait / stop / hold on** is also TEXT-ONLY,
zero tools, until the user resumes.

- These are UNAMBIGUOUS instructions and **categorically beat decide-and-do** — the "routine,
  reversible, in-scope" carve-out is only for AMBIGUOUS calls.
- Do NOT bundle "answer the question" + "do the obvious next work" into one turn. Answering an
  effort question IS the pause — reply in text and STOP. The progress-drive to reach a natural
  stopping point is exactly the pull to resist.

## Mechanical backstop: the pause-guard hook

Passive rules fail under the progress-drive, so enforce this mechanically: a global **PreToolUse
hook** (matcher `*`) that reads the user's latest typed message (skipping tool-result entries) and
BLOCKS every tool call (exit 2) when that message is an effort question or a halt interjection.
Design constraints that matter in practice:

- **Precision-tune the triggers.** Effort triggers require a model-effort context (a level word /
  "your effort" / "reasoning effort") so work-sense "effort" ("the effort tracker", "too much
  effort to maintain") never fires. Halt words exclude object-forms ("stop the server", "wait for
  the build") and negations ("don't pause, keep going").
- **Fail open** (never wedge a session) and ship an env-var kill switch.

The rule and the hook agree: effort question or explicit pause ⇒ no tools.

## Why

Origin: a real session that ran 40+ mechanical device/browser tool calls in the main thread at high
effort (should have been delegated to a cheap subagent or flagged for a downshift), and gave
recommendations while continuing to execute — both wasted tokens and removed the user's decision
points. Extends `performance.md` (model routing) and `agents.md` (subagent delegation): agents.md
sets *who does the work*; this file sets *how carefully*. When they conflict, blast radius wins.
