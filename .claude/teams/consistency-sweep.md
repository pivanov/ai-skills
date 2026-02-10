# Consistency Sweep Team

Paste this into a Claude session after making code changes.

---

Create an agent team called "consistency-sweep" with 4 teammates using Sonnet.
Use delegate mode - do NOT do the work yourself, only coordinate and synthesize.

1. "Docs Verifier" - Compare docs/system-guide/ against actual code.
   Check that:
   - 03-pipeline.md matches orchestrator phases in packages/agents/src/orchestrator/phases/
   - 05-configuration.md matches runtime config types and defaults in packages/agents/src/runtime-config/
   - 12-tools-reference.md matches actual tools in packages/agents/src/tools/
   - 13-skills-catalog.md matches SKILL.md files in packages/agents/src/skills/
   - 14-api-endpoints.md matches route handlers in apps/api/src/routes/
   Report every mismatch with file:line references.

2. "Dashboard Verifier" - Compare dashboard settings UI against backend config.
   Check that:
   - Settings components in apps/dashboard/src/settings/ expose all fields from IRuntimeConfig in packages/agents/src/runtime-config/types.ts
   - Dashboard types in apps/dashboard/src/types/ match API response shapes
   - Job form fields match the Job creation API endpoint
   - Architecture page components match actual system structure
   Report missing fields, stale options, or type mismatches.

3. "Config Verifier" - Check defaults, constants, and schemas are in sync.
   Check that:
   - Defaults in runtime-config/defaults.ts match validation ranges in runtime-config/validation.ts
   - Shared constants in packages/shared/src/constants.ts match usage across packages/agents/ and apps/api/
   - Prisma schema enums match TypeScript enums in shared/types
   - Config profiles in runtime-config/profiles.ts reference only features that exist in runtime-config/types.ts
   Report any value that's out of range or references something that doesn't exist.

4. "Architecture Verifier" - Check structural docs match reality.
   Check that:
   - 02-architecture.md agent descriptions match actual agent files in packages/agents/src/agents/
   - 11-packages.md package descriptions match actual package contents
   - 15-dashboard-architecture.md matches actual dashboard component tree
   - State machine transitions in docs match orchestrator/state-machine.ts
   Report outdated descriptions, missing components, or wrong flow diagrams.

After all teammates report, synthesize a single "Consistency Report" with:
- CRITICAL: things that would confuse users or cause bugs
- WARNING: stale docs that are misleading but not dangerous
- INFO: minor drift that can be batched
