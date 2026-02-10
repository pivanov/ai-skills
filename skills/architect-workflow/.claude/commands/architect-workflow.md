# Architect Mode

You are now entering **Architect Mode** — a deterministic framework for AI software engineering.

**CRITICAL: Do NOT write any code until Phase 5.**

## Your Goal

$ARGUMENTS

## Instructions

Read and follow `.claude/teams/architect.md` exactly. It defines a 6-phase workflow:

1. **Deconstruct** — Break down the goal, formulate research questions, confirm with user
2. **Research** — Spawn parallel Explore agents to investigate the codebase
3. **Plan** — Write PLAN.md with steps, tasks, success criteria, sandboxes
4. **Approve** — Present plan to user, iterate until approved
5. **Execute** — Spawn Implementer (Opus) + Reviewer (Sonnet/Bash) agents, enforce accept/reject loops
6. **Integrate** — Final checks, delete temp files (PLAN.md, KNOWLEDGE.md), completion report

Key rules:
- Use `TeamCreate` with team name "architect"
- For small tasks (≤3 files), use Quick Mode — skip Phases 1-2
- Phases 1-4: NO code, research and planning only
- Phase 2: Use `Explore` subagent type for Researchers
- Phase 5: Delegate ALL implementation to teammates — do not code yourself
- Phase 5: Implementers use `general-purpose` (Opus), Reviewers use `Bash` (Sonnet)
- Implementers read both PLAN.md and KNOWLEDGE.md
- Do NOT tell teammates to write tests unless Success Criteria requires it
- Every task needs measurable Success Criteria and a file Sandbox
- Reviewer must ACCEPT before a task is done (max 3 revision cycles)
- Full test suite must pass between Steps

Begin with Phase 1: Deconstruct the Goal.
