# Agent Orchestration

## Immediate Agent Usage

No user prompt needed (bring your own agents, or use any common agent pack — the roles matter, not
the pack):

1. Complex feature requests — use a **planner** agent
2. Code just written/modified — use a **code-reviewer** agent
3. Bug fix or new feature — use a **tdd-guide** agent
4. Architectural decision — use an **architect** agent

## Pick the agent TYPE before you write the brief

Built-in agent types differ in what they load. Claude Code's read-only `Explore` and `Plan` agents
skip your CLAUDE.md files and project rules to stay fast and cheap; other subagents load them
([Claude Code docs](https://code.claude.com/docs/en/sub-agents)).

- If the task only reads and searches, the read-only type is the cheaper choice.
- **The trade: it does not see your CLAUDE.md files or project rules.** Its brief must carry any
  binding context, or it will reopen a question you already settled.
- **A read-only agent cannot write a file.** If you want its report on disk, the main session
  writes it from the returned text. Say so in the brief.

## Parallel Task Execution

ALWAYS use parallel Task execution for independent operations:

```markdown
# GOOD: Parallel execution
Launch 3 agents in parallel:
1. Agent 1: Security analysis of auth.ts
2. Agent 2: Performance review of cache system
3. Agent 3: Type checking of utils.ts

# BAD: Sequential when unnecessary
First agent 1, then agent 2, then agent 3
```

## Multi-Perspective Analysis

For complex problems, use split role sub-agents:
- Factual reviewer
- Senior engineer
- Security expert
- Consistency reviewer
- Redundancy checker

## Delegation Scales With Tier (model-agnostic)

**Why:** a capable model's scarcest resource is its own judgment and context, not its
ability to grind. Spending a strong model on mechanical work is the waste; spending it
on orchestration and the hard calls is the point. So how much you delegate tracks how
capable the model you're running is — stated in tiers, not model names, so it survives
model churn.

- **More capable the model → push more work down.** Keep your context for judgment;
  hand off the mechanical and the parallelizable. A weak-tier model does the work
  itself; a strong-tier model orchestrates.
- **Children inherit nothing — brief every one fully:** context, why, and what "done"
  looks like. It starts blank.
- **Escalation is the one move up:** the parent need not be the top model — spawn a
  stronger child for the single hard call, take its answer, return.
- **Work above your tier? Return it,** don't burn tokens grinding a call you're not
  the right model to make.
- **When NOT to delegate (the conflict case):** don't hand off the judgment itself,
  and don't push an irreversible or money-path step to a cheaper child just to save
  tokens. There, correctness beats delegation — reversibility and blast radius override
  the push-work-down instinct.

> This rule sets *who does the work*. `effort-and-pause-discipline.md` sets *how
> carefully* (by reversibility + blast radius). When they conflict, blast radius wins.
