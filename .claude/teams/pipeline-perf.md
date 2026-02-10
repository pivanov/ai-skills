# Pipeline Health Investigation Team

Run this team to audit the content generation pipeline for bugs, correctness issues, edge cases, and performance problems.

---

Create an agent team called "pipeline-health" with 4 teammates using Sonnet.
Use delegate mode - do NOT do the work yourself, only coordinate and synthesize.

1. "Draft Phase Auditor" - Audit the draft phase end-to-end. Analyze packages/agents/src/agents/writer.ts, writer-prompts.ts, writer-tools.ts, and orchestrator/phases/draft-phase.ts. Look for: logic bugs, unhandled edge cases, error handling gaps, prompt inconsistencies, incorrect tool usage, race conditions, and performance waste (redundant LLM calls, oversized prompts, sequential work that could be parallel).

2. "Critique Phase Auditor" - Audit the critique and re-draft flow. Analyze packages/agents/src/agents/critic.ts, critic-prompts.ts, critic-schema.ts, and orchestrator/phases/critique-phase.ts. Look for: scoring bugs, dimension weight issues, feedback that gets lost between critique and re-draft, edge cases in pass/fail logic, and performance waste (unnecessary re-evaluations, redundant artifact reads).

3. "Validator & Enforcer Auditor" - Audit all validators and enforcers. Check packages/agents/src/validators/ and enforcers/. Look for: validators that can produce false positives/negatives, missing edge case handling, validators that contradict each other, repair logic bugs, and performance waste (sequential checks that could be parallel, duplicate work across validators).

4. "Orchestration Auditor" - Audit the state machine and phase coordination. Analyze orchestrator/state-machine.ts, all phase files in orchestrator/phases/, and runtime-config/. Look for: invalid state transitions, phases that can silently fail, data lost between phase handoffs, config values that aren't respected at runtime, and performance waste (unnecessary transitions, recomputed data, missing early-exit paths).

Have teammates share findings and challenge each other. If one auditor finds a problem, others should check whether it cascades into their area.

After all teammates report, synthesize a single "Pipeline Health Report" with:
- BUGS: confirmed logic errors or incorrect behavior
- RISKS: edge cases that could fail under certain conditions
- PERFORMANCE: waste or inefficiency worth addressing
- IMPROVEMENTS: suggestions for robustness or maintainability

Prioritize each finding as P0 (fix now) / P1 (fix soon) / P2 (nice to have).
